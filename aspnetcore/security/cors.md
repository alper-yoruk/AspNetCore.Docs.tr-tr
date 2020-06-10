---
title: ASP.NET Core 'de çıkış noktaları arası Istekleri (CORS) etkinleştirme
author: rick-anderson
description: ASP.NET Core uygulamasında çapraz kaynak isteklerini izin vermek veya reddetmek için CORS 'nin nasıl standart olduğunu öğrenin.
ms.author: riande
ms.custom: mvc
ms.date: 04/17/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/cors
ms.openlocfilehash: a78aff2d2e16f36ed034e6af110d7ed763271583
ms.sourcegitcommit: 6a71b560d897e13ad5b61d07afe4fcb57f8ef6dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84105759"
---
# <a name="enable-cross-origin-requests-cors-in-aspnet-core"></a>ASP.NET Core 'de çıkış noktaları arası Istekleri (CORS) etkinleştirme

::: moniker range=">= aspnetcore-3.0"

Tarafından [Rick Anderson](https://twitter.com/RickAndMSFT) ve [Kirk larkabağı](https://twitter.com/serpent5)

Bu makalede, ASP.NET Core uygulamasında CORS 'nin nasıl etkinleştirileceği gösterilmektedir.

Tarayıcı güvenliği, bir Web sayfasının Web sayfasını sunduğundan farklı bir etki alanına istek yapmasını engeller. Bu kısıtlamaya *aynı-Origin ilkesi*adı verilir. Aynı-kaynak ilkesi, kötü niyetli bir sitenin gizli verileri başka bir siteden okumasını engeller. Bazen diğer sitelerin uygulamanıza çapraz çıkış istekleri yapmasına izin vermek isteyebilirsiniz. Daha fazla bilgi için bkz. [MOZILLA CORS makalesi](https://developer.mozilla.org/docs/Web/HTTP/CORS).

[Çapraz kaynak kaynak paylaşımı](https://www.w3.org/TR/cors/) (CORS):

* , Bir sunucunun aynı kaynak ilkeyi rahat bir şekilde sağlamasına izin veren bir W3C standardıdır.
* Güvenlik özelliği **değil** , CORS güvenliği. CORS 'nin izin vermesini sağlayan bir API daha güvenli değildir. Daha fazla bilgi için bkz. [CORS çalışma](#how-cors).
* Bir sunucunun bazı çapraz kaynak isteklerine, diğerlerini reddetirken açık olarak izin almasına izin verir.
* , [JSONP](/dotnet/framework/wcf/samples/jsonp)gibi önceki tekniklerin daha güvenli ve daha esnektir.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="same-origin"></a>Aynı kaynak

Özdeş şemaları, konakları ve bağlantı noktalarına sahip olmaları durumunda iki URL aynı kaynağa sahiptir ([RFC 6454](https://tools.ietf.org/html/rfc6454)).

Bu iki URL aynı kaynağa sahiptir:

* `https://example.com/foo.html`
* `https://example.com/bar.html`

Bu URL 'Ler, önceki iki URL 'den farklı kaynaklardan farklıdır:

* `https://example.net`: Farklı etki alanı
* `https://www.example.com/foo.html`: Farklı alt etki alanı
* `http://example.com/foo.html`: Farklı düzen
* `https://example.com:9000/foo.html`: Farklı bağlantı noktası

## <a name="enable-cors"></a>CORS'yi etkinleştirme

CORS 'yi etkinleştirmenin üç yolu vardır:

* [Adlandırılmış bir ilke](#np) veya [varsayılan ilke](#dp)kullanan ara yazılımlar.
* [Uç nokta yönlendirme](#ecors)kullanılıyor.
* [[Enablecors]](#attr) özniteliğiyle birlikte.

Adlandırılmış ilkeyle [[Enablecors]](#attr) ÖZNITELIĞINI kullanmak CORS 'yi destekleyen uç noktaları sınırlayan en yoğun denetimi sağlar.

Her yaklaşım aşağıdaki bölümlerde ayrıntılı olarak verilmiştir.

<a name="np"></a>

## <a name="cors-with-named-policy-and-middleware"></a>Adlandırılmış ilke ve ara yazılım ile CORS

CORS ara yazılımı, çıkış noktaları arası istekleri işler. Aşağıdaki kod, belirtilen kaynakları kullanarak tüm uygulamanın uç noktalarına bir CORS ilkesi uygular:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=3,9,31)]

Yukarıdaki kod:

* İlke adını olarak ayarlar `_myAllowSpecificOrigins` . İlke adı rastgele olur.
* <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*>Uzantı yöntemini çağırır ve `_myAllowSpecificOrigins` CORS ilkesini belirtir. `UseCors`CORS ara yazılımını ekler. Çağrısının `UseCors` sonra `UseRouting` , öncesi yerleştirilmesi gerekir `UseAuthorization` . Daha fazla bilgi için bkz. [Ara yazılım sırası](xref:fundamentals/middleware/index#middleware-order).
* <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> [Lambda ifadesi](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)ile çağrılar. Lambda bir nesnesi alır <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> . Gibi [yapılandırma seçenekleri](#cors-policy-options), `WithOrigins` Bu makalenin ilerleyen kısımlarında açıklanmıştır.
* `_myAllowSpecificOrigins`Tüm denetleyici uç noktaları için CORS ilkesini etkinleştirilir. Belirli uç noktalara bir CORS ilkesi uygulamak için bkz. [Endpoint Routing](#ecors) .

Endpoint Routing ile **CORS ara yazılımı** , ve çağrıları arasında yürütülecek şekilde yapılandırılmalıdır `UseRouting` `UseEndpoints` .

Yukarıdaki koda benzer test kodu hakkında yönergeler için bkz. [Test CORS](#testc) .

<xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*>Yöntem çağrısı, uygulamanın hizmet KAPSAYıCıSıNA CORS Hizmetleri ekler:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet2)]

Daha fazla bilgi için bu belgedeki [CORS ilke seçenekleri](#cpo) bölümüne bakın.

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder>Yöntemler aşağıdaki kodda gösterildiği gibi zincirleme olabilir:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup2.cs?name=snippet)]

Not: belirtilen URL sonunda eğik **not** çizgi ( `/` ) içermemelidir. URL ile sonlandığında `/` karşılaştırma döndürülür `false` ve üst bilgi döndürülmez.

<a name="dp"></a>

### <a name="cors-with-default-policy-and-middleware"></a>Varsayılan ilke ve ara yazılım ile CORS

Aşağıdaki vurgulanan kod varsayılan CORS ilkesini sunar:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupDefaultPolicy.cs?name=snippet2&highlight=7,29)]

Yukarıdaki kod, tüm denetleyici uç noktalarına varsayılan CORS ilkesini uygular.

<a name="ecors"></a>

## <a name="enable-cors-with-endpoint-routing"></a>Uç nokta yönlendirme ile CORS 'yi etkinleştirme

Şu anda bir uç nokta temelinde CORS etkinleştirildiğinde `RequireCors` [Otomatik ön kontrol istekleri](#apf) **desteklenmez** . Daha fazla bilgi için [Bu GitHub sorununa](https://github.com/dotnet/aspnetcore/issues/20709) bakın ve [uç nokta yönlendirme ve [HttpOptions] ile test CORS 'yi test](#tcer)edin.

Uç nokta yönlendirmesinde, CORS uzantı yöntemleri kümesi kullanılarak uç nokta temelinde etkinleştirilebilir <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> :

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPt.cs?name=snippet2&highlight=3,7-15,32,40,43)]

Yukarıdaki kodda:

* `app.UseCors`CORS ara yazılımını sunar. Varsayılan bir ilke yapılandırılmadığından, `app.UseCors()` tek BAŞıNA CORS 'yi etkinleştirmeyin.
* `/echo`Ve denetleyici uç noktaları, belirtilen ilkeyi kullanarak çıkış noktaları arası isteklere izin verir.
* `/echo2` Razor Varsayılan ilke belirtilmediğinden ve sayfa uç noktaları, çıkış noktaları arası isteklere izin **vermez** .

[[Disablecors]](#dc) özniteliği, ile Endpoint Routing tarafından etkinleştirilen CORS 'yi devre dışı **bırakmıyor** `RequireCors` .

Yukarıdaki koda benzer test kodu hakkında yönergeler için bkz. [Endpoint Routing Ile test CORS ve [HttpOptions]](#tcer) .

<a name="attr"></a>

## <a name="enable-cors-with-attributes"></a>CORS 'yi özniteliklerle etkinleştir

CORS 'yi [[Enablecors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) özniteliğiyle etkinleştirmek ve yalnızca CORS gerektiren uç noktalara adlandırılmış bir ilke uygulamak, en derin denetimi sağlar.

[[Enablecors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) ÖZNITELIĞI, CORS 'yi küresel olarak uygulamaya bir alternatif sağlar. `[EnableCors]`Öznitelik, tüm uç noktalar yerine seçili uç noktalar IÇIN CORS 'yi sunar:

* `[EnableCors]`varsayılan ilkeyi belirtir.
* `[EnableCors("{Policy String}")]`adlandırılmış bir ilke belirtir.

`[EnableCors]`Özniteliği öğesine uygulanabilir:

* RazorSayfasında`PageModel`
* Kumandasını
* Denetleyici eylemi yöntemi

Öznitelikleri olan denetleyicilere, sayfa modellerine veya eylem yöntemlerine farklı ilkeler uygulanabilir `[EnableCors]` . `[EnableCors]`Öznitelik bir denetleyiciye, sayfa modeline veya eylem yöntemine uygulandığında ve bir ara yazılım IÇINDE CORS etkinleştirildiğinde, **her iki ilke de** uygulanır. **İlkelerin birleştirilmesi önerilir. ** `[EnableCors]` **Aynı uygulamada değil, özniteliğini veya ara yazılımını kullanın.**

Aşağıdaki kod her bir yönteme farklı bir ilke uygular:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

Aşağıdaki kod iki CORS ilkesi oluşturur:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup3.cs?name=snippet&highlight=12-28,44)]

CORS isteklerini sınırlayan en yoğun denetim için:

* `[EnableCors("MyPolicy")]`Adlandırılmış ilkeyle kullanın.
* Varsayılan bir ilke tanımlamayın.
* [Endpoint Routing](#ecors)kullanmayın.

Sonraki bölümdeki kod, önceki listeyi karşılar.

Yukarıdaki koda benzer test kodu hakkında yönergeler için bkz. [Test CORS](#testc) .

<a name="dc"></a>

### <a name="disable-cors"></a>CORS 'yi devre dışı bırak

[[Disablecors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) özniteliği [Endpoint ROUTING](#ecors)tarafından etkinleştirilen CORS 'yi devre dışı **bırakmıyor** .

Aşağıdaki kod CORS ilkesini tanımlar `"MyPolicy"` :

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTestMyPolicy.cs?name=snippet)]

Aşağıdaki kod, eylem için CORS 'yi devre dışı bırakır `GetValues2` :

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet&highlight=1,23)]

Yukarıdaki kod:

* , [Uç nokta yönlendirme](#ecors)ile CORS 'yi etkinleştirmez.
* [Varsayılan BIR CORS ilkesi](#dp)tanımlamaz.
* Denetleyicinin CORS ilkesini etkinleştirmek için [[Enablecors ("MyPolicy")]](#attr) kullanır `"MyPolicy"` .
* Yöntemi için CORS 'yi devre dışı bırakır `GetValues2` .

Önceki kodun test edilmesine ilişkin yönergeler için bkz. [Test CORS](#testc) .

<a name="cpo"></a>

## <a name="cors-policy-options"></a>CORS ilke seçenekleri

Bu bölümde, bir CORS ilkesinde ayarlanmakta olabilecek çeşitli seçenekler açıklanmaktadır:

* [İzin verilen kaynakları ayarla](#set-the-allowed-origins)
* [İzin verilen HTTP yöntemlerini ayarlama](#set-the-allowed-http-methods)
* [İzin verilen istek üst bilgilerini ayarlama](#set-the-allowed-request-headers)
* [Gösterilen yanıt üst bilgilerini ayarlama](#set-the-exposed-response-headers)
* [Kaynaklar arası isteklerde kimlik bilgileri](#credentials-in-cross-origin-requests)
* [Ön kontrol sona erme süresini ayarlama](#set-the-preflight-expiration-time)

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>içinde çağırılır `Startup.ConfigureServices` . Bazı seçenekler için, ilk olarak [CORS 'Nin nasıl çalıştığı](#how-cors) bölümü okumanız yararlı olabilir.

## <a name="set-the-allowed-origins"></a>İzin verilen kaynakları ayarla

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: Herhangi bir düzen (veya) ile tüm kaynaklardan gelen CORS isteklerine izin verir `http` `https` . `AllowAnyOrigin`, *herhangi bir Web sitesi* uygulamaya çapraz kaynak istekleri yapabildiğinden güvenli değildir.

> [!NOTE]
> `AllowAnyOrigin`Ve `AllowCredentials` güvenli olmayan bir yapılandırma belirtip, siteler arası istek sahteciliği ile sonuçlanabilir. Bir uygulama her iki yöntemle yapılandırıldığında, CORS hizmeti geçersiz bir CORS yanıtı döndürür.

`AllowAnyOrigin`ön kontrol isteklerini ve `Access-Control-Allow-Origin` üstbilgiyi etkiler. Daha fazla bilgi için bkz. [ön kontrol istekleri](#preflight-requests) bölümü.

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>: <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> Kaynağa izin verilip verilmediğini değerlendirirken, kaynağın yapılandırılmış bir joker karakterle eşleşmesini sağlayan bir işlev olarak ilkenin özelliğini ayarlar.

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet)]

### <a name="set-the-allowed-http-methods"></a>İzin verilen HTTP yöntemlerini ayarlama

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:

* Herhangi bir HTTP yöntemine izin verir:
* Ön kontrol isteklerini ve `Access-Control-Allow-Methods` üstbilgiyi etkiler. Daha fazla bilgi için bkz. [ön kontrol istekleri](#preflight-requests) bölümü.

### <a name="set-the-allowed-request-headers"></a>İzin verilen istek üst bilgilerini ayarlama

Belirli başlıkların, [Yazar isteği üstbilgileri](https://xhr.spec.whatwg.org/#request)ADLı bir CORS isteğinde gönderilmesine izin vermek için, ' i çağırın <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> ve izin verilen üst bilgileri belirtin:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

Tüm [Yazar isteği başlıklarına](https://www.w3.org/TR/cors/#author-request-headers)izin vermek için şunu çağırın <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> :

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

`AllowAnyHeader`ön kontrol isteklerini ve [erişim-denetim-istek-üst](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method) bilgi üst bilgisini etkiler. Daha fazla bilgi için bkz. [ön kontrol istekleri](#preflight-requests) bölümü.

Tarafından belirtilen belirli başlıklarıyla eşleşen bir CORS ara yazılım ilkesi, `WithHeaders` yalnızca `Access-Control-Request-Headers` ' de belirtilen üstbilgiler ile tam olarak eşleşiyorsa mümkündür `WithHeaders` .

Örneğin, aşağıdaki gibi yapılandırılmış bir uygulamayı göz önünde bulundurun:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet4)]

CORS ara yazılımı, `Content-Language` ([headernames. contentlanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) içinde listelenmediği için aşağıdaki istek üst bilgisi ile bir ön denetim isteğini reddettiğinde `WithHeaders` :

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

Uygulama *200 ok* yanıtı DÖNDÜRÜYOR ancak CORS üst bilgilerini geri göndermez. Bu nedenle tarayıcı, çıkış noktaları arası isteği denemez.

### <a name="set-the-exposed-response-headers"></a>Gösterilen yanıt üst bilgilerini ayarlama

Varsayılan olarak tarayıcı, tüm yanıt üst bilgilerini uygulamaya sunmaz. Daha fazla bilgi için bkz. [W3C çıkış noktaları arası kaynak paylaşımı (terminoloji): basit yanıt üst bilgisi](https://www.w3.org/TR/cors/#simple-response-header).

Varsayılan olarak kullanılabilen yanıt üstbilgileri şunlardır:

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

CORS belirtimi, bu üst bilgiler *basit yanıt üst bilgilerini*çağırır. Diğer üst bilgileri uygulama için kullanılabilir hale getirmek için şunu çağırın <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*> :

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet5)]
### <a name="credentials-in-cross-origin-requests"></a>Kaynaklar arası isteklerde kimlik bilgileri

Kimlik bilgileri CORS isteğinde özel işleme gerektirir. Varsayılan olarak tarayıcı, kimlik bilgilerini bir çapraz kaynak isteğiyle göndermez. Kimlik bilgileri, tanımlama bilgileri ve HTTP kimlik doğrulama düzenlerini içerir. Bir çapraz kaynak isteğiyle kimlik bilgilerini göndermek için, istemcisinin olarak ayarlanması gerekir `XMLHttpRequest.withCredentials` `true` .

`XMLHttpRequest`Doğrudan kullanarak:

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

JQuery kullanarak:

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

[Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)'sini kullanma:

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

Sunucu kimlik bilgilerine izin vermelidir. Çıkış noktaları arası kimlik bilgilerine izin vermek için şunu arayın <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*> :

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet6)]

HTTP yanıtı, `Access-Control-Allow-Credentials` tarayıcıya sunucunun bir çapraz kaynak isteği için kimlik bilgileri verdiğini bildiren bir üst bilgi içerir.

Tarayıcı kimlik bilgilerini gönderirse ancak yanıt geçerli bir `Access-Control-Allow-Credentials` üst bilgi içermiyorsa, tarayıcı uygulamaya yanıtı kullanıma sunmaz ve çapraz kaynak isteği başarısız olur.

Çıkış noktaları arası kimlik bilgilerine izin vermek bir güvenlik riskidir. Başka bir etki alanındaki Web sitesi, kullanıcının bilgisi olmadan kullanıcı adına, oturum açmış bir kullanıcının kimlik bilgilerini uygulamaya gönderebilir. <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

CORS belirtimi Ayrıca `"*"` üst bilgi varsa, çıkış (tüm kaynaklar) ayarının geçersiz olduğunu belirtir `Access-Control-Allow-Credentials` .

<a name="pref"></a>

## <a name="preflight-requests"></a>Ön kontrol istekleri

Bazı CORS istekleri için, tarayıcı gerçek isteği yapmadan önce ek bir [seçenek](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) isteği gönderir. Bu isteğe bir [ön kontrol isteği](https://developer.mozilla.org/docs/Glossary/Preflight_request)denir. Aşağıdaki koşulların **Tümü** doğruysa tarayıcı, ön kontrol isteğini atlayabilir:

* İstek yöntemi al, HEAD veya POST.
* Uygulama,,, veya dışındaki istek üst bilgilerini ayarlanmamış `Accept` `Accept-Language` `Content-Language` `Content-Type` `Last-Event-ID` .
* , `Content-Type` Ayarlandıysa, aşağıdaki değerlerden birine sahip olan üst bilgi:
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

İstemci isteği için ayarlanan istek üst bilgileri kuralı, uygulamanın, nesne üzerinde çağırarak ayarladığı üst bilgiler için geçerlidir `setRequestHeader` `XMLHttpRequest` . CORS belirtimi, bu üst bilgiler [Yazar istek üst bilgilerini](https://www.w3.org/TR/cors/#author-request-headers)çağırır. Kural,, veya gibi tarayıcının ayarlayabilmesi için, veya gibi bir üst bilgiye uygulanmaz `User-Agent` `Host` `Content-Length` .

Aşağıda, bu belgenin [Test CORS](#testc) bölümündeki **[testi koy]** düğmesinden yapılmış olan ön kontrol isteğine benzer bir yanıt verilmiştir.

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

Ön kontrol isteği [http seçenekleri](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) yöntemini kullanır. Aşağıdaki üstbilgiler bulunabilir:

* [Access-Control-Request-Method](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): gerçek istek IÇIN kullanılacak http yöntemi.
* [Erişim-denetim-istek-üstbilgiler](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers): uygulamanın gerçek istekte ayarladığı istek üst bilgilerinin bir listesi. Daha önce belirtildiği gibi, bu, tarayıcının ayarladığı üst bilgileri içermez `User-Agent` .
* [Erişim-denetim-Izin-Yöntemler](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Methods)

Ön kontrol isteği reddedilirse, uygulama bir `200 OK` yanıt döndürür ancak CORS üst bilgilerini yapmaz. Bu nedenle tarayıcı, çıkış noktaları arası isteği denemez. Reddedilen bir ön kontrol isteğine bir örnek için, bu belgenin [Test CORS](#testc) bölümüne bakın.

Konsol uygulaması, F12 araçlarını kullanarak tarayıcıya bağlı olarak aşağıdakilerden birine benzer bir hata gösterir:

* Firefox: çapraz kaynak Isteği engellendi: aynı kaynak Ilkesi, konumundaki uzak kaynağı okumaktan izin vermez `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5` . (Neden: CORS isteği başarılı olmadı). [Daha Fazla Bilgi](https://developer.mozilla.org/docs/Web/HTTP/CORS/Errors/CORSDidNotSucceed)
* Kmıum tabanlı: ' ' kaynağından ' ' konumundaki getirme erişimi https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5 https://cors3.azurewebsites.net CORS ilkesi tarafından engellendi: ön kontrol Isteğine verilen yanıt erişim denetimi denetimine geçti: İstenen kaynakta ' Access-Control-Allow-Origin ' üst bilgisi yok. Donuk bir yanıt ihtiyaçlarınıza hizmet veriyorsa, isteği CORS devre dışı olarak getirmek için isteğin modunu ' No-CORS ' olarak ayarlayın.

Belirli üstbilgilere izin vermek için şunu arayın <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> :

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

Tüm [Yazar isteği başlıklarına](https://www.w3.org/TR/cors/#author-request-headers)izin vermek için şunu çağırın <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> :

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

Tarayıcılar, nasıl ayarlandıklarından tutarlı değildir `Access-Control-Request-Headers` . Aşağıdakilerden biri:

* Üst bilgiler, dışında bir şeye ayarlanır`"*"`
* <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>çağrıldı: en az `Accept` , `Content-Type` , ve, ve `Origin` desteklemek istediğiniz tüm özel üstbilgileri ekleyin.

<a name="apf"></a>

### <a name="automatic-preflight-request-code"></a>Otomatik ön kontrol istek kodu

CORS ilkesi uygulandığında:

* ' İ çağırarak küresel olarak `app.UseCors` `Startup.Configure` .
* Özniteliği kullanılıyor `[EnableCors]` .

ASP.NET Core, ön kontrol SEÇENEKLERI isteğine yanıt verir.

Şu anda bir uç nokta temelinde CORS etkinleştirildiğinde `RequireCors` Otomatik ön kontrol **not** istekleri desteklenmez.

Bu belgenin [Test CORS](#testc) bölümü bu davranışı gösterir.

<a name="pro"></a>

### <a name="httpoptions-attribute-for-preflight-requests"></a>[HttpOptions] ön kontrol istekleri için öznitelik

CORS uygun ilkeyle etkinleştirildiğinde, ASP.NET Core genellikle CORS ön denetim isteklerini otomatik olarak yanıtlar. Bazı senaryolarda bu durum olmayabilir. Örneğin, [uç nokta yönlendirme Ile CORS](#ecors)'yi kullanma.

Aşağıdaki kod, Seçenekler istekleri için uç noktalar oluşturmak için [[HttpOptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) özniteliğini kullanır:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet&highlight=5-17)]

Önceki kodun test edilmesine ilişkin yönergeler için bkz. [Endpoint Routing Ile test CORS ve [HttpOptions]](#tcer) .

### <a name="set-the-preflight-expiration-time"></a>Ön kontrol sona erme süresini ayarlama

`Access-Control-Max-Age`Üst bilgi, ön kontrol isteğine olan yanıtın ne kadar süreyle önbelleğe alınacağını belirtir. Bu üstbilgiyi ayarlamak için şunu arayın <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*> :

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet7)]
<a name="how-cors"></a>

## <a name="how-cors-works"></a>CORS nasıl kullanılır?

Bu bölümde, HTTP iletileri düzeyindeki bir [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) isteğinde ne olacağı açıklanmaktadır.

* CORS bir güvenlik özelliği **değil** . CORS, bir sunucunun aynı kaynaklı ilkeyi rahat bir şekilde sağlamasına izin veren bir W3C standardıdır.
  * Örneğin, kötü niyetli bir aktör sitenize karşı [siteler arası komut dosyası (XSS)](xref:security/cross-site-scripting) kullanabilir ve bilgileri çalmak için CORS özellikli sitesine bir siteler arası istek yürütebilir.
* Bir API, CORS 'ye izin vererek daha güvenli değildir.
  * CORS 'yi zorlamak için istemciye (tarayıcı) sahiptir. Sunucu isteği yürütür ve yanıtı döndürür. Bu, bir hatayı döndüren ve yanıtı engelleyen istemcdir. Örneğin, aşağıdaki araçlardan herhangi birinde sunucu yanıtı görüntülenir:
    * [Fiddler](https://www.telerik.com/fiddler)
    * [Postman](https://www.getpostman.com/)
    * [.NET HttpClient](/dotnet/csharp/tutorials/console-webapiclient)
    * Adres çubuğuna URL girerek bir Web tarayıcısı.
* Bir sunucu, tarayıcıların çapraz kaynak [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) veya [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) isteği çalıştırmasına izin vermesinin yasaktır bir yoludur.
  * CORS içermeyen tarayıcılar, çıkış noktaları arası istekleri yapamıyor. CORS 'den önce, bu kısıtlamayı aşmak için [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) kullanılmıştır. JSONP XHR kullanmaz, `<script>` yanıtı almak için etiketini kullanır. Betiklerin, çapraz kaynak olarak yüklenmesine izin verilir.

[CORS belirtimi](https://www.w3.org/TR/cors/) , çıkış noktaları arası istekleri etkinleştiren bırkaç yeni http üst bilgisi sunmuştur. Bir tarayıcı CORS 'yi destekliyorsa, bu üst bilgileri, çıkış noktaları arası istekler için otomatik olarak ayarlar. CORS 'yi etkinleştirmek için özel JavaScript kodu gerekli değildir.

Dağıtılan [örnekteki](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) [testi Yerleştir düğmesi](https://cors3.azurewebsites.net/test)

Aşağıda, [Values](https://cors3.azurewebsites.net/) test düğmesinden olan bir çapraz kaynak isteğine bir örnek verilmiştir `https://cors1.azurewebsites.net/api/values` . `Origin`Üst bilgi:

* , İsteği yapan sitenin etki alanını sağlar.
* Gereklidir ve konaktan farklı olmalıdır.

**Genel üstbilgiler**

```
Request URL: https://cors1.azurewebsites.net/api/values
Request Method: GET
Status Code: 200 OK
```

**Yanıt üst bilgileri**

```
Content-Encoding: gzip
Content-Type: text/plain; charset=utf-8
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Transfer-Encoding: chunked
Vary: Accept-Encoding
X-Powered-By: ASP.NET
```

**İstek üst bilgileri**

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

`OPTIONS`İsteklerde, sunucu yanıtta **yanıt üstbilgileri** `Access-Control-Allow-Origin: {allowed origin}` üst bilgisini ayarlar. Örneğin, dağıtılan [örnek](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI), [delete [enablecors]](https://cors1.azurewebsites.net/test?number=2) düğme `OPTIONS` isteği aşağıdaki üstbilgileri içerir:

**Genel üstbilgiler**

```
Request URL: https://cors3.azurewebsites.net/api/TodoItems2/MyDelete2/5
Request Method: OPTIONS
Status Code: 204 No Content
```

**Yanıt üst bilgileri**

```
Access-Control-Allow-Headers: Content-Type,x-custom-header
Access-Control-Allow-Methods: PUT,DELETE,GET,OPTIONS
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors3.azurewebsites.net
Vary: Origin
X-Powered-By: ASP.NET
```

**İstek üst bilgileri**

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

Önceki **yanıt üst bilgilerinde**sunucu, yanıtta [Access-Control-Allow-Origin](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) üst bilgisini ayarlar. `https://cors1.azurewebsites.net`Bu üstbilginin değeri, `Origin` istekten gelen üstbilgiyle eşleşir.

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>Çağrılırsa, `Access-Control-Allow-Origin: *` joker karakter değeri döndürülür. `AllowAnyOrigin`Tüm kaynağa izin verir.

Yanıt `Access-Control-Allow-Origin` üstbilgiyi içermiyorsa, çapraz kaynak isteği başarısız olur. Özellikle, tarayıcı isteğe izin vermez. Sunucu başarılı bir yanıt döndürse bile tarayıcı, yanıtı istemci uygulama için kullanılabilir hale getirir.

<a name="options"></a>

### <a name="display-options-requests"></a>Görüntüleme SEÇENEKLERI istekleri

Varsayılan olarak, Chrome ve Edge tarayıcıları, F12 araçlarının Ağ sekmesinde seçenek isteklerini göstermez. Bu tarayıcılarda seçenek isteklerini göstermek için:

* `chrome://flags/#out-of-blink-cors` veya `edge://flags/#out-of-blink-cors`
* bayrağı devre dışı bırakın.
* uygulamasını.

Firefox, varsayılan olarak seçenek isteklerini gösterir.

## <a name="cors-in-iis"></a>IIS 'de CORS

IIS 'ye dağıtım yaparken, sunucu anonim erişime izin verecek şekilde yapılandırılmamışsa CORS, Windows kimlik doğrulamasından önce çalıştırılmalıdır. Bu senaryoyu desteklemek için, [IIS CORS modülünün](https://www.iis.net/downloads/microsoft/iis-cors-module) uygulama için yüklenmiş ve yapılandırılmış olması gerekir.

<a name="testc"></a>

## <a name="test-cors"></a>Test CORS

[Örnek indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) , CORS 'yi test etmek için kod içerir. Bkz. [indirme](xref:index#how-to-download-a-sample). Örnek, eklenen sayfaların bulunduğu bir API projem örneğidir Razor :

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTest2.cs?name=snippet2)]

  > [!WARNING]
  > `WithOrigins("https://localhost:<port>");`yalnızca [indirme örnek koduna](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors)benzer bir örnek uygulamanın test edilmesi için kullanılmalıdır.

Aşağıdakiler, `ValuesController` test için uç noktaları sağlar:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet)]

[Mydisplayrouteınfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) , [Rick. docs. Samples. routeınfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) NuGet paketi tarafından sağlanır ve rota bilgilerini görüntüler.

Aşağıdaki yaklaşımlardan birini kullanarak önceki örnek kodu test edin:

* Üzerinde dağıtılan örnek uygulamayı kullanın [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/) . Örneği indirmeniz gerekmez.
* `dotnet run`Varsayılan URL 'sini kullanarak örneği çalıştırın `https://localhost:5001` .
* Bir URL 'SI için, bağlantı noktası 44398 olarak ayarlanan Visual Studio 'daki örneği çalıştırın `https://localhost:44398` .

F12 araçlarıyla bir tarayıcı kullanma:

* **Değerler** düğmesini seçin ve **ağ** sekmesindeki üst bilgileri gözden geçirin.
* **Testi yerleştir** düğmesini seçin. Seçenekler isteği görüntüleme yönergeleri için bkz. [görüntüleme seçenekleri istekleri](#options) . **PUT testi** iki istek, bir seçenekler ön hazırlığı ISTEğI ve PUT isteği oluşturur.
* **`GetValues2 [DisableCors]`** Başarısız BIR CORS isteğini tetiklemek için düğmeyi seçin. Belgede bahsedildiği gibi, yanıt 200 başarılı döndürür, ancak CORS isteği yapılmaz. CORS hatasını görmek için **konsol** sekmesini seçin. Tarayıcıya bağlı olarak aşağıdakine benzer bir hata görüntülenir:

     `'https://cors1.azurewebsites.net/api/values/GetValues2'`Kaynak kimden kaynağı `'https://cors3.azurewebsites.net'` CORS ilkesi tarafından engellendi: İstenen kaynakta ' erişim-denetim-Izin-Origin ' üst bilgisi yok. Donuk bir yanıt ihtiyaçlarınıza hizmet veriyorsa, isteği CORS devre dışı olarak getirmek için isteğin modunu ' No-CORS ' olarak ayarlayın.
     
CORS özellikli uç noktalar, [kıvrımlı](https://curl.haxx.se/), [Fiddler](https://www.telerik.com/fiddler)veya [Postman](https://www.getpostman.com/)gibi bir araçla test edilebilir. Bir araç kullanırken, üst bilgi tarafından belirtilen isteğin kaynağı `Origin` isteği alan konaktan farklı olmalıdır. İstek, üst bilgi değerine göre *Çıkış dışı* değilse `Origin` :

* CORS ara yazılımı için isteği işleme gereksinimi yoktur.
* CORS üstbilgileri yanıtta döndürülmedi.

Aşağıdaki komut, `curl` bilgileri içeren BIR seçenek isteği vermek için kullanır:

```bash
curl -X OPTIONS https://cors3.azurewebsites.net/api/TodoItems2/5 -i
```

<!--
curl come with Git. Add to path variable
C:\Program Files\Git\mingw64\bin\
-->

<a name="tcer"></a>

### <a name="test-cors-with-endpoint-routing-and-httpoptions"></a>ENDPOINT yönlendirme ve [HttpOptions] ile test CORS

Şu anda bir uç nokta temelinde CORS etkinleştirildiğinde `RequireCors` [Otomatik ön kontrol istekleri](#apf) **desteklenmez** . [CORS 'yi etkinleştirmek için Endpoint Routing](#ecors)kullanan aşağıdaki kodu göz önünde bulundurun:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPointBugTest.cs?name=snippet2)]

Aşağıdakiler `TodoItems1Controller` test için uç noktalar sağlar:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems1Controller.cs?name=snippet2)]

Önceki kodu dağıtılan [Örneğin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) [Test sayfasından](https://cors1.azurewebsites.net/test?number=1) test edin.

Bitiş noktaları, ön kontrol isteklerini içerdiğinden ve yanıt verdiği için **delete [enablecors]** ve **Get [enablecors]** düğmeleri başarılı olur `[EnableCors]` . Diğer uç noktalar başarısız olur. [JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js) şu şekilde gönderdiği için **Al** düğmesi başarısız olur:

```javascript
 headers: {
      "Content-Type": "x-custom-header"
 },
```

Aşağıdakiler `TodoItems2Controller` benzer uç noktalar sağlar, ancak seçenek isteklerine yanıt vermek için açık kod içerir:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet2)]

Önceki kodu dağıtılan örneğin [Test sayfasından](https://cors1.azurewebsites.net/test?number=2) test edin. **Denetleyici** açılan listesinde, **ön kontrol** ' yı ve ardından **denetleyiciyi ayarla**' yı seçin. Uç noktalara yönelik tüm CORS çağrıları `TodoItems2Controller` başarılı olur.

## <a name="additional-resources"></a>Ek kaynaklar

* [Çıkış Noktaları Arası Kaynak Paylaşımı (CORS)](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [IIS CORS modülünü kullanmaya başlama](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)

Bu makalede, ASP.NET Core uygulamasında CORS 'nin nasıl etkinleştirileceği gösterilmektedir.

Tarayıcı güvenliği, bir Web sayfasının Web sayfasını sunduğundan farklı bir etki alanına istek yapmasını engeller. Bu kısıtlamaya *aynı-Origin ilkesi*adı verilir. Aynı-kaynak ilkesi, kötü niyetli bir sitenin gizli verileri başka bir siteden okumasını engeller. Bazen diğer sitelerin uygulamanıza çapraz çıkış istekleri yapmasına izin vermek isteyebilirsiniz. Daha fazla bilgi için bkz. [MOZILLA CORS makalesi](https://developer.mozilla.org/docs/Web/HTTP/CORS).

[Çapraz kaynak kaynak paylaşımı](https://www.w3.org/TR/cors/) (CORS):

* , Bir sunucunun aynı kaynak ilkeyi rahat bir şekilde sağlamasına izin veren bir W3C standardıdır.
* Güvenlik özelliği **değil** , CORS güvenliği. CORS 'nin izin vermesini sağlayan bir API daha güvenli değildir. Daha fazla bilgi için bkz. [CORS çalışma](#how-cors).
* Bir sunucunun bazı çapraz kaynak isteklerine, diğerlerini reddetirken açık olarak izin almasına izin verir.
* , [JSONP](/dotnet/framework/wcf/samples/jsonp)gibi önceki tekniklerin daha güvenli ve daha esnektir.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="same-origin"></a>Aynı kaynak

Özdeş şemaları, konakları ve bağlantı noktalarına sahip olmaları durumunda iki URL aynı kaynağa sahiptir ([RFC 6454](https://tools.ietf.org/html/rfc6454)).

Bu iki URL aynı kaynağa sahiptir:

* `https://example.com/foo.html`
* `https://example.com/bar.html`

Bu URL 'Ler, önceki iki URL 'den farklı kaynaklardan farklıdır:

* `https://example.net`: Farklı etki alanı
* `https://www.example.com/foo.html`: Farklı alt etki alanı
* `http://example.com/foo.html`: Farklı düzen
* `https://example.com:9000/foo.html`: Farklı bağlantı noktası

Internet Explorer, kaynakları karşılaştırırken bağlantı noktasını dikkate almaz.

## <a name="cors-with-named-policy-and-middleware"></a>Adlandırılmış ilke ve ara yazılım ile CORS

CORS ara yazılımı, çıkış noktaları arası istekleri işler. Aşağıdaki kod, belirtilen kaynağa sahip tüm uygulama için CORS 'yi sunar:

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

Yukarıdaki kod:

* İlke adını " \_ myallowspecifickaynaklarına" olarak ayarlar. İlke adı rastgele olur.
* <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*>CORS 'yi sağlayan genişletme yöntemini çağırır.
* <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> [Lambda ifadesi](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)ile çağrılar. Lambda bir nesnesi alır <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> . Gibi [yapılandırma seçenekleri](#cors-policy-options), `WithOrigins` Bu makalenin ilerleyen kısımlarında açıklanmıştır.

<xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*>Yöntem çağrısı, uygulamanın hizmet KAPSAYıCıSıNA CORS Hizmetleri ekler:

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

Daha fazla bilgi için bu belgedeki [CORS ilke seçenekleri](#cpo) bölümüne bakın.

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder>Yöntemi aşağıdaki kodda gösterildiği gibi yöntemleri zincirleyebilir:

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

Not: URL sonunda eğik **not** çizgi ( `/` ) bulunmamalıdır. URL ile sonlandığında `/` karşılaştırma döndürülür `false` ve üst bilgi döndürülmez.

Aşağıdaki kod, CORS ara yazılımı aracılığıyla CORS ilkelerini tüm uygulama uç noktalarına uygular:
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
Note: öğesinden `UseCors` önce çağrılmalıdır `UseMvc` .

CORS ilkesini sayfa/denetleyici/eylem düzeyinde uygulamak için bkz. [ Razor sayfalarda, denetleyicilerde ve eylem yöntemlerinde CORS 'yi etkinleştirme](#ecors) .

Yukarıdaki koda benzer test kodu hakkında yönergeler için bkz. [Test CORS](#test) .

## <a name="enable-cors-with-attributes"></a>CORS 'yi özniteliklerle etkinleştir

[ &lbrack; Enablecors &rbrack; ](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) özniteliği, CORS 'yi küresel olarak uygulamaya bir alternatif sağlar. `[EnableCors]`Öznitelik, tüm bitiş noktaları yerine, seçilen bitiş noktaları IÇIN CORS 'yi mümkün.

`[EnableCors]`Varsayılan ilkeyi belirtmek ve `[EnableCors("{Policy String}")]` bir ilke belirlemek için kullanın.

`[EnableCors]`Özniteliği öğesine uygulanabilir:

* RazorSayfasında`PageModel`
* Kumandasını
* Denetleyici eylemi yöntemi

Özniteliği ile denetleyici/sayfa-model/eylem 'e farklı ilkeler uygulayabilirsiniz `[EnableCors]` . `[EnableCors]`Özniteliği bir denetleyiciler/sayfa modeli/eylem yöntemine uygulandığında ve bu işlem, ara yazılım 'NDA CORS 'yi etkinleştirmişse, **her iki ilke de** uygulanır. İlkeleri **birleştirmediğimiz** için önerilir. `[EnableCors]`Özniteliği veya ara yazılımı kullanın, **her ikisini birden**kullanmayın. Kullanırken `[EnableCors]` , varsayılan bir **not** ilke tanımlamayın.

Aşağıdaki kod her bir yönteme farklı bir ilke uygular:

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

Aşağıdaki kod, bir CORS varsayılan ilkesi ve adlı bir ilke oluşturur `"AnotherPolicy"` :

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a>CORS 'yi devre dışı bırak

[ &lbrack; Disablecors &rbrack; ](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) özniteliği denetleyici/sayfa MODELI/eylem için CORS 'yi devre dışı bırakır.

<a name="cpo"></a>

## <a name="cors-policy-options"></a>CORS ilke seçenekleri

Bu bölümde, bir CORS ilkesinde ayarlanmakta olabilecek çeşitli seçenekler açıklanmaktadır:

* [İzin verilen kaynakları ayarla](#set-the-allowed-origins)
* [İzin verilen HTTP yöntemlerini ayarlama](#set-the-allowed-http-methods)
* [İzin verilen istek üst bilgilerini ayarlama](#set-the-allowed-request-headers)
* [Gösterilen yanıt üst bilgilerini ayarlama](#set-the-exposed-response-headers)
* [Kaynaklar arası isteklerde kimlik bilgileri](#credentials-in-cross-origin-requests)
* [Ön kontrol sona erme süresini ayarlama](#set-the-preflight-expiration-time)

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>içinde çağırılır `Startup.ConfigureServices` . Bazı seçenekler için, ilk olarak [CORS 'Nin nasıl çalıştığı](#how-cors) bölümü okumanız yararlı olabilir.

## <a name="set-the-allowed-origins"></a>İzin verilen kaynakları ayarla

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: Herhangi bir düzen (veya) ile tüm kaynaklardan gelen CORS isteklerine izin verir `http` `https` . `AllowAnyOrigin`, *herhangi bir Web sitesi* uygulamaya çapraz kaynak istekleri yapabildiğinden güvenli değildir.

> [!NOTE]
> `AllowAnyOrigin`Ve `AllowCredentials` güvenli olmayan bir yapılandırma belirtip, siteler arası istek sahteciliği ile sonuçlanabilir. Güvenli bir uygulama için, istemcinin sunucu kaynaklarına erişim yetkisi olması gerekiyorsa, kaynakların tam bir listesini belirtin.

`AllowAnyOrigin`ön kontrol isteklerini ve `Access-Control-Allow-Origin` üstbilgiyi etkiler. Daha fazla bilgi için bkz. [ön kontrol istekleri](#preflight-requests) bölümü.

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>: <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> Kaynağa izin verilip verilmediğini değerlendirirken, kaynağın yapılandırılmış bir joker karakterle eşleşmesini sağlayan bir işlev olarak ilkenin özelliğini ayarlar.

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-105&highlight=4-5)]

### <a name="set-the-allowed-http-methods"></a>İzin verilen HTTP yöntemlerini ayarlama

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:

* Herhangi bir HTTP yöntemine izin verir:
* Ön kontrol isteklerini ve `Access-Control-Allow-Methods` üstbilgiyi etkiler. Daha fazla bilgi için bkz. [ön kontrol istekleri](#preflight-requests) bölümü.

### <a name="set-the-allowed-request-headers"></a>İzin verilen istek üst bilgilerini ayarlama

Belirli başlıkların, *Yazar isteği üstbilgileri*ADLı bir CORS isteğinde gönderilmesine izin vermek için, ' i çağırın <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> ve izin verilen üst bilgileri belirtin:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

Tüm yazar isteği başlıklarına izin vermek için şunu çağırın <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> :

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

Bu ayar, ön kontrol isteklerini ve `Access-Control-Request-Headers` üstbilgiyi etkiler. Daha fazla bilgi için bkz. [ön kontrol istekleri](#preflight-requests) bölümü.

CORS ara yazılımı her zaman `Access-Control-Request-Headers` CorsPolicy. Headers ' de yapılandırılan değerlerden bağımsız olarak, ' deki dört üst bilgilerin gönderilmesine izin verir. Bu üst bilgi listesi şunları içerir:

* `Accept`
* `Accept-Language`
* `Content-Language`
* `Origin`

Örneğin, aşağıdaki gibi yapılandırılmış bir uygulamayı göz önünde bulundurun:

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

CORS ara yazılımı, `Content-Language` her zaman beyaz listelenmiş olduğundan, aşağıdaki istek üstbilgisiyle bir ön kontrol isteğine başarıyla yanıt veriyor:

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

### <a name="set-the-exposed-response-headers"></a>Gösterilen yanıt üst bilgilerini ayarlama

Varsayılan olarak tarayıcı, tüm yanıt üst bilgilerini uygulamaya sunmaz. Daha fazla bilgi için bkz. [W3C çıkış noktaları arası kaynak paylaşımı (terminoloji): basit yanıt üst bilgisi](https://www.w3.org/TR/cors/#simple-response-header).

Varsayılan olarak kullanılabilen yanıt üstbilgileri şunlardır:

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

CORS belirtimi, bu üst bilgiler *basit yanıt üst bilgilerini*çağırır. Diğer üst bilgileri uygulama için kullanılabilir hale getirmek için şunu çağırın <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*> :

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a>Kaynaklar arası isteklerde kimlik bilgileri

Kimlik bilgileri CORS isteğinde özel işleme gerektirir. Varsayılan olarak tarayıcı, kimlik bilgilerini bir çapraz kaynak isteğiyle göndermez. Kimlik bilgileri, tanımlama bilgileri ve HTTP kimlik doğrulama düzenlerini içerir. Bir çapraz kaynak isteğiyle kimlik bilgilerini göndermek için, istemcisinin olarak ayarlanması gerekir `XMLHttpRequest.withCredentials` `true` .

`XMLHttpRequest`Doğrudan kullanarak:

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

JQuery kullanarak:

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

[Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)'sini kullanma:

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

Sunucu kimlik bilgilerine izin vermelidir. Çıkış noktaları arası kimlik bilgilerine izin vermek için şunu arayın <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*> :

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

HTTP yanıtı, `Access-Control-Allow-Credentials` tarayıcıya sunucunun bir çapraz kaynak isteği için kimlik bilgileri verdiğini bildiren bir üst bilgi içerir.

Tarayıcı kimlik bilgilerini gönderirse ancak yanıt geçerli bir `Access-Control-Allow-Credentials` üst bilgi içermiyorsa, tarayıcı uygulamaya yanıtı kullanıma sunmaz ve çapraz kaynak isteği başarısız olur.

Çıkış noktaları arası kimlik bilgilerine izin vermek bir güvenlik riskidir. Başka bir etki alanındaki Web sitesi, kullanıcının bilgisi olmadan kullanıcı adına, oturum açmış bir kullanıcının kimlik bilgilerini uygulamaya gönderebilir. <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

CORS belirtimi Ayrıca `"*"` üst bilgi varsa, çıkış (tüm kaynaklar) ayarının geçersiz olduğunu belirtir `Access-Control-Allow-Credentials` .

### <a name="preflight-requests"></a>Ön kontrol istekleri

Bazı CORS istekleri için, tarayıcı gerçek isteği yapmadan önce ek bir istek gönderir. Bu isteğe bir *ön kontrol isteği*denir. Aşağıdaki koşullar doğruysa tarayıcı, ön kontrol isteğini atlayabilir:

* İstek yöntemi al, HEAD veya POST.
* Uygulama,,, veya dışındaki istek üst bilgilerini ayarlanmamış `Accept` `Accept-Language` `Content-Language` `Content-Type` `Last-Event-ID` .
* , `Content-Type` Ayarlandıysa, aşağıdaki değerlerden birine sahip olan üst bilgi:
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

İstemci isteği için ayarlanan istek üst bilgileri kuralı, uygulamanın, nesne üzerinde çağırarak ayarladığı üst bilgiler için geçerlidir `setRequestHeader` `XMLHttpRequest` . CORS belirtimi, bu üst bilgiler *Yazar istek üst bilgilerini*çağırır. Kural,, veya gibi tarayıcının ayarlayabilmesi için, veya gibi bir üst bilgiye uygulanmaz `User-Agent` `Host` `Content-Length` .

Aşağıda bir ön denetim isteğine örnek verilmiştir:

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

Uçuş öncesi isteği HTTP SEÇENEKLERI yöntemini kullanır. İki özel üst bilgi içerir:

* `Access-Control-Request-Method`: Gerçek istek için kullanılacak HTTP yöntemi.
* `Access-Control-Request-Headers`: Uygulamanın gerçek istekte ayarladığı istek üst bilgilerinin bir listesi. Daha önce belirtildiği gibi, bu, tarayıcının ayarladığı üst bilgileri içermez `User-Agent` .

<!-- I think this needs to be removed, was put here accidently -->

CORS uygun ilkeyle etkinleştirildiğinde ASP.NET Core, genellikle CORS ön denetim isteklerini otomatik olarak yanıtlar. [Ön kontrol istekleri için bkz. [HttpOptions] özniteliği](#pro).

CORS ön kontrol isteği `Access-Control-Request-Headers` , sunucuya gerçek istekle gönderilen üstbilgileri belirten bir üst bilgi içerebilir.

Belirli üstbilgilere izin vermek için şunu arayın <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> :

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

Tüm yazar isteği başlıklarına izin vermek için şunu çağırın <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> :

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

Tarayıcılar, nasıl ayarlandıklarından tamamen tutarlı değildir `Access-Control-Request-Headers` . Üst bilgileri `"*"` (veya kullanımı) dışında bir şeye ayarlarsanız, <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*> desteklemek istediğiniz en az `Accept` , `Content-Type` , ve ve `Origin` Ek üstbilgileri dahil etmelisiniz.

Aşağıda, ön kontrol isteğine örnek bir yanıt verilmiştir (sunucunun isteğe izin verdiği varsayıldığında):

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

Yanıt, izin verilen `Access-Control-Allow-Methods` üst bilgileri listeleyen, izin verilen yöntemleri ve isteğe bağlı olarak bir üst bilgiyi listeleyen bir üst bilgi içerir `Access-Control-Allow-Headers` . Ön kontrol isteği başarılı olursa, tarayıcı gerçek isteği gönderir.

Ön kontrol isteği reddedilirse, uygulama *200 ok* yanıtı döndürür ancak CORS üst bilgilerini geri göndermez. Bu nedenle tarayıcı, çıkış noktaları arası isteği denemez.

### <a name="set-the-preflight-expiration-time"></a>Ön kontrol sona erme süresini ayarlama

`Access-Control-Max-Age`Üst bilgi, ön kontrol isteğine olan yanıtın ne kadar süreyle önbelleğe alınacağını belirtir. Bu üstbilgiyi ayarlamak için şunu arayın <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*> :

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=91-96&highlight=5)]

<a name="how-cors"></a>

## <a name="how-cors-works"></a>CORS nasıl kullanılır?

Bu bölümde, HTTP iletileri düzeyindeki bir [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) isteğinde ne olacağı açıklanmaktadır.

* CORS bir güvenlik özelliği **değil** . CORS, bir sunucunun aynı kaynaklı ilkeyi rahat bir şekilde sağlamasına izin veren bir W3C standardıdır.
  * Örneğin, kötü niyetli bir aktör sitenize karşı [siteler arası komut dosyalarını (XSS) engelliyor](xref:security/cross-site-scripting) ve bilgileri çalmak için CORS özellikli sitesine bir siteler arası istek yürütebilir.
* CORS 'nin izin vermesini sağlayan API 'niz daha güvenli değildir.
  * CORS 'yi zorlamak için istemciye (tarayıcı) sahiptir. Sunucu isteği yürütür ve yanıtı döndürür. Bu, bir hatayı döndüren ve yanıtı engelleyen istemcdir. Örneğin, aşağıdaki araçlardan herhangi birinde sunucu yanıtı görüntülenir:
    * [Fiddler](https://www.telerik.com/fiddler)
    * [Postman](https://www.getpostman.com/)
    * [.NET HttpClient](/dotnet/csharp/tutorials/console-webapiclient)
    * Adres çubuğuna URL girerek bir Web tarayıcısı.
* Bir sunucu, tarayıcıların çapraz kaynak [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) veya [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) isteği çalıştırmasına izin vermesinin yasaktır bir yoludur.
  * Tarayıcılar (CORS olmadan), çapraz kaynak istekleri yapamıyor. CORS 'den önce, bu kısıtlamayı aşmak için [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) kullanılmıştır. JSONP XHR kullanmaz, `<script>` yanıtı almak için etiketini kullanır. Betiklerin, çapraz kaynak olarak yüklenmesine izin verilir.

[CORS belirtimi](https://www.w3.org/TR/cors/) , çıkış noktaları arası istekleri etkinleştiren bırkaç yeni http üst bilgisi sunmuştur. Bir tarayıcı CORS 'yi destekliyorsa, bu üst bilgileri, çıkış noktaları arası istekler için otomatik olarak ayarlar. CORS 'yi etkinleştirmek için özel JavaScript kodu gerekli değildir.

Aşağıda, bir çapraz kaynak isteğine bir örnek verilmiştir. `Origin`Üst bilgi, isteği yapan sitenin etki alanını sağlar. `Origin`Üst bilgi gereklidir ve konaktan farklı olmalıdır.

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

Sunucu isteğe izin veriyorsa, `Access-Control-Allow-Origin` yanıttaki üstbilgiyi ayarlar. Bu üstbilginin değeri, `Origin` istekten gelen üstbilgiyle eşleşir veya joker değerdir `"*"` , yani herhangi bir kaynağa izin verilir.

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

Yanıt `Access-Control-Allow-Origin` üstbilgiyi içermiyorsa, çapraz kaynak isteği başarısız olur. Özellikle, tarayıcı isteğe izin vermez. Sunucu başarılı bir yanıt döndürse bile tarayıcı, yanıtı istemci uygulama için kullanılabilir hale getirir.

<a name="test"></a>

## <a name="test-cors"></a>Test CORS

CORS 'yi sınamak için:

1. [BIR API projesi oluşturun](xref:tutorials/first-web-api). Alternatif olarak, [örneği de indirebilirsiniz](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).
1. Bu belgedeki yaklaşımlardan birini kullanarak CORS 'yi etkinleştirin. Örnek:

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]

  > [!WARNING]
  > `WithOrigins("https://localhost:<port>");`yalnızca [indirme örnek koduna](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors)benzer bir örnek uygulamanın test edilmesi için kullanılmalıdır.

1. Web uygulaması projesi ( Razor Sayfalar veya Mvc) oluşturun. Örnek, Razor sayfaları kullanır. Web uygulamasını, API projesiyle aynı çözümde oluşturabilirsiniz.
1. Aşağıdaki Vurgulanan kodu *Index. cshtml* dosyasına ekleyin:

  [!code-csharp[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. Yukarıdaki kodda, `url: 'https://<web app>.azurewebsites.net/api/values/1',` dağıtılan UYGULAMANıN URL 'siyle değiştirin.
1. API projesini dağıtın. Örneğin, [Azure 'a dağıtın](xref:host-and-deploy/azure-apps/index).
1. RazorMasaüstünden sayfaları veya MVC uygulamasını çalıştırın ve **Test** düğmesine tıklayın. Hata iletilerini gözden geçirmek için F12 araçlarını kullanın.
1. Localhost kaynağını kaldırın `WithOrigins` ve uygulamayı dağıtın. Alternatif olarak, istemci uygulamasını farklı bir bağlantı noktasıyla çalıştırın. Örneğin, Visual Studio 'dan çalıştırın.
1. İstemci uygulamasıyla test edin. CORS hataları bir hata döndürüyor, ancak hata iletisi JavaScript için kullanılabilir değil. Hatayı görmek için F12 araçlarındaki konsol sekmesini kullanın. Tarayıcıya bağlı olarak, aşağıdakine benzer bir hata alırsınız (F12 araçları konsolunda):

   * Microsoft Edge 'i kullanarak:

     **SEC7120: [CORS] kaynak, `https://localhost:44375` `https://localhost:44375` ' deki çıkış noktaları için erişim-denetim-Izin-kaynak yanıt üstbilgisinde bulunamadı`https://webapi.azurewebsites.net/api/values/1`**

   * Chrome kullanarak:

     **Kaynak başında XMLHttpRequest erişimi `https://webapi.azurewebsites.net/api/values/1` `https://localhost:44375` CORS ilkesi tarafından engellendi: İstenen kaynakta ' erişim-denetim-Izin-Origin ' üst bilgisi yok.**
     
CORS özellikli uç noktalar [Fiddler](https://www.telerik.com/fiddler) veya [Postman](https://www.getpostman.com/)gibi bir araçla test edilebilir. Bir araç kullanırken, üst bilgi tarafından belirtilen isteğin kaynağı `Origin` isteği alan konaktan farklı olmalıdır. İstek, üst bilgi değerine göre *Çıkış dışı* değilse `Origin` :

* CORS ara yazılımı için isteği işleme gereksinimi yoktur.
* CORS üstbilgileri yanıtta döndürülmedi.

## <a name="cors-in-iis"></a>IIS 'de CORS

IIS 'ye dağıtım yaparken, sunucu anonim erişime izin verecek şekilde yapılandırılmamışsa CORS, Windows kimlik doğrulamasından önce çalıştırılmalıdır. Bu senaryoyu desteklemek için, [IIS CORS modülünün](https://www.iis.net/downloads/microsoft/iis-cors-module) uygulama için yüklenmiş ve yapılandırılmış olması gerekir.

## <a name="additional-resources"></a>Ek kaynaklar

* [Çıkış Noktaları Arası Kaynak Paylaşımı (CORS)](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [IIS CORS modülünü kullanmaya başlama](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end
