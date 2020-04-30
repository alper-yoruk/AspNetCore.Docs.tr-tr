---
title: ASP.NET Core oturum
author: rick-anderson
description: İstekler arasındaki oturumu korumak için yaklaşımları bulur.
ms.author: riande
ms.custom: mvc
ms.date: 03/06/2020
no-loc:
- SignalR
uid: fundamentals/app-state
ms.openlocfilehash: 706468d44ddabbd3a695dbb60aaf1be15fe166e2
ms.sourcegitcommit: f9a5069577e8f7c53f8bcec9e13e117950f4f033
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82558897"
---
# <a name="session-and-state-management-in-aspnet-core"></a>ASP.NET Core 'de oturum ve durum yönetimi

::: moniker range=">= aspnetcore-3.0"

[Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkabağı](https://twitter.com/serpent5)ve [Diana lagül](https://github.com/DianaLaRose)

HTTP durum bilgisiz bir protokoldür. Varsayılan olarak, HTTP istekleri Kullanıcı değerlerini içermeyen bağımsız iletilerdir. Bu makalede, istekler arasında kullanıcı verilerini korumak için çeşitli yaklaşımlar açıklanmaktadır.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/app-state/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="state-management"></a>Durum yönetimi

Durum, çeşitli yaklaşımlar kullanılarak depolanabilir. Her yaklaşım, bu konunun ilerleyen kısımlarında açıklanmıştır.

| Depolama yaklaşımı | Depolama mekanizması |
| ---------------- | ----------------- |
| [Çerezler](#cookies) | HTTP tanımlama bilgileri. , Sunucu tarafı uygulama kodu kullanılarak depolanan verileri içerebilir. |
| [Oturum durumu](#session-state) | HTTP tanımlama bilgileri ve sunucu tarafı uygulama kodu |
| [TempData](#tempdata) | HTTP tanımlama bilgileri veya oturum durumu |
| [Sorgu dizeleri](#query-strings) | HTTP sorgu dizeleri |
| [Gizli alanlar](#hidden-fields) | HTTP form alanları |
| [HttpContext. Items](#httpcontextitems) | Sunucu tarafı uygulama kodu |
| [Önbellek](#cache) | Sunucu tarafı uygulama kodu |

## <a name="cookies"></a>Tanımlama bilgileri

Tanımlama bilgileri istekler arasında veri depolar. Tanımlama bilgileri her istekle birlikte gönderildiğinden, boyutları minimum olarak tutulmalıdır. İdeal olarak, uygulama tarafından depolanan verileri içeren bir tanımlama bilgisinde yalnızca bir tanımlayıcı depolanmalıdır. Tarayıcıların çoğu, tanımlama bilgisi boyutunu 4096 bayt olarak kısıtlar. Her etki alanı için yalnızca sınırlı sayıda tanımlama bilgisi vardır.

Tanımlama bilgileri değişikliklere tabi olduğundan, uygulama tarafından doğrulanması gerekir. Tanımlama bilgileri kullanıcılar tarafından silinebilir ve istemciler üzerinde zaman alabilir. Ancak, tanımlama bilgileri genellikle istemcide en dayanıklı veri kalıcılığı biçimidir.

Tanımlama bilgileri genellikle içerik bilinen bir kullanıcı için özelleştirildiğinde kişiselleştirme için kullanılır. Kullanıcı yalnızca tanımlı ve kimlik doğrulaması değil çoğu durumda. Tanımlama bilgisi, kullanıcının adını, hesap adını veya GUID gibi benzersiz kullanıcı KIMLIĞINI depolayabilirler. Tanımlama bilgisi, kullanıcının tercih ettiği Web sitesi arka plan rengi gibi kişiselleştirilmiş ayarlarına erişmek için kullanılabilir.

Tanımlama bilgilerini verirken ve gizlilik kaygılarıyla ilgilenirken [Avrupa Birliği genel veri koruma yönetmeliklerine (GDPR)](https://ec.europa.eu/info/law/law-topic/data-protection) bakın. Daha fazla bilgi için [ASP.NET Core genel veri koruma yönetmeliği (GDPR) desteğini](xref:security/gdpr)inceleyin.

## <a name="session-state"></a>Oturum durumu

Oturum durumu, Kullanıcı bir Web uygulamasına göz atarken Kullanıcı verilerinin depolanması için bir ASP.NET Core senaryodur. Oturum durumu, bir istemciden gelen istekler arasında verileri kalıcı hale getirmek için uygulama tarafından tutulan bir depoyu kullanır. Oturum verileri bir önbellek tarafından desteklenir ve kısa ömürlü veriler olarak değerlendirilir. Site, oturum verileri olmadan çalışmaya devam etmelidir. Kritik uygulama verileri Kullanıcı veritabanında depolanmalıdır ve oturum yalnızca bir performans iyileştirmesi olarak önbelleğe alınmalıdır.

Bir [SignalR hub 'ı](xref:signalr/hubs) bir http bağlamından bağımsız olarak yürütülemediğinden, [SignalR](xref:signalr/index) uygulamalarında oturum desteklenmez. Örneğin, bir uzun yoklama isteği, isteğin HTTP bağlamının ömrü ötesinde bir hub tarafından açık tutulduğunda bu durum oluşabilir.

ASP.NET Core, oturum KIMLIĞI içeren istemciye bir tanımlama bilgisi sağlayarak oturum durumunu korur. Tanımlama bilgisi oturum KIMLIĞI:

* Her istekle birlikte uygulamaya gönderilir.
* Uygulama tarafından oturum verilerini getirmek için kullanılır.

Oturum durumu aşağıdaki davranışları sergiler:

* Oturum tanımlama bilgisi tarayıcıya özeldir. Oturumlar tarayıcılar arasında paylaşılmaz.
* Tarayıcı oturumu sona erdiğinde oturum tanımlama bilgileri silinir.
* Kullanım dışı bir oturum için tanımlama bilgisi alınmışsa, aynı oturum tanımlama bilgisini kullanan yeni bir oturum oluşturulur.
* Boş oturumlar korunmaz. Oturumun istekler arasında kalıcı olması için oturumun en az bir değere ayarlanmış olması gerekir. Bir oturum tutulmadığı zaman, her yeni istek için yeni bir oturum KIMLIĞI oluşturulur.
* Uygulama, son istekten sonra sınırlı bir süre boyunca bir oturum tutar. Uygulama, oturum zaman aşımını ayarlar ya da 20 dakikalık varsayılan değeri kullanır. Oturum durumu, Kullanıcı verilerini depolamak için idealdir:
  * Belirli bir oturuma özgüdür.
  * Verilerin oturumlar arasında kalıcı depolama gerektirmediğini.
* Oturum verileri, [ISession. Clear](/dotnet/api/microsoft.aspnetcore.http.isession.clear) uygulaması çağrıldığında veya oturumun süresi dolarsa silinir.
* Uygulama kodunu istemci tarayıcısının kapatıldığını veya istemcide oturum tanımlama bilgisinin silindiği veya süresi dolduğunda bilgilendirmeye yönelik varsayılan bir mekanizma yoktur.
* Oturum durumu tanımlama bilgileri varsayılan olarak temel olarak işaretlenmez. Site ziyaretçisi tarafından izlemeye izin verilmediği müddetçe oturum durumu işlevsel değildir. Daha fazla bilgi için bkz. <xref:security/gdpr#tempdata-provider-and-session-state-cookies-arent-essential>.

> [!WARNING]
> Gizli verileri oturum durumunda depolamayin. Kullanıcı tarayıcıyı Kapatmayabilir ve oturum tanımlama bilgisini temizleyebilir. Bazı tarayıcılar tarayıcı pencereleri arasında geçerli oturum tanımlama bilgilerini korur. Bir oturum, tek bir kullanıcıyla kısıtlanmayabilir. Sonraki Kullanıcı aynı oturum tanımlama bilgisiyle uygulamaya gözatmaya devam edebilir.

Bellek içi önbellek sağlayıcısı, oturum verilerini uygulamanın bulunduğu sunucunun belleğinde depolar. Sunucu grubu senaryosunda:

* Her oturumu tek bir sunucudaki belirli bir uygulama örneğine bağlamak için *yapışkan oturumları* kullanın. [Azure App Service](https://azure.microsoft.com/services/app-service/) , varsayılan olarak yapışkan oturumları zorlamak Için [uygulama isteği yönlendirme (ARR)](/iis/extensions/planning-for-arr/using-the-application-request-routing-module) kullanır. Ancak, yapışkan oturumlar ölçeklenebilirliği etkileyebilir ve Web uygulaması güncelleştirmelerini karmaşıklaştırır. Daha iyi bir yaklaşım, yapışkan oturum gerektirmeyen bir redya veya SQL Server dağıtılmış önbellek kullanmaktır. Daha fazla bilgi için bkz. <xref:performance/caching/distributed>.
* Oturum tanımlama bilgisi, [ıdataprotector](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotector)aracılığıyla şifrelenir. Veri koruma, her makinede oturum tanımlama bilgilerini okumak için düzgün şekilde yapılandırılmalıdır. Daha fazla bilgi için bkz <xref:security/data-protection/introduction> . ve [anahtar depolama sağlayıcıları](xref:security/data-protection/implementation/key-storage-providers).

### <a name="configure-session-state"></a>Oturum durumunu yapılandırma

[Microsoft. AspNetCore. Session](https://www.nuget.org/packages/Microsoft.AspNetCore.Session/) paketi:

* Framework tarafından örtük olarak dahil edilir.
* Oturum durumunu yönetmek için ara yazılım sağlar.

Oturum ara yazılımını `Startup` etkinleştirmek için şunları içermelidir:

* [Idistributedönbellek](/dotnet/api/microsoft.extensions.caching.distributed.idistributedcache) belleği önbellekler. `IDistributedCache` Uygulama, oturum için bir yedekleme deposu olarak kullanılır. Daha fazla bilgi için bkz. <xref:performance/caching/distributed>.
* İçinde `ConfigureServices` [addsession](/dotnet/api/microsoft.extensions.dependencyinjection.sessionservicecollectionextensions.addsession) çağrısı.
* İçinde `Configure` [usesession](/dotnet/api/microsoft.aspnetcore.builder.sessionmiddlewareextensions.usesession#Microsoft_AspNetCore_Builder_SessionMiddlewareExtensions_UseSession_Microsoft_AspNetCore_Builder_IApplicationBuilder_) çağrısı.

Aşağıdaki kod, bellek içi oturum sağlayıcısını varsayılan bir bellek içi uygulamasıyla ayarlamayı gösterir `IDistributedCache`:

[!code-csharp[](app-state/samples/3.x/SessionSample/Startup4.cs?name=snippet1&highlight=12-19,45)]

Yukarıdaki kod, testi basitleştirmek için kısa bir zaman aşımı ayarlar.

Ara yazılım sırası önemlidir.  Sonra `UseSession` `UseRouting` ve önce `UseEndpoints`çağırın. Bkz. [Ara yazılım sıralaması](xref:fundamentals/middleware/index#order).

[HttpContext. Session](xref:Microsoft.AspNetCore.Http.HttpContext.Session) , oturum durumu yapılandırıldıktan sonra kullanılabilir.

`HttpContext.Session`çağrılmadan önce `UseSession` erişilemez.

Uygulama yanıt akışına yazmaya başladıktan sonra yeni bir oturum tanımlama bilgisine sahip yeni bir oturum oluşturulamıyor. Özel durum Web sunucusu günlüğüne kaydedilir ve tarayıcıda gösterilmez.

### <a name="load-session-state-asynchronously"></a>Oturum durumunu zaman uyumsuz olarak yükle

ASP.NET Core varsayılan oturum sağlayıcısı, arka plandaki [ıdistributedcache](/dotnet/api/microsoft.extensions.caching.distributed.idistributedcache) 'ten oturum kayıtlarını, yalnızca [ISession. LoadAsync](/dotnet/api/microsoft.aspnetcore.http.isession.loadasync) yöntemi doğrudan [TryGetValue](/dotnet/api/microsoft.aspnetcore.http.isession.trygetvalue), [set](/dotnet/api/microsoft.aspnetcore.http.isession.set)veya [Remove](/dotnet/api/microsoft.aspnetcore.http.isession.remove) yöntemleriyle önce çağrılırsa zaman uyumsuz olarak yükler. İlk `LoadAsync` çağrılmadıysa, temel alınan oturum kaydı zaman uyumlu olarak yüklenir ve bu da ölçekte performans cezası oluşturabilir.

Uygulamaların bu kalıbı zorunlu kılmak için, [distributedsessionstore](/dotnet/api/microsoft.aspnetcore.session.distributedsessionstore) ve [distributedoturum](/dotnet/api/microsoft.aspnetcore.session.distributedsession) uygulamalarını bir özel `LoadAsync` durum oluşturan sürümlerle sarın, `TryGetValue` `Set`, veya `Remove`öncesinde çağrılmaması gerekir. Sarmalanan sürümleri hizmetler kapsayıcısına kaydedin.

### <a name="session-options"></a>Oturum seçenekleri

Oturum varsayılanlarını geçersiz kılmak için [SessionOptions](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions)' ı kullanın.

| Seçenek | Açıklama |
| ------ | ----------- |
| [Bilgilerinin](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions.cookie) | Tanımlama bilgisini oluşturmak için kullanılan ayarları belirler. [Ad](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.name) varsayılan olarak [Sessiondefaults. tanımlama](/dotnet/api/microsoft.aspnetcore.session.sessiondefaults.cookiename) adı (`.AspNetCore.Session`) değerini alır. [Yol](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.path) varsayılan olarak [Sessiondefaults. tarif ıepath](/dotnet/api/microsoft.aspnetcore.session.sessiondefaults.cookiepath) (`/`) olarak belirlenmiştir. [SameSite](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.samesite) varsayılan olarak [Samesitemode. LAX](/dotnet/api/microsoft.aspnetcore.http.samesitemode) (`1`) olarak belirlenmiştir. [HttpOnly](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.httponly) varsayılan olarak `true`varsayılandır. [IsEssential](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.isessential) varsayılan olarak `false`. |
| [Timeout](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions.idletimeout) | , `IdleTimeout` Oturumun içeriği terk edilmeden önce ne kadar süreyle boşta kalabileceğini gösterir. Her oturum erişimi zaman aşımını sıfırlar. Bu ayar, tanımlama bilgisi değil yalnızca oturumun içeriği için geçerlidir. Varsayılan değer 20 dakikadır. |
| [IOTimeout](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions.iotimeout) | Mağazadan bir oturumu yüklemesine veya depolama alanına geri kaydetmeye izin verilen en uzun süre. Bu ayar yalnızca zaman uyumsuz işlemlere uygulanabilir. Bu zaman aşımı, [InfiniteTimeSpan](/dotnet/api/system.threading.timeout.infinitetimespan)kullanılarak devre dışı bırakılabilir. Varsayılan değer 1 dakikadır. |

Oturum, tek bir tarayıcıdan gelen istekleri izlemek ve tanımlamak için bir tanımlama bilgisi kullanır. Bu tanımlama bilgisi varsayılan olarak adlandırılır `.AspNetCore.Session`ve bir yolu kullanır. `/` Tanımlama bilgisi varsayılan olarak bir etki alanı belirtmediği için, sayfada istemci tarafı komut dosyası için kullanılamaz hale getirilmez ( [HttpOnly](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.httponly) varsayılan olarak `true`' dir.)

Tanımlama bilgisi oturum varsayılanlarını geçersiz kılmak için <xref:Microsoft.AspNetCore.Builder.SessionOptions>şunu kullanın:

[!code-csharp[](app-state/samples/3.x/SessionSample/Startup2.cs?name=snippet1&highlight=5-10)]

Uygulama, sunucunun önbelleğindeki içeriği terk edilmeden önce bir oturumun ne kadar süreyle boşta kalabileceğini anlamak için [IdleTimeout](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions.idletimeout) özelliğini kullanır. Bu özellik, tanımlama bilgisi bitiş zamanından bağımsızdır. [Oturum ara yazılımı](/dotnet/api/microsoft.aspnetcore.session.sessionmiddleware) üzerinden geçen her istek zaman aşımını sıfırlar.

Oturum durumu *kilitli*değil. İki istek aynı anda bir oturumun içeriğini değiştirmeyi denerseniz, son istek ilk geçersiz kılar. `Session`*tutarlı bir oturum*olarak uygulanır, yani tüm içerikler birlikte depolanır. İki istek farklı oturum değerlerini değiştirmek için arama yaparken, son istek ilk tarafından yapılan oturum değişikliklerini geçersiz kılabilir.

### <a name="set-and-get-session-values"></a>Oturum değerlerini ayarlama ve edinme

Razor Pages [Pagemodel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel) sınıfından ya da [HttpContext. Session](/dotnet/api/microsoft.aspnetcore.http.httpcontext.session)ile MVC [Denetleyici](/dotnet/api/microsoft.aspnetcore.mvc.controller) sınıfından oturum durumuna erişilir. Bu özellik bir [ISession](/dotnet/api/microsoft.aspnetcore.http.isession) uygulamasıdır.

Uygulama `ISession` , tamsayı ve dize değerlerini ayarlamak ve almak için birkaç uzantı yöntemi sağlar. Uzantı yöntemleri [Microsoft. AspNetCore. http](/dotnet/api/microsoft.aspnetcore.http) ad alanıdır.

`ISession`Uzantı yöntemleri:

* [Al (ISession, dize)](/dotnet/api/microsoft.aspnetcore.http.sessionextensions.get)
* [Getınt32 (ISession, dize)](/dotnet/api/microsoft.aspnetcore.http.sessionextensions.getint32)
* [GetString (ISession, dize)](/dotnet/api/microsoft.aspnetcore.http.sessionextensions.getstring)
* [Setınt32 (ISession, dize, Int32)](/dotnet/api/microsoft.aspnetcore.http.sessionextensions.setint32)
* [SetString (ISession, dize, dize)](/dotnet/api/microsoft.aspnetcore.http.sessionextensions.setstring)

Aşağıdaki örnek, bir Razor Pages sayfasındaki `IndexModel.SessionKeyName` anahtarın (`_Name` örnek uygulamada) oturum değerini alır:

```csharp
@page
@using Microsoft.AspNetCore.Http
@model IndexModel

...

Name: @HttpContext.Session.GetString(IndexModel.SessionKeyName)
```

Aşağıdaki örnek, bir tamsayı ve bir dizenin nasıl ayarlanacağını ve alınacağını gösterir:

[!code-csharp[](app-state/samples/3.x/SessionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=18-19,22-23)]

Tüm oturum verileri, bellek içi önbellek kullanılırken bile dağıtılmış önbellek senaryosunu etkinleştirmek üzere serileştirilmelidir. Dize ve tamsayı serileştiriciler, [ISession](/dotnet/api/microsoft.aspnetcore.http.isession)'un genişletme yöntemleri tarafından sağlanır. Karmaşık türler JSON gibi başka bir mekanizma kullanılarak Kullanıcı tarafından serileştirilmelidir.

Nesneleri seri hale getirmek için aşağıdaki örnek kodu kullanın:

[!code-csharp[](app-state/samples/3.x/SessionSample/Extensions/SessionExtensions.cs?name=snippet1)]

Aşağıdaki örnek, `SessionExtensions` sınıfı ile serileştirilebilir bir nesnenin nasıl ayarlanacağını ve alınacağını gösterir:

[!code-csharp[](app-state/samples/3.x/SessionSample/Pages/Index.cshtml.cs?name=snippet2)]

## <a name="tempdata"></a>TempData

ASP.NET Core, Razor Pages [TempData](xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.TempData) veya Controller <xref:Microsoft.AspNetCore.Mvc.Controller.TempData>'ı kullanıma sunar. Bu özellik, verileri başka bir istekte okunana kadar depolar. [Saklama (dize)](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Keep*) ve [Peek (dize)](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Peek*) yöntemleri, isteğin sonunda silme yapılmadan verileri incelemek için kullanılabilir. [Saklama için](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Keep*) sözlükte tüm öğeleri işaretler. `TempData`eklenir

* Tek bir istek için veri gerektiğinde yeniden yönlendirme için kullanışlıdır.
* Tanımlama bilgileri `TempData` ya da oturum durumu kullanılarak sağlayıcılar tarafından uygulanır.

## <a name="tempdata-samples"></a>TempData örnekleri

Bir müşteri oluşturan aşağıdaki sayfayı göz önünde bulundurun:

[!code-csharp[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet&highlight=15-16,30)]

Aşağıdaki sayfa görüntülenir `TempData["Message"]`:

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/IndexPeek.cshtml?range=1-14)]

Önceki İşaretlemede, isteğin `TempData["Message"]` sonunda, kullanıldığı için silinmez. **not** `Peek` Sayfanın yenilenmesi, içeriğini görüntüler `TempData["Message"]`.

Aşağıdaki biçimlendirme önceki koda benzerdir, ancak isteğin sonundaki verileri korumak için `Keep` kullanır:

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/IndexKeep.cshtml?range=1-14)]

*Indexpeek* ve *ındexkeep* sayfaları arasında gezinmek silinmez `TempData["Message"]`.

Aşağıdaki kod görüntülenir `TempData["Message"]`, ancak isteğin `TempData["Message"]` sonunda silinir:

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/Index.cshtml?range=1-14)]

### <a name="tempdata-providers"></a>TempData sağlayıcıları

Tanımlama bilgisi tabanlı TempData sağlayıcısı, TempData 'ı tanımlama bilgilerinde depolamak için varsayılan olarak kullanılır.

Tanımlama bilgisi verileri, [Base64UrlTextEncoder](/dotnet/api/microsoft.aspnetcore.webutilities.base64urltextencoder)ile kodlanan ve sonra öbekli [ıdataprotector](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotector)kullanılarak şifrelenir. En büyük tanımlama bilgisi boyutu, şifreleme ve parçalama nedeniyle [4096 bayttan](http://www.faqs.org/rfcs/rfc2965.html) daha azdır. Şifreli verileri sıkıştırmak, [suç](https://wikipedia.org/wiki/CRIME_(security_exploit)) ve [ihlal](https://wikipedia.org/wiki/BREACH_(security_exploit)) saldırıları gibi güvenlik sorunlarına yol açacağından, tanımlama bilgisi verileri sıkıştırılmaz. Tanımlama bilgisi tabanlı TempData sağlayıcısı hakkında daha fazla bilgi için bkz. tanımlama, [ıetempdataprovider](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.cookietempdataprovider).

### <a name="choose-a-tempdata-provider"></a>Bir TempData sağlayıcısı seçin

Bir TempData sağlayıcısı seçmek şöyle bazı hususlar içerir:

* Uygulama oturum durumunu zaten kullanıyor mu? Bu durumda, oturum durumu ' nu kullanmak TempData Provider 'ın veri boyutunun ötesinde uygulamaya ek maliyeti yoktur.
* Uygulama, 500 bayta kadar yalnızca görece küçük miktarlarda veri için TempData kullanıyor mu? Bu durumda, bir tanımlama bilgisi TempData Provider, TempData kullanan her isteğe küçük bir maliyet ekler. Aksi takdirde, oturum durumu TempData Provider, Geçicimiz veri tüketilene kadar her istekte büyük miktarda veri dönüşü olmaması yararlı olabilir.
* Uygulama, birden çok sunucuda bir sunucu grubunda mi çalışıyor? Bu durumda, veri koruma (bkz <xref:security/data-protection/introduction> . ve [anahtar depolama sağlayıcıları](xref:security/data-protection/implementation/key-storage-providers)) dışındaki tanımlama bilgisi TempData sağlayıcısını kullanmak için ek bir yapılandırma gerekmez.

Web tarayıcıları gibi birçok Web istemcisi, her tanımlama bilgisinin en büyük boyutu ve toplam tanımlama bilgisi sayısı üzerinde sınırlar uygular. Bu tanımlama bilgisi TempData sağlayıcısını kullanırken, uygulamanın [Bu sınırları](http://www.faqs.org/rfcs/rfc2965.html)aşmadığını doğrulayın. Verilerin toplam boyutunu göz önünde bulundurun. Şifreleme ve parçalama nedeniyle tanımlama bilgisi boyutundaki artışlar için hesap.

### <a name="configure-the-tempdata-provider"></a>TempData sağlayıcısını yapılandırma

Tanımlama bilgisi tabanlı TempData sağlayıcısı varsayılan olarak etkindir.

Oturum tabanlı TempData sağlayıcısını etkinleştirmek için [Addsessionstatetempdataprovider](/dotnet/api/microsoft.extensions.dependencyinjection.mvcviewfeaturesmvcbuilderextensions.addsessionstatetempdataprovider) genişletme yöntemini kullanın. Yalnızca bir çağrısı `AddSessionStateTempDataProvider` gereklidir:

[!code-csharp[](app-state/samples/3.x/SessionSample/Startup3.cs?name=snippet1&highlight=4,6,30)]

## <a name="query-strings"></a>Sorgu dizeleri

Yeni isteğin sorgu dizesine eklenerek sınırlı miktarda veri, bir istekten diğerine geçirilebilir. Bu durum, gömülü durum ile bağlantıların e-posta veya sosyal ağlar aracılığıyla paylaşılmasını sağlamak için durumu kalıcı bir şekilde yakalamak için yararlıdır. URL sorgu dizeleri ortak olduğundan, gizli veriler için hiçbir şekilde Sorgu dizelerini kullanmayın.

Sorgu dizelerindeki veriler de dahil olmak üzere, istenmeden paylaşıma ek olarak uygulamayı [siteler arası Istek forgery (CSRF)](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) saldırılarına açık duruma getirebilir. Korunan oturum durumunun, CSRF saldırılarına karşı korunması gerekir. Daha fazla bilgi için bkz. [siteler arası Istek forgery (XSRF/CSRF) saldırılarını önleme](xref:security/anti-request-forgery).

## <a name="hidden-fields"></a>Gizli alanlar

Veriler gizli form alanlarına kaydedilebilir ve sonraki istek üzerine geri gönderilebilir. Bu çok sayfalı formlarda yaygındır. İstemci verilerle oynayabilir olabileceğinden, uygulamanın gizli alanlarda depolanan verileri her zaman yeniden doğrulaması gerekir.

## <a name="httpcontextitems"></a>HttpContext. Items

[HttpContext. Items](/dotnet/api/microsoft.aspnetcore.http.httpcontext.items) koleksiyonu, tek bir isteği işlerken verileri depolamak için kullanılır. Koleksiyon içeriği bir istek işlendikten sonra atılır. `Items` Koleksiyon genellikle, bir istek sırasında zaman içinde farklı noktalarda çalıştıklarında ve parametreleri geçirmek için doğrudan bir yol olmadığında bileşenlerin veya ara yazılımların iletişim kurmasına izin vermek için kullanılır.

Aşağıdaki örnekte, [Ara yazılım](xref:fundamentals/middleware/index) `Items` koleksiyona ekler `isVerified` :

[!code-csharp[](app-state/samples/3.x/SessionSample/Startup.cs?name=snippet1)]

Yalnızca tek bir uygulamada kullanılan ara yazılım için sabit `string` anahtarlar kabul edilebilir. Uygulamalar arasında paylaşılan ara yazılım, anahtar çakışmalarını önlemek için benzersiz nesne anahtarları kullanmalıdır. Aşağıdaki örnek, bir ara yazılım sınıfında tanımlanan benzersiz bir nesne anahtarının nasıl kullanılacağını gösterir:

[!code-csharp[](app-state/samples/3.x/SessionSample/Middleware/HttpContextItemsMiddleware.cs?name=snippet1&highlight=4,13)]

Diğer kod, ara yazılım sınıfının açığa çıkarılan `HttpContext.Items` anahtarını kullanarak içinde depolanan değere erişebilir:

[!code-csharp[](app-state/samples/3.x/SessionSample/Pages/Index.cshtml.cs?name=snippet3)]

Bu yaklaşım ayrıca koddaki anahtar dizelerinin kullanımını ortadan kaldırma avantajına sahiptir.

## <a name="cache"></a>Önbellek

Önbelleğe alma, verileri depolamak ve almak için etkili bir yoldur. Uygulama, önbelleğe alınmış öğelerin ömrünü denetleyebilir. Daha fazla bilgi için bkz. <xref:performance/caching/response>.

Önbelleğe alınan veriler belirli bir istek, Kullanıcı veya oturumla ilişkili değildir. **Diğer Kullanıcı istekleri tarafından alınabilecek kullanıcıya özgü verileri önbelleğe vermeyin.**

Uygulama genelinde verileri önbelleğe almak için bkz <xref:performance/caching/memory>..

## <a name="common-errors"></a>Sık karşılaşılan hatalar

* "' Microsoft. AspNetCore. Session. DistributedSessionStore ' etkinleştirilmeye çalışılırken ' Microsoft. Extensions. Caching. Distributed. ıdistributedcache ' türü için hizmet çözümlenemiyor."

  Bunun nedeni genellikle en az bir `IDistributedCache` uygulamanın yapılandırması başarısız olmuştur. Daha fazla bilgi için <xref:performance/caching/distributed> ve <xref:performance/caching/memory> bölümlerine bakın.

Oturum ara yazılımı bir oturumu kalıcı hale getiremezse:

* Ara yazılım özel durumu günlüğe kaydeder ve istek normal olarak devam eder.
* Bu, öngörülemeyen davranışa yol açar.

Yedekleme deposu kullanılamıyorsa, oturum ara yazılımı bir oturumu kalıcı hale getiremeyebilir. Örneğin, bir Kullanıcı bir alışveriş sepetini oturum içinde depolar. Kullanıcı sepete bir öğe ekler, ancak kayıt başarısız olur. Uygulama hata hakkında bilgi sahibi değildir, bu nedenle bu, doğru olmayan, kullanıcıya öğenin sepetine eklendiğini bildirir.

Hataları denetlemek için önerilen yaklaşım, uygulama oturuma yazma tamamlandığında `await feature.Session.CommitAsync` çağrmalıdır. <xref:Microsoft.AspNetCore.Http.ISession.CommitAsync*>yedekleme deposu kullanılamıyorsa bir özel durum oluşturur. `CommitAsync` Başarısız olursa, uygulama özel durumu işleyebilir. <xref:Microsoft.AspNetCore.Http.ISession.LoadAsync*>, veri deposu kullanılamadığında aynı koşulların altına atar.
  
## <a name="signalr-and-session-state"></a>SignalR ve oturum durumu

SignalR uygulamalarının, bilgileri depolamak için oturum durumunu kullanmamalıdır. SignalR uygulamaları, hub `Context.Items` 'da bağlantı durumu başına depolama yapabilir. <!-- https://github.com/aspnet/SignalR/issues/2139 -->

## <a name="additional-resources"></a>Ek kaynaklar

<xref:host-and-deploy/web-farm>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

By [Rick Anderson](https://twitter.com/RickAndMSFT), [Steve Smith](https://ardalis.com/), [Diana Lagül](https://github.com/DianaLaRose)ve [Luke Latham](https://github.com/guardrex)

HTTP durum bilgisiz bir protokoldür. Ek adımlar uygulamadan, HTTP istekleri Kullanıcı değerlerini veya uygulama durumunu içermeyen bağımsız iletilerdir. Bu makalede, istekler arasında kullanıcı verilerini ve uygulama durumunu korumak için çeşitli yaklaşımlar açıklanmaktadır.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/app-state/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="state-management"></a>Durum yönetimi

Durum, çeşitli yaklaşımlar kullanılarak depolanabilir. Her yaklaşım, bu konunun ilerleyen kısımlarında açıklanmıştır.

| Depolama yaklaşımı | Depolama mekanizması |
| ---------------- | ----------------- |
| [Çerezler](#cookies) | HTTP tanımlama bilgileri (sunucu tarafı uygulama kodu kullanılarak depolanan veriler içerebilir) |
| [Oturum durumu](#session-state) | HTTP tanımlama bilgileri ve sunucu tarafı uygulama kodu |
| [TempData](#tempdata) | HTTP tanımlama bilgileri veya oturum durumu |
| [Sorgu dizeleri](#query-strings) | HTTP sorgu dizeleri |
| [Gizli alanlar](#hidden-fields) | HTTP form alanları |
| [HttpContext. Items](#httpcontextitems) | Sunucu tarafı uygulama kodu |
| [Önbellek](#cache) | Sunucu tarafı uygulama kodu |
| [Bağımlılık ekleme](#dependency-injection) | Sunucu tarafı uygulama kodu |

## <a name="cookies"></a>Tanımlama bilgileri

Tanımlama bilgileri istekler arasında veri depolar. Tanımlama bilgileri her istekle birlikte gönderildiğinden, boyutları minimum olarak tutulmalıdır. İdeal olarak, uygulama tarafından depolanan verileri içeren bir tanımlama bilgisinde yalnızca bir tanımlayıcı depolanmalıdır. Tarayıcıların çoğu, tanımlama bilgisi boyutunu 4096 bayt olarak kısıtlar. Her etki alanı için yalnızca sınırlı sayıda tanımlama bilgisi vardır.

Tanımlama bilgileri değişikliklere tabi olduğundan, uygulama tarafından doğrulanması gerekir. Tanımlama bilgileri kullanıcılar tarafından silinebilir ve istemciler üzerinde zaman alabilir. Ancak, tanımlama bilgileri genellikle istemcide en dayanıklı veri kalıcılığı biçimidir.

Tanımlama bilgileri genellikle içerik bilinen bir kullanıcı için özelleştirildiğinde kişiselleştirme için kullanılır. Kullanıcı yalnızca tanımlı ve kimlik doğrulaması değil çoğu durumda. Tanımlama bilgisi kullanıcının adını, hesap adını veya benzersiz kullanıcı KIMLIĞINI (GUID gibi) saklayabilir. Daha sonra kullanıcının tercih ettiği Web sitesi arka plan rengi gibi kişiselleştirilmiş ayarlarına erişmek için tanımlama bilgisini kullanabilirsiniz.

Tanımlama bilgilerini verirken ve gizlilik kaygılarıyla ilgilenirken [Avrupa Birliği genel veri koruma düzenlemelerine (GDPR)](https://ec.europa.eu/info/law/law-topic/data-protection) sahip olun. Daha fazla bilgi için [ASP.NET Core genel veri koruma yönetmeliği (GDPR) desteğini](xref:security/gdpr)inceleyin.

## <a name="session-state"></a>Oturum durumu

Oturum durumu, Kullanıcı bir Web uygulamasına göz atarken Kullanıcı verilerinin depolanması için bir ASP.NET Core senaryodur. Oturum durumu, bir istemciden gelen istekler arasında verileri kalıcı hale getirmek için uygulama tarafından tutulan bir depoyu kullanır. Oturum verileri bir önbellek tarafından desteklenir ve kısa ömürlü veriler&mdash;olarak kabul edilir. Bu, sitenin oturum verileri olmadan çalışmaya devam etmesi gerekir. Kritik uygulama verileri Kullanıcı veritabanında depolanmalıdır ve oturum yalnızca bir performans iyileştirmesi olarak önbelleğe alınmalıdır.

> [!NOTE]
> Bir [SignalR hub 'ı](xref:signalr/hubs) bir http bağlamından bağımsız olarak yürütülemediğinden, [SignalR](xref:signalr/index) uygulamalarında oturum desteklenmez. Örneğin, bir uzun yoklama isteği, isteğin HTTP bağlamının ömrü ötesinde bir hub tarafından açık tutulduğunda bu durum oluşabilir.

ASP.NET Core, her istekle birlikte uygulamaya gönderilen oturum KIMLIĞI içeren istemciye bir tanımlama bilgisi sağlayarak oturum durumunu korur. Uygulama, oturum verilerini getirmek için oturum KIMLIĞINI kullanır.

Oturum durumu aşağıdaki davranışları sergiler:

* Oturum tanımlama bilgisi tarayıcıya özel olduğundan, oturumlar tarayıcılar arasında paylaşılmaz.
* Tarayıcı oturumu sona erdiğinde oturum tanımlama bilgileri silinir.
* Kullanım dışı bir oturum için tanımlama bilgisi alınmışsa, aynı oturum tanımlama bilgisini kullanan yeni bir oturum oluşturulur.
* Boş oturumlar tutulmadığı&mdash;için oturum, oturum istekleri arasında kalıcı hale getirmek için en az bir değere ayarlanmış olmalıdır. Bir oturum tutulmadığı zaman, her yeni istek için yeni bir oturum KIMLIĞI oluşturulur.
* Uygulama, son istekten sonra sınırlı bir süre boyunca bir oturum tutar. Uygulama, oturum zaman aşımını ayarlar ya da 20 dakikalık varsayılan değeri kullanır. Oturum durumu, belirli bir oturuma özgü kullanıcı verilerini depolamak için idealdir, ancak verilerin oturumlarda kalıcı depolama gerektirmez.
* Oturum verileri, [ISession. Clear](/dotnet/api/microsoft.aspnetcore.http.isession.clear) uygulaması çağrıldığında veya oturumun süresi dolarsa silinir.
* Uygulama kodunu istemci tarayıcısının kapatıldığını veya istemcide oturum tanımlama bilgisinin silindiği veya süresi dolduğunda bilgilendirmeye yönelik varsayılan bir mekanizma yoktur.
* ASP.NET Core MVC ve Razor sayfaları şablonları, Genel Veri Koruma Yönetmeliği (GDPR) desteğini içerir. Oturum durumu tanımlama bilgileri varsayılan olarak temel olarak işaretlenmez, bu nedenle site ziyaretçisi tarafından izlemeye izin verilmediği takdirde oturum durumu işlevsel değildir. Daha fazla bilgi için bkz. <xref:security/gdpr#tempdata-provider-and-session-state-cookies-arent-essential>.

> [!WARNING]
> Gizli verileri oturum durumunda depolamayin. Kullanıcı tarayıcıyı Kapatmayabilir ve oturum tanımlama bilgisini temizleyebilir. Bazı tarayıcılar tarayıcı pencereleri arasında geçerli oturum tanımlama bilgilerini korur. Bir oturum tek bir kullanıcıyla&mdash;kısıtlanmayabilir ve bir sonraki Kullanıcı aynı oturum tanımlama bilgisiyle uygulamaya gözatmaya devam edebilir.

Bellek içi önbellek sağlayıcısı, oturum verilerini uygulamanın bulunduğu sunucunun belleğinde depolar. Sunucu grubu senaryosunda:

* Her oturumu tek bir sunucudaki belirli bir uygulama örneğine bağlamak için *yapışkan oturumları* kullanın. [Azure App Service](https://azure.microsoft.com/services/app-service/) , varsayılan olarak yapışkan oturumları zorlamak Için [uygulama isteği yönlendirme (ARR)](/iis/extensions/planning-for-arr/using-the-application-request-routing-module) kullanır. Ancak, yapışkan oturumlar ölçeklenebilirliği etkileyebilir ve Web uygulaması güncelleştirmelerini karmaşıklaştırır. Daha iyi bir yaklaşım, yapışkan oturum gerektirmeyen bir redya veya SQL Server dağıtılmış önbellek kullanmaktır. Daha fazla bilgi için bkz. <xref:performance/caching/distributed>.
* Oturum tanımlama bilgisi, [ıdataprotector](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotector)aracılığıyla şifrelenir. Veri koruma, her makinede oturum tanımlama bilgilerini okumak için düzgün şekilde yapılandırılmalıdır. Daha fazla bilgi için bkz <xref:security/data-protection/introduction> . ve [anahtar depolama sağlayıcıları](xref:security/data-protection/implementation/key-storage-providers).

### <a name="configure-session-state"></a>Oturum durumunu yapılandırma

[Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app)içinde yer alan [Microsoft. Aspnetcore. Session](https://www.nuget.org/packages/Microsoft.AspNetCore.Session/) paketi, oturum durumunu yönetmek için ara yazılım sağlar. Oturum ara yazılımını `Startup` etkinleştirmek için şunları içermelidir:

* [Idistributedönbellek](/dotnet/api/microsoft.extensions.caching.distributed.idistributedcache) belleği önbellekler. `IDistributedCache` Uygulama, oturum için bir yedekleme deposu olarak kullanılır. Daha fazla bilgi için bkz. <xref:performance/caching/distributed>.
* İçinde `ConfigureServices` [addsession](/dotnet/api/microsoft.extensions.dependencyinjection.sessionservicecollectionextensions.addsession) çağrısı.
* İçinde `Configure` [usesession](/dotnet/api/microsoft.aspnetcore.builder.sessionmiddlewareextensions.usesession#Microsoft_AspNetCore_Builder_SessionMiddlewareExtensions_UseSession_Microsoft_AspNetCore_Builder_IApplicationBuilder_) çağrısı.

Aşağıdaki kod, bellek içi oturum sağlayıcısını varsayılan bir bellek içi uygulamasıyla ayarlamayı gösterir `IDistributedCache`:

[!code-csharp[](app-state/samples/2.x/SessionSample/Startup.cs?name=snippet1&highlight=5-14,34)]

Ara yazılım sırası önemlidir. Önceki örnekte, sonrasında `InvalidOperationException` `UseSession` `UseMvc`çağrıldığında bir özel durum oluşur. Daha fazla bilgi için bkz. [Ara yazılım sıralaması](xref:fundamentals/middleware/index#order).

[HttpContext. Session](/dotnet/api/microsoft.aspnetcore.http.httpcontext.session) , oturum durumu yapılandırıldıktan sonra kullanılabilir.

`HttpContext.Session`çağrılmadan önce `UseSession` erişilemez.

Uygulama yanıt akışına yazmaya başladıktan sonra yeni bir oturum tanımlama bilgisine sahip yeni bir oturum oluşturulamıyor. Özel durum Web sunucusu günlüğüne kaydedilir ve tarayıcıda gösterilmez.

### <a name="load-session-state-asynchronously"></a>Oturum durumunu zaman uyumsuz olarak yükle

ASP.NET Core varsayılan oturum sağlayıcısı, arka plandaki [ıdistributedcache](/dotnet/api/microsoft.extensions.caching.distributed.idistributedcache) 'ten oturum kayıtlarını, yalnızca [ISession. LoadAsync](/dotnet/api/microsoft.aspnetcore.http.isession.loadasync) yöntemi doğrudan [TryGetValue](/dotnet/api/microsoft.aspnetcore.http.isession.trygetvalue), [set](/dotnet/api/microsoft.aspnetcore.http.isession.set)veya [Remove](/dotnet/api/microsoft.aspnetcore.http.isession.remove) yöntemleriyle önce çağrılırsa zaman uyumsuz olarak yükler. İlk `LoadAsync` çağrılmadıysa, temel alınan oturum kaydı zaman uyumlu olarak yüklenir ve bu da ölçekte performans cezası oluşturabilir.

Uygulamaların bu kalıbı zorunlu kılmak için, [distributedsessionstore](/dotnet/api/microsoft.aspnetcore.session.distributedsessionstore) ve [distributedoturum](/dotnet/api/microsoft.aspnetcore.session.distributedsession) uygulamalarını bir özel `LoadAsync` durum oluşturan sürümlerle sarın, `TryGetValue` `Set`, veya `Remove`öncesinde çağrılmaması gerekir. Sarmalanan sürümleri hizmetler kapsayıcısına kaydedin.

### <a name="session-options"></a>Oturum seçenekleri

Oturum varsayılanlarını geçersiz kılmak için [SessionOptions](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions)' ı kullanın.

| Seçenek | Açıklama |
| ------ | ----------- |
| [Bilgilerinin](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions.cookie) | Tanımlama bilgisini oluşturmak için kullanılan ayarları belirler. [Ad](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.name) varsayılan olarak [Sessiondefaults. tanımlama](/dotnet/api/microsoft.aspnetcore.session.sessiondefaults.cookiename) adı (`.AspNetCore.Session`) değerini alır. [Yol](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.path) varsayılan olarak [Sessiondefaults. tarif ıepath](/dotnet/api/microsoft.aspnetcore.session.sessiondefaults.cookiepath) (`/`) olarak belirlenmiştir. [SameSite](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.samesite) varsayılan olarak [Samesitemode. LAX](/dotnet/api/microsoft.aspnetcore.http.samesitemode) (`1`) olarak belirlenmiştir. [HttpOnly](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.httponly) varsayılan olarak `true`varsayılandır. [IsEssential](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.isessential) varsayılan olarak `false`. |
| [Timeout](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions.idletimeout) | , `IdleTimeout` Oturumun içeriği terk edilmeden önce ne kadar süreyle boşta kalabileceğini gösterir. Her oturum erişimi zaman aşımını sıfırlar. Bu ayar, tanımlama bilgisi değil yalnızca oturumun içeriği için geçerlidir. Varsayılan değer 20 dakikadır. |
| [IOTimeout](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions.iotimeout) | Mağazadan bir oturumu yüklemesine veya depolama alanına geri kaydetmeye izin verilen en uzun süre. Bu ayar yalnızca zaman uyumsuz işlemlere uygulanabilir. Bu zaman aşımı, [InfiniteTimeSpan](/dotnet/api/system.threading.timeout.infinitetimespan)kullanılarak devre dışı bırakılabilir. Varsayılan değer 1 dakikadır. |

Oturum, tek bir tarayıcıdan gelen istekleri izlemek ve tanımlamak için bir tanımlama bilgisi kullanır. Bu tanımlama bilgisi varsayılan olarak adlandırılır `.AspNetCore.Session`ve bir yolu kullanır. `/` Tanımlama bilgisi varsayılan olarak bir etki alanı belirtmediği için, sayfada istemci tarafı komut dosyası için kullanılamaz hale getirilmez ( [HttpOnly](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.httponly) varsayılan olarak `true`' dir.)

Tanımlama bilgisi oturum varsayılanlarını geçersiz kılmak için `SessionOptions`şunu kullanın:

[!code-csharp[](app-state/samples_snapshot/2.x/SessionSample/Startup.cs?name=snippet1&highlight=14-19)]

Uygulama, sunucunun önbelleğindeki içeriği terk edilmeden önce bir oturumun ne kadar süreyle boşta kalabileceğini anlamak için [IdleTimeout](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions.idletimeout) özelliğini kullanır. Bu özellik, tanımlama bilgisi bitiş zamanından bağımsızdır. [Oturum ara yazılımı](/dotnet/api/microsoft.aspnetcore.session.sessionmiddleware) üzerinden geçen her istek zaman aşımını sıfırlar.

Oturum durumu *kilitli*değil. İki istek aynı anda bir oturumun içeriğini değiştirmeyi denerseniz, son istek ilk geçersiz kılar. `Session`*tutarlı bir oturum*olarak uygulanır, yani tüm içerikler birlikte depolanır. İki istek farklı oturum değerlerini değiştirmek için arama yaparken, son istek ilk tarafından yapılan oturum değişikliklerini geçersiz kılabilir.

### <a name="set-and-get-session-values"></a>Oturum değerlerini ayarlama ve edinme

Razor Pages [Pagemodel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel) sınıfından ya da [HttpContext. Session](/dotnet/api/microsoft.aspnetcore.http.httpcontext.session)ile MVC [Denetleyici](/dotnet/api/microsoft.aspnetcore.mvc.controller) sınıfından oturum durumuna erişilir. Bu özellik bir [ISession](/dotnet/api/microsoft.aspnetcore.http.isession) uygulamasıdır.

Uygulama `ISession` , tamsayı ve dize değerlerini ayarlamak ve almak için birkaç uzantı yöntemi sağlar. Uzantı yöntemleri, proje tarafından [Microsoft. AspNetCore. http. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.Http.Extensions/) paketine başvurulduğunda [Microsoft. aspnetcore. http](/dotnet/api/microsoft.aspnetcore.http) ad alanında (uzantı yöntemlerine erişim sağlamak için bir `using Microsoft.AspNetCore.Http;` ifade ekleyin). Her iki paket de [Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app)'e dahildir.

`ISession`Uzantı yöntemleri:

* [Al (ISession, dize)](/dotnet/api/microsoft.aspnetcore.http.sessionextensions.get)
* [Getınt32 (ISession, dize)](/dotnet/api/microsoft.aspnetcore.http.sessionextensions.getint32)
* [GetString (ISession, dize)](/dotnet/api/microsoft.aspnetcore.http.sessionextensions.getstring)
* [Setınt32 (ISession, dize, Int32)](/dotnet/api/microsoft.aspnetcore.http.sessionextensions.setint32)
* [SetString (ISession, dize, dize)](/dotnet/api/microsoft.aspnetcore.http.sessionextensions.setstring)

Aşağıdaki örnek, bir Razor Pages sayfasındaki `IndexModel.SessionKeyName` anahtarın (`_Name` örnek uygulamada) oturum değerini alır:

```csharp
@page
@using Microsoft.AspNetCore.Http
@model IndexModel

...

Name: @HttpContext.Session.GetString(IndexModel.SessionKeyName)
```

Aşağıdaki örnek, bir tamsayı ve bir dizenin nasıl ayarlanacağını ve alınacağını gösterir:

[!code-csharp[](app-state/samples/2.x/SessionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=18-19,22-23)]

Tüm oturum verileri, bellek içi önbellek kullanılırken bile dağıtılmış önbellek senaryosunu etkinleştirmek üzere serileştirilmelidir. Dize ve tamsayı serileştiriciler, [ISession](/dotnet/api/microsoft.aspnetcore.http.isession)'un genişletme yöntemleri tarafından sağlanır. Karmaşık türler JSON gibi başka bir mekanizma kullanılarak Kullanıcı tarafından serileştirilmelidir.

Seri hale getirilebilir nesneleri ayarlamak ve almak için aşağıdaki uzantı yöntemlerini ekleyin:

[!code-csharp[](app-state/samples/2.x/SessionSample/Extensions/SessionExtensions.cs?name=snippet1)]

Aşağıdaki örnek, uzantı yöntemleriyle bir serileştirilebilir nesnenin nasıl ayarlanacağını ve alınacağını gösterir:

[!code-csharp[](app-state/samples/2.x/SessionSample/Pages/Index.cshtml.cs?name=snippet2)]

## <a name="tempdata"></a>TempData

ASP.NET Core, Razor Pages [TempData](xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.TempData) veya Controller <xref:Microsoft.AspNetCore.Mvc.Controller.TempData>'ı kullanıma sunar. Bu özellik, verileri başka bir istekte okunana kadar depolar. [Sakla (dize)](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Keep*) ve [Peek (dize)](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Peek*) yöntemleri, isteğin sonunda silme yapılmadan verileri incelemek için kullanılabilir. [Keep ()](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Keep*) sözlükte tüm öğeleri bekletme için işaretler. `TempData`, tek bir istek için veri gerektiğinde yeniden yönlendirme için özellikle kullanışlıdır. `TempData`, tanımlama bilgileri `TempData` ya da oturum durumu kullanılarak sağlayıcılar tarafından uygulanır.

## <a name="tempdata-samples"></a>TempData örnekleri

Bir müşteri oluşturan aşağıdaki sayfayı göz önünde bulundurun:

[!code-csharp[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet&highlight=15-16,30)]

Aşağıdaki sayfa görüntülenir `TempData["Message"]`:

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/IndexPeek.cshtml?range=1-14)]

Önceki İşaretlemede, isteğin `TempData["Message"]` sonunda, kullanıldığı için silinmez. **not** `Peek` Sayfayı yenileme sayfası görüntülenir `TempData["Message"]`.

Aşağıdaki biçimlendirme önceki koda benzerdir, ancak isteğin sonundaki verileri korumak için `Keep` kullanır:

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/IndexKeep.cshtml?range=1-14)]

*Indexpeek* ve *ındexkeep* sayfaları arasında gezinmek silinmez `TempData["Message"]`.

Aşağıdaki kod görüntülenir `TempData["Message"]`, ancak isteğin `TempData["Message"]` sonunda silinir:

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/Index.cshtml?range=1-14)]

### <a name="tempdata-providers"></a>TempData sağlayıcıları

Tanımlama bilgisi tabanlı TempData sağlayıcısı, TempData 'ı tanımlama bilgilerinde depolamak için varsayılan olarak kullanılır.

Tanımlama bilgisi verileri, [Base64UrlTextEncoder](/dotnet/api/microsoft.aspnetcore.webutilities.base64urltextencoder)ile kodlanan ve sonra öbekli [ıdataprotector](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotector)kullanılarak şifrelenir. Tanımlama bilgisi öbekli olduğundan, ASP.NET Core 1. x içinde bulunan tek tanımlama bilgisi boyut sınırı uygulanmaz. Şifreli verileri sıkıştırmak, [suç](https://wikipedia.org/wiki/CRIME_(security_exploit)) ve [ihlal](https://wikipedia.org/wiki/BREACH_(security_exploit)) saldırıları gibi güvenlik sorunlarına yol açacağından, tanımlama bilgisi verileri sıkıştırılmaz. Tanımlama bilgisi tabanlı TempData sağlayıcısı hakkında daha fazla bilgi için bkz. tanımlama, [ıetempdataprovider](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.cookietempdataprovider).

### <a name="choose-a-tempdata-provider"></a>Bir TempData sağlayıcısı seçin

Bir TempData sağlayıcısı seçmek şöyle bazı hususlar içerir:

1. Uygulama oturum durumunu zaten kullanıyor mu? Bu durumda, oturum durumu, TempData Provider 'ın kullanılması uygulamaya ek bir ücret vermez (verilerin boyutundan itibaren).
2. Uygulama yalnızca görece küçük miktarlarda veri (500 bayta kadar) için TempData kullanıyor mu? Bu durumda, bir tanımlama bilgisi TempData Provider, TempData kullanan her isteğe küçük bir maliyet ekler. Aksi takdirde, oturum durumu TempData Provider, Geçicimiz veri tüketilene kadar her istekte büyük miktarda veri dönüşü olmaması yararlı olabilir.
3. Uygulama, birden çok sunucuda bir sunucu grubunda mi çalışıyor? Bu durumda, veri koruma (bkz <xref:security/data-protection/introduction> . ve [anahtar depolama sağlayıcıları](xref:security/data-protection/implementation/key-storage-providers)) dışındaki tanımlama bilgisi TempData sağlayıcısını kullanmak için ek bir yapılandırma gerekmez.

> [!NOTE]
> Çoğu Web istemcisi (Web tarayıcıları gibi), her tanımlama bilgisinin en büyük boyutu, toplam tanımlama bilgisi sayısı veya her ikisi için sınır uygular. Bu tanımlama bilgisi TempData sağlayıcısını kullanırken, uygulamanın bu sınırları aşmadığını doğrulayın. Verilerin toplam boyutunu göz önünde bulundurun. Şifreleme ve parçalama nedeniyle tanımlama bilgisi boyutundaki artışlar için hesap.

### <a name="configure-the-tempdata-provider"></a>TempData sağlayıcısını yapılandırma

Tanımlama bilgisi tabanlı TempData sağlayıcısı varsayılan olarak etkindir.

Oturum tabanlı TempData sağlayıcısını etkinleştirmek için [Addsessionstatetempdataprovider](/dotnet/api/microsoft.extensions.dependencyinjection.mvcviewfeaturesmvcbuilderextensions.addsessionstatetempdataprovider) genişletme yöntemini kullanın:

[!code-csharp[](app-state/samples_snapshot_2/2.x/SessionSample/Startup.cs?name=snippet1&highlight=11,13,32)]

Ara yazılım sırası önemlidir. Önceki örnekte, sonrasında `InvalidOperationException` `UseSession` `UseMvc`çağrıldığında bir özel durum oluşur. Daha fazla bilgi için bkz. [Ara yazılım sıralaması](xref:fundamentals/middleware/index#order).

> [!IMPORTANT]
> .NET Framework hedefleme ve oturum tabanlı TempData sağlayıcısını kullanıyorsanız, [Microsoft. AspNetCore. Session](https://www.nuget.org/packages/Microsoft.AspNetCore.Session/) paketini projeye ekleyin.

## <a name="query-strings"></a>Sorgu dizeleri

Yeni isteğin sorgu dizesine eklenerek sınırlı miktarda veri, bir istekten diğerine geçirilebilir. Bu durum, gömülü durum ile bağlantıların e-posta veya sosyal ağlar aracılığıyla paylaşılmasını sağlamak için durumu kalıcı bir şekilde yakalamak için yararlıdır. URL sorgu dizeleri ortak olduğundan, gizli veriler için hiçbir şekilde Sorgu dizelerini kullanmayın.

Sorgu dizelerindeki veriler de dahil olmak üzere, istenmeyen paylaşıma ek olarak, [siteler arası Istek forgery (CSRF)](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) saldırıları için fırsat oluşturabilir ve bu da kullanıcıların kimliği doğrulandığında kötü amaçlı siteleri ziyaret etmesini sağlayabilir. Saldırganlar daha sonra Kullanıcı verilerini uygulamadan çalabilir veya Kullanıcı adına kötü amaçlı eylemler gerçekleştirebilir. Korunan uygulamaların veya oturum durumunun CSRF saldırılarına karşı korunması gerekir. Daha fazla bilgi için bkz. [siteler arası Istek forgery (XSRF/CSRF) saldırılarını önleme](xref:security/anti-request-forgery).

## <a name="hidden-fields"></a>Gizli alanlar

Veriler gizli form alanlarına kaydedilebilir ve sonraki istek üzerine geri gönderilebilir. Bu çok sayfalı formlarda yaygındır. İstemci verilerle oynayabilir olabileceğinden, uygulamanın gizli alanlarda depolanan verileri her zaman yeniden doğrulaması gerekir.

## <a name="httpcontextitems"></a>HttpContext. Items

[HttpContext. Items](/dotnet/api/microsoft.aspnetcore.http.httpcontext.items) koleksiyonu, tek bir isteği işlerken verileri depolamak için kullanılır. Koleksiyon içeriği bir istek işlendikten sonra atılır. `Items` Koleksiyon genellikle, bir istek sırasında zaman içinde farklı noktalarda çalıştıklarında ve parametreleri geçirmek için doğrudan bir yol olmadığında bileşenlerin veya ara yazılımların iletişim kurmasına izin vermek için kullanılır.

Aşağıdaki örnekte, [Ara yazılım](xref:fundamentals/middleware/index) `Items` koleksiyona eklenir `isVerified` .

```csharp
app.Use(async (context, next) =>
{
    // perform some verification
    context.Items["isVerified"] = true;
    await next.Invoke();
});
```

İşlem hattının ilerleyen kısımlarında, başka bir ara yazılım şu değere erişebilir `isVerified`:

```csharp
app.Run(async (context) =>
{
    await context.Response.WriteAsync($"Verified: {context.Items["isVerified"]}");
});
```

Yalnızca tek bir uygulama tarafından kullanılan ara yazılım için `string` anahtarlar kabul edilebilir. Uygulama örnekleri arasında paylaşılan ara yazılım, anahtar çakışmalarını önlemek için benzersiz nesne anahtarları kullanmalıdır. Aşağıdaki örnek, bir ara yazılım sınıfında tanımlanan benzersiz bir nesne anahtarının nasıl kullanılacağını gösterir:

[!code-csharp[](app-state/samples/2.x/SessionSample/Middleware/HttpContextItemsMiddleware.cs?name=snippet1&highlight=4,13)]

Diğer kod, ara yazılım sınıfının açığa çıkarılan `HttpContext.Items` anahtarını kullanarak içinde depolanan değere erişebilir:

[!code-csharp[](app-state/samples/2.x/SessionSample/Pages/Index.cshtml.cs?name=snippet3)]

Bu yaklaşım ayrıca koddaki anahtar dizelerinin kullanımını ortadan kaldırma avantajına sahiptir.

## <a name="cache"></a>Önbellek

Önbelleğe alma, verileri depolamak ve almak için etkili bir yoldur. Uygulama, önbelleğe alınmış öğelerin ömrünü denetleyebilir.

Önbelleğe alınan veriler belirli bir istek, Kullanıcı veya oturumla ilişkili değildir. **Diğer kullanıcıların istekleri tarafından alınabilecek kullanıcıya özgü verileri önbelleğe alma konusunda dikkatli olun.**

Daha fazla bilgi için bkz. <xref:performance/caching/response>.

## <a name="dependency-injection"></a>Bağımlılık Ekleme

Verilerin tüm kullanıcılar tarafından kullanılabilmesini sağlamak için [bağımlılık ekleme](xref:fundamentals/dependency-injection) 'yi kullanın:

1. Verileri içeren bir hizmet tanımlayın. Örneğin, adlı `MyAppData` bir sınıf tanımlanmıştır:

    ```csharp
    public class MyAppData
    {
        // Declare properties and methods
    }
    ```

2. Hizmet sınıfını şu şekilde `Startup.ConfigureServices`ekleyin:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSingleton<MyAppData>();
    }
    ```

3. Veri hizmeti sınıfını tüketme:

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

* "' Microsoft. AspNetCore. Session. DistributedSessionStore ' etkinleştirilmeye çalışılırken ' Microsoft. Extensions. Caching. Distributed. ıdistributedcache ' türü için hizmet çözümlenemiyor."

  Bunun nedeni genellikle en az bir `IDistributedCache` uygulamanın yapılandırması başarısız olmuştur. Daha fazla bilgi için <xref:performance/caching/distributed> ve <xref:performance/caching/memory> bölümlerine bakın.

* Oturum ara yazılımı bir oturumu kalıcı hale getiremediğinde (örneğin, yedekleme deposu kullanılamıyorsa), ara yazılım özel durumu günlüğe kaydeder ve istek normal olarak devam eder. Bu, öngörülemeyen davranışa yol açar.

  Örneğin, bir Kullanıcı bir alışveriş sepetini oturum içinde depolar. Kullanıcı sepete bir öğe ekler, ancak kayıt başarısız olur. Uygulama hata hakkında bilgi sahibi değildir, bu nedenle bu, doğru olmayan, kullanıcıya öğenin sepetine eklendiğini bildirir.

  Hataları denetlemek için önerilen yaklaşım, uygulama oturuma yazma işlemi `await feature.Session.CommitAsync();` tamamlandığında uygulama kodundan çağırmalıdır. `CommitAsync`yedekleme deposu kullanılamıyorsa bir özel durum oluşturur. `CommitAsync` Başarısız olursa, uygulama özel durumu işleyebilir. `LoadAsync`veri deposunun kullanılamadığı koşulların aynısını oluşturur.
  
## <a name="signalr-and-session-state"></a>SignalRve oturum durumu

SignalRuygulamalar, bilgileri depolamak için oturum durumunu kullanmamalıdır. SignalRuygulamalar, hub `Context.Items` 'da bağlantı durumu başına depolama yapabilir. <!-- https://github.com/aspnet/SignalR/issues/2139 -->

## <a name="additional-resources"></a>Ek kaynaklar

<xref:host-and-deploy/web-farm>
::: moniker-end
