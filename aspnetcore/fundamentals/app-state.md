---
title: ASP.NET Core oturum
author: rick-anderson
description: İstekler arasındaki oturumu korumak için yaklaşımları bulur.
ms.author: riande
ms.custom: mvc
ms.date: 03/06/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/app-state
ms.openlocfilehash: c05129c0f239fb28c83ab1c561dd910305eeb54b
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88017642"
---
# <a name="session-and-state-management-in-aspnet-core"></a>ASP.NET Core’da oturum ve durum yönetimi

::: moniker range=">= aspnetcore-3.0"

[Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkabağı](https://twitter.com/serpent5)ve [Diana lagül](https://github.com/DianaLaRose)

HTTP durum bilgisiz bir protokoldür. Varsayılan olarak, HTTP istekleri Kullanıcı değerlerini içermeyen bağımsız iletilerdir. Bu makalede, istekler arasında kullanıcı verilerini korumak için çeşitli yaklaşımlar açıklanmaktadır.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/app-state/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="state-management"></a>Durum yönetimi

Durum, çeşitli yaklaşımlar kullanılarak depolanabilir. Her yaklaşım, bu konunun ilerleyen kısımlarında açıklanmıştır.

| Depolama yaklaşımı | Depolama mekanizması |
| ---------------- | ----------------- |
| [Cookiemalar](#cookies) | HTTP cookie s. , Sunucu tarafı uygulama kodu kullanılarak depolanan verileri içerebilir. |
| [Oturum durumu](#session-state) | HTTP cookie s ve sunucu tarafı uygulama kodu |
| [TempData](#tempdata) | HTTP cookie s veya oturum durumu |
| [Sorgu dizeleri](#query-strings) | HTTP sorgu dizeleri |
| [Gizli alanlar](#hidden-fields) | HTTP form alanları |
| [HttpContext. Items](#httpcontextitems) | Sunucu tarafı uygulama kodu |
| [Önbellek](#cache) | Sunucu tarafı uygulama kodu |

## <a name="no-loccookies"></a>Cookiemalar

Cookies verileri istekler arasında depolar. cookieS her istek ile gönderildiğinden, boyutu en az bir olmalıdır. İdeal olarak, cookie uygulama tarafından depolanan verilerle birlikte yalnızca bir tanımlayıcı depolanmalıdır. Tarayıcıların çoğu, cookie boyutu 4096 bayt olarak kısıtlar. cookieHer etki alanı için yalnızca sınırlı sayıda s vardır.

cookieS, değişikliklere tabi olduğundan uygulama tarafından doğrulanması gerekir. Cookies, kullanıcılar tarafından silinebilir ve istemciler üzerinde sona erer. Ancak, cookie genel olarak istemcide en dayanıklı veri kalıcılığı biçimidir.

Cookie, içerik bilinen bir kullanıcı için özelleştirildiğinde, genellikle kişiselleştirme için kullanılır. Kullanıcı yalnızca tanımlı ve kimlik doğrulaması değil çoğu durumda. , cookie Kullanıcının adını, hesap adını veya GUID gibi benzersiz kullanıcı kimliğini depolayabilirler. , cookie Kullanıcının tercih edilen web sitesi arka plan rengi gibi kişiselleştirilmiş ayarlarına erişmek için kullanılabilir.

' İ verirken ve gizlilik kaygılarıyla ilgilenirken [Avrupa Birliği genel veri koruma yönetmeliklerine (GDPR)](https://ec.europa.eu/info/law/law-topic/data-protection) bakın cookie . Daha fazla bilgi için [ASP.NET Core genel veri koruma yönetmeliği (GDPR) desteğini](xref:security/gdpr)inceleyin.

## <a name="session-state"></a>Oturum durumu

Oturum durumu, Kullanıcı bir Web uygulamasına göz atarken Kullanıcı verilerinin depolanması için bir ASP.NET Core senaryodur. Oturum durumu, bir istemciden gelen istekler arasında verileri kalıcı hale getirmek için uygulama tarafından tutulan bir depoyu kullanır. Oturum verileri bir önbellek tarafından desteklenir ve kısa ömürlü veriler olarak değerlendirilir. Site, oturum verileri olmadan çalışmaya devam etmelidir. Kritik uygulama verileri Kullanıcı veritabanında depolanmalıdır ve oturum yalnızca bir performans iyileştirmesi olarak önbelleğe alınmalıdır.

[SignalR](xref:signalr/index)Bir [ SignalR hub](xref:signalr/hubs) http bağlamından bağımsız olarak yürütülemediğinden, oturum uygulamalarda desteklenmez. Örneğin, bir uzun yoklama isteği, isteğin HTTP bağlamının ömrü ötesinde bir hub tarafından açık tutulduğunda bu durum oluşabilir.

ASP.NET Core cookie , oturum kimliği içeren istemciye bir sağlayarak oturum durumunu korur. cookieOturum kimliği:

* Her istekle birlikte uygulamaya gönderilir.
* Uygulama tarafından oturum verilerini getirmek için kullanılır.

Oturum durumu aşağıdaki davranışları sergiler:

* Oturum, cookie tarayıcıya özeldir. Oturumlar tarayıcılar arasında paylaşılmaz.
* cookieTarayıcı oturumu sona erdiğinde oturum öğeleri silinir.
* Bir cookie oturum, tarihi geçen bir oturum için alınmışsa, aynı oturumu kullanan yeni bir oturum oluşturulur cookie .
* Boş oturumlar korunmaz. Oturumun istekler arasında kalıcı olması için oturumun en az bir değere ayarlanmış olması gerekir. Bir oturum tutulmadığı zaman, her yeni istek için yeni bir oturum KIMLIĞI oluşturulur.
* Uygulama, son istekten sonra sınırlı bir süre boyunca bir oturum tutar. Uygulama, oturum zaman aşımını ayarlar ya da 20 dakikalık varsayılan değeri kullanır. Oturum durumu, Kullanıcı verilerini depolamak için idealdir:
  * Belirli bir oturuma özgüdür.
  * Verilerin oturumlar arasında kalıcı depolama gerektirmediğini.
* Oturum verileri, <xref:Microsoft.AspNetCore.Http.ISession.Clear%2A?displayProperty=nameWithType> uygulama çağrıldığında veya oturum sona erdiğinde silinir.
* Uygulama kodunu bir istemci tarayıcısının kapatıldığını veya istemcide ne zaman dolduğunu veya bittiğini bildirmek için varsayılan bir mekanizma yoktur cookie .
* Oturum durumu cookie Varsayılan olarak temel olarak işaretlenmez. Site ziyaretçisi tarafından izlemeye izin verilmediği müddetçe oturum durumu işlevsel değildir. Daha fazla bilgi için bkz. <xref:security/gdpr#tempdata-provider-and-session-state-cookies-arent-essential>.

> [!WARNING]
> Gizli verileri oturum durumunda depolamayin. Kullanıcı tarayıcıyı Kapatmayabilir ve oturumu temizleyebilir cookie . Bazı tarayıcılarda cookie tarayıcı pencereleri arasında geçerli oturum vardır. Bir oturum, tek bir kullanıcıyla kısıtlanmayabilir. Sonraki Kullanıcı aynı oturumla uygulamaya gözatmaya devam edebilir cookie .

Bellek içi önbellek sağlayıcısı, oturum verilerini uygulamanın bulunduğu sunucunun belleğinde depolar. Sunucu grubu senaryosunda:

* Her oturumu tek bir sunucudaki belirli bir uygulama örneğine bağlamak için *yapışkan oturumları* kullanın. [Azure App Service](https://azure.microsoft.com/services/app-service/) , varsayılan olarak yapışkan oturumları zorlamak Için [uygulama isteği yönlendirme (ARR)](/iis/extensions/planning-for-arr/using-the-application-request-routing-module) kullanır. Ancak, yapışkan oturumlar ölçeklenebilirliği etkileyebilir ve Web uygulaması güncelleştirmelerini karmaşıklaştırır. Daha iyi bir yaklaşım, yapışkan oturum gerektirmeyen bir redya veya SQL Server dağıtılmış önbellek kullanmaktır. Daha fazla bilgi için bkz. <xref:performance/caching/distributed>.
* Oturum, cookie aracılığıyla şifrelenir <xref:Microsoft.AspNetCore.DataProtection.IDataProtector> . Veri koruma, her makinede oturum s 'yi okumak için düzgün şekilde yapılandırılmalıdır cookie . Daha fazla bilgi için bkz <xref:security/data-protection/introduction> . ve [anahtar depolama sağlayıcıları](xref:security/data-protection/implementation/key-storage-providers).

### <a name="configure-session-state"></a>Oturum durumunu yapılandırma

[Microsoft. AspNetCore. Session](https://www.nuget.org/packages/Microsoft.AspNetCore.Session/) paketi:

* Framework tarafından örtük olarak dahil edilir.
* Oturum durumunu yönetmek için ara yazılım sağlar.

Oturum ara yazılımını etkinleştirmek için şunları `Startup` içermelidir:

* Herhangi bir <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> bellek önbelleğidir. `IDistributedCache`Uygulama, oturum için bir yedekleme deposu olarak kullanılır. Daha fazla bilgi için bkz. <xref:performance/caching/distributed>.
* İçinde öğesine bir <xref:Microsoft.Extensions.DependencyInjection.SessionServiceCollectionExtensions.AddSession%2A> çağrı `ConfigureServices` .
* İçinde öğesine bir <xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession%2A> çağrı `Configure` .

Aşağıdaki kod, bellek içi oturum sağlayıcısını varsayılan bir bellek içi uygulamasıyla ayarlamayı gösterir `IDistributedCache` :

[!code-csharp[](app-state/samples/3.x/SessionSample/Startup4.cs?name=snippet1&highlight=12-19,45)]

Yukarıdaki kod, testi basitleştirmek için kısa bir zaman aşımı ayarlar.

Ara yazılım sırası önemlidir.  `UseSession`Sonra `UseRouting` ve önce çağırın `UseEndpoints` . Bkz. [Ara yazılım sıralaması](xref:fundamentals/middleware/index#order).

[HttpContext. Session](xref:Microsoft.AspNetCore.Http.HttpContext.Session) , oturum durumu yapılandırıldıktan sonra kullanılabilir.

`HttpContext.Session`çağrılmadan önce erişilemez `UseSession` .

Uygulama yanıt akışına yazmaya başladıktan sonra yeni bir oturum ile yeni bir oturum cookie oluşturulamaz. Özel durum Web sunucusu günlüğüne kaydedilir ve tarayıcıda gösterilmez.

### <a name="load-session-state-asynchronously"></a>Oturum durumunu zaman uyumsuz olarak yükle

ASP.NET Core varsayılan oturum sağlayıcısı, oturum kayıtlarını, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> yalnızca <xref:Microsoft.AspNetCore.Http.ISession.LoadAsync%2A?displayProperty=nameWithType> Yöntem, <xref:Microsoft.AspNetCore.Http.ISession.TryGetValue%2A> <xref:Microsoft.AspNetCore.Http.ISession.Set%2A> veya metotlardan önce açıkça çağrılırsa, temel alınan yedekleme deposundan zaman uyumsuz olarak yükler <xref:Microsoft.AspNetCore.Http.ISession.Remove%2A> . `LoadAsync`İlk çağrılmadıysa, temel alınan oturum kaydı zaman uyumlu olarak yüklenir ve bu da ölçekte performans cezası oluşturabilir.

Uygulamaların bu kalıbı zorunlu kılmak için, <xref:Microsoft.AspNetCore.Session.DistributedSessionStore> ve <xref:Microsoft.AspNetCore.Session.DistributedSession> uygulamalarını bir özel durum oluşturan sürümlerle sarın `LoadAsync` ,, veya öncesinde çağrılamaz `TryGetValue` `Set` `Remove` . Sarmalanan sürümleri hizmetler kapsayıcısına kaydedin.

### <a name="session-options"></a>Oturum seçenekleri

Oturum varsayılanlarını geçersiz kılmak için kullanın <xref:Microsoft.AspNetCore.Builder.SessionOptions> .

| Seçenek | Açıklama |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Builder.SessionOptions.Cookie> | Oluşturmak için kullanılan ayarları belirler cookie . <xref:Microsoft.AspNetCore.Http.CookieBuilder.Name>Varsayılan olarak <xref:Microsoft.AspNetCore.Session.SessionDefaults.CookieName?displayProperty=nameWithType> ( `.AspNetCore.Session` ). <xref:Microsoft.AspNetCore.Http.CookieBuilder.Path>Varsayılan olarak <xref:Microsoft.AspNetCore.Session.SessionDefaults.CookiePath?displayProperty=nameWithType> ( `/` ). <xref:Microsoft.AspNetCore.Http.CookieBuilder.SameSite>Varsayılan olarak <xref:Microsoft.AspNetCore.Http.SameSiteMode.Lax?displayProperty=nameWithType> ( `1` ). <xref:Microsoft.AspNetCore.Http.CookieBuilder.HttpOnly>Varsayılan olarak olur `true` . <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential>Varsayılan olarak olur `false` . |
| <xref:Microsoft.AspNetCore.Builder.SessionOptions.IdleTimeout> | , `IdleTimeout` Oturumun içeriği terk edilmeden önce ne kadar süreyle boşta kalabileceğini gösterir. Her oturum erişimi zaman aşımını sıfırlar. Bu ayar, yalnızca oturum içeriği için geçerlidir cookie . Varsayılan değer 20 dakikadır. |
| <xref:Microsoft.AspNetCore.Builder.SessionOptions.IOTimeout> | Mağazadan bir oturumu yüklemesine veya depolama alanına geri kaydetmeye izin verilen en uzun süre. Bu ayar yalnızca zaman uyumsuz işlemlere uygulanabilir. Bu zaman aşımı, kullanılarak devre dışı bırakılabilir <xref:System.Threading.Timeout.InfiniteTimeSpan> . Varsayılan değer 1 dakikadır. |

Oturum cookie , tek bir tarayıcıdan gelen istekleri izlemek ve tanımlamak için bir kullanır. Varsayılan olarak, bu olarak cookie adlandırılır `.AspNetCore.Session` ve bir yolu kullanır `/` . Varsayılan, cookie bir etki alanı belirtmediği için, sayfada istemci tarafı komut dosyası tarafından kullanılamaz ( <xref:Microsoft.AspNetCore.Http.CookieBuilder.HttpOnly> Varsayılan olarak ' dir `true` ).

Oturum varsayılanlarını geçersiz kılmak için cookie şunu kullanın <xref:Microsoft.AspNetCore.Builder.SessionOptions> :

[!code-csharp[](app-state/samples/3.x/SessionSample/Startup2.cs?name=snippet1&highlight=5-10)]

Uygulama, <xref:Microsoft.AspNetCore.Builder.SessionOptions.IdleTimeout> bir oturumun, sunucunun önbelleğindeki içeriği terk edilmeden önce ne kadar süreyle boşta kalabileceğini anlamak için özelliğini kullanır. Bu özellik, cookie süre sonundan bağımsızdır. [Oturum ara yazılımı](xref:Microsoft.AspNetCore.Session.SessionMiddleware) üzerinden geçen her istek zaman aşımını sıfırlar.

Oturum durumu *kilitli*değil. İki istek aynı anda bir oturumun içeriğini değiştirmeyi denerseniz, son istek ilk geçersiz kılar. `Session`*tutarlı bir oturum*olarak uygulanır, yani tüm içerikler birlikte depolanır. İki istek farklı oturum değerlerini değiştirmek için arama yaparken, son istek ilk tarafından yapılan oturum değişikliklerini geçersiz kılabilir.

### <a name="set-and-get-session-values"></a>Oturum değerlerini ayarlama ve edinme

Oturum durumuna, Razor ile bir Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> SıNıFıNDAN veya MVC sınıfından erişilir <xref:Microsoft.AspNetCore.Mvc.Controller> <xref:Microsoft.AspNetCore.Http.HttpContext.Session?displayProperty=nameWithType> . Bu özellik bir <xref:Microsoft.AspNetCore.Http.ISession> uygulamasıdır.

`ISession`Uygulama, tamsayı ve dize değerlerini ayarlamak ve almak için birkaç uzantı yöntemi sağlar. Uzantı yöntemleri <xref:Microsoft.AspNetCore.Http> ad alanıdır.

`ISession`Uzantı yöntemleri:

* [Al (ISession, dize)](xref:Microsoft.AspNetCore.Http.SessionExtensions.Get%2A)
* [Getınt32 (ISession, dize)](xref:Microsoft.AspNetCore.Http.SessionExtensions.GetInt32%2A)
* [GetString (ISession, dize)](xref:Microsoft.AspNetCore.Http.SessionExtensions.GetString%2A)
* [Setınt32 (ISession, dize, Int32)](xref:Microsoft.AspNetCore.Http.SessionExtensions.SetInt32%2A)
* [SetString (ISession, dize, dize)](xref:Microsoft.AspNetCore.Http.SessionExtensions.SetString%2A)

Aşağıdaki örnek, `IndexModel.SessionKeyName` `_Name` bir sayfalar sayfasındaki anahtar (örnek uygulamada) için oturum değerini alır Razor :

```csharp
@page
@using Microsoft.AspNetCore.Http
@model IndexModel

...

Name: @HttpContext.Session.GetString(IndexModel.SessionKeyName)
```

Aşağıdaki örnek, bir tamsayı ve bir dizenin nasıl ayarlanacağını ve alınacağını gösterir:

[!code-csharp[](app-state/samples/3.x/SessionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=18-19,22-23)]

Tüm oturum verileri, bellek içi önbellek kullanılırken bile dağıtılmış önbellek senaryosunu etkinleştirmek üzere serileştirilmelidir. Dize ve tamsayı serileştiriciler, öğesinin genişletme yöntemleri tarafından sağlanır <xref:Microsoft.AspNetCore.Http.ISession> . Karmaşık türler JSON gibi başka bir mekanizma kullanılarak Kullanıcı tarafından serileştirilmelidir.

Nesneleri seri hale getirmek için aşağıdaki örnek kodu kullanın:

[!code-csharp[](app-state/samples/3.x/SessionSample/Extensions/SessionExtensions.cs?name=snippet1)]

Aşağıdaki örnek, sınıfı ile serileştirilebilir bir nesnenin nasıl ayarlanacağını ve alınacağını gösterir `SessionExtensions` :

[!code-csharp[](app-state/samples/3.x/SessionSample/Pages/Index.cshtml.cs?name=snippet2)]

## <a name="tempdata"></a>TempData

ASP.NET Core, Razor [TempData](xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.TempData) veya Controller sayfalarını kullanıma sunar <xref:Microsoft.AspNetCore.Mvc.Controller.TempData> . Bu özellik, verileri başka bir istekte okunana kadar depolar. [Saklama (dize)](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Keep*) ve [Peek (dize)](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Peek*) yöntemleri, isteğin sonunda silme yapılmadan verileri incelemek için kullanılabilir. [Saklama için](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Keep*) sözlükte tüm öğeleri işaretler. `TempData`eklenir

* Tek bir istek için veri gerektiğinde yeniden yönlendirme için kullanışlıdır.
* Sağlayıcılar tarafından ya `TempData` da cookie oturum durumu kullanılarak uygulanır.

## <a name="tempdata-samples"></a>TempData örnekleri

Bir müşteri oluşturan aşağıdaki sayfayı göz önünde bulundurun:

[!code-csharp[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet&highlight=15-16,30)]

Aşağıdaki sayfa görüntülenir `TempData["Message"]` :

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/IndexPeek.cshtml?range=1-14)]

Önceki İşaretlemede, isteğin sonunda, `TempData["Message"]` kullanıldığı için **not** silinmez `Peek` . Sayfanın yenilenmesi, içeriğini görüntüler `TempData["Message"]` .

Aşağıdaki biçimlendirme önceki koda benzerdir, ancak `Keep` isteğin sonundaki verileri korumak için kullanır:

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/IndexKeep.cshtml?range=1-14)]

*Indexpeek* ve *ındexkeep* sayfaları arasında gezinmek silinmez `TempData["Message"]` .

Aşağıdaki kod görüntülenir `TempData["Message"]` , ancak isteğin sonunda `TempData["Message"]` silinir:

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/Index.cshtml?range=1-14)]

### <a name="tempdata-providers"></a>TempData sağlayıcıları

cookie-Tabanlı TempData sağlayıcısı, ' deki TempData ' i depolamak için varsayılan olarak kullanılır cookie .

cookieVeriler, <xref:Microsoft.AspNetCore.DataProtection.IDataProtector> ile kodlanmış, <xref:Microsoft.AspNetCore.WebUtilities.Base64UrlTextEncoder> ve ardından öbekli kullanılarak şifrelenir. En büyük cookie boyut, şifreleme ve parçalama nedeniyle [4096 bayttan](http://www.faqs.org/rfcs/rfc2965.html) daha azdır. cookieŞifrelenmiş verilerin sıkıştırılması, [suç](https://wikipedia.org/wiki/CRIME_(security_exploit)) ve [ihlal](https://wikipedia.org/wiki/BREACH_(security_exploit)) saldırıları gibi güvenlik sorunlarına yol açacağından veriler sıkıştırılmaz. Tabanlı TempData sağlayıcısı hakkında daha fazla bilgi için cookie bkz <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.CookieTempDataProvider> ..

### <a name="choose-a-tempdata-provider"></a>Bir TempData sağlayıcısı seçin

Bir TempData sağlayıcısı seçmek şöyle bazı hususlar içerir:

* Uygulama oturum durumunu zaten kullanıyor mu? Bu durumda, oturum durumu ' nu kullanmak TempData Provider 'ın veri boyutunun ötesinde uygulamaya ek maliyeti yoktur.
* Uygulama, 500 bayta kadar yalnızca görece küçük miktarlarda veri için TempData kullanıyor mu? Bu durumda, cookie TempData sağlayıcısı her bir istek Için TempData içeren küçük bir maliyet ekler. Aksi takdirde, oturum durumu TempData Provider, Geçicimiz veri tüketilene kadar her istekte büyük miktarda veri dönüşü olmaması yararlı olabilir.
* Uygulama, birden çok sunucuda bir sunucu grubunda mi çalışıyor? Bu durumda, cookie veri koruma dışında TempData sağlayıcısını kullanmak için ek yapılandırma gerekmez (bkz <xref:security/data-protection/introduction> . ve [anahtar depolama sağlayıcıları](xref:security/data-protection/implementation/key-storage-providers)).

Web tarayıcıları gibi birçok Web istemcisi, her birinin en büyük boyutu cookie ve toplam sayısı için sınırlar uygular cookie . cookieTempData sağlayıcısını kullanırken, uygulamanın [Bu sınırları](http://www.faqs.org/rfcs/rfc2965.html)aşmadığını doğrulayın. Verilerin toplam boyutunu göz önünde bulundurun. cookieŞifreleme ve öbek oluşturma nedeniyle hesabın boyutu artar.

### <a name="configure-the-tempdata-provider"></a>TempData sağlayıcısını yapılandırma

cookieTabanlı TempData sağlayıcısı varsayılan olarak etkindir.

Oturum tabanlı TempData sağlayıcısını etkinleştirmek için <xref:Microsoft.Extensions.DependencyInjection.MvcViewFeaturesMvcBuilderExtensions.AddSessionStateTempDataProvider%2A> genişletme yöntemini kullanın. Yalnızca bir çağrısı `AddSessionStateTempDataProvider` gereklidir:

[!code-csharp[](app-state/samples/3.x/SessionSample/Startup3.cs?name=snippet1&highlight=4,6,8,30)]

## <a name="query-strings"></a>Sorgu dizeleri

Yeni isteğin sorgu dizesine eklenerek sınırlı miktarda veri, bir istekten diğerine geçirilebilir. Bu durum, gömülü durum ile bağlantıların e-posta veya sosyal ağlar aracılığıyla paylaşılmasını sağlamak için durumu kalıcı bir şekilde yakalamak için yararlıdır. URL sorgu dizeleri ortak olduğundan, gizli veriler için hiçbir şekilde Sorgu dizelerini kullanmayın.

Sorgu dizelerindeki veriler de dahil olmak üzere, istenmeden paylaşıma ek olarak uygulamayı [siteler arası Istek forgery (CSRF)](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) saldırılarına açık duruma getirebilir. Korunan oturum durumunun, CSRF saldırılarına karşı korunması gerekir. Daha fazla bilgi için bkz. [siteler arası Istek forgery (XSRF/CSRF) saldırılarını önleme](xref:security/anti-request-forgery).

## <a name="hidden-fields"></a>Gizli alanlar

Veriler gizli form alanlarına kaydedilebilir ve sonraki istek üzerine geri gönderilebilir. Bu çok sayfalı formlarda yaygındır. İstemci verilerle oynayabilir olabileceğinden, uygulamanın gizli alanlarda depolanan verileri her zaman yeniden doğrulaması gerekir.

## <a name="httpcontextitems"></a>HttpContext. Items

<xref:Microsoft.AspNetCore.Http.HttpContext.Items?displayProperty=nameWithType>Koleksiyon, tek bir isteği işlerken verileri depolamak için kullanılır. Koleksiyon içeriği bir istek işlendikten sonra atılır. `Items`Koleksiyon genellikle, bir istek sırasında zaman içinde farklı noktalarda çalıştıklarında ve parametreleri geçirmek için doğrudan bir yol olmadığında bileşenlerin veya ara yazılımların iletişim kurmasına izin vermek için kullanılır.

Aşağıdaki örnekte, [Ara yazılım](xref:fundamentals/middleware/index) koleksiyona ekler `isVerified` `Items` :

[!code-csharp[](app-state/samples/3.x/SessionSample/Startup.cs?name=snippet1)]

Yalnızca tek bir uygulamada kullanılan ara yazılım için sabit `string` anahtarlar kabul edilebilir. Uygulamalar arasında paylaşılan ara yazılım, anahtar çakışmalarını önlemek için benzersiz nesne anahtarları kullanmalıdır. Aşağıdaki örnek, bir ara yazılım sınıfında tanımlanan benzersiz bir nesne anahtarının nasıl kullanılacağını gösterir:

[!code-csharp[](app-state/samples/3.x/SessionSample/Middleware/HttpContextItemsMiddleware.cs?name=snippet1&highlight=4,13)]

Diğer kod, `HttpContext.Items` Ara yazılım sınıfının açığa çıkarılan anahtarını kullanarak içinde depolanan değere erişebilir:

[!code-csharp[](app-state/samples/3.x/SessionSample/Pages/Index.cshtml.cs?name=snippet3)]

Bu yaklaşım ayrıca koddaki anahtar dizelerinin kullanımını ortadan kaldırma avantajına sahiptir.

## <a name="cache"></a>Önbellek

Önbelleğe alma, verileri depolamak ve almak için etkili bir yoldur. Uygulama, önbelleğe alınmış öğelerin ömrünü denetleyebilir. Daha fazla bilgi için bkz. <xref:performance/caching/response>.

Önbelleğe alınan veriler belirli bir istek, Kullanıcı veya oturumla ilişkili değildir. **Diğer Kullanıcı istekleri tarafından alınabilecek kullanıcıya özgü verileri önbelleğe vermeyin.**

Uygulama genelinde verileri önbelleğe almak için bkz <xref:performance/caching/memory> ..

## <a name="common-errors"></a>Sık karşılaşılan hatalar

* "' Microsoft. AspNetCore. Session. DistributedSessionStore ' etkinleştirilmeye çalışılırken ' Microsoft. Extensions. Caching. Distributed. ıdistributedcache ' türü için hizmet çözümlenemiyor."

  Bunun nedeni genellikle en az bir uygulamanın yapılandırması başarısız olmuştur `IDistributedCache` . Daha fazla bilgi için <xref:performance/caching/distributed> ve <xref:performance/caching/memory> bölümlerine bakın.

Oturum ara yazılımı bir oturumu kalıcı hale getiremezse:

* Ara yazılım özel durumu günlüğe kaydeder ve istek normal olarak devam eder.
* Bu, öngörülemeyen davranışa yol açar.

Yedekleme deposu kullanılamıyorsa, oturum ara yazılımı bir oturumu kalıcı hale getiremeyebilir. Örneğin, bir Kullanıcı bir alışveriş sepetini oturum içinde depolar. Kullanıcı sepete bir öğe ekler, ancak kayıt başarısız olur. Uygulama hata hakkında bilgi sahibi değildir, bu nedenle bu, doğru olmayan, kullanıcıya öğenin sepetine eklendiğini bildirir.

Hataları denetlemek için önerilen yaklaşım, `await feature.Session.CommitAsync` uygulama oturuma yazma tamamlandığında çağrmalıdır. <xref:Microsoft.AspNetCore.Http.ISession.CommitAsync*>yedekleme deposu kullanılamıyorsa bir özel durum oluşturur. `CommitAsync`Başarısız olursa, uygulama özel durumu işleyebilir. <xref:Microsoft.AspNetCore.Http.ISession.LoadAsync*>, veri deposu kullanılamadığında aynı koşulların altına atar.
  
## <a name="no-locsignalr-and-session-state"></a>SignalRve oturum durumu

SignalRuygulamalar, bilgileri depolamak için oturum durumunu kullanmamalıdır. SignalRuygulamalar, hub 'da bağlantı durumu başına depolama yapabilir `Context.Items` . <!-- https://github.com/aspnet/SignalR/issues/2139 -->

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
| [Cookiemalar](#cookies) | HTTP cookie s (sunucu tarafı uygulama kodu kullanılarak depolanan verileri içerebilir) |
| [Oturum durumu](#session-state) | HTTP cookie s ve sunucu tarafı uygulama kodu |
| [TempData](#tempdata) | HTTP cookie s veya oturum durumu |
| [Sorgu dizeleri](#query-strings) | HTTP sorgu dizeleri |
| [Gizli alanlar](#hidden-fields) | HTTP form alanları |
| [HttpContext. Items](#httpcontextitems) | Sunucu tarafı uygulama kodu |
| [Önbellek](#cache) | Sunucu tarafı uygulama kodu |
| [Bağımlılık Ekleme](#dependency-injection) | Sunucu tarafı uygulama kodu |

## <a name="no-loccookies"></a>Cookiemalar

Cookies verileri istekler arasında depolar. cookieS her istek ile gönderildiğinden, boyutu en az bir olmalıdır. İdeal olarak, cookie uygulama tarafından depolanan verilerle birlikte yalnızca bir tanımlayıcı depolanmalıdır. Tarayıcıların çoğu, cookie boyutu 4096 bayt olarak kısıtlar. cookieHer etki alanı için yalnızca sınırlı sayıda s vardır.

cookieS, değişikliklere tabi olduğundan uygulama tarafından doğrulanması gerekir. Cookies, kullanıcılar tarafından silinebilir ve istemciler üzerinde sona erer. Ancak, cookie genel olarak istemcide en dayanıklı veri kalıcılığı biçimidir.

Cookie, içerik bilinen bir kullanıcı için özelleştirildiğinde, genellikle kişiselleştirme için kullanılır. Kullanıcı yalnızca tanımlı ve kimlik doğrulaması değil çoğu durumda. cookieKullanıcının adını, hesap adını veya benzersiz kullanıcı kimliğini (GUID gibi) depolayabilirler. Daha sonra, cookie tercih edilen web sitesi arka plan rengi gibi kullanıcının kişiselleştirilmiş ayarlarına erişmek için kullanabilirsiniz.

, Ve gizlilik sorunları ile ilgilenirken [Avrupa Birliği genel veri koruma düzenlemelerine (GDPR)](https://ec.europa.eu/info/law/law-topic/data-protection) cookie sahip olun. Daha fazla bilgi için [ASP.NET Core genel veri koruma yönetmeliği (GDPR) desteğini](xref:security/gdpr)inceleyin.

## <a name="session-state"></a>Oturum durumu

Oturum durumu, Kullanıcı bir Web uygulamasına göz atarken Kullanıcı verilerinin depolanması için bir ASP.NET Core senaryodur. Oturum durumu, bir istemciden gelen istekler arasında verileri kalıcı hale getirmek için uygulama tarafından tutulan bir depoyu kullanır. Oturum verileri bir önbellek tarafından desteklenir ve kısa ömürlü veriler olarak kabul edilir. Bu, &mdash; sitenin oturum verileri olmadan çalışmaya devam etmesi gerekir. Kritik uygulama verileri Kullanıcı veritabanında depolanmalıdır ve oturum yalnızca bir performans iyileştirmesi olarak önbelleğe alınmalıdır.

> [!NOTE]
> [SignalR](xref:signalr/index)Bir [ SignalR hub](xref:signalr/hubs) http bağlamından bağımsız olarak yürütülemediğinden, oturum uygulamalarda desteklenmez. Örneğin, bir uzun yoklama isteği, isteğin HTTP bağlamının ömrü ötesinde bir hub tarafından açık tutulduğunda bu durum oluşabilir.

ASP.NET Core cookie , her istekle birlikte uygulamaya gönderilen oturum kimliği içeren bir istemciye sağlayarak oturum durumunu korur. Uygulama, oturum verilerini getirmek için oturum KIMLIĞINI kullanır.

Oturum durumu aşağıdaki davranışları sergiler:

* Oturum cookie tarayıcıya özel olduğundan, oturumlar tarayıcılar arasında paylaşılmaz.
* cookieTarayıcı oturumu sona erdiğinde oturum öğeleri silinir.
* Bir cookie oturum, tarihi geçen bir oturum için alınmışsa, aynı oturumu kullanan yeni bir oturum oluşturulur cookie .
* Boş oturumlar tutulmadığı &mdash; için oturum, oturum istekleri arasında kalıcı hale getirmek için en az bir değere ayarlanmış olmalıdır. Bir oturum tutulmadığı zaman, her yeni istek için yeni bir oturum KIMLIĞI oluşturulur.
* Uygulama, son istekten sonra sınırlı bir süre boyunca bir oturum tutar. Uygulama, oturum zaman aşımını ayarlar ya da 20 dakikalık varsayılan değeri kullanır. Oturum durumu, belirli bir oturuma özgü kullanıcı verilerini depolamak için idealdir, ancak verilerin oturumlarda kalıcı depolama gerektirmez.
* Oturum verileri, <xref:Microsoft.AspNetCore.Http.ISession.Clear%2A?displayProperty=nameWithType> uygulama çağrıldığında veya oturum sona erdiğinde silinir.
* Uygulama kodunu bir istemci tarayıcısının kapatıldığını veya istemcide ne zaman dolduğunu veya bittiğini bildirmek için varsayılan bir mekanizma yoktur cookie .
* ASP.NET Core MVC ve Razor sayfa şablonları, genel veri koruma yönetmeliği (GDPR) desteğini içerir. Oturum durumu cookie Varsayılan olarak gerekli değildir, bu nedenle site ziyaretçisi tarafından izlemeye izin verilmediği takdirde oturum durumu işlevsel değildir. Daha fazla bilgi için bkz. <xref:security/gdpr#tempdata-provider-and-session-state-cookies-arent-essential>.

> [!WARNING]
> Gizli verileri oturum durumunda depolamayin. Kullanıcı tarayıcıyı Kapatmayabilir ve oturumu temizleyebilir cookie . Bazı tarayıcılarda cookie tarayıcı pencereleri arasında geçerli oturum vardır. Bir oturum tek bir kullanıcıyla kısıtlanmayabilir &mdash; ve bir sonraki Kullanıcı aynı oturumla uygulamaya gözatmaya devam edebilir cookie .

Bellek içi önbellek sağlayıcısı, oturum verilerini uygulamanın bulunduğu sunucunun belleğinde depolar. Sunucu grubu senaryosunda:

* Her oturumu tek bir sunucudaki belirli bir uygulama örneğine bağlamak için *yapışkan oturumları* kullanın. [Azure App Service](https://azure.microsoft.com/services/app-service/) , varsayılan olarak yapışkan oturumları zorlamak Için [uygulama isteği yönlendirme (ARR)](/iis/extensions/planning-for-arr/using-the-application-request-routing-module) kullanır. Ancak, yapışkan oturumlar ölçeklenebilirliği etkileyebilir ve Web uygulaması güncelleştirmelerini karmaşıklaştırır. Daha iyi bir yaklaşım, yapışkan oturum gerektirmeyen bir redya veya SQL Server dağıtılmış önbellek kullanmaktır. Daha fazla bilgi için bkz. <xref:performance/caching/distributed>.
* Oturum, cookie aracılığıyla şifrelenir <xref:Microsoft.AspNetCore.DataProtection.IDataProtector> . Veri koruma, her makinede oturum s 'yi okumak için düzgün şekilde yapılandırılmalıdır cookie . Daha fazla bilgi için bkz <xref:security/data-protection/introduction> . ve [anahtar depolama sağlayıcıları](xref:security/data-protection/implementation/key-storage-providers).

### <a name="configure-session-state"></a>Oturum durumunu yapılandırma

[Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app)içinde yer alan [Microsoft. Aspnetcore. Session](https://www.nuget.org/packages/Microsoft.AspNetCore.Session/) paketi, oturum durumunu yönetmek için ara yazılım sağlar. Oturum ara yazılımını etkinleştirmek için şunları `Startup` içermelidir:

* Herhangi bir <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> bellek önbelleğidir. `IDistributedCache`Uygulama, oturum için bir yedekleme deposu olarak kullanılır. Daha fazla bilgi için bkz. <xref:performance/caching/distributed>.
* İçinde öğesine bir <xref:Microsoft.Extensions.DependencyInjection.SessionServiceCollectionExtensions.AddSession%2A> çağrı `ConfigureServices` .
* İçinde öğesine bir <xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession%2A> çağrı `Configure` .

Aşağıdaki kod, bellek içi oturum sağlayıcısını varsayılan bir bellek içi uygulamasıyla ayarlamayı gösterir `IDistributedCache` :

[!code-csharp[](app-state/samples/2.x/SessionSample/Startup.cs?name=snippet1&highlight=5-14,34)]

Ara yazılım sırası önemlidir. Önceki örnekte, `InvalidOperationException` sonrasında çağrıldığında bir özel durum oluşur `UseSession` `UseMvc` . Daha fazla bilgi için bkz. [Ara yazılım sıralaması](xref:fundamentals/middleware/index#order).

<xref:Microsoft.AspNetCore.Http.HttpContext.Session?displayProperty=nameWithType>, oturum durumu yapılandırıldıktan sonra kullanılabilir.

`HttpContext.Session`çağrılmadan önce erişilemez `UseSession` .

Uygulama yanıt akışına yazmaya başladıktan sonra yeni bir oturum ile yeni bir oturum cookie oluşturulamaz. Özel durum Web sunucusu günlüğüne kaydedilir ve tarayıcıda gösterilmez.

### <a name="load-session-state-asynchronously"></a>Oturum durumunu zaman uyumsuz olarak yükle

ASP.NET Core varsayılan oturum sağlayıcısı, oturum kayıtlarını, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> yalnızca <xref:Microsoft.AspNetCore.Http.ISession.LoadAsync%2A?displayProperty=nameWithType> Yöntem, <xref:Microsoft.AspNetCore.Http.ISession.TryGetValue%2A> <xref:Microsoft.AspNetCore.Http.ISession.Set%2A> veya metotlardan önce açıkça çağrılırsa, temel alınan yedekleme deposundan zaman uyumsuz olarak yükler <xref:Microsoft.AspNetCore.Http.ISession.Remove%2A> . `LoadAsync`İlk çağrılmadıysa, temel alınan oturum kaydı zaman uyumlu olarak yüklenir ve bu da ölçekte performans cezası oluşturabilir.

Uygulamaların bu kalıbı zorunlu kılmak için, <xref:Microsoft.AspNetCore.Session.DistributedSessionStore> ve <xref:Microsoft.AspNetCore.Session.DistributedSession> uygulamalarını bir özel durum oluşturan sürümlerle sarın `LoadAsync` ,, veya öncesinde çağrılamaz `TryGetValue` `Set` `Remove` . Sarmalanan sürümleri hizmetler kapsayıcısına kaydedin.

### <a name="session-options"></a>Oturum seçenekleri

Oturum varsayılanlarını geçersiz kılmak için kullanın <xref:Microsoft.AspNetCore.Builder.SessionOptions> .

| Seçenek | Açıklama |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Builder.SessionOptions.Cookie> | Oluşturmak için kullanılan ayarları belirler cookie . <xref:Microsoft.AspNetCore.Http.CookieBuilder.Name>Varsayılan olarak <xref:Microsoft.AspNetCore.Session.SessionDefaults.CookieName?displayProperty=nameWithType> ( `.AspNetCore.Session` ). <xref:Microsoft.AspNetCore.Http.CookieBuilder.Path>Varsayılan olarak <xref:Microsoft.AspNetCore.Session.SessionDefaults.CookiePath?displayProperty=nameWithType> ( `/` ). <xref:Microsoft.AspNetCore.Http.CookieBuilder.SameSite>Varsayılan olarak <xref:Microsoft.AspNetCore.Http.SameSiteMode.Lax?displayProperty=nameWithType> ( `1` ). <xref:Microsoft.AspNetCore.Http.CookieBuilder.HttpOnly>Varsayılan olarak olur `true` . <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential>Varsayılan olarak olur `false` . |
| <xref:Microsoft.AspNetCore.Builder.SessionOptions.IdleTimeout> | , `IdleTimeout` Oturumun içeriği terk edilmeden önce ne kadar süreyle boşta kalabileceğini gösterir. Her oturum erişimi zaman aşımını sıfırlar. Bu ayar, yalnızca oturum içeriği için geçerlidir cookie . Varsayılan değer 20 dakikadır. |
| <xref:Microsoft.AspNetCore.Builder.SessionOptions.IOTimeout> | Mağazadan bir oturumu yüklemesine veya depolama alanına geri kaydetmeye izin verilen en uzun süre. Bu ayar yalnızca zaman uyumsuz işlemlere uygulanabilir. Bu zaman aşımı, kullanılarak devre dışı bırakılabilir <xref:System.Threading.Timeout.InfiniteTimeSpan> . Varsayılan değer 1 dakikadır. |

Oturum cookie , tek bir tarayıcıdan gelen istekleri izlemek ve tanımlamak için bir kullanır. Varsayılan olarak, bu olarak cookie adlandırılır `.AspNetCore.Session` ve bir yolu kullanır `/` . Varsayılan, cookie bir etki alanı belirtmediği için, sayfada istemci tarafı komut dosyası tarafından kullanılamaz ( <xref:Microsoft.AspNetCore.Http.CookieBuilder.HttpOnly> Varsayılan olarak ' dir `true` ).

Oturum varsayılanlarını geçersiz kılmak için cookie şunu kullanın `SessionOptions` :

[!code-csharp[](app-state/samples_snapshot/2.x/SessionSample/Startup.cs?name=snippet1&highlight=14-19)]

Uygulama, <xref:Microsoft.AspNetCore.Builder.SessionOptions.IdleTimeout> bir oturumun, sunucunun önbelleğindeki içeriği terk edilmeden önce ne kadar süreyle boşta kalabileceğini anlamak için özelliğini kullanır. Bu özellik, cookie süre sonundan bağımsızdır. [Oturum ara yazılımı](xref:Microsoft.AspNetCore.Session.SessionMiddleware) üzerinden geçen her istek zaman aşımını sıfırlar.

Oturum durumu *kilitli*değil. İki istek aynı anda bir oturumun içeriğini değiştirmeyi denerseniz, son istek ilk geçersiz kılar. `Session`*tutarlı bir oturum*olarak uygulanır, yani tüm içerikler birlikte depolanır. İki istek farklı oturum değerlerini değiştirmek için arama yaparken, son istek ilk tarafından yapılan oturum değişikliklerini geçersiz kılabilir.

### <a name="set-and-get-session-values"></a>Oturum değerlerini ayarlama ve edinme

Oturum durumuna, Razor ile bir Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> SıNıFıNDAN veya MVC sınıfından erişilir <xref:Microsoft.AspNetCore.Mvc.Controller> <xref:Microsoft.AspNetCore.Http.HttpContext.Session?displayProperty=nameWithType> . Bu özellik bir <xref:Microsoft.AspNetCore.Http.ISession> uygulamasıdır.

`ISession`Uygulama, tamsayı ve dize değerlerini ayarlamak ve almak için birkaç uzantı yöntemi sağlar. Uzantı yöntemleri, <xref:Microsoft.AspNetCore.Http> `using Microsoft.AspNetCore.Http;` Proje tarafından [Microsoft. Aspnetcore. http. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.Http.Extensions/) paketine başvurulduğunda ad alanında (uzantı yöntemlerine erişim kazanmak için bir ifade ekleyin). Her iki paket de [Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app)'e dahildir.

`ISession`Uzantı yöntemleri:

* [Al (ISession, dize)](xref:Microsoft.AspNetCore.Http.SessionExtensions.Get%2A)
* [Getınt32 (ISession, dize)](xref:Microsoft.AspNetCore.Http.SessionExtensions.GetInt32%2A)
* [GetString (ISession, dize)](xref:Microsoft.AspNetCore.Http.SessionExtensions.GetString%2A)
* [Setınt32 (ISession, dize, Int32)](xref:Microsoft.AspNetCore.Http.SessionExtensions.SetInt32%2A)
* [SetString (ISession, dize, dize)](xref:Microsoft.AspNetCore.Http.SessionExtensions.SetString%2A)

Aşağıdaki örnek, `IndexModel.SessionKeyName` `_Name` bir sayfalar sayfasındaki anahtar (örnek uygulamada) için oturum değerini alır Razor :

```csharp
@page
@using Microsoft.AspNetCore.Http
@model IndexModel

...

Name: @HttpContext.Session.GetString(IndexModel.SessionKeyName)
```

Aşağıdaki örnek, bir tamsayı ve bir dizenin nasıl ayarlanacağını ve alınacağını gösterir:

[!code-csharp[](app-state/samples/2.x/SessionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=18-19,22-23)]

Tüm oturum verileri, bellek içi önbellek kullanılırken bile dağıtılmış önbellek senaryosunu etkinleştirmek üzere serileştirilmelidir. Dize ve tamsayı serileştiriciler, ' nin uzantı yöntemleri tarafından sağlanır <xref:Microsoft.AspNetCore.Http.ISession> ). Karmaşık türler JSON gibi başka bir mekanizma kullanılarak Kullanıcı tarafından serileştirilmelidir.

Seri hale getirilebilir nesneleri ayarlamak ve almak için aşağıdaki uzantı yöntemlerini ekleyin:

[!code-csharp[](app-state/samples/2.x/SessionSample/Extensions/SessionExtensions.cs?name=snippet1)]

Aşağıdaki örnek, uzantı yöntemleriyle bir serileştirilebilir nesnenin nasıl ayarlanacağını ve alınacağını gösterir:

[!code-csharp[](app-state/samples/2.x/SessionSample/Pages/Index.cshtml.cs?name=snippet2)]

## <a name="tempdata"></a>TempData

ASP.NET Core, Razor [TempData](xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.TempData) veya Controller sayfalarını kullanıma sunar <xref:Microsoft.AspNetCore.Mvc.Controller.TempData> . Bu özellik, verileri başka bir istekte okunana kadar depolar. [Sakla (dize)](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Keep*) ve [Peek (dize)](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Peek*) yöntemleri, isteğin sonunda silme yapılmadan verileri incelemek için kullanılabilir. [Keep ()](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Keep*) sözlükte tüm öğeleri bekletme için işaretler. `TempData`, tek bir istek için veri gerektiğinde yeniden yönlendirme için özellikle kullanışlıdır. `TempData`sağlayıcılar tarafından ya `TempData` da cookie oturum durumu kullanılarak uygulanır.

## <a name="tempdata-samples"></a>TempData örnekleri

Bir müşteri oluşturan aşağıdaki sayfayı göz önünde bulundurun:

[!code-csharp[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet&highlight=15-16,30)]

Aşağıdaki sayfa görüntülenir `TempData["Message"]` :

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/IndexPeek.cshtml?range=1-14)]

Önceki İşaretlemede, isteğin sonunda, `TempData["Message"]` kullanıldığı için **not** silinmez `Peek` . Sayfayı yenileme sayfası görüntülenir `TempData["Message"]` .

Aşağıdaki biçimlendirme önceki koda benzerdir, ancak `Keep` isteğin sonundaki verileri korumak için kullanır:

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/IndexKeep.cshtml?range=1-14)]

*Indexpeek* ve *ındexkeep* sayfaları arasında gezinmek silinmez `TempData["Message"]` .

Aşağıdaki kod görüntülenir `TempData["Message"]` , ancak isteğin sonunda `TempData["Message"]` silinir:

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/Index.cshtml?range=1-14)]

### <a name="tempdata-providers"></a>TempData sağlayıcıları

cookie-Tabanlı TempData sağlayıcısı, ' deki TempData ' i depolamak için varsayılan olarak kullanılır cookie .

cookieVeriler, <xref:Microsoft.AspNetCore.DataProtection.IDataProtector> ile kodlanmış, <xref:Microsoft.AspNetCore.WebUtilities.Base64UrlTextEncoder> ve ardından öbekli kullanılarak şifrelenir. Çünkü, cookie cookie ASP.NET Core 1. x içinde bulunan tek boyut sınırı uygulanmaz. cookieŞifrelenmiş verilerin sıkıştırılması, [suç](https://wikipedia.org/wiki/CRIME_(security_exploit)) ve [ihlal](https://wikipedia.org/wiki/BREACH_(security_exploit)) saldırıları gibi güvenlik sorunlarına yol açacağından veriler sıkıştırılmaz. Tabanlı TempData sağlayıcısı hakkında daha fazla bilgi için cookie bkz <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.CookieTempDataProvider> ..

### <a name="choose-a-tempdata-provider"></a>Bir TempData sağlayıcısı seçin

Bir TempData sağlayıcısı seçmek şöyle bazı hususlar içerir:

1. Uygulama oturum durumunu zaten kullanıyor mu? Bu durumda, oturum durumu, TempData Provider 'ın kullanılması uygulamaya ek bir ücret vermez (verilerin boyutundan itibaren).
2. Uygulama yalnızca görece küçük miktarlarda veri (500 bayta kadar) için TempData kullanıyor mu? Bu durumda, cookie TempData sağlayıcısı her bir istek Için TempData içeren küçük bir maliyet ekler. Aksi takdirde, oturum durumu TempData Provider, Geçicimiz veri tüketilene kadar her istekte büyük miktarda veri dönüşü olmaması yararlı olabilir.
3. Uygulama, birden çok sunucuda bir sunucu grubunda mi çalışıyor? Bu durumda, cookie veri koruma dışında TempData sağlayıcısını kullanmak için ek yapılandırma gerekmez (bkz <xref:security/data-protection/introduction> . ve [anahtar depolama sağlayıcıları](xref:security/data-protection/implementation/key-storage-providers)).

> [!NOTE]
> Çoğu Web istemcisi (Web tarayıcıları gibi), her birinin en büyük boyutu cookie , toplam sayısı cookie veya her ikisi için sınır uygular. cookieTempData sağlayıcısını kullanırken, uygulamanın bu sınırları aşmadığını doğrulayın. Verilerin toplam boyutunu göz önünde bulundurun. cookieŞifreleme ve öbek oluşturma nedeniyle hesabın boyutu artar.

### <a name="configure-the-tempdata-provider"></a>TempData sağlayıcısını yapılandırma

cookieTabanlı TempData sağlayıcısı varsayılan olarak etkindir.

Oturum tabanlı TempData sağlayıcısını etkinleştirmek için <xref:Microsoft.Extensions.DependencyInjection.MvcViewFeaturesMvcBuilderExtensions.AddSessionStateTempDataProvider%2A> genişletme yöntemini kullanın:

[!code-csharp[](app-state/samples_snapshot_2/2.x/SessionSample/Startup.cs?name=snippet1&highlight=11,13,32)]

Ara yazılım sırası önemlidir. Önceki örnekte, `InvalidOperationException` sonrasında çağrıldığında bir özel durum oluşur `UseSession` `UseMvc` . Daha fazla bilgi için bkz. [Ara yazılım sıralaması](xref:fundamentals/middleware/index#order).

> [!IMPORTANT]
> .NET Framework hedefleme ve oturum tabanlı TempData sağlayıcısını kullanıyorsanız, [Microsoft. AspNetCore. Session](https://www.nuget.org/packages/Microsoft.AspNetCore.Session/) paketini projeye ekleyin.

## <a name="query-strings"></a>Sorgu dizeleri

Yeni isteğin sorgu dizesine eklenerek sınırlı miktarda veri, bir istekten diğerine geçirilebilir. Bu durum, gömülü durum ile bağlantıların e-posta veya sosyal ağlar aracılığıyla paylaşılmasını sağlamak için durumu kalıcı bir şekilde yakalamak için yararlıdır. URL sorgu dizeleri ortak olduğundan, gizli veriler için hiçbir şekilde Sorgu dizelerini kullanmayın.

Sorgu dizelerindeki veriler de dahil olmak üzere, istenmeyen paylaşıma ek olarak, [siteler arası Istek forgery (CSRF)](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) saldırıları için fırsat oluşturabilir ve bu da kullanıcıların kimliği doğrulandığında kötü amaçlı siteleri ziyaret etmesini sağlayabilir. Saldırganlar daha sonra Kullanıcı verilerini uygulamadan çalabilir veya Kullanıcı adına kötü amaçlı eylemler gerçekleştirebilir. Korunan uygulamaların veya oturum durumunun CSRF saldırılarına karşı korunması gerekir. Daha fazla bilgi için bkz. [siteler arası Istek forgery (XSRF/CSRF) saldırılarını önleme](xref:security/anti-request-forgery).

## <a name="hidden-fields"></a>Gizli alanlar

Veriler gizli form alanlarına kaydedilebilir ve sonraki istek üzerine geri gönderilebilir. Bu çok sayfalı formlarda yaygındır. İstemci verilerle oynayabilir olabileceğinden, uygulamanın gizli alanlarda depolanan verileri her zaman yeniden doğrulaması gerekir.

## <a name="httpcontextitems"></a>HttpContext. Items

<xref:Microsoft.AspNetCore.Http.HttpContext.Items?displayProperty=nameWithType>Koleksiyon, tek bir isteği işlerken verileri depolamak için kullanılır. Koleksiyon içeriği bir istek işlendikten sonra atılır. `Items`Koleksiyon genellikle, bir istek sırasında zaman içinde farklı noktalarda çalıştıklarında ve parametreleri geçirmek için doğrudan bir yol olmadığında bileşenlerin veya ara yazılımların iletişim kurmasına izin vermek için kullanılır.

Aşağıdaki örnekte, [Ara yazılım](xref:fundamentals/middleware/index) koleksiyona eklenir `isVerified` `Items` .

```csharp
app.Use(async (context, next) =>
{
    // perform some verification
    context.Items["isVerified"] = true;
    await next.Invoke();
});
```

İşlem hattının ilerleyen kısımlarında, başka bir ara yazılım şu değere erişebilir `isVerified` :

```csharp
app.Run(async (context) =>
{
    await context.Response.WriteAsync($"Verified: {context.Items["isVerified"]}");
});
```

Yalnızca tek bir uygulama tarafından kullanılan ara yazılım için `string` anahtarlar kabul edilebilir. Uygulama örnekleri arasında paylaşılan ara yazılım, anahtar çakışmalarını önlemek için benzersiz nesne anahtarları kullanmalıdır. Aşağıdaki örnek, bir ara yazılım sınıfında tanımlanan benzersiz bir nesne anahtarının nasıl kullanılacağını gösterir:

[!code-csharp[](app-state/samples/2.x/SessionSample/Middleware/HttpContextItemsMiddleware.cs?name=snippet1&highlight=4,13)]

Diğer kod, `HttpContext.Items` Ara yazılım sınıfının açığa çıkarılan anahtarını kullanarak içinde depolanan değere erişebilir:

[!code-csharp[](app-state/samples/2.x/SessionSample/Pages/Index.cshtml.cs?name=snippet3)]

Bu yaklaşım ayrıca koddaki anahtar dizelerinin kullanımını ortadan kaldırma avantajına sahiptir.

## <a name="cache"></a>Önbellek

Önbelleğe alma, verileri depolamak ve almak için etkili bir yoldur. Uygulama, önbelleğe alınmış öğelerin ömrünü denetleyebilir.

Önbelleğe alınan veriler belirli bir istek, Kullanıcı veya oturumla ilişkili değildir. **Diğer kullanıcıların istekleri tarafından alınabilecek kullanıcıya özgü verileri önbelleğe alma konusunda dikkatli olun.**

Daha fazla bilgi için bkz. <xref:performance/caching/response>.

## <a name="dependency-injection"></a>Bağımlılık Ekleme

Verilerin tüm kullanıcılar tarafından kullanılabilmesini sağlamak için [bağımlılık ekleme](xref:fundamentals/dependency-injection) 'yi kullanın:

1. Verileri içeren bir hizmet tanımlayın. Örneğin, adlı bir sınıf `MyAppData` tanımlanmıştır:

    ```csharp
    public class MyAppData
    {
        // Declare properties and methods
    }
    ```

2. Hizmet sınıfını şu şekilde ekleyin `Startup.ConfigureServices` :

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

  Bunun nedeni genellikle en az bir uygulamanın yapılandırması başarısız olmuştur `IDistributedCache` . Daha fazla bilgi için <xref:performance/caching/distributed> ve <xref:performance/caching/memory> bölümlerine bakın.

* Oturum ara yazılımı bir oturumu kalıcı hale getiremediğinde (örneğin, yedekleme deposu kullanılamıyorsa), ara yazılım özel durumu günlüğe kaydeder ve istek normal olarak devam eder. Bu, öngörülemeyen davranışa yol açar.

  Örneğin, bir Kullanıcı bir alışveriş sepetini oturum içinde depolar. Kullanıcı sepete bir öğe ekler, ancak kayıt başarısız olur. Uygulama hata hakkında bilgi sahibi değildir, bu nedenle bu, doğru olmayan, kullanıcıya öğenin sepetine eklendiğini bildirir.

  Hataları denetlemek için önerilen yaklaşım, `await feature.Session.CommitAsync();` uygulama oturuma yazma işlemi tamamlandığında uygulama kodundan çağırmalıdır. `CommitAsync`yedekleme deposu kullanılamıyorsa bir özel durum oluşturur. `CommitAsync`Başarısız olursa, uygulama özel durumu işleyebilir. `LoadAsync`veri deposunun kullanılamadığı koşulların aynısını oluşturur.
  
## <a name="no-locsignalr-and-session-state"></a>SignalRve oturum durumu

SignalRuygulamalar, bilgileri depolamak için oturum durumunu kullanmamalıdır. SignalRuygulamalar, hub 'da bağlantı durumu başına depolama yapabilir `Context.Items` . <!-- https://github.com/aspnet/SignalR/issues/2139 -->

## <a name="additional-resources"></a>Ek kaynaklar

<xref:host-and-deploy/web-farm>
::: moniker-end
