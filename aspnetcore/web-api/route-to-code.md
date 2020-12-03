---
title: ASP.NET Core ile temel JSON API 'Leri Route-to-code
author: jamesnk
description: Route-to-codeBasıt JSON Web API 'leri oluşturmak için ve JSON uzantısı yöntemlerini nasıl kullanacağınızı öğrenin.
monikerRange: '>= aspnetcore-5.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 11/30/2020
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
- Route-to-code
uid: web-api/route-to-code
ms.openlocfilehash: f8a3804a887ebfa0f5284d8991e903c978b18208
ms.sourcegitcommit: 92439194682dc788b8b5b3a08bd2184dc00e200b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 12/03/2020
ms.locfileid: "96556612"
---
# <a name="basic-json-apis-with-no-locroute-to-code-in-aspnet-core"></a>ASP.NET Core ile temel JSON API 'Leri Route-to-code

, [James bAyKiNg](https://github.com/jamesnk)

ASP.NET Core, JSON Web API 'Leri oluşturmanın çeşitli yollarını destekler:

* [ASP.NET Core Web API 'si](xref:web-api/index) , API 'ler oluşturmak için kapsamlı bir çerçeve sağlar. Bir hizmet öğesinden devralarak oluşturulur <xref:Microsoft.AspNetCore.Mvc.ControllerBase> . Framework tarafından sunulan bazı özellikler arasında model bağlama, doğrulama, içerik anlaşması, giriş ve çıkış biçimlendirmesi ve Openapı dahildir.
* Route-to-code , Web API ASP.NET Core için çerçeve dışı bir alternatiftir. Route-to-code[ASP.NET Core yönlendirmeyi](xref:fundamentals/routing) doğrudan kodunuza bağlar. Kodunuz istekten okur ve yanıtı yazar. Route-to-code Web API 'sinin gelişmiş özelliklerine sahip değildir, ancak bunu kullanmak için bir yapılandırma gerekmez.

Route-to-code , küçük ve temel JSON Web API 'Leri oluştururken iyi bir yaklaşımdır.

## <a name="create-json-web-apis"></a>JSON Web API 'Leri oluşturma

ASP.NET Core, JSON Web API 'lerinin oluşturulmasını kolaylaştıran yardımcı yöntemler sağlar:

* <xref:Microsoft.AspNetCore.Http.HttpRequestJsonExtensions.HasJsonContentType%2A>`Content-Type`JSON içerik türünün üst bilgisini denetler.
* <xref:Microsoft.AspNetCore.Http.HttpRequestJsonExtensions.ReadFromJsonAsync%2A> istekten JSON okur ve belirtilen türe göre serileştirir.
* <xref:Microsoft.AspNetCore.Http.HttpResponseJsonExtensions.WriteAsJsonAsync%2A> belirtilen değeri, yanıt gövdesine JSON olarak yazar ve yanıt içerik türünü olarak ayarlar `application/json` .

Hafif, rota tabanlı JSON API 'Leri *Startup.cs* içinde belirtilmiştir. Yol ve API mantığı, <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> bir uygulamanın istek ardışık düzeninin bir parçası olarak ' de yapılandırılır.

### <a name="write-json-response"></a>JSON yanıtı yaz

Bir uygulama için JSON API 'YI yapılandıran aşağıdaki kodu göz önünde bulundurun:

[!code-csharp[](route-to-code/sample/Startup3.cs?name=snippet&highlight=6)]

Yukarıdaki kod:

* Yönlendirme şablonu olarak içeren bir HTTP GET API uç noktası ekler `/hello/{name:alpha}` .
* Yol eşleştiğinde API, `name` istekten yol değerini okur.
* Anonim bir türü, ile JSON yanıtı olarak yazar `WriteAsJsonAsync` .

### <a name="read-json-request"></a>JSON isteğini oku

`HasJsonContentType` ve `ReadFromJsonAsync` , rota tabanlı BIR JSON API 'SINDE JSON yanıtının serisini kaldırmak için kullanılabilir:

[!code-csharp[](route-to-code/sample/Startup2.cs?name=snippet&highlight=5,11)]

Yukarıdaki kod:

* Yönlendirme şablonu olarak içeren bir HTTP POST API uç noktası ekler `/weather` .
* Yol eşleştirildiği zaman, `HasJsonContentType` istek içerik türünü doğrular. JSON olmayan bir içerik türü 415 durum kodu döndürür.
* İçerik türü JSON ise, istek içeriği tarafından seri durumdan çıkarılmış olur `ReadFromJsonAsync` .

### <a name="configure-json-serialization"></a>JSON serileştirmesini yapılandırma

JSON serileştirmesini özelleştirmenin iki yolu vardır:

* Varsayılan serileştirme seçenekleri yöntemiyle ile yapılandırılabilir <xref:Microsoft.AspNetCore.Http.Json.JsonOptions> `Startup.ConfigureServices` .
* `WriteAsJsonAsync` ve `ReadFromJsonAsync` bir nesneyi kabul eden aşırı yüklemeleri vardır <xref:System.Text.Json.JsonSerializerOptions> . Bu seçenek nesnesi varsayılan seçenekleri geçersiz kılar.

[!code-csharp[](route-to-code/sample/Startup6.cs?name=snippet)]

## <a name="authentication-and-authorization"></a>Kimlik doğrulama ve yetkilendirme

Route-to-code kimlik doğrulama ve yetkilendirmeyi destekler. Ve gibi öznitelikler `[Authorize]` `[AllowAnonymous]` bir istek temsilcisiyle eşlenen uç noktalara yerleştirilemez. Bunun yerine, yetkilendirme meta verileri <xref:Microsoft.AspNetCore.Builder.AuthorizationEndpointConventionBuilderExtensions.RequireAuthorization%2A> ve <xref:Microsoft.AspNetCore.Builder.AuthorizationEndpointConventionBuilderExtensions.AllowAnonymous%2A> genişletme yöntemleri kullanılarak eklenir.

[!code-csharp[](route-to-code/sample/Startup.cs?name=snippet&highlight=30)]

## <a name="dependency-injection"></a>Bağımlılık ekleme

Bir Oluşturucu kullanılarak [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection) ile mümkün değildir Route-to-code . Web API 'SI, oluşturucuya eklenen hizmetler ile sizin için bir denetleyici oluşturur. Bir uç nokta yürütüldüğünde bir tür oluşturulmaz, bu nedenle hizmetler el ile çözümlenmelidir.

Rota tabanlı API 'Ler, <xref:System.IServiceProvider> Hizmetleri çözümlemek için kullanabilir:

* Gibi geçici ve kapsamlı ömür Hizmetleri, `DbContext` bir uç noktanın istek temsilcisi Içindeki [HttpContext. requestservices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) 'ten çözümlenmelidir.
* Gibi tek yaşam süresi Hizmetleri `ILogger` [IEndpointRouteBuilder. ServiceProvider](xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder.ServiceProvider)öğesinden çözülebilir. Hizmetler, istek temsilcileri dışında çözülebilir ve uç noktalar arasında paylaşılabilir.

[!code-csharp[](route-to-code/sample/Startup4.cs?name=snippet&highlight=3,7)]

Dı kullanımı yoğun kullanan API 'Ler, DI 'yi destekleyen ASP.NET Core bir uygulama türü kullanmayı göz önünde bulundurmalıdır. Örneğin, ASP.NET Core Web API 'SI. Bir denetleyicinin oluşturucusunu kullanan hizmet ekleme hizmetleri el ile çözümlenmeden daha kolay.

## <a name="api-project-structure"></a>API proje yapısı

Rota tabanlı API 'Lerin *Startup.cs* içinde bulunması gerekmez. API 'Ler diğer dosyalara yerleştirilebilecek ve ile başlangıç sırasında eşlenebilir `UseEndpoints` . Bu yaklaşım, başlangıç yapılandırması dosya boyutunu azaltır.

Bir yöntemi tanımlayan aşağıdaki statik sınıfı göz önünde bulundurun `UserApi` `Map` . Yöntemi rota tabanlı API 'Leri eşleştirir.

[!code-csharp[](route-to-code/sample/UserApi.cs?name=snippet)]

`Startup.Configure`Yönteminde, `Map` yöntemi ve diğer sınıfın statik yöntemleri içinde çağrılır `UseEndpoints` :

[!code-csharp[](route-to-code/sample/Startup5.cs?name=snippet)]

## <a name="notable-missing-features-compared-to-web-api"></a>Web API 'siyle karşılaştırılan Notable eksik Özellikler

Route-to-code , temel JSON API 'Leri için tasarlanmıştır. ASP.NET Core Web API 'SI tarafından sunulan gelişmiş özelliklerin birçoğu için destek sahibi değildir.

Tarafından sağlanmayan Özellikler Route-to-code şunlardır:

* Model bağlama
* Model doğrulaması
* Openapı/Swagger
* İçerik anlaşması
* Oluşturucu bağımlılığı ekleme
* `ProblemDetails` ([RFC 7807](https://tools.ietf.org/html/rfc7807))

Önceki listede bazı özelliklerden birini gerektiriyorsa API oluşturmak için [ASP.NET Core Web API 'sini](xref:web-api/index) kullanmayı düşünün.

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:web-api/index>
* <xref:fundamentals/routing>
* <xref:fundamentals/dependency-injection>
