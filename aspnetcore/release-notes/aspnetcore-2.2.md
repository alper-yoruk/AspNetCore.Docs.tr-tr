---
title: ASP.NET Core 2.2'deki yenilikler
author: rick-anderson
description: ASP.NET Core 2.2'deki yeni özellikler hakkında bilgi edinin.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- SignalR
uid: aspnetcore-2.2
ms.openlocfilehash: 54d3f1e7b0c94d69781c052694305a389a675019
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977177"
---
# <a name="whats-new-in-aspnet-core-22"></a>ASP.NET Core 2.2'deki yenilikler

Bu makalede, ilgili belgelere bağlantılar içeren ASP.NET Core 2.2'deki en önemli değişiklikler vurgulamaktadır.

## <a name="openapi-analyzers--conventions"></a>OpenAPI Analizörleri & Konvansiyonları

OpenAPI (eski adıyla Swagger), REST API'lerini tanımlamak için bir dil-agnostik belirtimidir. OpenAPI ekosistemi, belirtimi kullanarak istemci kodunu keşfetmeye, sınanabilenve üreten araçlara sahiptir. ASP.NET Core MVC'de OpenAPI belgelerinin üretilmesi ve görselleştirilmesi için destek, [NSwag](https://github.com/RicoSuter/NSwag) ve [Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore)gibi topluluk odaklı projeler aracılığıyla sağlanmaktadır. ASP.NET Core 2.2, OpenAPI belgeleri oluşturmak için gelişmiş takım oluşturma ve çalışma zamanı deneyimleri sağlar.

Daha fazla bilgi için aşağıdaki kaynaklara bakın:

* <xref:web-api/advanced/analyzers>
* <xref:web-api/advanced/conventions>
* [ASP.NET Core 2.2.0-preview1: OpenAPI Analizörleri & Kuralları](https://blogs.msdn.microsoft.com/webdev/2018/08/23/asp-net-core-2-20-preview1-open-api-analyzers-conventions/)

## <a name="problem-details-support"></a>Sorun ayrıntıları desteği

ASP.NET Core 2.1 `ProblemDetails`tanıtıldı , [RFC 7807](https://tools.ietf.org/html/rfc7807) belirtimine dayalı bir http yanıt ile bir hata ayrıntıları taşımak için. 2.2'de, `ProblemDetails` `ApiControllerAttribute`'ile atfedilen denetleyicilerde istemci hata kodları için standart yanıt. `IActionResult` Dönen bir istemci hata durum kodu (4xx) şimdi bir `ProblemDetails` gövde döndürür. Sonuç, istek günlüklerini kullanarak hatayı ilişkilendirmek için kullanılabilecek bir korelasyon kimliği de içerir. İstemci `ProducesResponseType` hataları için, `ProblemDetails` yanıt türü olarak kullanmak için varsayılan. Bu OpenAPI / Swagger çıkış NSwag veya Swashbuckle.AspNetCore kullanılarak oluşturulan belgelenmiştir.

## <a name="endpoint-routing"></a>Uç Nokta Yönlendirme

ASP.NET Core 2.2, isteklerin daha iyi gönderilmesi için yeni bir *uç nokta yönlendirme* sistemi kullanır. Değişiklikler yeni bağlantı oluşturma API üyeleri ve rota parametre transformatörleri içerir.

Daha fazla bilgi için aşağıdaki kaynaklara bakın:

* [2.2'de uç nokta yönlendirme](https://blogs.msdn.microsoft.com/webdev/2018/08/27/asp-net-core-2-2-0-preview1-endpoint-routing/)
* [Rota parametre transformatörleri](https://www.hanselman.com/blog/ASPNETCore22ParameterTransformersForCleanURLGenerationAndSlugsInRazorPagesOrMVC.aspx) (bkz. **Yönlendirme** bölümü)
* [IRouter ve uç nokta tabanlı yönlendirme arasındaki farklar](xref:fundamentals/routing?view=aspnetcore-2.2#differences-from-earlier-versions-of-routing)

## <a name="health-checks"></a>Sistem durumu denetimleri

Yeni bir sağlık denetimi hizmeti, ASP.NET Core'un Kubernetes gibi sistem durumu denetimleri gerektiren ortamlarda kullanılmasını kolaylaştırır. Sistem durumu denetimleri ara yazılım ve `IHealthCheck` bir soyutlama ve hizmet tanımlayan kitaplıklar kümesi içerir.

Sistem denetimleri, bir sistemin isteklere normal şekilde yanıt verilip vermeyince hızlı bir şekilde belirlemek için bir konteyner orchestrator veya yük dengeleyicisi tarafından kullanılır. Bir kapsayıcı orkestratör, yuvarlanan dağıtımı durdurarak veya bir kapsayıcıyı yeniden başlatarak başarısız bir sistem durumu denetimine yanıt verebilir. Yük dengeleyicisi, trafiği hizmetin başarısız örneğinden uzağa yönlendirme yaparak sistem durumu denetimine yanıt verebilir.

Sistem durumu kontrolleri, izleme sistemleri tarafından kullanılan bir HTTP bitiş noktası olarak bir uygulama tarafından ortaya konur. Sistem durumu denetimleri, çeşitli gerçek zamanlı izleme senaryoları ve izleme sistemleri için yapılandırılabilir. Sağlık kontrolleri hizmeti [BeatPulse projesi](https://github.com/Xabaril/BeatPulse)ile entegre. bu da düzinelerce popüler sistem ve bağımlılık için çek eklemeyi kolaylaştırır.

Daha fazla bilgi için, [ASP.NET Core'daki Sağlık denetimleri'ne](xref:host-and-deploy/health-checks)bakın.

## <a name="http2-in-kestrel"></a>HTTP/2 kerkenez içinde

ASP.NET Core 2.2 HTTP/2 için destek ekler.

HTTP/2, HTTP protokolünün önemli bir revizyonudur. HTTP/2'nin önemli özellikleri şunlardır:

* Üstbilgi sıkıştırma desteği.
* Tek bir bağlantı üzerinden tamamen çok katlı akışlar.

HTTP/2, HTTP'nin semantiklerini (örneğin, HTTP üstbilgileri ve yöntemleri) korurken, verilerin istemci ve sunucu arasında nasıl çerçevelendirilip gönderildiğine ilişkin HTTP/1.x'ten bir kırılma değişikliğidir.

Çerçeveleme bu değişikliğin bir sonucu olarak, sunucular ve istemciler kullanılan protokol sürümü müzakere etmek gerekir. Uygulama Katmanı Protokol Anlaşması (ALPN), sunucu ve istemcinin TLS el sıkışmasının bir parçası olarak kullanılan protokol sürümünü müzakere etmesine olanak tanıyan bir TLS uzantısıdır. Protokol üzerinde sunucu ve istemci arasında önceden bilgi sahibi olmak mümkün olsa da, tüm büyük tarayıcılar BIR HTTP/2 bağlantısı kurmanın tek yolu olarak ALPN'i destekler.

Daha fazla bilgi için [HTTP/2 desteğine](xref:fundamentals/servers/index?view=aspnetcore-2.2#http2-support)bakın.

## <a name="kestrel-configuration"></a>Kerkenez konfigürasyonu

ASP.NET Core'un önceki sürümlerinde Kerkenez seçenekleri `UseKestrel`çağrılarek yapılandırılır. 2.2'de, Kerkenez seçenekleri ev `ConfigureKestrel` sahibi oluşturucuyu çağırarak yapılandırılır. Bu değişiklik, süreç içi barındırma `IServer` için kayıt sırası ile ilgili bir sorunu giderir. Daha fazla bilgi için aşağıdaki kaynaklara bakın:

* [UseIIS çakışma azaltmak](https://github.com/aspnet/KestrelHttpServer/issues/2760)
* [ConfigureKestrel ile Kerkenez sunucu seçeneklerini yapılandırın](xref:fundamentals/servers/kestrel?view=aspnetcore-2.2#how-to-use-kestrel-in-aspnet-core-apps)

## <a name="iis-in-process-hosting"></a>IIS süreç içi barındırma

ASP.NET Core'un önceki sürümlerinde, IIS ters proxy olarak hizmet vermektedir. 2.2'de, ASP.NET Çekirdek Modülü CoreCLR'yi önyüklemeyapabilir ve IIS işçi süreci içinde bir uygulamayı barındırabilir *(w3wp.exe).* Süreç içi barındırma, IIS ile çalışırken performans ve tanılama kazançları sağlar.

Daha fazla bilgi [için, IIS için süreç içi barındırma](xref:host-and-deploy/aspnet-core-module?view=aspnetcore-2.2#in-process-hosting-model)ya bakın.

## <a name="opno-locsignalr-java-client"></a>SignalRJava istemcisi

ASP.NET Core 2.2 için SignalRbir Java İstemci tanıttı. Bu istemci, Android uygulamaları SignalR da dahil olmak üzere Java kodundan ASP.NET Core Server'a bağlanmayı destekler.

Daha fazla bilgi için [Core SignalR Java istemcisi ASP.NET](https://docs.microsoft.com/aspnet/core/signalr/java-client?view=aspnetcore-2.2)bakın.

## <a name="cors-improvements"></a>CORS iyileştirmeleri

`Accept`ASP.NET Core'un önceki sürümlerinde, `Accept-Language` `Content-Language` `Origin` `CorsPolicy.Headers`CORS Middleware yapılandırılan değerlere bakılmaksızın gönderilmesine izin verir. 2.2'de, BIR CORS Middleware ilke eşleşmesi `Access-Control-Request-Headers` yalnızca gönderilen üstbilgiler `WithHeaders`üstbilgilerle tam olarak eşleştiğinde mümkündür.

Daha fazla bilgi için [CORS Middleware'e](xref:security/cors?view=aspnetcore-2.2#set-the-allowed-request-headers)bakın.

## <a name="response-compression"></a>Yanıt sıkıştırma

ASP.NET Core 2.2 [Brotli sıkıştırma biçimi](https://tools.ietf.org/html/rfc7932)ile yanıtları sıkıştırabilirsiniz.

Daha fazla bilgi için bkz: [Yanıt Sıkıştırma Middleware Brotli sıkıştırma destekler.](xref:performance/response-compression?view=aspnetcore-2.2#brotli-compression-provider)

## <a name="project-templates"></a>Proje şablonları

ASP.NET Core web proje şablonları [Bootstrap 4](https://getbootstrap.com/docs/4.1/migration/) ve [Açısal 6](https://blog.angular.io/version-6-of-angular-now-available-cc56b0efa7a4)güncellendi. Yeni görünüm görsel olarak daha basit tir ve uygulamanın önemli yapılarını görmenizi kolaylaştırır.

![Ana Sayfa veya Dizin sayfası](~/tutorials/razor-pages/razor-pages-start/_static/home2.2.png)

## <a name="validation-performance"></a>Doğrulama performansı

MVC'nin doğrulama sistemi genişletilebilir ve esnek olacak şekilde tasarlanmıştır ve istek bazında belirli bir modele hangi doğrulayıcıların uygulanacağınızı belirlemenize olanak sağlar. Bu, karmaşık doğrulama sağlayıcıları nın yazılması için harikadır. Ancak, en yaygın durumda bir uygulama yalnızca yerleşik doğrulayıcıları kullanır ve bu ekstra esneklik gerektirmez. Yerleşik doğrulayıcılar [Gerekli] ve [StringLength] gibi DataAnnotations içerir ve. `IValidatableObject`

Core 2.2ASP.NET, belirli bir model grafiğinin doğrulama gerektirmediğini belirlerse, MVC kısa devre doğrulama yapabilir. Doğrulamayı atlamak, doğrulayıcısı olmayan veya olmayan modelleri doğrularken önemli iyileştirmelere neden olur. Bu, ilkel lerin koleksiyonları `byte[]`(, , `string[]`, `Dictionary<string, string>`gibi) veya çok sayıda doğrulayıcı sıyrık içermeyen karmaşık nesne grafikleri gibi nesneleri içerir.

## <a name="http-client-performance"></a>HTTP İstemci performansı

Core 2.2ASP.NETde, bağlantı `SocketsHttpHandler` havuzu kilitleme çekişmesini azaltarak performansı artırıldı. Bazı mikro hizmetler mimarileri gibi giden birçok HTTP isteği nde bulunan uygulamalar için iş bölümü geliştirilir. Yük altında, `HttpClient` iş çıktısı Linux'ta %60'a ve Windows'da %20'ye kadar geliştirilebilir.

Daha fazla bilgi için, [bu iyileştirmeyi yapan çekme isteğine](https://github.com/dotnet/corefx/pull/32568)bakın.

## <a name="additional-information"></a>Ek bilgiler

Değişikliklerin tam listesi [için, ASP.NET Core 2.2 Sürüm Notları](https://github.com/dotnet/aspnetcore/releases/tag/2.2.0)bölümüne bakın.
