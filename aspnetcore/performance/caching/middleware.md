---
title: ASP.NET Core 'de yanıt önbelleğe alma ara yazılımı
author: rick-anderson
description: ASP.NET Core 'de yanıt önbelleğe alma ara yazılımını yapılandırmayı ve kullanmayı öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: performance/caching/middleware
ms.openlocfilehash: 3c28b6c736f07c0d0483152eeec4300a5a92224c
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93052116"
---
# <a name="response-caching-middleware-in-aspnet-core"></a>ASP.NET Core 'de yanıt önbelleğe alma ara yazılımı

[John Luo](https://github.com/JunTaoLuo) tarafından

::: moniker range=">= aspnetcore-3.0"

Bu makalede, yanıt önbelleğe alma ara yazılımı ASP.NET Core bir uygulamada nasıl yapılandırılacağı açıklanmaktadır. Ara yazılım, yanıtların önbelleklenebilir olup olmadığını belirler, yanıtları depolar ve önbellekten yanıt verir. HTTP önbelleği ve özniteliğe giriş için [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) bkz. [Yanıt önbelleği](xref:performance/caching/response).

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/middleware/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="configuration"></a>Yapılandırma

Yanıt önbelleğe alma ara yazılımı, paylaşılan Framework aracılığıyla ASP.NET Core uygulamalar için örtülü olarak kullanılabilir.

' De `Startup.ConfigureServices` , yanıt önbelleğe alma ara yazılımını hizmet koleksiyonuna ekleyin:

[!code-csharp[](middleware/samples/3.x/ResponseCachingMiddleware/Startup.cs?name=snippet1&highlight=3)]

Uygulamayı, <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching*> içindeki istek işleme işlem hattına bir ara yazılım ekleyen uzantı yöntemiyle ara yazılımı kullanacak şekilde yapılandırın `Startup.Configure` :

[!code-csharp[](middleware/samples/3.x/ResponseCachingMiddleware/Startup.cs?name=snippet2&highlight=17)]

> [!WARNING]
> <xref:Owin.CorsExtensions.UseCors%2A><xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching%2A> [CORS ara yazılımı](xref:security/cors)kullanılmadan önce çağrılmalıdır.

Örnek uygulama, sonraki isteklerde önbelleğe almayı denetlemek için üstbilgiler ekler:

* [Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2): 10 saniyeye kadar önbelleklenebilir yanıtları önbelleğe alır.
* [Farklılık](https://tools.ietf.org/html/rfc7231#section-7.1.4): yalnızca sonraki Isteklerin [kabul etme-kodlama](https://tools.ietf.org/html/rfc7231#section-5.3.4) üst bilgisi özgün istekten eşleşiyorsa, önbelleğe alınmış bir yanıt sunacak şekilde ara yazılımı yapılandırır.

[!code-csharp[](middleware/samples_snippets/3.x/AddHeaders.cs)]

Yukarıdaki üstbilgiler yanıta yazılmaz ve bir denetleyici, eylem veya sayfa olduğunda geçersiz kılınır Razor :

* [[Responsecache]](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) özniteliğine sahiptir. Bir özellik ayarlanmamışsa bile bu geçerlidir. Örneğin, [VaryByHeader](./response.md#vary) özelliğinin atlanması karşılık gelen üstbilginin yanıttan kaldırılmasına neden olur.

Yanıt önbelleğe alma ara yazılımı yalnızca 200 (Tamam) durum kodu ile sonuçlanan sunucu yanıtlarını önbelleğe alır. [Hata sayfaları](xref:fundamentals/error-handling)dahil diğer tüm yanıtlar, ara yazılım tarafından yok sayılır.

> [!WARNING]
> Kimliği doğrulanmış istemciler için içerik içeren yanıtların, ara yazılımın bu yanıtları depolamasını ve hizmet vermek için önbelleğe alınamaz olarak işaretlenmesi gerekir. Bir yanıtın önbelleklenmesini nasıl belirlediği hakkında bilgi için bkz. [önbelleğe alma koşulları](#conditions-for-caching) .

## <a name="options"></a>Seçenekler

Yanıt önbelleğe alma seçenekleri aşağıdaki tabloda gösterilmiştir.

| Seçenek | Açıklama |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize> | Yanıt gövdesi için bayt cinsinden en büyük önbelleklenebilir boyut. Varsayılan değer `64 * 1024 * 1024` (64 MB). |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit> | Yanıt önbelleği ara yazılımı için bayt cinsinden boyut sınırı. Varsayılan değer `100 * 1024 * 1024` (100 MB). |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.UseCaseSensitivePaths> | Yanıtların büyük/küçük harfe duyarlı yollarda önbelleğe alınıp alınmayacağını belirler. `false` varsayılan değerdir. |

Aşağıdaki örnek, şu şekilde bir ara yazılım yapılandırır:

* Gövde boyutu 1.024 bayttan küçük veya buna eşit olan önbellek yanıtları.
* Yanıtları büyük/küçük harfe duyarlı yollarla depolayın. Örneğin, `/page1` ve `/Page1` ayrı olarak depolanır.

```csharp
services.AddResponseCaching(options =>
{
    options.MaximumBodySize = 1024;
    options.UseCaseSensitivePaths = true;
});
```

## <a name="varybyquerykeys"></a>VaryByQueryKeys

MVC/web API denetleyicileri veya Razor sayfaları sayfa modellerini kullanırken [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) öznitelik, yanıt önbelleğe alma için uygun üst bilgileri ayarlamak için gereken parametreleri belirtir. Yalnızca `[ResponseCache]` Ara yazılım gerektiren özniteliğin tek parametresi, <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute.VaryByQueryKeys> gerçek bir http üst bilgisine karşılık gelmiyor. Daha fazla bilgi için bkz. <xref:performance/caching/response#responsecache-attribute>.

`[ResponseCache]`Özniteliği kullanmıyorsanız, yanıt önbelleğe alma ile değiştirilebilir `VaryByQueryKeys` . <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingFeature>Doğrudan [HttpContext. Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features)içinden kullanın:

```csharp
var responseCachingFeature = context.HttpContext.Features.Get<IResponseCachingFeature>();

if (responseCachingFeature != null)
{
    responseCachingFeature.VaryByQueryKeys = new[] { "MyKey" };
}
```

Tek bir değerin ' de değerine eşit olması `*` `VaryByQueryKeys` , önbelleğin tüm istek sorgu parametrelerine göre değişiklik gösterir.

## <a name="http-headers-used-by-response-caching-middleware"></a>Yanıt önbelleğe alma ara yazılımı tarafından kullanılan HTTP üstbilgileri

Aşağıdaki tabloda, yanıt önbelleğini etkileyen HTTP üstbilgileri hakkında bilgi verilmektedir.

| Üst bilgi | Ayrıntılar |
| ------ | ------- |
| `Authorization` | Üst bilgi varsa yanıt önbelleğe alınmaz. |
| `Cache-Control` | Ara yazılım yalnızca önbellek yönergesi ile işaretlenmiş önbelleğe alma yanıtlarını dikkate alır `public` . Aşağıdaki parametrelerle önbelleğe alma denetimi:<ul><li>Maksimum yaş</li><li>en fazla-eski&#8224;</li><li>en az-yeni</li><li>yeniden doğrulama gerekir</li><li>önbellek yok</li><li>mağaza yok</li><li>yalnızca-if-önbelleğe alındı</li><li>private</li><li>public</li><li>s-maxage</li><li>Proxy-yeniden doğrulama&#8225;</li></ul>&#8224;hiçbir sınır belirtilmemişse `max-stale` , ara yazılım hiçbir eylemde bulunmaz.<br>&#8225;`proxy-revalidate` , ile aynı etkiye sahiptir `must-revalidate` .<br><br>Daha fazla bilgi için bkz. [RFC 7231: istek Cache-Control yönergeleri](https://tools.ietf.org/html/rfc7234#section-5.2.1). |
| `Pragma` | `Pragma: no-cache`İstekteki bir üst bilgi, ile aynı etkiyi üretir `Cache-Control: no-cache` . Bu üst bilgi, varsa başlıktaki ilgili yönergeler tarafından geçersiz kılınır `Cache-Control` . HTTP/1.0 ile geriye dönük uyumluluk için değerlendirilir. |
| `Set-Cookie` | Üst bilgi varsa yanıt önbelleğe alınmaz. İstek işleme ardışık düzeninde bir veya daha fazla s ayarlayan herhangi bir ara yazılım, cookie yanıt önbelleğe alma ara hattının yanıtı önbelleğe almasını engeller (örneğin, [ cookie tabanlı TempData sağlayıcısı](xref:fundamentals/app-state#tempdata)).  |
| `Vary` | Üst bilgi, `Vary` başka bir üst bilgi tarafından önbelleğe alınan yanıtı değiştirmek için kullanılır. Örneğin, üst bilgi `Vary: Accept-Encoding` `Accept-Encoding: gzip` ve ayrı ayrı istekler için yanıtları önbelleğe alan üstbilgiyi ekleyerek kodlamaya göre yanıtları önbelleğe alır `Accept-Encoding: text/plain` . Üstbilgi değeri olan bir yanıt hiçbir şekilde `*` depolanmaz. |
| `Expires` | Bu üstbilginin eski olduğu bir yanıt, diğer üstbilgiler tarafından geçersiz kılınmadıkça depolanmaz veya alınamaz `Cache-Control` . |
| `If-None-Match` | Tam yanıt, değer değilse önbellekten `*` , `ETag` yanıtın ise belirtilen değerlerden hiçbiriyle eşleşmez. Aksi takdirde, 304 (değiştirilmez) yanıtı sunulur. |
| `If-Modified-Since` | `If-None-Match`Üst bilgi yoksa, önbelleğe alınmış yanıt tarihi verilen değerden daha yeniyse önbellekten tam bir yanıt sunulur. Aksi takdirde, *304 olarak değiştirilmemiş* bir yanıt sunulur. |
| `Date` | Önbellekten hizmet verirken, `Date` özgün yanıtta sağlanmadıysa üst bilgi ara yazılım tarafından ayarlanır. |
| `Content-Length` | Önbellekten hizmet verirken, `Content-Length` özgün yanıtta sağlanmadıysa üst bilgi ara yazılım tarafından ayarlanır. |
| `Age` | `Age`Özgün yanıtta gönderilen üstbilgi yok sayılır. Ara yazılım, önbelleğe alınmış bir yanıta hizmet verirken yeni bir değeri hesaplar. |

## <a name="caching-respects-request-cache-control-directives"></a>İstekleri önbelleğe alma isteği Cache-Control yönergeleri

Ara yazılım, [HTTP 1,1 önbelleğe alma belirtiminin](https://tools.ietf.org/html/rfc7234#section-5.2)kurallarına uyar. Kurallar, istemci tarafından gönderilen geçerli bir üst bilgiyi kabul etmek için bir önbellek gerektirir `Cache-Control` . Belirtim altında, bir istemci bir `no-cache` üst bilgi değeri ile istek yapabilir ve sunucuyu her istek için yeni bir yanıt oluşturmaya zorlayabilir. Şu anda, ara yazılım resmi önbelleğe alma belirtimine bağlı olduğundan, ara yazılım kullanılırken bu önbelleğe alma davranışı üzerinde geliştirici denetimi yoktur.

Önbelleğe alma davranışı üzerinde daha fazla denetim için ASP.NET Core diğer önbelleğe alma özelliklerine göz atın. Aşağıdaki konulara bakın:

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

## <a name="troubleshooting"></a>Sorun giderme

Önbelleğe alma davranışı beklenmiyorsa, yanıtların önbelleklenmesini ve önbellekten sunulduğunu doğrulayın. İsteğin gelen üst bilgilerini ve yanıtın giden üst bilgilerini inceleyin. Hata ayıklamaya yardımcı olmak için [günlük kaydını](xref:fundamentals/logging/index) etkinleştirin.

Önbelleğe alma davranışını test ederken ve sorun giderirken, bir tarayıcı, istenmeyen yollarla önbelleğe almayı etkileyen istek üst bilgilerini ayarlayabilir. Örneğin, bir tarayıcı `Cache-Control` üstbilgiyi `no-cache` `max-age=0` bir sayfa yenileyene veya olarak ayarlayabilir. Aşağıdaki araçlar, istek üst bilgilerini açık bir şekilde ayarlayabilir ve önbelleğe alma testi için tercih edilir:

* [Fiddler](https://www.telerik.com/fiddler)
* [Postman](https://www.getpostman.com/)

### <a name="conditions-for-caching"></a>Önbelleğe alma koşulları

* İstek, 200 (Tamam) durum kodu ile bir sunucu yanıtı ile sonuçlanmalıdır.
* İstek yöntemi GET veya HEAD olmalıdır.
* `Startup.Configure`' De, yanıt önbelleğe alma ara yazılımı, önbelleğe alma gerektiren ara yazılımlar için yerleştirilmelidir. Daha fazla bilgi için bkz. <xref:fundamentals/middleware/index>.
* `Authorization`Üst bilgi mevcut olmamalıdır.
* `Cache-Control` üst bilgi parametreleri geçerli olmalıdır ve yanıtın işaretlenmiş ve işaretlenmemiş olması gerekir `public` `private` .
* Üst bilgi mevcut olmadığında üst bilgi mevcut olmamalıdır, üstbilgi mevcut `Pragma: no-cache` `Cache-Control` olduğunda üstbilgiyi `Cache-Control` geçersiz kılar `Pragma` .
* `Set-Cookie`Üst bilgi mevcut olmamalıdır.
* `Vary` üst bilgi parametreleri geçerli ve eşit olmalıdır `*` .
* `Content-Length`Üst bilgi değeri (ayarlandıysa), yanıt gövdesinin boyutuyla aynı olmalıdır.
* <xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature>Kullanılmaz.
* Yanıtın `Expires` üst bilgi ve `max-age` ve önbellek yönergeleri tarafından belirtilen eski olmaması gerekir `s-maxage` .
* Yanıt arabelleğe alma başarılı olmalıdır. Yanıtın boyutu yapılandırılan veya varsayılan değerden küçük olmalıdır <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit> . Yanıtın gövde boyutu yapılandırılan veya varsayılan değerden küçük olmalıdır <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize> .
* Yanıt, [RFC 7234](https://tools.ietf.org/html/rfc7234) belirtimlerine göre önbelleklenebilir olmalıdır. Örneğin, `no-store` yönerge istek veya yanıt üst bilgisi alanlarında mevcut olmamalıdır. Ayrıntılar için bkz. 3. Bölüm: [RFC 7234](https://tools.ietf.org/html/rfc7234) *önbelleklerinde yanıtları depolama* .

> [!NOTE]
> Siteler arası Istek sahteciliği (CSRF) saldırılarını önlemeye yönelik güvenli belirteçler oluşturmaya yönelik Antiforgery sistemi, `Cache-Control` `Pragma` `no-cache` yanıtların önbelleğe alınmaması için ve üst bilgilerini olarak ayarlar. HTML form öğeleri için antiforgery belirteçlerini devre dışı bırakma hakkında daha fazla bilgi için bkz <xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration> ..

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Bu makalede, yanıt önbelleğe alma ara yazılımı ASP.NET Core bir uygulamada nasıl yapılandırılacağı açıklanmaktadır. Ara yazılım, yanıtların önbelleklenebilir olup olmadığını belirler, yanıtları depolar ve önbellekten yanıt verir. HTTP önbelleği ve özniteliğe giriş için [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) bkz. [Yanıt önbelleği](xref:performance/caching/response).

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/middleware/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="configuration"></a>Yapılandırma

Microsoft. [aspnetcore. app metapackage](xref:fundamentals/metapackage-app) kullanın veya [Microsoft. Aspnetcore. responsecaching](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCaching/) paketine bir paket başvurusu ekleyin.

' De `Startup.ConfigureServices` , yanıt önbelleğe alma ara yazılımını hizmet koleksiyonuna ekleyin:

[!code-csharp[](middleware/samples/2.x/ResponseCachingMiddleware/Startup.cs?name=snippet1&highlight=3)]

Uygulamayı, <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching*> içindeki istek işleme işlem hattına bir ara yazılım ekleyen uzantı yöntemiyle ara yazılımı kullanacak şekilde yapılandırın `Startup.Configure` :

[!code-csharp[](middleware/samples/2.x/ResponseCachingMiddleware/Startup.cs?name=snippet2&highlight=14)]

Örnek uygulama, sonraki isteklerde önbelleğe almayı denetlemek için üstbilgiler ekler:

* [Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2): 10 saniyeye kadar önbelleklenebilir yanıtları önbelleğe alır.
* [Farklılık](https://tools.ietf.org/html/rfc7231#section-7.1.4): yalnızca sonraki Isteklerin [kabul etme-kodlama](https://tools.ietf.org/html/rfc7231#section-5.3.4) üst bilgisi özgün istekten eşleşiyorsa, önbelleğe alınmış bir yanıt sunacak şekilde ara yazılımı yapılandırır.

[!code-csharp[](middleware/samples_snippets/2.x/AddHeaders.cs)]

Yukarıdaki üstbilgiler yanıta yazılmaz ve bir denetleyici, eylem veya sayfa olduğunda geçersiz kılınır Razor :

* [[Responsecache]](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) özniteliğine sahiptir. Bir özellik ayarlanmamışsa bile bu geçerlidir. Örneğin, [VaryByHeader](./response.md#vary) özelliğinin atlanması karşılık gelen üstbilginin yanıttan kaldırılmasına neden olur.

Yanıt önbelleğe alma ara yazılımı yalnızca 200 (Tamam) durum kodu ile sonuçlanan sunucu yanıtlarını önbelleğe alır. [Hata sayfaları](xref:fundamentals/error-handling)dahil diğer tüm yanıtlar, ara yazılım tarafından yok sayılır.

> [!WARNING]
> Kimliği doğrulanmış istemciler için içerik içeren yanıtların, ara yazılımın bu yanıtları depolamasını ve hizmet vermek için önbelleğe alınamaz olarak işaretlenmesi gerekir. Bir yanıtın önbelleklenmesini nasıl belirlediği hakkında bilgi için bkz. [önbelleğe alma koşulları](#conditions-for-caching) .

## <a name="options"></a>Seçenekler

Yanıt önbelleğe alma seçenekleri aşağıdaki tabloda gösterilmiştir.

| Seçenek | Açıklama |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize> | Yanıt gövdesi için bayt cinsinden en büyük önbelleklenebilir boyut. Varsayılan değer `64 * 1024 * 1024` (64 MB). |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit> | Yanıt önbelleği ara yazılımı için bayt cinsinden boyut sınırı. Varsayılan değer `100 * 1024 * 1024` (100 MB). |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.UseCaseSensitivePaths> | Yanıtların büyük/küçük harfe duyarlı yollarda önbelleğe alınıp alınmayacağını belirler. `false` varsayılan değerdir. |

Aşağıdaki örnek, şu şekilde bir ara yazılım yapılandırır:

* Gövde boyutu 1.024 bayttan küçük veya buna eşit olan önbellek yanıtları.
* Yanıtları büyük/küçük harfe duyarlı yollarla depolayın. Örneğin, `/page1` ve `/Page1` ayrı olarak depolanır.

```csharp
services.AddResponseCaching(options =>
{
    options.MaximumBodySize = 1024;
    options.UseCaseSensitivePaths = true;
});
```

## <a name="varybyquerykeys"></a>VaryByQueryKeys

MVC/web API denetleyicileri veya Razor sayfaları sayfa modellerini kullanırken [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) öznitelik, yanıt önbelleğe alma için uygun üst bilgileri ayarlamak için gereken parametreleri belirtir. Yalnızca `[ResponseCache]` Ara yazılım gerektiren özniteliğin tek parametresi, <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute.VaryByQueryKeys> gerçek bir http üst bilgisine karşılık gelmiyor. Daha fazla bilgi için bkz. <xref:performance/caching/response#responsecache-attribute>.

`[ResponseCache]`Özniteliği kullanmıyorsanız, yanıt önbelleğe alma ile değiştirilebilir `VaryByQueryKeys` . <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingFeature>Doğrudan [HttpContext. Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features)içinden kullanın:

```csharp
var responseCachingFeature = context.HttpContext.Features.Get<IResponseCachingFeature>();

if (responseCachingFeature != null)
{
    responseCachingFeature.VaryByQueryKeys = new[] { "MyKey" };
}
```

Tek bir değerin ' de değerine eşit olması `*` `VaryByQueryKeys` , önbelleğin tüm istek sorgu parametrelerine göre değişiklik gösterir.

## <a name="http-headers-used-by-response-caching-middleware"></a>Yanıt önbelleğe alma ara yazılımı tarafından kullanılan HTTP üstbilgileri

Aşağıdaki tabloda, yanıt önbelleğini etkileyen HTTP üstbilgileri hakkında bilgi verilmektedir.

| Üst bilgi | Ayrıntılar |
| ------ | ------- |
| `Authorization` | Üst bilgi varsa yanıt önbelleğe alınmaz. |
| `Cache-Control` | Ara yazılım yalnızca önbellek yönergesi ile işaretlenmiş önbelleğe alma yanıtlarını dikkate alır `public` . Aşağıdaki parametrelerle önbelleğe alma denetimi:<ul><li>Maksimum yaş</li><li>en fazla-eski&#8224;</li><li>en az-yeni</li><li>yeniden doğrulama gerekir</li><li>önbellek yok</li><li>mağaza yok</li><li>yalnızca-if-önbelleğe alındı</li><li>private</li><li>public</li><li>s-maxage</li><li>Proxy-yeniden doğrulama&#8225;</li></ul>&#8224;hiçbir sınır belirtilmemişse `max-stale` , ara yazılım hiçbir eylemde bulunmaz.<br>&#8225;`proxy-revalidate` , ile aynı etkiye sahiptir `must-revalidate` .<br><br>Daha fazla bilgi için bkz. [RFC 7231: istek Cache-Control yönergeleri](https://tools.ietf.org/html/rfc7234#section-5.2.1). |
| `Pragma` | `Pragma: no-cache`İstekteki bir üst bilgi, ile aynı etkiyi üretir `Cache-Control: no-cache` . Bu üst bilgi, varsa başlıktaki ilgili yönergeler tarafından geçersiz kılınır `Cache-Control` . HTTP/1.0 ile geriye dönük uyumluluk için değerlendirilir. |
| `Set-Cookie` | Üst bilgi varsa yanıt önbelleğe alınmaz. İstek işleme ardışık düzeninde bir veya daha fazla s ayarlayan herhangi bir ara yazılım, cookie yanıt önbelleğe alma ara hattının yanıtı önbelleğe almasını engeller (örneğin, [ cookie tabanlı TempData sağlayıcısı](xref:fundamentals/app-state#tempdata)).  |
| `Vary` | Üst bilgi, `Vary` başka bir üst bilgi tarafından önbelleğe alınan yanıtı değiştirmek için kullanılır. Örneğin, üst bilgi `Vary: Accept-Encoding` `Accept-Encoding: gzip` ve ayrı ayrı istekler için yanıtları önbelleğe alan üstbilgiyi ekleyerek kodlamaya göre yanıtları önbelleğe alır `Accept-Encoding: text/plain` . Üstbilgi değeri olan bir yanıt hiçbir şekilde `*` depolanmaz. |
| `Expires` | Bu üstbilginin eski olduğu bir yanıt, diğer üstbilgiler tarafından geçersiz kılınmadıkça depolanmaz veya alınamaz `Cache-Control` . |
| `If-None-Match` | Tam yanıt, değer değilse önbellekten `*` , `ETag` yanıtın ise belirtilen değerlerden hiçbiriyle eşleşmez. Aksi takdirde, 304 (değiştirilmez) yanıtı sunulur. |
| `If-Modified-Since` | `If-None-Match`Üst bilgi yoksa, önbelleğe alınmış yanıt tarihi verilen değerden daha yeniyse önbellekten tam bir yanıt sunulur. Aksi takdirde, *304 olarak değiştirilmemiş* bir yanıt sunulur. |
| `Date` | Önbellekten hizmet verirken, `Date` özgün yanıtta sağlanmadıysa üst bilgi ara yazılım tarafından ayarlanır. |
| `Content-Length` | Önbellekten hizmet verirken, `Content-Length` özgün yanıtta sağlanmadıysa üst bilgi ara yazılım tarafından ayarlanır. |
| `Age` | `Age`Özgün yanıtta gönderilen üstbilgi yok sayılır. Ara yazılım, önbelleğe alınmış bir yanıta hizmet verirken yeni bir değeri hesaplar. |

## <a name="caching-respects-request-cache-control-directives"></a>İstekleri önbelleğe alma isteği Cache-Control yönergeleri

Ara yazılım, [HTTP 1,1 önbelleğe alma belirtiminin](https://tools.ietf.org/html/rfc7234#section-5.2)kurallarına uyar. Kurallar, istemci tarafından gönderilen geçerli bir üst bilgiyi kabul etmek için bir önbellek gerektirir `Cache-Control` . Belirtim altında, bir istemci bir `no-cache` üst bilgi değeri ile istek yapabilir ve sunucuyu her istek için yeni bir yanıt oluşturmaya zorlayabilir. Şu anda, ara yazılım resmi önbelleğe alma belirtimine bağlı olduğundan, ara yazılım kullanılırken bu önbelleğe alma davranışı üzerinde geliştirici denetimi yoktur.

Önbelleğe alma davranışı üzerinde daha fazla denetim için ASP.NET Core diğer önbelleğe alma özelliklerine göz atın. Aşağıdaki konulara bakın:

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

## <a name="troubleshooting"></a>Sorun giderme

Önbelleğe alma davranışı beklenmiyorsa, yanıtların önbelleklenmesini ve önbellekten sunulduğunu doğrulayın. İsteğin gelen üst bilgilerini ve yanıtın giden üst bilgilerini inceleyin. Hata ayıklamaya yardımcı olmak için [günlük kaydını](xref:fundamentals/logging/index) etkinleştirin.

Önbelleğe alma davranışını test ederken ve sorun giderirken, bir tarayıcı, istenmeyen yollarla önbelleğe almayı etkileyen istek üst bilgilerini ayarlayabilir. Örneğin, bir tarayıcı `Cache-Control` üstbilgiyi `no-cache` `max-age=0` bir sayfa yenileyene veya olarak ayarlayabilir. Aşağıdaki araçlar, istek üst bilgilerini açık bir şekilde ayarlayabilir ve önbelleğe alma testi için tercih edilir:

* [Fiddler](https://www.telerik.com/fiddler)
* [Postman](https://www.getpostman.com/)

### <a name="conditions-for-caching"></a>Önbelleğe alma koşulları

* İstek, 200 (Tamam) durum kodu ile bir sunucu yanıtı ile sonuçlanmalıdır.
* İstek yöntemi GET veya HEAD olmalıdır.
* `Startup.Configure`' De, yanıt önbelleğe alma ara yazılımı, önbelleğe alma gerektiren ara yazılımlar için yerleştirilmelidir. Daha fazla bilgi için bkz. <xref:fundamentals/middleware/index>.
* `Authorization`Üst bilgi mevcut olmamalıdır.
* `Cache-Control` üst bilgi parametreleri geçerli olmalıdır ve yanıtın işaretlenmiş ve işaretlenmemiş olması gerekir `public` `private` .
* Üst bilgi mevcut olmadığında üst bilgi mevcut olmamalıdır, üstbilgi mevcut `Pragma: no-cache` `Cache-Control` olduğunda üstbilgiyi `Cache-Control` geçersiz kılar `Pragma` .
* `Set-Cookie`Üst bilgi mevcut olmamalıdır.
* `Vary` üst bilgi parametreleri geçerli ve eşit olmalıdır `*` .
* `Content-Length`Üst bilgi değeri (ayarlandıysa), yanıt gövdesinin boyutuyla aynı olmalıdır.
* <xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature>Kullanılmaz.
* Yanıtın `Expires` üst bilgi ve `max-age` ve önbellek yönergeleri tarafından belirtilen eski olmaması gerekir `s-maxage` .
* Yanıt arabelleğe alma başarılı olmalıdır. Yanıtın boyutu yapılandırılan veya varsayılan değerden küçük olmalıdır <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit> . Yanıtın gövde boyutu yapılandırılan veya varsayılan değerden küçük olmalıdır <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize> .
* Yanıt, [RFC 7234](https://tools.ietf.org/html/rfc7234) belirtimlerine göre önbelleklenebilir olmalıdır. Örneğin, `no-store` yönerge istek veya yanıt üst bilgisi alanlarında mevcut olmamalıdır. Ayrıntılar için bkz. 3. Bölüm: [RFC 7234](https://tools.ietf.org/html/rfc7234) *önbelleklerinde yanıtları depolama* .

> [!NOTE]
> Siteler arası Istek sahteciliği (CSRF) saldırılarını önlemeye yönelik güvenli belirteçler oluşturmaya yönelik Antiforgery sistemi, `Cache-Control` `Pragma` `no-cache` yanıtların önbelleğe alınmaması için ve üst bilgilerini olarak ayarlar. HTML form öğeleri için antiforgery belirteçlerini devre dışı bırakma hakkında daha fazla bilgi için bkz <xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration> ..

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

::: moniker-end