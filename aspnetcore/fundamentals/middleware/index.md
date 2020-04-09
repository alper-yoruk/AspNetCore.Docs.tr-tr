---
title: ASP.NET Çekirdek Middleware
author: rick-anderson
description: ASP.NET Core middleware ve istek ardışık alanı hakkında bilgi edinin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/06/2020
uid: fundamentals/middleware/index
ms.openlocfilehash: 6bf8ed823386ca4e1cf78982f7fba41fba429db8
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80751061"
---
# <a name="aspnet-core-middleware"></a>ASP.NET Çekirdek Middleware

::: moniker range=">= aspnetcore-3.0"

Yazar: [Rick Anderson](https://twitter.com/RickAndMSFT) ve [Steve Smith](https://ardalis.com/)

Middleware, istek ve yanıtları işlemek için bir uygulama ardışık hattında bir araya getirilen yazılımdır. Her bileşen:

* İsteğin ardışık ardışık ardışık alandaki bir sonraki bileşene geçirilip geçirilmeyeceğini seçer.
* Ardışık işlemden önce ve sonra iş yapabilir.

İstek temsilcileri istek ardışık hattını oluşturmak için kullanılır. İstek temsilcileri her HTTP isteğini işler.

İstek temsilcileri , ve <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*> <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*> uzantı yöntemleri kullanılarak yapılandırılır. Tek bir istek temsilcisi anonim bir yöntem olarak satır içi belirtilebilir (satır içi ara yazılım olarak adlandırılır) veya yeniden kullanılabilir bir sınıfta tanımlanabilir. Bu yeniden kullanılabilir sınıflar ve satır içi anonim yöntemler *ara yazılım,* ayrıca *ara yazılım bileşenleri*denir. İstek ardışık ardışık ardışık ardışık her ara yazılım bileşeni, ardışık ardışık lıktaki bir sonraki bileşeni çağırmakveya ardışık hatlar için kısa devre den sorumludur. Bir ara yazılım kısa devre yaptığında, daha fazla ara yazılımın isteği işlemesini engellediği için *buna terminal ara ware* denir.

<xref:migration/http-modules>ASP.NET Core ve ASP.NET 4.x'teki istek ardışık hatları arasındaki farkı açıklar ve ek ara yazılım örnekleri sağlar.

## <a name="create-a-middleware-pipeline-with-iapplicationbuilder"></a>iApplicationBuilder ile bir ara yazılım ardışık hattı oluşturma

ASP.NET Çekirdek istek ardışık alanı, birbiri ardına çağrılan bir dizi istek temsilcisinden oluşur. Aşağıdaki diyagram kavramı göstermektedir. Yürütme iş parçacığı siyah okları izler.

![Bir isteğin gelişini, üç ara yazılım aracılığıyla işlenmesini ve uygulamadan ayrılan yanıtı gösteren işleme deseni isteyin. Her ara yazılım kendi mantığını çalıştırıyor ve isteği sonraki ara() deyimine teslim eder. Üçüncü ara yazılım isteği işledikten sonra, istek, istemciye yanıt olarak uygulamadan ayrılmadan önce bir sonraki () ek bildirimlerinden sonra ek işleme için ters sırayla önceki iki ara yazılımdan geri geçer.](index/_static/request-delegate-pipeline.png)

Her temsilci, bir sonraki temsilciden önce ve sonra işlemleri gerçekleştirebilir. Özel durum işleme temsilcileri, ardışık dizinin sonraki aşamalarında oluşan özel durumları yakalayabilmeleri için ardışık alt idamların erken çağrılması gerekir.

Mümkün olan en basit ASP.NET Core uygulaması, tüm istekleri işleyen tek bir istek temsilcisi ayarlar. Bu servis talebi gerçek bir istek ardışık hattını içermez. Bunun yerine, her HTTP isteğine yanıt olarak tek bir anonim işlev çağrılır.

[!code-csharp[](index/snapshot/Middleware/Startup.cs)]

Birden çok istek temsilcisini ' ile <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>birlikte zincirleme. Parametre, `next` ardışık ardışık ardışık alandaki bir sonraki temsilciyi temsil eder. Bir *sonraki* parametreyi *aramayarak* boru hattını kısa devre yapabilirsiniz. Aşağıdaki örnekte gösterildiği gibi, genellikle bir sonraki temsilciden önce ve sonra eylemler gerçekleştirebilirsiniz:

[!code-csharp[](index/snapshot/Chain/Startup.cs?highlight=5-10)]

Bir temsilci bir sonraki temsilciye bir istek geçirmezse, *istek ardışık hattını kısa devre*olarak adlandırın. Gereksiz çalışmayı önlediği için kısa devre genellikle arzu edilir. Örneğin, [Statik Dosya Middleware](xref:fundamentals/static-files) statik bir dosya için bir istek işleme ve ardışık boru hattının geri kalanı kısa devre bir *terminal ara yazılım* olarak hareket edebilir. Middleware, daha fazla işleme sonlandırın ara yazılım önce `next.Invoke` boru hattı eklendi hala kendi deyimleri sonra kod işler. Ancak, önceden gönderilmiş bir yanıta yazmaya çalışma yla ilgili aşağıdaki uyarıya bakın.

> [!WARNING]
> Yanıt istemciye `next.Invoke` gönderildikten sonra aramayın. Yanıt <xref:Microsoft.AspNetCore.Http.HttpResponse> başladıktan sonra yapılan değişiklikler bir özel durum oluşturur. Örneğin, ayar üstbilgi ve durum kodu gibi değişiklikler bir özel durum atabilir. Aradıktan `next`sonra yanıt gövdesine yazma:
>
> * Protokol ihlaline neden olabilir. Örneğin, belirtilenden `Content-Length`daha fazla yazma .
> * Vücut biçimini bozabilir. Örneğin, bir CSS dosyasına HTML altbilgisi yazma.
>
> <xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted*>üstbilginin gönderilip gönderilmediğini veya gövdenin yazılıp yazılmediğini belirtmek için yararlı bir ipucudur.

<xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>delegeler bir `next` parametre almaz. İlk `Run` temsilci her zaman terminaldir ve ardışık hattı sonlandırır. `Run`bir kongredir. Bazı ara yazılım `Run[Middleware]` bileşenleri, ardışık yolun sonunda çalışan yöntemleri ortaya çıkarabilir:

[!code-csharp[](index/snapshot/Chain/Startup.cs?highlight=12-15)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

Önceki örnekte, `Run` temsilci yanıta yazar `"Hello from 2nd delegate."` ve ardışık ardışık alanı sonlandırır. Temsilciden `Use` `Run` sonra başka bir `Run` temsilci eklenirse, çağrılmez.

<a name="order"></a>

## <a name="middleware-order"></a>Ara yazılım sırası

Aşağıdaki diyagram, ASP.NET Core MVC ve Razor Pages uygulamaları için tam istek işleme ardışık hattını gösterir. Tipik bir uygulamada, varolan ara yazılımların nasıl sipariş edildiğini ve özel ara yazılımların nerede eklendiği görebilirsiniz. Senaryolarınız için gerektiğinde varolan ara yazılımları yeniden sıralama veya yeni özel ara yazılımlar enjekte etme konusunda tam denetime sahipsiniz.

![ASP.NET Core middleware boru hattı](index/_static/middleware-pipeline.svg)

Önceki diyagramdaki **Endpoint** ara yazılımı, ilgili uygulama türü&mdash;MVC veya Jilet Sayfaları için filtre ardışık noktasını yürütür.

![ASP.NET Çekirdek filtre boru hattı](index/_static/mvc-endpoint.svg)

Yönteme ara yazılım bileşenlerinin `Startup.Configure` eklenmesi sırası, ara yazılım bileşenlerinin isteklerde çağrılma sırasını ve yanıt için ters sırayı tanımlar. Sipariş, güvenlik, performans ve işlevsellik açısından **çok önemlidir.**

Aşağıdaki `Startup.Configure` yöntem, önerilen sırada güvenlikle ilgili ara yazılım bileşenlerini ekler:

[!code-csharp[](index/snapshot/StartupAll3.cs?name=snippet)]

Yukarıdaki kodda:

* [Bireysel kullanıcı hesapları](xref:security/authentication/identity) ile yeni bir web uygulaması oluştururken eklenmez Middleware yorumlanır.
* Her ara yazılım bu tam sırada gitmek gerekiyor, ama birçok yok. Örneğin, `UseCors`, `UseAuthentication`, `UseAuthorization` ve gösterilen sırada gitmeli.

Aşağıdaki `Startup.Configure` yöntem, yaygın uygulama senaryoları için ara yazılım bileşenleri ekler:

1. Özel durum/hata işleme
   * Uygulama Geliştirme ortamında çalıştığında:
     * Geliştirici Özel Durum<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*>Sayfası Middleware ( ) uygulama çalışma zamanı hatalarını bildirir.
     * Veritabanı Hata Sayfası Middleware veritabanı çalışma zamanı hatalarını bildirir.
   * Uygulama Üretim ortamında çalıştığında:
     * Özel Durum Handler<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>Middleware ( ) aşağıdaki ara yazılımlarda atılan özel durumları yakalar.
     * HTTP Sıkı Aktarım Güvenlik Protokolü (HSTS) Middleware (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*>) üstbilgi ekler. `Strict-Transport-Security`
1. HTTPS Yönlendirme Middleware<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>( ) HTTP isteklerini HTTPS'ye yönlendirir.
1. Statik Dosya Middleware (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>) statik dosyaları ve kısa devreleri daha fazla istek işleme döndürür.
1. Çerez Politikası Aracı<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*>( ) uygulamayı AB Genel Veri Koruma Yönetmeliği (GDPR) yönetmeliklerine uygun olarak düzenlemeye uygun olarak düzenlemeyi sağlar.
1. Yönlendirme Middleware (`UseRouting`) istekleri yönlendirmek için.
1. Kimlik Doğrulama Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>) güvenli kaynaklara erişim izni verilmeden önce kullanıcının kimliğini doğrulamaya çalışır.
1. Yetkilendirme Middleware`UseAuthorization`( ) güvenli kaynaklara erişmek için bir kullanıcı yetkilendirır.
1. Session Middleware<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession*>( ) oturum durumunu kurar ve korur. Uygulama oturum durumunu kullanıyorsa, Çerez İlkesi Middleware'den sonra ve MVC Middleware'den önce Session Middleware'i arayın.
1. İstek ardışık hattına`UseEndpoints` Razor `MapRazorPages`Pages uç noktaları eklemek için Endpoint Yönlendirme Middleware (ile)

<!--

FUTURE UPDATE

On the next topic overhaul/release update, add API crosslink to "Database Error Page Middleware" in Item 1 of the list ...

Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage*

... when available via the API docs.

-->

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
        app.UseDatabaseErrorPage();
    }
    else
    {
        app.UseExceptionHandler("/Error");
        app.UseHsts();
    }

    app.UseHttpsRedirection();
    app.UseStaticFiles();
    app.UseCookiePolicy();
    app.UseRouting();
    app.UseAuthentication();
    app.UseAuthorization();
    app.UseSession();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapRazorPages();
    });
}
```

Önceki örnek kodda, her ara yazılım uzantısı <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> yöntemi <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> ad alanı üzerinden açıklanır.

<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>boru hattına eklenen ilk ara yazılım bileşenidir. Bu nedenle, Özel Durum Işleyicisi Middleware sonraki aramalarda oluşan özel durumları yakalar.

Statik Dosya Middleware, kalan bileşenlerden geçmeden istekleri ve kısa devreyi işleyebilmek için ardışık ardışık alanda erken çağrılır. Statik Dosya Middleware **hiçbir** yetkilendirme denetimi sağlar. Static File Middleware tarafından sunulan, *wwwroot*altındakiler de dahil olmak üzere tüm dosyalar herkese açıktır. Statik dosyaları güvenli bir yaklaşım <xref:fundamentals/static-files>için bkz.

İstek Statik Dosya Middleware tarafından işlenmezse, kimlik doğrulaması gerçekleştiren Kimlik<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>Doğrulama Middleware'e () aktarılır. Kimlik doğrulama, kimlik doğrulamasız istekleri kısa devre yapmaz. Kimlik Doğrulama Middleware istekleri doğrulamasına rağmen, yetkilendirme (ve reddetme) yalnızca MVC belirli bir Razor Page veya MVC denetleyicisi ve eylemi seçtikten sonra gerçekleşir.

Aşağıdaki örnek, yanıt sıkıştırma ara yazılımönce statik dosya middleware tarafından statik dosya istekleri işlenir bir ara yazılım sırası gösterir. Statik dosyalar bu ara yazılım sırası ile sıkıştırılmış değildir. Razor Pages yanıtları sıkıştırılabilir.

```csharp
public void Configure(IApplicationBuilder app)
{
    // Static files aren't compressed by Static File Middleware.
    app.UseStaticFiles();

    app.UseResponseCompression();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapRazorPages();
    });
}
```

Tek Sayfa uygulamaları (SPA'lar) için, SPA ara yazılımı genellikle ara yazılım <xref:Microsoft.Extensions.DependencyInjection.SpaStaticFilesExtensions.UseSpaStaticFiles*> boru hattında sonuncu gelir. SPA ara ware son gelir:

* Diğer tüm ara yazılımların önce eşleşen isteklere yanıt vermesine izin vermek için.
* İstemci tarafı yönlendirmeli SCA'ların sunucu uygulaması tarafından tanınmayan tüm rotalar için çalışmasına izin vermek için.

SP'ler hakkında daha fazla bilgi için [React](xref:spa/react) ve [Açısal](xref:spa/angular) proje şablonları kılavuzlarına bakın.

## <a name="branch-the-middleware-pipeline"></a>Ara yazılım boru hattını dalla

<xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*>uzantıları boru hattı dallanma için bir kural olarak kullanılır. `Map`verilen istek yolunun eşleşmelerine göre istek ardışık hattını dallar. İstek yolu verilen yol ile başlarsa, dal yürütülür.

[!code-csharp[](index/snapshot/Chain/StartupMap.cs)]

Aşağıdaki tablo, önceki kodu `http://localhost:1234` kullanarak gelen istek ve yanıtları gösterir.

