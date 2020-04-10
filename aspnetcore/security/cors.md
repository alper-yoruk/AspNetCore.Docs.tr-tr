---
title: ASP.NET Çekirdekte Orijinler Arası İstekleri (CORS) etkinleştirme
author: rick-anderson
description: ASP.NET Core uygulamasında, KORS'un çapraz orijin isteklerine izin vermek veya reddetmek için nasıl bir standart olduğunu öğrenin.
ms.author: riande
ms.custom: mvc
ms.date: 01/23/2020
uid: security/cors
ms.openlocfilehash: 601e26e1990a86ad60aa50c8c93ffa490ff6b708
ms.sourcegitcommit: e72a58d6ebde8604badd254daae8077628f9d63e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/10/2020
ms.locfileid: "81007190"
---
# <a name="enable-cross-origin-requests-cors-in-aspnet-core"></a>ASP.NET Çekirdekte Orijinler Arası İstekleri (CORS) etkinleştirme

::: moniker range=">= aspnetcore-3.0"

Yazar: [Rick Anderson](https://twitter.com/RickAndMSFT) ve [Kirk Larkin](https://twitter.com/serpent5)

Bu makalede, bir ASP.NET Core uygulamasında CORS'in nasıl etkinleştirilen gösterilmektedir.

Tarayıcı güvenliği, bir web sayfasının web sayfasına hizmet eden den farklı bir etki alanına istekte bulunmasını engeller. Bu kısıtlamaya *aynı kaynak ilkesi*denir. Aynı kaynak ilkesi, kötü amaçlı bir sitenin başka bir siteden gelen hassas verileri okumasını engeller. Bazen, diğer sitelerin uygulamanızda çapraz orijin isteklerinde yer almasına izin vermek isteyebilirsiniz. Daha fazla bilgi için [Mozilla CORS makalesine](https://developer.mozilla.org/docs/Web/HTTP/CORS)bakın.

[Çapraz Kaynak Paylaşımı](https://www.w3.org/TR/cors/) (CORS):

* Bir sunucunun aynı orijin ilkesini gevşetmesine olanak tanıyan bir W3C standardıdır.
* Bir güvenlik özelliği **değildir,** CORS güvenliği rahatlatır. BIR API CORS izin vererek daha güvenli değildir. Daha fazla bilgi için [CORS'in nasıl çalıştığını](#how-cors)görün.
* Sunucunun, diğerlerini reddederken bazı kişiler arası isteklere açıkça izin vermesine izin verir.
* [JSONP](/dotnet/framework/wcf/samples/jsonp)gibi önceki tekniklerden daha güvenli ve daha esnektir.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ( nasıl[indirilir](xref:index#how-to-download-a-sample))

## <a name="same-origin"></a>Aynı köken

Aynı düzenleri, ana bilgisayarlar ve bağlantı noktaları[(RFC 6454)](https://tools.ietf.org/html/rfc6454)varsa, iki URL aynı kökene sahiptir.

Bu iki URL aynı kökene sahiptir:

* `https://example.com/foo.html`
* `https://example.com/bar.html`

Bu URL'lerin önceki iki URL'den farklı kökenleri vardır:

* `https://example.net`&ndash; Farklı etki alanı
* `https://www.example.com/foo.html`&ndash; Farklı alt etki alanı
* `http://example.com/foo.html`&ndash; Farklı şema
* `https://example.com:9000/foo.html`&ndash; Farklı bağlantı noktası

## <a name="enable-cors"></a>CORS'yi etkinleştirme

CORS'i etkinleştirmenin üç yolu vardır:

* Adlandırılmış bir [ilke](#np) veya [varsayılan ilke](#dp)kullanarak ara yazılımda.
* [Uç nokta yönlendirmeyi](#ecors)kullanma.
* [[EnableCors]](#attr) özelliği ile.

[[EnableCors]](#attr) özniteliğinin adlandırılmış bir ilkeyle kullanılması, CORS'i destekleyen uç noktaları sınırlamada en iyi denetimi sağlar.

Her yaklaşım aşağıdaki bölümlerde ayrıntılı olarak açıklanır.

<a name="np"></a>

## <a name="cors-with-named-policy-and-middleware"></a>Adlı ilke ve middleware ile CORS

CORS Middleware, çapraz orijin isteklerini işler. Aşağıdaki kod, belirtilen kökenlere sahip uygulamanın tüm uç noktalarına bir CORS ilkesi uygular:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=3,9,31)]

Yukarıdaki kod:

* İlke adını `_myAllowSpecificOrigins`' a ayarlar. İlke adı rasgele.
* Uzantı <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> yöntemini çağırır ve `_myAllowSpecificOrigins` CORS ilkesini belirtir. `UseCors`CORS ara yazılımekler.
* <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> [Lambda ifadesi](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)ile aramalar . Lambda bir <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> nesne alır. [Yapılandırma seçenekleri](#cors-policy-options), `WithOrigins`örneğin, bu makalede daha sonra açıklanmıştır.
* Tüm denetleyici `_myAllowSpecificOrigins` uç noktaları için CORS ilkesini etkinleştiri. Belirli uç noktalara BIR CORS ilkesi uygulamak için [uç nokta yönlendirmesine](#ecors) bakın.

Uç nokta yönlendirmesi ile, CORS ara ware ve aramalar `UseRouting` arasında `UseEndpoints`yürütmek için ***yapılandırılmalıdır.***

Önceki koda benzer test kodu yla ilgili talimatlar için [Test CORS'e](#testc) bakın.

Yöntem <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> çağrısı, uygulamanın servis konteynerine CORS hizmetlerini ekler:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet2)]

Daha fazla bilgi için bu belgedeki [CORS ilkesi seçeneklerine](#cpo) bakın.

Yöntemler, <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> aşağıdaki kodda gösterildiği gibi zincirlenebilir:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup2.cs?name=snippet)]

Not: Belirtilen URL, sondaki eğik`/`çizgi () **içermemelidir.** URL ile sonlandırma, `/`karşılaştırma `false` döndürür ve hiçbir üstbilgi döndürülür.

<a name="dp"></a>

### <a name="cors-with-default-policy-and-middleware"></a>Varsayılan ilke ve ara yazılım ile CORS

Aşağıdaki vurgulanan kod varsayılan CORS ilkesini sağlar:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupDefaultPolicy.cs?name=snippet2&highlight=7,29)]

Önceki kod, varsayılan CORS ilkesini tüm denetleyici uç noktalarına uygular.

<a name="ecors"></a>

## <a name="enable-cors-with-endpoint-routing"></a>Uç nokta yönlendirme ile Kors'u etkinleştirme

Cors'un şu anda kullanarak `RequireCors` uç nokta bazında etkinleştirilmesi otomatik ön uçuş [isteklerini](#apf) ***desteklemez.*** Daha fazla bilgi [için, bu GitHub sorunu](https://github.com/dotnet/aspnetcore/issues/20709) ve [test CORS uç noktası yönlendirme ve [HttpOptions] bakın.](#tcer)

Uç nokta yönlendirmeile CORS, uzantı yöntemleri <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> kümesi kullanılarak uç nokta başına etkinleştirilebilir:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPt.cs?name=snippet2&highlight=3,7-15,32,41,44)]

Yukarıdaki kodda:

* `app.UseCors`CORS ara yazılımını sağlar. Varsayılan ilke yapılandırılmadığından, `app.UseCors()` tek başına CORS'u etkinleştirmez.
* Ve `/echo` denetleyici uç noktaları belirtilen ilkeyi kullanarak orijinler arası isteklere izin verir.
* Ve `/echo2` Razor Pages bitiş noktaları, varsayılan ilke belirtilmedi, çünkü çapraz başlangıç isteklerine izin ***vermez.***

[[Engelleyici Korse]](#dc) özelliği, uç nokta yönlendirme ile `RequireCors`etkinleştirilen CORS'leri ***devre*** dışı almaz.

Yukarıdakine benzer test koduyla ilgili talimatlar için [uç nokta yönlendirme stoyonu ve [HttpOptions] içeren Test CORS'e](#tcer) bakın.

<a name="attr"></a>

## <a name="enable-cors-with-attributes"></a>Özniteliklerle CORS'i etkinleştirme

[[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) özelliği ile CORS etkinleştirme ve yalnızca CORS gerektiren uç noktalara adlandırılmış bir ilke uygulamak en iyi denetimi sağlar.

[[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) özniteliği, CORS'u küresel olarak uygulamak için bir alternatif sağlar. Öznitelik, `[EnableCors]` tüm uç noktalar yerine seçili uç noktalar için CORS'e olanak sağlar:

* `[EnableCors]`varsayılan ilkeyi belirtir.
* `[EnableCors("{Policy String}")]`adlı bir ilke belirtir.

Öznitelik `[EnableCors]` uygulanabilir:

* Jilet Sayfası`PageModel`
* Denetleyicisi
* Denetleyici eylem yöntemi

Öznitelik ile denetleyicilere, sayfa modellerine veya eylem `[EnableCors]` yöntemlerine farklı ilkeler uygulanabilir. Öznitelik `[EnableCors]` bir denetleyici, sayfa modeli veya eylem yöntemine uygulandığında ve ARA Yazılım'da CORS etkinleştirildiğinde, ***her iki*** ilke de uygulanır. ***Politikaların birleştirilmesine karşı tavsiye ediyoruz. ***Her ***ikisini de aynı uygulamada değil, öznitelik veya ara yazılımı kullanın.*** `[EnableCors]`

Aşağıdaki kod her yöntem için farklı bir ilke uygular:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

Aşağıdaki kod iki CORS ilkeleri oluşturur:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup3.cs?name=snippet&highlight=12-28,44)]

CORS isteklerini sınırlandırma nın en iyi kontrolü için:

* Adlandırılmış bir ilke ile kullanın. `[EnableCors("MyPolicy")]`
* Varsayılan bir ilke tanımlamayın.
* Uç nokta [yönlendirmekullanmayın.](#ecors)

Sonraki bölümdeki kod önceki listeyi karşılar.

Önceki koda benzer test kodu yla ilgili talimatlar için [Test CORS'e](#testc) bakın.

<a name="dc"></a>

### <a name="disable-cors"></a>CORS'u devre dışı

[[Engelleyici Ler]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) [özniteliği, bitiş noktası yönlendirmesi](#ecors)ile etkinleştirilen CORS'leri devre ***dışı*** almaz.

Aşağıdaki kod CORS ilkesini `"MyPolicy"`tanımlar:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTestMyPolicy.cs?name=snippet)]

Aşağıdaki kod `GetValues2` eylem için CORS devre dışı kalmaktadır:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet&highlight=1,23)]

Yukarıdaki kod:

* [Uç nokta yönlendirmeile](#ecors)CORS'u etkinleştirmez.
* [Varsayılan BIR CORS ilkesi](#dp)tanımlamaz.
* Denetleyiciiçin CORS ilkesini `"MyPolicy"` etkinleştirmek için [[EnableCors("MyPolicy")]](#attr) kullanır.
* `GetValues2` Yöntem için CORS'u devre dışı kılabilir.

Yukarıdaki kodun sınanma talimatları için [Test CORS'a](#testc) bakın.

<a name="cpo"></a>

## <a name="cors-policy-options"></a>CORS politika seçenekleri

Bu bölümde, BIR CORS ilkesinde ayarlanabilecek çeşitli seçenekler açıklanmaktadır:

* [İzin verilen kökenleri ayarlama](#set-the-allowed-origins)
* [İzin verilen HTTP yöntemlerini ayarlama](#set-the-allowed-http-methods)
* [İzin verilen istek üstbilgisini ayarlama](#set-the-allowed-request-headers)
* [Açıkta kalan yanıt üstaylarını ayarlama](#set-the-exposed-response-headers)
* [Orijinler arası isteklerde kimlik bilgileri](#credentials-in-cross-origin-requests)
* [Uçuş öncesi son kullanma süresini ayarlama](#set-the-preflight-expiration-time)

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>olarak `Startup.ConfigureServices`adlandırılır. Bazı seçenekler için, [önce CORS nasıl çalışır](#how-cors) bölümünü okumak yararlı olabilir.

## <a name="set-the-allowed-origins"></a>İzin verilen kökenleri ayarlama

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>&ndash; Herhangi bir şema (veya)`http` `https`ile tüm kökenlerden CORS isteklerini sağlar. `AllowAnyOrigin`herhangi bir *web sitesi* uygulamaya çapraz orijin isteklerinde bulunabileceğinden güvenli değildir.

> [!NOTE]
> Belirtilmesi `AllowAnyOrigin` `AllowCredentials` ve güvenli olmayan bir yapılandırmadır ve site ler arası istek sahteciliğiyle sonuçlanabilir. Bir uygulama her iki yöntemle de yapılandırıldığında CORS hizmeti geçersiz bir CORS yanıtını döndürür.

`AllowAnyOrigin`ön kontrol isteklerini `Access-Control-Allow-Origin` ve üstbilgietkiler. Daha fazla bilgi için [Ön Uçuş istekleri](#preflight-requests) bölümüne bakın.

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>&ndash; İlkenin <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> özelliğini, kaynağın izin verilip verilmediğini değerlendirirken, kökenlerin yapılandırılmış bir joker etki alanıyla eşleşmesine olanak tanıyan bir işlev olarak ayarlar.

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet)]

### <a name="set-the-allowed-http-methods"></a>İzin verilen HTTP yöntemlerini ayarlama

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:

* Herhangi bir HTTP yöntemine izin verir:
* Ön kontrol isteklerini `Access-Control-Allow-Methods` ve üstbilgiyi etkiler. Daha fazla bilgi için [Ön Uçuş istekleri](#preflight-requests) bölümüne bakın.

### <a name="set-the-allowed-request-headers"></a>İzin verilen istek üstbilgisini ayarlama

Belirli üstbilginin CORS isteğinde gönderilmesine izin vermek için <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> yazar [istek üstbilgisini](https://xhr.spec.whatwg.org/#request)arayın, arayın ve izin verilen üstbilgibelirtin:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

Tüm [yazar istek üstbilgilerine](https://www.w3.org/TR/cors/#author-request-headers)izin vermek için , arayın: <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

`AllowAnyHeader`ön kontrol isteklerini ve [Erişim-Kontrol-İstek-Üstbilgi](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method) üstbilgisini etkiler. Daha fazla bilgi için [Ön Uçuş istekleri](#preflight-requests) bölümüne bakın.

Cors Middleware ilkesi tarafından `WithHeaders` belirtilen belirli üstbilgilerle eşleşerek, `Access-Control-Request-Headers` yalnızca gönderilen üstbilgiler `WithHeaders`üstbilgilerle tam olarak eşleştiğinde mümkündür.

Örneğin, aşağıdaki gibi yapılandırılan bir uygulamayı düşünün:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet4)]

([HeaderNames.ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) listede yer olmadığı `Content-Language` için CORS Middleware aşağıdaki istek üstbilgisi ile ön kontrol isteğini `WithHeaders`reddeder:

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

Uygulama *200 Ok* yanıtı döndürür, ancak CORS üstbilgilerini geri göndermez. Bu nedenle, tarayıcı çapraz kaynak isteği denemez.

### <a name="set-the-exposed-response-headers"></a>Açıkta kalan yanıt üstaylarını ayarlama

Varsayılan olarak, tarayıcı tüm yanıt üstbilgisini uygulamaya maruz bırakmaz. Daha fazla bilgi için [W3C Cross-Origin Kaynak Paylaşımı (Terminoloji): Basit Yanıt Üstbilgisi'ne](https://www.w3.org/TR/cors/#simple-response-header)bakın.

Varsayılan olarak kullanılabilir yanıt üstbilgi:

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

CORS belirtimi bu üstbilgi *basit yanıt üstbilgi çağırır.* Diğer üstbilgileri uygulamaiçin kullanılabilir <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>hale getirmek için aşağıdakileri arayın:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet5)]
### <a name="credentials-in-cross-origin-requests"></a>Orijinler arası isteklerde kimlik bilgileri

Kimlik bilgileri, CORS isteğinde özel kullanım gerektirir. Varsayılan olarak, tarayıcı bir çapraz kaynak isteği ile kimlik bilgileri göndermez. Kimlik bilgileri çerezleri ve HTTP kimlik doğrulama şemalarını içerir. Bir çapraz kaynak isteği ile kimlik bilgileri `XMLHttpRequest.withCredentials` göndermek `true`için, istemci nin .

Doğrudan `XMLHttpRequest` kullanma:

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

jQuery kullanma:

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

Getir [API'sini](https://developer.mozilla.org/docs/Web/API/Fetch_API)kullanma :

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

Sunucu kimlik bilgilerine izin vermelidir. Orijinler arası kimlik <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>bilgilerine izin vermek için:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet6)]

HTTP yanıtı, `Access-Control-Allow-Credentials` tarayıcıya sunucunun bir çapraz kaynak isteği için kimlik bilgilerine izin verdiğini söyleyen bir üstbilgi içerir.

Tarayıcı kimlik bilgileri gönderir, ancak yanıt geçerli `Access-Control-Allow-Credentials` bir üstbilgi içermiyorsa, tarayıcı uygulamaya yanıtı açıklamaz ve çapraz kaynak isteği başarısız olur.

Orjinal kimlik bilgilerine izin vermek bir güvenlik riskidir. Başka bir etki alanında bulunan bir web sitesi, kullanıcının bilgisi olmadan kullanıcı adına oturum açmış bir kullanıcının kimlik bilgilerini uygulamaya gönderebilir. <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

CORS belirtimi, üstbilgi `"*"` varsa(tüm kökenlerin) kaynağını ayarlamanın `Access-Control-Allow-Credentials` geçersiz olduğunu da belirtir.

<a name="pref"></a>

## <a name="preflight-requests"></a>Ön kontrol istekleri

Bazı CORS istekleri için tarayıcı, gerçek isteği yapmadan önce ek bir [OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) isteği gönderir. Bu istek [bir ön kontrol isteği](https://developer.mozilla.org/docs/Glossary/Preflight_request)olarak adlandırılır. Aşağıdaki ***tüm*** koşullar doğruysa, tarayıcı ön kontrol isteğini atlayabilir:

* İstek yöntemi GET, HEAD veya POST'dur.
* Uygulama, istek `Accept`üstbilgisini , , `Accept-Language` `Content-Language`, `Content-Type`veya `Last-Event-ID`.
* Üstbilgi, `Content-Type` küme, aşağıdaki değerlerden birine sahiptir:
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

İstemci isteği için ayarlanan istek üstbilgilerine ilişkin kural, `setRequestHeader` `XMLHttpRequest` uygulamanın nesneyi çağırarak ayarladığını üstbilgi için geçerlidir. CORS belirtimi bu üstbilgi [yazar istek üstbilgi](https://www.w3.org/TR/cors/#author-request-headers)çağırır. Kural, tarayıcının ayarlayabileceği `User-Agent`, , `Host`veya `Content-Length`.

Aşağıda, bu belgenin [Test CORS](#testc) bölümündeki **[Test Yap]** düğmesinden yapılan ön kontrol isteğine benzer bir örnek yanıt verilmiştir.

```
General:
Request URL: https://cors3.azurewebsites.net/api/values/5
Request Method: OPTIONS
Status Code: 204 No Content

Response Headers:
Access-Control-Allow-Methods: PUT,DELETE,GET
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f8...8;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Vary: Origin

Request Headers:
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Access-Control-Request-Method: PUT
Connection: keep-alive
Host: cors3.azurewebsites.net
Origin: https://cors1.azurewebsites.net
Referer: https://cors1.azurewebsites.net/
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0
```

Ön kontrol isteği [HTTP OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) yöntemini kullanır. Aşağıdaki üstbilgi içerebilir:

* [Access-Control-Request-Method](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): Gerçek istek için kullanılacak HTTP yöntemi.
* [Erişim-Denetim-İstek-Üstbilgi](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers): Uygulamanın gerçek istek üzerine ayarladığının istek üstbilgilerinin listesi. Daha önce belirtildiği gibi, `User-Agent`bu, tarayıcının belirlediği üstleri içermez.
* [Erişim-Denetim-İzin Verme-Yöntemler](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Methods)

Ön kontrol isteği reddedilirse, uygulama bir `200 OK` yanıt verir, ancak CORS üstbilgisini ayarlamaz. Bu nedenle, tarayıcı çapraz kaynak isteği denemez. Reddedilen ön kontrol isteği örneği için, bu belgenin [Test CORS](#testc) bölümüne bakın.

Konsol uygulaması, F12 araçlarını kullanarak tarayıcıya bağlı olarak aşağıdakilerden birine benzer bir hata gösterir:

* Firefox: Cross-Origin İstek Engellendi: Aynı Başlangıç Politikası uzak `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5`kaynağı okuma ya da izin vermiyor. (Sebep: CORS isteği başarılı olmadı). [Daha Fazla Bilgi Edinin](https://developer.mozilla.org/docs/Web/HTTP/CORS/Errors/CORSDidNotSucceed)
* Krom bazlı: ' 'https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5from origin 'https://cors3.azurewebsites.net' 'eve erişim CORS ilkesi tarafından engellendi: Ön kontrol isteğine yanıt erişim denetiminden geçmiyor: İstenen kaynakta 'Access-Control-Allow-Allow-Origin' başlığı bulunmamaktadır. İhtiyaçlarınıza opak bir yanıt yanıt veriyorsa, CORS devre dışı bırakılmış kaynağı almak için istek modunu 'kors'a göre ayarlayın.

Belirli üstbilgi için: <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

Tüm [yazar istek üstbilgilerine](https://www.w3.org/TR/cors/#author-request-headers)izin vermek için , arayın: <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

Tarayıcılar, ayarlarken `Access-Control-Request-Headers`tutarlı değildir. Eğer ikisinden biri:

* Üstbilgi, başka bir şeye ayarlanır`"*"`
* <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>denir: En az `Accept` `Content-Type`, `Origin`, ve , artı desteklemek istediğiniz herhangi bir özel üstbilgi içerir.

<a name="apf"></a>

### <a name="automatic-preflight-request-code"></a>Otomatik ön kontrol istek kodu

CORS ilkesi uygulandığında:

* Global olarak `app.UseCors` arayarak `Startup.Configure`.
* Özniteliği `[EnableCors]` kullanarak.

ASP.NET Core, uçuş öncesi OPTIONS isteğine yanıt verir.

Cors'un şu anda kullanarak `RequireCors` uç nokta bazında etkinleştirilmesi otomatik ön uçuş isteklerini ***desteklemez.***

Bu belgenin [Test CORS](#testc) bölümü bu davranışı gösterir.

<a name="pro"></a>

### <a name="httpoptions-attribute-for-preflight-requests"></a>[HttpOptions] ön kontrol istekleri için öznitelik

CORS uygun ilke ile etkinleştirildiğinde, ASP.NET Core genellikle CORS uçuş öncesi isteklerine otomatik olarak yanıt verir. Bazı senaryolarda durum böyle olmayabilir. Örneğin, [uç nokta yönlendirme ile CORS](#ecors)kullanarak.

Aşağıdaki kod, OPTIONS istekleri için uç noktaları oluşturmak için [[HttpOptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) özniteliğini kullanır:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet&highlight=5-17)]

Yukarıdaki kodun sınanma yönergeleri için [uç nokta yönlendirme ve [HttpOptions] içeren Test CORS'e](#tcer) bakın.

### <a name="set-the-preflight-expiration-time"></a>Uçuş öncesi son kullanma süresini ayarlama

Üstbilgi, `Access-Control-Max-Age` ön kontrol isteğine verilen yanıtın ne kadar süreyle önbelleğe alınabileceğini belirtir. Bu üstbilgi ayarlamak <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>için:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet7)]
<a name="how-cors"></a>

## <a name="how-cors-works"></a>CORS nasıl çalışır?

Bu bölümde, HTTP iletileri düzeyinde bir [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) isteğine neler olduğu açıklanmaktadır.

* CORS bir güvenlik özelliği **değildir.** CORS, bir sunucunun aynı orijin ilkesini gevşetmesine olanak tanıyan bir W3C standardıdır.
  * Örneğin, kötü niyetli bir aktör sitenize karşı [Site Arası Komut Dosyası (XSS)](xref:security/cross-site-scripting) kullanabilir ve cors özellikli sitesine bilgi çalmak için bir çapraz site isteği yürütebilir.
* BIR API CORS izin vererek daha güvenli değildir.
  * CORS'i uygulamak istemciye (tarayıcıya) kalmış. Sunucu isteği yürütür ve yanıtı döndürür, bir hata döndürür ve yanıtı engeller istemcisidir. Örneğin, aşağıdaki araçlardan herhangi biri sunucu yanıtını görüntüler:
    * [Fiddler](https://www.telerik.com/fiddler)
    * [Postman](https://www.getpostman.com/)
    * [.NET Https://yayla](/dotnet/csharp/tutorials/console-webapiclient)
    * Adres çubuğuna URL girerek bir web tarayıcısı.
* Bu, bir sunucunun tarayıcıların aksi takdirde yasaklanacak bir çapraz kökenli [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) veya [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) isteğini yürütmesine izin vermenin bir yoludur.
  * CORS'siz tarayıcılar çapraz kökenli isteklerde bulunabilir. CORS önce, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) bu kısıtlamayı atlatmak için kullanılmıştır. JSONP XHR kullanmaz, yanıtı `<script>` almak için etiketi kullanır. Komut dosyalarıçapraz kökenli yüklenebilir.

[CORS belirtimi,](https://www.w3.org/TR/cors/) orijinler arası istekleri etkinleştiren birkaç yeni HTTP üstbilgisini tanıttı. Bir tarayıcı CORS'i destekliyorsa, bu başlıkları çapraz başlangıç istekleri için otomatik olarak ayarlar. CORS'i etkinleştirmek için özel JavaScript kodu gerekmez.

Dağıtılan [örnekteki](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) [PUT test düğmesi](https://cors3.azurewebsites.net/test)

Aşağıda, [Değerler](https://cors3.azurewebsites.net/) test düğmesinden `https://cors1.azurewebsites.net/api/values`' e bir çapraz kaynak isteği örneği verilmiştir. Üstbilgi: `Origin`

* İstekte bulunduran sitenin etki alanını sağlar.
* Gereklidir ve ana bilgisayardan farklı olmalıdır.

**Genel başlıklar**

```
Request URL: https://cors1.azurewebsites.net/api/values
Request Method: GET
Status Code: 200 OK
```

**Yanıt üstleri**

```
Content-Encoding: gzip
Content-Type: text/plain; charset=utf-8
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Transfer-Encoding: chunked
Vary: Accept-Encoding
X-Powered-By: ASP.NET
```

**İstek üstbilgi**

```
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Connection: keep-alive
Host: cors1.azurewebsites.net
Origin: https://cors3.azurewebsites.net
Referer: https://cors3.azurewebsites.net/
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0 ...
```

İsteklerde, `OPTIONS` sunucu yanıt **üstbilgisini** `Access-Control-Allow-Origin: {allowed origin}` yanıt olarak ayarlar. Örneğin, dağıtılan [örnek](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI), [Sil [EnableCors]](https://cors1.azurewebsites.net/test?number=2) düğmesi `OPTIONS` isteği aşağıdaki üstbilgileri içerir:

**Genel başlıklar**

```
Request URL: https://cors3.azurewebsites.net/api/TodoItems2/MyDelete2/5
Request Method: OPTIONS
Status Code: 204 No Content
```

**Yanıt üstleri**

```
Access-Control-Allow-Headers: Content-Type,x-custom-header
Access-Control-Allow-Methods: PUT,DELETE,GET,OPTIONS
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors3.azurewebsites.net
Vary: Origin
X-Powered-By: ASP.NET
```

**İstek üstbilgi**

```
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Access-Control-Request-Headers: content-type
Access-Control-Request-Method: DELETE
Connection: keep-alive
Host: cors3.azurewebsites.net
Origin: https://cors1.azurewebsites.net
Referer: https://cors1.azurewebsites.net/test?number=2
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0
```

Önceki Yanıt **üstbilgisinde,** sunucu yanıtta [Access-Control-Allow-Origin](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) üstbilgisini ayarlar. Bu `https://cors1.azurewebsites.net` üstbilginin değeri `Origin` istekteki üstbilgiyle eşleşir.

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> Çağrıldığında, `Access-Control-Allow-Origin: *`joker karakter değeri döndürülür. `AllowAnyOrigin`herhangi bir menşe sağlar.

Yanıt `Access-Control-Allow-Origin` üstbilgi içermiyorsa, çapraz kaynak isteği başarısız olur. Özellikle, tarayıcı isteği izin verir. Sunucu başarılı bir yanıt döndürse bile, tarayıcı yanıtı istemci uygulamasına sunmaz.

<a name="options"></a>

### <a name="display-options-requests"></a>SEÇENEKLER isteklerini görüntüleme

Varsayılan olarak, Chrome ve Edge tarayıcıları F12 araçlarının ağ sekmesinde OPTIONS isteklerini göstermez. Bu tarayıcılarda OPTIONS isteklerini görüntülemek için:

* `chrome://flags/#out-of-blink-cors` veya `edge://flags/#out-of-blink-cors`
* bayrağı devre dışı kılabilir.
* Yeni -den başlatın.

Firefox varsayılan olarak OPTIONS isteklerini gösterir.

## <a name="cors-in-iis"></a>CORS in IIS

IIS'ye dağıtılırken, sunucu anonim erişime izin verecek şekilde yapılandırılmamışsa CORS'in Windows Kimlik Doğrulama'dan önce çalışması gerekiyor. Bu senaryoyu desteklemek için [IIS CORS modülünün](https://www.iis.net/downloads/microsoft/iis-cors-module) uygulama için yüklenmesi ve yapılandırılması gerekir.

<a name="testc"></a>

## <a name="test-cors"></a>Test CORS

[Örnek indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) CORS test etmek için kodu vardır. [Nasıl indirilir bakın.](xref:index#how-to-download-a-sample) Örnek Razor Pages eklenen bir API projesidir:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTest2.cs?name=snippet2)]

  > [!WARNING]
  > `WithOrigins("https://localhost:<port>");`yalnızca [indirme örnek koduna](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors)benzer bir örnek uygulamayı test etmek için kullanılmalıdır.

Aşağıdaki `ValuesController` sınama için uç noktaları sağlar:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet)]

[MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) [Rick.Docs.Samples.RouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) NuGet paketi tarafından sağlanır ve rota bilgilerini görüntüler.

Aşağıdaki yaklaşımlardan birini kullanarak önceki örnek kodu test edin:

* Dağıtılan örnek uygulamayı ' [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/)da kullanın. Örneği indirmenize gerek yoktur.
* Örneği varsayılan `dotnet run` URL'sini kullanarak `https://localhost:5001`çalıştırın.
* Bir URL için 44398 olarak ayarlanmış bağlantı noktası `https://localhost:44398`ile Visual Studio örnek çalıştırın.

F12 araçları ile bir tarayıcı kullanma:

* **Değerler** düğmesini seçin ve **Ağ** sekmesindeki üstbilgiincelemesi.
* PUT **test** düğmesini seçin. SEÇENEKLER isteğini görüntülerken verilen talimatlar için SEÇENEKLER'i görüntüleme [isteklerine](#options) bakın. **PUT testi** iki istek, bir OPTIONS ön uçuş isteği ve PUT isteği oluşturur.
* Başarısız **`GetValues2 [DisableCors]`** bir CORS isteğini tetiklemek için düğmeyi seçin. Belgede belirtildiği gibi, yanıt 200 başarı döndürür, ancak CORS isteği yapılmaz. CORS hatasını görmek için **Konsol** sekmesini seçin. Tarayıcıya bağlı olarak, aşağıdakilere benzer bir hata görüntülenir:

     `'https://cors1.azurewebsites.net/api/values/GetValues2'` Orijinden `'https://cors3.azurewebsites.net'` erişim CORS ilkesi tarafından engellendi: İstenen kaynakta 'Access-Control-Allow-Origin' başlığı bulunmamaktadır. İhtiyaçlarınıza opak bir yanıt yanıt veriyorsa, CORS devre dışı bırakılmış kaynağı almak için istek modunu 'kors'a göre ayarlayın.
     
CORS özellikli uç [noktalar, curl,](https://curl.haxx.se/) [Fiddler](https://www.telerik.com/fiddler)veya [Postacı](https://www.getpostman.com/)gibi bir araçla test edilebilir. Bir araç kullanırken, `Origin` üstbilgi tarafından belirtilen isteğin kaynağı, isteği alan ana bilgisayardan farklı olmalıdır. Üstbilginin değerine *cross-origin* `Origin` göre istek çapraz kökenli değilse:

* CORS Middleware'in isteği işlemesine gerek yoktur.
* CORS başlıkları yanıt olarak döndürülür.

Aşağıdaki komut, `curl` bir OPTIONS isteğini bilgiyle birlikte vermek için kullanır:

```bash
curl -X OPTIONS https://cors3.azurewebsites.net/api/TodoItems2/5 -i
```

<!--
curl come with Git. Add to path variable
C:\Program Files\Git\mingw64\bin\
-->

<a name="tcer"></a>

### <a name="test-cors-with-endpoint-routing-and-httpoptions"></a>Uç nokta yönlendirme ile TEST CORS ve [HttpOptions]

Cors'un şu anda kullanarak `RequireCors` uç nokta bazında etkinleştirilmesi otomatik ön uçuş [isteklerini](#apf) ***desteklemez.*** [CORS'i etkinleştirmek için uç nokta yönlendirmeyi](#ecors)kullanan aşağıdaki kodu göz önünde bulundurun:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPointBugTest.cs?name=snippet2)]

Aşağıdaki `TodoItems1Controller` sınama için uç noktaları sağlar:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems1Controller.cs?name=snippet2)]

Dağıtılan [örneğin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) [test sayfasından](https://cors1.azurewebsites.net/test?number=1) önceki kodu test edin.

**[EnableCors]** ve **GET [EnableCors] düğmeleri** başarılı olur, `[EnableCors]` çünkü uç noktalar uçuş öncesi isteklere sahip ve yanıt verir. Diğer uç noktalar başarısız olur. [JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js) gönderdiğinden **GET** düğmesi başarısız olur:

```javascript
 headers: {
      "Content-Type": "x-custom-header"
 },
```

Aşağıdakibenzer `TodoItems2Controller` uç noktaları sağlar, ancak OPTIONS isteklerini yanıtlamak için açık kod içerir:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet2)]

Dağıtılan örneğin [test sayfasından](https://cors1.azurewebsites.net/test?number=2) önceki kodu test edin. **Denetleyici** açılır listesinde Ön **Kontrol'i** seçin ve ardından **Denetleyiciyi Ayarlayın.** `TodoItems2Controller` Uç noktalara yapılan tüm CORS çağrıları başarılı dır.

## <a name="additional-resources"></a>Ek kaynaklar

* [Çıkış Noktaları Arası Kaynak Paylaşımı (CORS)](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [IIS CORS modülü ile başlarken](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)

Bu makalede, bir ASP.NET Core uygulamasında CORS'in nasıl etkinleştirilen gösterilmektedir.

Tarayıcı güvenliği, bir web sayfasının web sayfasına hizmet eden den farklı bir etki alanına istekte bulunmasını engeller. Bu kısıtlamaya *aynı kaynak ilkesi*denir. Aynı kaynak ilkesi, kötü amaçlı bir sitenin başka bir siteden gelen hassas verileri okumasını engeller. Bazen, diğer sitelerin uygulamanızda çapraz orijin isteklerinde yer almasına izin vermek isteyebilirsiniz. Daha fazla bilgi için [Mozilla CORS makalesine](https://developer.mozilla.org/docs/Web/HTTP/CORS)bakın.

[Çapraz Kaynak Paylaşımı](https://www.w3.org/TR/cors/) (CORS):

* Bir sunucunun aynı orijin ilkesini gevşetmesine olanak tanıyan bir W3C standardıdır.
* Bir güvenlik özelliği **değildir,** CORS güvenliği rahatlatır. BIR API CORS izin vererek daha güvenli değildir. Daha fazla bilgi için [CORS'in nasıl çalıştığını](#how-cors)görün.
* Sunucunun, diğerlerini reddederken bazı kişiler arası isteklere açıkça izin vermesine izin verir.
* [JSONP](/dotnet/framework/wcf/samples/jsonp)gibi önceki tekniklerden daha güvenli ve daha esnektir.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ( nasıl[indirilir](xref:index#how-to-download-a-sample))

## <a name="same-origin"></a>Aynı köken

Aynı düzenleri, ana bilgisayarlar ve bağlantı noktaları[(RFC 6454)](https://tools.ietf.org/html/rfc6454)varsa, iki URL aynı kökene sahiptir.

Bu iki URL aynı kökene sahiptir:

* `https://example.com/foo.html`
* `https://example.com/bar.html`

Bu URL'lerin önceki iki URL'den farklı kökenleri vardır:

* `https://example.net`&ndash; Farklı etki alanı
* `https://www.example.com/foo.html`&ndash; Farklı alt etki alanı
* `http://example.com/foo.html`&ndash; Farklı şema
* `https://example.com:9000/foo.html`&ndash; Farklı bağlantı noktası

Internet Explorer, kaynaklarını karşılaştırırken bağlantı noktasını dikkate almaz.

## <a name="cors-with-named-policy-and-middleware"></a>Adlı ilke ve middleware ile CORS

CORS Middleware, çapraz orijin isteklerini işler. Aşağıdaki kod, belirtilen menşee sahip tüm uygulama için CORS sağlar:

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

Yukarıdaki kod:

* İlke adını "myAllowSpecificOrigins"\_olarak ayarlar. İlke adı rasgele.
* CORS'u sağlayan uzatma yöntemini <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> çağırır.
* <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> [Lambda ifadesi](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)ile aramalar . Lambda bir <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> nesne alır. [Yapılandırma seçenekleri](#cors-policy-options), `WithOrigins`örneğin, bu makalede daha sonra açıklanmıştır.

Yöntem <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> çağrısı, uygulamanın servis konteynerine CORS hizmetlerini ekler:

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

Daha fazla bilgi için bu belgedeki [CORS ilke seçeneklerine](#cpo) bakın.

Yöntem, <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> aşağıdaki kodda gösterildiği gibi yöntemleri zincirleyebilir:

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

Not: URL' de sondaki eğik çizgi ()`/` **içermemelidir.** URL ile sonlandırma, `/`karşılaştırma `false` döndürür ve hiçbir üstbilgi döndürülür.

Aşağıdaki kod, CORS Middleware aracılığıyla tüm uygulama uç noktalarına CORS ilkelerini uygular:
```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseHsts();
    }

    app.UseCors();

    app.UseHttpsRedirection();
    app.UseMvc();
}
```
Not: `UseCors` önce `UseMvc`çağrılmalıdır.

Bkz. Sayfa/denetleyici/eylem düzeyinde CORS ilkesini uygulamak için [Razor Pages, denetleyiciler ve eylem yöntemlerinde CORS'i etkinleştirin.](#ecors)

Önceki koda benzer test kodu yla ilgili talimatlar için [Test CORS'e](#test) bakın.

## <a name="enable-cors-with-attributes"></a>Özniteliklerle CORS'i etkinleştirme

[EnableCors&rbrack; özniteliği, CORS'u küresel olarak uygulamak için bir alternatif sağlar. &lbrack;](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) Öznitelik, `[EnableCors]` tüm uç noktalar yerine seçilen uç noktalar için CORS'e olanak sağlar.

Varsayılan `[EnableCors]` ilkeyi belirtmek `[EnableCors("{Policy String}")]` ve bir ilke belirtmek için kullanın.

Öznitelik `[EnableCors]` uygulanabilir:

* Jilet Sayfası`PageModel`
* Denetleyicisi
* Denetleyici eylem yöntemi

Öznitelik ile `[EnableCors]` denetleyici/sayfa modeli/eylem için farklı ilkeler uygulayabilirsiniz. Öznitelik `[EnableCors]` denetleyicilere/sayfa modeli/eylem yöntemine uygulandığında ve ara yazılımda CORS etkinleştirildiğinde, ***her iki*** ilke de uygulanır. İlkeleri ***birleştirmemenizi*** öneririz. Öznitelik veya ara yazılım kullanın, ***her ikisi de değil**. `[EnableCors]` Kullanırken, `[EnableCors]`varsayılan bir ilke **tanımlamayın.**

Aşağıdaki kod her yöntem için farklı bir ilke uygular:

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

Aşağıdaki kod bir CORS varsayılan ilkesi ve `"AnotherPolicy"`adlı bir ilke oluşturur:

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a>CORS'u devre dışı

[DisableCors&rbrack; denetleyici/sayfa modeli/eylem için CORS'u devre dışı bırakmaz. &lbrack;](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute)

<a name="cpo"></a>

## <a name="cors-policy-options"></a>CORS politika seçenekleri

Bu bölümde, BIR CORS ilkesinde ayarlanabilecek çeşitli seçenekler açıklanmaktadır:

* [İzin verilen kökenleri ayarlama](#set-the-allowed-origins)
* [İzin verilen HTTP yöntemlerini ayarlama](#set-the-allowed-http-methods)
* [İzin verilen istek üstbilgisini ayarlama](#set-the-allowed-request-headers)
* [Açıkta kalan yanıt üstaylarını ayarlama](#set-the-exposed-response-headers)
* [Orijinler arası isteklerde kimlik bilgileri](#credentials-in-cross-origin-requests)
* [Uçuş öncesi son kullanma süresini ayarlama](#set-the-preflight-expiration-time)

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>olarak `Startup.ConfigureServices`adlandırılır. Bazı seçenekler için, [önce CORS nasıl çalışır](#how-cors) bölümünü okumak yararlı olabilir.

## <a name="set-the-allowed-origins"></a>İzin verilen kökenleri ayarlama

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>&ndash; Herhangi bir şema (veya)`http` `https`ile tüm kökenlerden CORS isteklerini sağlar. `AllowAnyOrigin`herhangi bir *web sitesi* uygulamaya çapraz orijin isteklerinde bulunabileceğinden güvenli değildir.

> [!NOTE]
> Belirtilmesi `AllowAnyOrigin` `AllowCredentials` ve güvenli olmayan bir yapılandırmadır ve site ler arası istek sahteciliğiyle sonuçlanabilir. Güvenli bir uygulama için, istemcinin sunucu kaynaklarına erişmek için kendisini yetkilendirmesi gerekiyorsa, tam bir kaynak listesi belirtin.

`AllowAnyOrigin`ön kontrol isteklerini `Access-Control-Allow-Origin` ve üstbilgietkiler. Daha fazla bilgi için [Ön Uçuş istekleri](#preflight-requests) bölümüne bakın.

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>&ndash; İlkenin <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> özelliğini, kaynağın izin verilip verilmediğini değerlendirirken, kökenlerin yapılandırılmış bir joker etki alanıyla eşleşmesine olanak tanıyan bir işlev olarak ayarlar.

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-105&highlight=4-5)]

### <a name="set-the-allowed-http-methods"></a>İzin verilen HTTP yöntemlerini ayarlama

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:

* Herhangi bir HTTP yöntemine izin verir:
* Ön kontrol isteklerini `Access-Control-Allow-Methods` ve üstbilgiyi etkiler. Daha fazla bilgi için [Ön Uçuş istekleri](#preflight-requests) bölümüne bakın.

### <a name="set-the-allowed-request-headers"></a>İzin verilen istek üstbilgisini ayarlama

Belirli üstbilginin CORS isteğinde gönderilmesine izin vermek için <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> yazar *istek üstbilgisini*arayın, arayın ve izin verilen üstbilgibelirtin:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

Tüm yazar istek üstbilgilerine <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>izin vermek için:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

Bu ayar, ön kontrol `Access-Control-Request-Headers` isteklerini ve üstbilgiyi etkiler. Daha fazla bilgi için [Ön Uçuş istekleri](#preflight-requests) bölümüne bakın.

CORS Middleware her zaman CorsPolicy.Headers yapılandırılan değerlerne bakılmaksızın gönderilmesi `Access-Control-Request-Headers` için dört üstbilgi sağlar. Bu üstbilgi listesi şunları içerir:

* `Accept`
* `Accept-Language`
* `Content-Language`
* `Origin`

Örneğin, aşağıdaki gibi yapılandırılan bir uygulamayı düşünün:

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

CORS Middleware, her zaman beyaz listeye alındığı için ön `Content-Language` kontrol isteğine aşağıdaki istek üstbilgisiyle başarıyla yanıt verir:

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

### <a name="set-the-exposed-response-headers"></a>Açıkta kalan yanıt üstaylarını ayarlama

Varsayılan olarak, tarayıcı tüm yanıt üstbilgisini uygulamaya maruz bırakmaz. Daha fazla bilgi için [W3C Cross-Origin Kaynak Paylaşımı (Terminoloji): Basit Yanıt Üstbilgisi'ne](https://www.w3.org/TR/cors/#simple-response-header)bakın.

Varsayılan olarak kullanılabilir yanıt üstbilgi:

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

CORS belirtimi bu üstbilgi *basit yanıt üstbilgi çağırır.* Diğer üstbilgileri uygulamaiçin kullanılabilir <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>hale getirmek için aşağıdakileri arayın:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a>Orijinler arası isteklerde kimlik bilgileri

Kimlik bilgileri, CORS isteğinde özel kullanım gerektirir. Varsayılan olarak, tarayıcı bir çapraz kaynak isteği ile kimlik bilgileri göndermez. Kimlik bilgileri çerezleri ve HTTP kimlik doğrulama şemalarını içerir. Bir çapraz kaynak isteği ile kimlik bilgileri `XMLHttpRequest.withCredentials` göndermek `true`için, istemci nin .

Doğrudan `XMLHttpRequest` kullanma:

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

jQuery kullanma:

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

Getir [API'sini](https://developer.mozilla.org/docs/Web/API/Fetch_API)kullanma :

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

Sunucu kimlik bilgilerine izin vermelidir. Orijinler arası kimlik <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>bilgilerine izin vermek için:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

HTTP yanıtı, `Access-Control-Allow-Credentials` tarayıcıya sunucunun bir çapraz kaynak isteği için kimlik bilgilerine izin verdiğini söyleyen bir üstbilgi içerir.

Tarayıcı kimlik bilgileri gönderir, ancak yanıt geçerli `Access-Control-Allow-Credentials` bir üstbilgi içermiyorsa, tarayıcı uygulamaya yanıtı açıklamaz ve çapraz kaynak isteği başarısız olur.

Orjinal kimlik bilgilerine izin vermek bir güvenlik riskidir. Başka bir etki alanında bulunan bir web sitesi, kullanıcının bilgisi olmadan kullanıcı adına oturum açmış bir kullanıcının kimlik bilgilerini uygulamaya gönderebilir. <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

CORS belirtimi, üstbilgi `"*"` varsa(tüm kökenlerin) kaynağını ayarlamanın `Access-Control-Allow-Credentials` geçersiz olduğunu da belirtir.

### <a name="preflight-requests"></a>Ön kontrol istekleri

Bazı CORS istekleri için tarayıcı, gerçek isteği yapmadan önce ek bir istek gönderir. Bu istek *bir ön kontrol isteği*olarak adlandırılır. Aşağıdaki koşullar doğruysa, tarayıcı ön kontrol isteğini atlayabilir:

* İstek yöntemi GET, HEAD veya POST'dur.
* Uygulama, istek `Accept`üstbilgisini , , `Accept-Language` `Content-Language`, `Content-Type`veya `Last-Event-ID`.
* Üstbilgi, `Content-Type` küme, aşağıdaki değerlerden birine sahiptir:
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

İstemci isteği için ayarlanan istek üstbilgilerine ilişkin kural, `setRequestHeader` `XMLHttpRequest` uygulamanın nesneyi çağırarak ayarladığını üstbilgi için geçerlidir. CORS belirtimi bu üstbilgi *yazar istek üstbilgi*çağırır. Kural, tarayıcının ayarlayabileceği `User-Agent`, , `Host`veya `Content-Length`.

Aşağıda, ön kontrol isteğine bir örnek verilmiştir:

```
OPTIONS https://myservice.azurewebsites.net/api/test HTTP/1.1
Accept: */*
Origin: https://myclient.azurewebsites.net
Access-Control-Request-Method: PUT
Access-Control-Request-Headers: accept, x-my-custom-header
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.2; WOW64; Trident/6.0)
Host: myservice.azurewebsites.net
Content-Length: 0
```

Uçuş öncesi istek HTTP OPTIONS yöntemini kullanır. İki özel üstbilgi içerir:

* `Access-Control-Request-Method`: Gerçek istek için kullanılacak HTTP yöntemi.
* `Access-Control-Request-Headers`: Uygulamanın gerçek istek üzerine ayarladığının istek üstbilgilerinin listesi. Daha önce belirtildiği gibi, `User-Agent`bu, tarayıcının belirlediği üstleri içermez.

<!-- I think this needs to be removed, was put here accidently -->

CORS uygun ilke ile etkinleştirildiğinde, ASP.NET Core genellikle CORS uçuş öncesi isteklerine otomatik olarak yanıt verir. [Ön uçuş istekleri için [HttpOptions] özniteliğine](#pro)bakın.

CORS ön kontrol isteği, `Access-Control-Request-Headers` sunucuya gerçek istekle birlikte gönderilen üstbilgileri gösteren bir üstbilgi içerebilir.

Belirli üstbilgi için: <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

Tüm yazar istek üstbilgilerine <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>izin vermek için:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

Tarayıcılar, nasıl ayarlandıkları `Access-Control-Request-Headers`konusunda tamamen tutarlı değildir. Üstbilginizi (veya kullanım) `"*"` <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>dışında başka bir şeye `Accept`ayarlarsanız, en az , `Content-Type`ve `Origin`, artı desteklemek istediğiniz özel üstbilgi içermelidir.

Aşağıda, ön kontrol isteğine örnek bir yanıt verilmiştir (sunucunun isteğe izin verdiğini varsayarsak):

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 0
Access-Control-Allow-Origin: https://myclient.azurewebsites.net
Access-Control-Allow-Headers: x-my-custom-header
Access-Control-Allow-Methods: PUT
Date: Wed, 20 May 2015 06:33:22 GMT
```

Yanıt, izin `Access-Control-Allow-Methods` verilen yöntemleri ve isteğe bağlı `Access-Control-Allow-Headers` olarak izin verilen üstbilgilisteleyen bir üstbilgi içeren bir üstbilgi içerir. Ön kontrol isteği başarılı olursa, tarayıcı gerçek isteği gönderir.

Ön kontrol isteği reddedilirse, uygulama *200 Tamam* yanıtını döndürür, ancak CORS üstbilgilerini geri göndermez. Bu nedenle, tarayıcı çapraz kaynak isteği denemez.

### <a name="set-the-preflight-expiration-time"></a>Uçuş öncesi son kullanma süresini ayarlama

Üstbilgi, `Access-Control-Max-Age` ön kontrol isteğine verilen yanıtın ne kadar süreyle önbelleğe alınabileceğini belirtir. Bu üstbilgi ayarlamak <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>için:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=91-96&highlight=5)]

<a name="how-cors"></a>

## <a name="how-cors-works"></a>CORS nasıl çalışır?

Bu bölümde, HTTP iletileri düzeyinde bir [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) isteğine neler olduğu açıklanmaktadır.

* CORS bir güvenlik özelliği **değildir.** CORS, bir sunucunun aynı orijin ilkesini gevşetmesine olanak tanıyan bir W3C standardıdır.
  * Örneğin, kötü niyetli bir aktör sitenize karşı [Site Ötesi Komut Dosyasını (XSS) engelleyebilir](xref:security/cross-site-scripting) ve CORS özellikli sitesine bilgi çalmak için bir çapraz site isteği gerçekleştirebilir.
* CORS'e izin vererek API'niz daha güvenli değildir.
  * CORS'i uygulamak istemciye (tarayıcıya) kalmış. Sunucu isteği yürütür ve yanıtı döndürür, bir hata döndürür ve yanıtı engeller istemcisidir. Örneğin, aşağıdaki araçlardan herhangi biri sunucu yanıtını görüntüler:
    * [Fiddler](https://www.telerik.com/fiddler)
    * [Postman](https://www.getpostman.com/)
    * [.NET Https://yayla](/dotnet/csharp/tutorials/console-webapiclient)
    * Adres çubuğuna URL girerek bir web tarayıcısı.
* Bu, bir sunucunun tarayıcıların aksi takdirde yasaklanacak bir çapraz kökenli [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) veya [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) isteğini yürütmesine izin vermenin bir yoludur.
  * Tarayıcılar (CORS olmadan) çapraz kökenli isteklerde olamaz. CORS önce, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) bu kısıtlamayı atlatmak için kullanılmıştır. JSONP XHR kullanmaz, yanıtı `<script>` almak için etiketi kullanır. Komut dosyalarıçapraz kökenli yüklenebilir.

[CORS belirtimi,](https://www.w3.org/TR/cors/) orijinler arası istekleri etkinleştiren birkaç yeni HTTP üstbilgisini tanıttı. Bir tarayıcı CORS'i destekliyorsa, bu başlıkları çapraz başlangıç istekleri için otomatik olarak ayarlar. CORS'i etkinleştirmek için özel JavaScript kodu gerekmez.

Aşağıda, bir çapraz kaynak isteği örneği verilmiştir. Üstbilgi, `Origin` isteği yapan sitenin etki alanını sağlar. Üstbilgi `Origin` gereklidir ve ana bilgisayardan farklı olmalıdır.

```
GET https://myservice.azurewebsites.net/api/test HTTP/1.1
Referer: https://myclient.azurewebsites.net/
Accept: */*
Accept-Language: en-US
Origin: https://myclient.azurewebsites.net
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.2; WOW64; Trident/6.0)
Host: myservice.azurewebsites.net
```

Sunucu isteğe izin veriyorsa, yanıttaki üstbilgiyi `Access-Control-Allow-Origin` ayarlar. Bu üstbilginin değeri, `Origin` istekteki üstbilgiyle eşleşir veya `"*"`joker karakter değeridir, yani herhangi bir menşee izin verilir:

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Type: text/plain; charset=utf-8
Access-Control-Allow-Origin: https://myclient.azurewebsites.net
Date: Wed, 20 May 2015 06:27:30 GMT
Content-Length: 12

Test message
```

Yanıt `Access-Control-Allow-Origin` üstbilgi içermiyorsa, çapraz kaynak isteği başarısız olur. Özellikle, tarayıcı isteği izin verir. Sunucu başarılı bir yanıt döndürse bile, tarayıcı yanıtı istemci uygulamasına sunmaz.

<a name="test"></a>

## <a name="test-cors"></a>Test CORS

CORS'u test etmek için:

1. [Bir API projesi oluşturun.](xref:tutorials/first-web-api) Alternatif olarak, [örnek indirebilirsiniz.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors)
1. Bu belgedeki yaklaşımlardan birini kullanarak CORS'i etkinleştirin. Örneğin:

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]

  > [!WARNING]
  > `WithOrigins("https://localhost:<port>");`yalnızca [indirme örnek koduna](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors)benzer bir örnek uygulamayı test etmek için kullanılmalıdır.

1. Bir web uygulaması projesi (Razor Pages veya MVC) oluşturun. Örnekte Razor Pages kullanır. Web uygulamasını API projesiyle aynı çözümde oluşturabilirsiniz.
1. *Index.cshtml* dosyasına aşağıdaki vurgulanmış kodu ekleyin:

  [!code-csharp[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. Önceki kodda, dağıtılan `url: 'https://<web app>.azurewebsites.net/api/values/1',` uygulamanın URL'si ile değiştirin.
1. API projesini dağıtın. Örneğin, [Azure'a dağıtın.](xref:host-and-deploy/azure-apps/index)
1. Masaüstünden Razor Pages veya MVC uygulamasını çalıştırın ve **Test** düğmesine tıklayın. Hata iletilerini gözden geçirmek için F12 araçlarını kullanın.
1. Yerel ana bilgisayar `WithOrigins` kaynağını kaldırın ve uygulamayı dağıtın. Alternatif olarak, istemci uygulamasını farklı bir bağlantı noktasıyla çalıştırın. Örneğin, Visual Studio çalıştırın.
1. İstemci uygulaması yla test edin. CORS hataları bir hata döndürer, ancak hata iletisi JavaScript için kullanılamaz. Hatayı görmek için F12 araçlarındaki konsol sekmesini kullanın. Tarayıcıya bağlı olarak, aşağıdakilere benzer bir hata (F12 araçları konsolunda) alırsınız:

   * Microsoft Edge'i kullanma:

     **SEC7120: [CORS] `https://localhost:44375` Menşei, erişim kaynağı kaynağı için Access-Control-Allow-Origin yanıt `https://localhost:44375` başlığında`https://webapi.azurewebsites.net/api/values/1`**

   * Chrome'u kullanma:

     **XMLHttpRequest'e `https://webapi.azurewebsites.net/api/values/1` orijinli `https://localhost:44375` erişim CORS ilkesi tarafından engellendi: İstenen kaynakta 'Access-Control-Allow-Origin' başlığı bulunmamaktadır.**
     
CORS özellikli uç [noktalar, Kemancı](https://www.telerik.com/fiddler) veya [Postacı](https://www.getpostman.com/)gibi bir araçla test edilebilir. Bir araç kullanırken, `Origin` üstbilgi tarafından belirtilen isteğin kaynağı, isteği alan ana bilgisayardan farklı olmalıdır. Üstbilginin değerine *cross-origin* `Origin` göre istek çapraz kökenli değilse:

* CORS Middleware'in isteği işlemesine gerek yoktur.
* CORS başlıkları yanıt olarak döndürülür.

## <a name="cors-in-iis"></a>CORS in IIS

IIS'ye dağıtılırken, sunucu anonim erişime izin verecek şekilde yapılandırılmamışsa CORS'in Windows Kimlik Doğrulama'dan önce çalışması gerekiyor. Bu senaryoyu desteklemek için [IIS CORS modülünün](https://www.iis.net/downloads/microsoft/iis-cors-module) uygulama için yüklenmesi ve yapılandırılması gerekir.

## <a name="additional-resources"></a>Ek kaynaklar

* [Çıkış Noktaları Arası Kaynak Paylaşımı (CORS)](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [IIS CORS modülü ile başlarken](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end