| İstek             | Yanıt                     |
| ------------------- | ---------------------------- |
| localhost:1234      | Harita olmayan temsilciden merhaba. |
| localhost:1234/map1 | Harita Testi 1                   |
| localhost:1234/map2 | Harita Testi 2                   |
| localhost:1234/map3 | Harita olmayan temsilciden merhaba. |

Kullanıldığında, `Map` eşleşen yol bölümleri kaldırılır `HttpRequest.Path` ve her istek `HttpRequest.PathBase` için eklenir.

`Map`iç içe geçmeyi destekler, örneğin:

```csharp
app.Map("/level1", level1App => {
    level1App.Map("/level2a", level2AApp => {
        // "/level1/level2a" processing
    });
    level1App.Map("/level2b", level2BApp => {
        // "/level1/level2b" processing
    });
});
```

`Map`aynı anda birden çok segmenti de eşleşebilir:

[!code-csharp[](index/snapshot/Chain/StartupMultiSeg.cs?highlight=13)]

<xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*>verilen yüklemin sonucuna göre istek boru hattını dallar. Herhangi bir tür `Func<HttpContext, bool>` yüklemi, istekleri boru hattının yeni bir dalıile eşlemek için kullanılabilir. Aşağıdaki örnekte, bir sorgu dize değişkeninin `branch`varlığını algılamak için bir yüklem kullanılır:

[!code-csharp[](index/snapshot/Chain/StartupMapWhen.cs?highlight=14-15)]

Aşağıdaki tablo, önceki kodu `http://localhost:1234` kullanarak gelen istek ve yanıtları gösterir:

| İstek                       | Yanıt                     |
| ----------------------------- | ---------------------------- |
| localhost:1234                | Harita olmayan temsilciden merhaba. |
| localhost:1234/?branch=master | Kullanılan dal = ana         |

<xref:Microsoft.AspNetCore.Builder.UseWhenExtensions.UseWhen*>ayrıca verilen yüklemin sonucuna göre istek boru hattını dallar. Aksine, `MapWhen`bu şube kısa devre yoksa veya bir terminal ara yazılım içermiyorsa ana boru hattına yeniden birleştirilir:

[!code-csharp[](index/snapshot/Chain/StartupUseWhen.cs?highlight=25-26)]

Önceki örnekte, "Ana boru hattından Merhaba" yanıtı. tüm istekler için yazılır. İstek bir sorgu dize `branch`değişkeni içeriyorsa, değeri ana ardışık alan yeniden birleştirilmeden önce günlüğe kaydedilir.

## <a name="built-in-middleware"></a>Dahili ara yazılım

ASP.NET Aşağıdaki ara yazılım bileşenlerine sahip Core gemileri. *Sipariş* sütunu, istek işleme ardışık düzeninde ara yazılım yerleşimi hakkında notlar sağlar ve ara yazılım istek işlemeyi hangi koşullar altında sonlandırabilir. Bir ara yazılım istek işleme ardışık hattını kısa devre yaptığında ve daha fazla downstream ara yazılımının bir isteği işlemesini engellediğinde, buna *terminal ara yazılımı*denir. Kısa devre hakkında daha fazla bilgi [için, IApplicationBuilder bölümüne sahip bir ara yazılım ardışık hattı oluştur](#create-a-middleware-pipeline-with-iapplicationbuilder) bölümüne bakın.

| Ara yazılım | Açıklama | Sipariş verme |
| ---------- | ----------- | ----- |
| [Kimlik Doğrulaması](xref:security/authentication/identity) | Kimlik doğrulama desteği sağlar. | Önce `HttpContext.User` gerekli. OAuth geri aramaları için terminal. |
| [Yetkilendirme](xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization*) | Yetkilendirme desteği sağlar. | Kimlik Doğrulama Middleware hemen sonra. |
| [Çerez Politikası](xref:security/gdpr) | Kişisel bilgileri depolamak için kullanıcıların onaylarını izler ve çerez `secure` alanları `SameSite`için minimum standartları uygular. | Çerezleri veren ara yazılımdan önce. Örnekler: Kimlik doğrulama, oturum, MVC (TempData). |
| [CORS](xref:security/cors) | Çapraz Kaynak Paylaşımını yapılandırır. | CORS kullanan bileşenlerden önce. |
| [Tanılama](xref:fundamentals/error-handling) | Geliştirici özel durum sayfası, özel durum işleme, durum kodu sayfaları ve yeni uygulamalar için varsayılan web sayfası sağlayan birkaç ayrı ara yazılım. | Hata üreten bileşenlerden önce. Özel durumlar için terminal veya yeni uygulamalar için varsayılan web sayfasına hizmet. |
| [İlezli Üstbilgi](xref:host-and-deploy/proxy-load-balancer) | İleriye dönük üstbilgi geçerli isteğe bağlı. | Güncelleştirilmiş alanları tüketen bileşenlerden önce. Örnekler: şema, ana bilgisayar, istemci IP, yöntem. |
| [Sağlık Kontrolü](xref:host-and-deploy/health-checks) | Bir ASP.NET Core uygulamasının durumunu ve veritabanı kullanılabilirliğini denetleme gibi bağımlılıklarını denetler. | Bir istek, sistem durumu kontrol bitiş noktasıyla eşleşiyorsa terminal. |
| [Üstbilgi Yayılımı](xref:fundamentals/http-requests#header-propagation-middleware) | Gelen istekten giden HTTP İstemci isteklerine HTTP üstbilgilerini yayılar. |
| [HTTP Yöntemi Geçersiz Kılma](xref:Microsoft.AspNetCore.Builder.HttpMethodOverrideExtensions) | Gelen bir POST isteğinin yöntemi geçersiz kılmasına izin verir. | Güncelleştirilmiş yöntemi tüketen bileşenlerden önce. |
| [HTTPS Yeniden Yönlendirme](xref:security/enforcing-ssl#require-https) | Tüm HTTP isteklerini HTTPS'ye yönlendirin. | URL'yi tüketen bileşenlerden önce. |
| [HTTP Sıkı Ulaşım Güvenliği (HSTS)](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts) | Özel yanıt üstbilgisi ekleyen güvenlik geliştirme ara ware. | Yanıtlar gönderilmeden önce ve istekleri değiştiren bileşenlerden sonra. Örnekler: İlezli Üstbilgi, URL Yeniden Yazma. |
| [MVC](xref:mvc/overview) | Talepleri MVC/Razor Pages ile işler. | Bir istek bir rotayla eşleşiyorsa terminal. |
| [OWIN](xref:fundamentals/owin) | OWIN tabanlı uygulamalar, sunucular ve ara yazılımlarla interop. | OWIN Middleware isteği tam olarak işlerse terminal. |
| [Yanıt Önbelleğe Alma](xref:performance/caching/middleware) | Yanıtları önbelleğe alma desteği sağlar. | Önbelleğe alma gerektiren bileşenlerden önce. |
| [Yanıt Sıkıştırma](xref:performance/response-compression) | Yanıtları sıkıştırmak için destek sağlar. | Sıkıştırma gerektiren bileşenlerden önce. |
| [Yerelleştirme İste](xref:fundamentals/localization) | Yerelleştirme desteği sağlar. | Yerelleştirme den önce hassas bileşenler. |
| [Uç Nokta Yönlendirme](xref:fundamentals/routing) | İstek yollarını tanımlar ve kısıtlar. | Eşleşen rotalar için terminal. |
| [Spa](xref:Microsoft.AspNetCore.Builder.SpaApplicationBuilderExtensions.UseSpa*) | Tek Sayfa Uygulaması (SPA) için varsayılan sayfayı döndürerek ara yazılım zincirindeki bu noktadan gelen tüm istekleri işler | Geç zincir, böylece statik dosyaları, MVC eylemleri, vb hizmet için diğer ara, önceliklidir.|
| [Oturum](xref:fundamentals/app-state) | Kullanıcı oturumlarını yönetmek için destek sağlar. | Oturum gerektiren bileşenlerden önce. | 
| [Statik Dosyalar](xref:fundamentals/static-files) | Statik dosyalara ve dizin tarama hizmet için destek sağlar. | Bir istek bir dosyayla eşleşiyorsa terminal. |
| [URL Yeniden Yazma](xref:fundamentals/url-rewriting) | URL'leri yeniden yazmak ve istekleri yeniden yönlendirmek için destek sağlar. | URL'yi tüketen bileşenlerden önce. |
| [WebSockets](xref:fundamentals/websockets) | WebSockets iletişim kuralını etkinleştirir. | WebSocket isteklerini kabul etmesi gereken bileşenlerden önce. |

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:fundamentals/middleware/write>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Yazar: [Rick Anderson](https://twitter.com/RickAndMSFT) ve [Steve Smith](https://ardalis.com/)

Middleware, istek ve yanıtları işlemek için bir uygulama ardışık hattında bir araya getirilen yazılımdır. Her bileşen:

* İsteğin ardışık ardışık ardışık alandaki bir sonraki bileşene geçirilip geçirilmeyeceğini seçer.
* Ardışık işlemden önce ve sonra iş yapabilir.

İstek temsilcileri istek ardışık hattını oluşturmak için kullanılır. İstek temsilcileri her HTTP isteğini işler.

İstek temsilcileri , ve <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*> <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*> uzantı yöntemleri kullanılarak yapılandırılır. Tek bir istek temsilcisi anonim bir yöntem olarak satır içi belirtilebilir (satır içi ara yazılım olarak adlandırılır) veya yeniden kullanılabilir bir sınıfta tanımlanabilir. Bu yeniden kullanılabilir sınıflar ve satır içi anonim yöntemler *ara yazılım,* ayrıca *ara yazılım bileşenleri*denir. İstek ardışık ardışık ardışık ardışık her ara yazılım bileşeni, ardışık ardışık lıktaki bir sonraki bileşeni çağırmakveya ardışık hatlar için kısa devre den sorumludur. Bir ara yazılım kısa devre yaptığında, daha fazla ara yazılımın isteği işlemesini engellediği için *buna terminal ara ware* denir.

<xref:migration/http-modules>ASP.NET Core ve ASP.NET 4.x'teki istek ardışık hatları arasındaki farkı açıklar ve ek ara yazılım örnekleri sağlar.

## <a name="create-a-middleware-pipeline-with-iapplicationbuilder"></a>iApplicationBuilder ile bir ara yazılım ardışık hattı oluşturma

ASP.NET Çekirdek istek ardışık alanı, birbiri ardına çağrılan bir dizi istek temsilcisinden oluşur. Aşağıdaki diyagram kavramı göstermektedir. Yürütme iş parçacığı siyah okları izler.

![Bir isteğin gelişini, üç ara yazılım aracılığıyla işlenmesini ve uygulamadan ayrılan yanıtı gösteren işleme deseni isteyin. Her ara yazılım kendi mantığını çalıştırıyor ve isteği sonraki ara() deyimine teslim eder. Üçüncü ara yazılım isteği işledikten sonra, istek, istemciye yanıt olarak uygulamadan ayrılmadan önce bir sonraki () ek bildirimlerinden sonra ek işleme için ters sırayla önceki iki ara yazılımdan geri geçer.](index/_static/request-delegate-pipeline.png)

Her temsilci, bir sonraki temsilciden önce ve sonra işlemleri gerçekleştirebilir. Özel durum işleme temsilcileri, ardışık dizinin sonraki aşamalarında oluşan özel durumları yakalayabilmeleri için ardışık alt idamların erken çağrılması gerekir.

Mümkün olan en basit ASP.NET Core uygulaması, tüm istekleri işleyen tek bir istek temsilcisi ayarlar. Bu servis talebi gerçek bir istek ardışık hattını içermez. Bunun yerine, her HTTP isteğine yanıt olarak tek bir anonim işlev çağrılır.

[!code-csharp[](index/snapshot/Middleware/Startup.cs)]

İlk <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*> temsilci ardışık alanı sonlandırır.

Birden çok istek temsilcisini ' ile <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>birlikte zincirleme. Parametre, `next` ardışık ardışık ardışık alandaki bir sonraki temsilciyi temsil eder. Bir *sonraki* parametreyi *aramayarak* boru hattını kısa devre yapabilirsiniz. Aşağıdaki örnekte gösterildiği gibi, genellikle bir sonraki temsilciden önce ve sonra eylemler gerçekleştirebilirsiniz:

[!code-csharp[](index/snapshot/Chain/Startup.cs)]

Bir temsilci bir sonraki temsilciye bir istek geçirmezse, *istek ardışık hattını kısa devre*olarak adlandırın. Gereksiz çalışmayı önlediği için kısa devre genellikle arzu edilir. Örneğin, [Statik Dosya Middleware](xref:fundamentals/static-files) statik bir dosya için bir istek işleme ve ardışık boru hattının geri kalanı kısa devre bir *terminal ara yazılım* olarak hareket edebilir. Middleware, daha fazla işleme sonlandırın ara yazılım önce `next.Invoke` boru hattı eklendi hala kendi deyimleri sonra kod işler. Ancak, önceden gönderilmiş bir yanıta yazmaya çalışma yla ilgili aşağıdaki uyarıya bakın.

> [!WARNING]
> Yanıt istemciye `next.Invoke` gönderildikten sonra aramayın. Yanıt <xref:Microsoft.AspNetCore.Http.HttpResponse> başladıktan sonra yapılan değişiklikler bir özel durum oluşturur. Örneğin, ayar üstbilgi ve durum kodu gibi değişiklikler bir özel durum atabilir. Aradıktan `next`sonra yanıt gövdesine yazma:
>
> * Protokol ihlaline neden olabilir. Örneğin, belirtilenden `Content-Length`daha fazla yazma .
> * Vücut biçimini bozabilir. Örneğin, bir CSS dosyasına HTML altbilgisi yazma.
>
> <xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted*>üstbilginin gönderilip gönderilmediğini veya gövdenin yazılıp yazılmediğini belirtmek için yararlı bir ipucudur.

<a name="order"></a>

## <a name="middleware-order"></a>Ara yazılım sırası

Yönteme ara yazılım bileşenlerinin `Startup.Configure` eklenmesi sırası, ara yazılım bileşenlerinin isteklerde çağrılma sırasını ve yanıt için ters sırayı tanımlar. Sipariş, güvenlik, performans ve işlevsellik açısından **çok önemlidir.**

Aşağıdaki `Startup.Configure` yöntem, önerilen sırada güvenlikle ilgili ara yazılım bileşenlerini ekler:

[!code-csharp[](index/snapshot/Startup22.cs?name=snippet)]

Yukarıdaki kodda:

* [Bireysel kullanıcı hesapları](xref:security/authentication/identity) ile yeni bir web uygulaması oluştururken eklenmez Middleware yorumlanır.
* Her ara yazılım bu tam sırada gitmek gerekiyor, ama birçok yok. Örneğin, `UseCors` ve `UseAuthentication` gösterilen sırada gitmeli.

Aşağıdaki `Startup.Configure` yöntem, yaygın uygulama senaryoları için ara yazılım bileşenleri ekler:

1. Özel durum/hata işleme
   * Uygulama Geliştirme ortamında çalıştığında:
     * Geliştirici Özel Durum<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*>Sayfası Middleware ( ) uygulama çalışma zamanı hatalarını bildirir.
     * Veritabanı Hata Sayfası`Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage`Middleware ( ) veritabanı çalışma zamanı hatalarını bildirir.
   * Uygulama Üretim ortamında çalıştığında:
     * Özel Durum Handler<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>Middleware ( ) aşağıdaki ara yazılımlarda atılan özel durumları yakalar.
     * HTTP Sıkı Aktarım Güvenlik Protokolü (HSTS) Middleware (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*>) üstbilgi ekler. `Strict-Transport-Security`
1. HTTPS Yönlendirme Middleware<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>( ) HTTP isteklerini HTTPS'ye yönlendirir.
1. Statik Dosya Middleware (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>) statik dosyaları ve kısa devreleri daha fazla istek işleme döndürür.
1. Çerez Politikası Aracı<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*>( ) uygulamayı AB Genel Veri Koruma Yönetmeliği (GDPR) yönetmeliklerine uygun olarak düzenlemeye uygun olarak düzenlemeyi sağlar.
1. Kimlik Doğrulama Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>) güvenli kaynaklara erişim izni verilmeden önce kullanıcının kimliğini doğrulamaya çalışır.
1. Session Middleware<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession*>( ) oturum durumunu kurar ve korur. Uygulama oturum durumunu kullanıyorsa, Çerez İlkesi Middleware'den sonra ve MVC Middleware'den önce Session Middleware'i arayın.
1. MVC<xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*>( ) istek ardışık hattına MVC eklemek için.

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
        app.UseDatabaseErrorPage();
    }
    else
    {
        app.UseExceptionHandler("/Error");
        app.UseHsts();
    }

    app.UseHttpsRedirection();
    app.UseStaticFiles();
    app.UseCookiePolicy();
    app.UseAuthentication();
    app.UseSession();
    app.UseMvc();
}
```

Önceki örnek kodda, her ara yazılım uzantısı <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> yöntemi <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> ad alanı üzerinden açıklanır.

<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>boru hattına eklenen ilk ara yazılım bileşenidir. Bu nedenle, Özel Durum Işleyicisi Middleware sonraki aramalarda oluşan özel durumları yakalar.

Statik Dosya Middleware, kalan bileşenlerden geçmeden istekleri ve kısa devreyi işleyebilmek için ardışık ardışık alanda erken çağrılır. Statik Dosya Middleware **hiçbir** yetkilendirme denetimi sağlar. Static File Middleware tarafından sunulan, *wwwroot*altındakiler de dahil olmak üzere tüm dosyalar herkese açıktır. Statik dosyaları güvenli bir yaklaşım <xref:fundamentals/static-files>için bkz.

İstek Statik Dosya Middleware tarafından işlenmezse, kimlik doğrulaması gerçekleştiren Kimlik<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>Doğrulama Middleware'e () aktarılır. Kimlik doğrulama, kimlik doğrulamasız istekleri kısa devre yapmaz. Kimlik Doğrulama Middleware istekleri doğrulamasına rağmen, yetkilendirme (ve reddetme) yalnızca MVC belirli bir Razor Page veya MVC denetleyicisi ve eylemi seçtikten sonra gerçekleşir.

Aşağıdaki örnek, yanıt sıkıştırma ara yazılımönce statik dosya middleware tarafından statik dosya istekleri işlenir bir ara yazılım sırası gösterir. Statik dosyalar bu ara yazılım sırası ile sıkıştırılmış değildir. MVC <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*> yanıtları sıkıştırılabilir.

```csharp
public void Configure(IApplicationBuilder app)
{
    // Static files aren't compressed by Static File Middleware.
    app.UseStaticFiles();

    app.UseResponseCompression();

    app.UseMvcWithDefaultRoute();
}
```

## <a name="use-run-and-map"></a>Kullanım, Çalıştırma ve Harita

HTTP ardışık hattını <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*> <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>, <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*>ve . Yöntem, `Use` ardışık hatlar kısa devre olabilir (yani, `next` bir istek temsilcisi çağırmazsa). `Run`bir kuraldır ve bazı ara `Run[Middleware]` yazılım bileşenleri ardışık yolun sonunda çalışan yöntemleri ortaya çıkarabilir.

<xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*>uzantıları boru hattı dallanma için bir kural olarak kullanılır. `Map`verilen istek yolunun eşleşmelerine göre istek ardışık hattını dallar. İstek yolu verilen yol ile başlarsa, dal yürütülür.

[!code-csharp[](index/snapshot/Chain/StartupMap.cs)]

Aşağıdaki tablo, önceki kodu `http://localhost:1234` kullanarak gelen istek ve yanıtları gösterir.

| İstek             | Yanıt                     |
| ------------------- | ---------------------------- |
| localhost:1234      | Harita olmayan temsilciden merhaba. |
| localhost:1234/map1 | Harita Testi 1                   |
| localhost:1234/map2 | Harita Testi 2                   |
| localhost:1234/map3 | Harita olmayan temsilciden merhaba. |

Kullanıldığında, `Map` eşleşen yol bölümleri kaldırılır `HttpRequest.Path` ve her istek `HttpRequest.PathBase` için eklenir.

<xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*>verilen yüklemin sonucuna göre istek boru hattını dallar. Herhangi bir tür `Func<HttpContext, bool>` yüklemi, istekleri boru hattının yeni bir dalıile eşlemek için kullanılabilir. Aşağıdaki örnekte, bir sorgu dize değişkeninin `branch`varlığını algılamak için bir yüklem kullanılır:

[!code-csharp[](index/snapshot/Chain/StartupMapWhen.cs)]

Aşağıdaki tablo, önceki kodu `http://localhost:1234` kullanarak gelen istek ve yanıtları gösterir.

| İstek                       | Yanıt                     |
| ----------------------------- | ---------------------------- |
| localhost:1234                | Harita olmayan temsilciden merhaba. |
| localhost:1234/?branch=master | Kullanılan dal = ana         |

`Map`iç içe geçmeyi destekler, örneğin:

```csharp
app.Map("/level1", level1App => {
    level1App.Map("/level2a", level2AApp => {
        // "/level1/level2a" processing
    });
    level1App.Map("/level2b", level2BApp => {
        // "/level1/level2b" processing
    });
});
```

`Map`aynı anda birden çok segmenti de eşleşebilir:

[!code-csharp[](index/snapshot/Chain/StartupMultiSeg.cs?highlight=13)]

## <a name="built-in-middleware"></a>Dahili ara yazılım

ASP.NET Aşağıdaki ara yazılım bileşenlerine sahip Core gemileri. *Sipariş* sütunu, istek işleme ardışık düzeninde ara yazılım yerleşimi hakkında notlar sağlar ve ara yazılım istek işlemeyi hangi koşullar altında sonlandırabilir. Bir ara yazılım istek işleme ardışık hattını kısa devre yaptığında ve daha fazla downstream ara yazılımının bir isteği işlemesini engellediğinde, buna *terminal ara yazılımı*denir. Kısa devre hakkında daha fazla bilgi [için, IApplicationBuilder bölümüne sahip bir ara yazılım ardışık hattı oluştur](#create-a-middleware-pipeline-with-iapplicationbuilder) bölümüne bakın.

| Ara yazılım | Açıklama | Sipariş verme |
| ---------- | ----------- | ----- |
| [Kimlik Doğrulaması](xref:security/authentication/identity) | Kimlik doğrulama desteği sağlar. | Önce `HttpContext.User` gerekli. OAuth geri aramaları için terminal. |
| [Çerez Politikası](xref:security/gdpr) | Kişisel bilgileri depolamak için kullanıcıların onaylarını izler ve çerez `secure` alanları `SameSite`için minimum standartları uygular. | Çerezleri veren ara yazılımdan önce. Örnekler: Kimlik doğrulama, oturum, MVC (TempData). |
| [CORS](xref:security/cors) | Çapraz Kaynak Paylaşımını yapılandırır. | CORS kullanan bileşenlerden önce. |
| [Tanılama](xref:fundamentals/error-handling) | Geliştirici özel durum sayfası, özel durum işleme, durum kodu sayfaları ve yeni uygulamalar için varsayılan web sayfası sağlayan birkaç ayrı ara yazılım. | Hata üreten bileşenlerden önce. Özel durumlar için terminal veya yeni uygulamalar için varsayılan web sayfasına hizmet. |
| [İlezli Üstbilgi](xref:host-and-deploy/proxy-load-balancer) | İleriye dönük üstbilgi geçerli isteğe bağlı. | Güncelleştirilmiş alanları tüketen bileşenlerden önce. Örnekler: şema, ana bilgisayar, istemci IP, yöntem. |
| [Sağlık Kontrolü](xref:host-and-deploy/health-checks) | Bir ASP.NET Core uygulamasının durumunu ve veritabanı kullanılabilirliğini denetleme gibi bağımlılıklarını denetler. | Bir istek, sistem durumu kontrol bitiş noktasıyla eşleşiyorsa terminal. |
| [HTTP Yöntemi Geçersiz Kılma](xref:Microsoft.AspNetCore.Builder.HttpMethodOverrideExtensions) | Gelen bir POST isteğinin yöntemi geçersiz kılmasına izin verir. | Güncelleştirilmiş yöntemi tüketen bileşenlerden önce. |
| [HTTPS Yeniden Yönlendirme](xref:security/enforcing-ssl#require-https) | Tüm HTTP isteklerini HTTPS'ye yönlendirin. | URL'yi tüketen bileşenlerden önce. |
| [HTTP Sıkı Ulaşım Güvenliği (HSTS)](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts) | Özel yanıt üstbilgisi ekleyen güvenlik geliştirme ara ware. | Yanıtlar gönderilmeden önce ve istekleri değiştiren bileşenlerden sonra. Örnekler: İlezli Üstbilgi, URL Yeniden Yazma. |
| [MVC](xref:mvc/overview) | Talepleri MVC/Razor Pages ile işler. | Bir istek bir rotayla eşleşiyorsa terminal. |
| [OWIN](xref:fundamentals/owin) | OWIN tabanlı uygulamalar, sunucular ve ara yazılımlarla interop. | OWIN Middleware isteği tam olarak işlerse terminal. |
| [Yanıt Önbelleğe Alma](xref:performance/caching/middleware) | Yanıtları önbelleğe alma desteği sağlar. | Önbelleğe alma gerektiren bileşenlerden önce. |
| [Yanıt Sıkıştırma](xref:performance/response-compression) | Yanıtları sıkıştırmak için destek sağlar. | Sıkıştırma gerektiren bileşenlerden önce. |
| [Yerelleştirme İste](xref:fundamentals/localization) | Yerelleştirme desteği sağlar. | Yerelleştirme den önce hassas bileşenler. |
| [Uç Nokta Yönlendirme](xref:fundamentals/routing) | İstek yollarını tanımlar ve kısıtlar. | Eşleşen rotalar için terminal. |
| [Oturum](xref:fundamentals/app-state) | Kullanıcı oturumlarını yönetmek için destek sağlar. | Oturum gerektiren bileşenlerden önce. |
| [Statik Dosyalar](xref:fundamentals/static-files) | Statik dosyalara ve dizin tarama hizmet için destek sağlar. | Bir istek bir dosyayla eşleşiyorsa terminal. |
| [URL Yeniden Yazma](xref:fundamentals/url-rewriting) | URL'leri yeniden yazmak ve istekleri yeniden yönlendirmek için destek sağlar. | URL'yi tüketen bileşenlerden önce. |
| [WebSockets](xref:fundamentals/websockets) | WebSockets iletişim kuralını etkinleştirir. | WebSocket isteklerini kabul etmesi gereken bileşenlerden önce. |

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:fundamentals/middleware/write>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>

::: moniker-end
