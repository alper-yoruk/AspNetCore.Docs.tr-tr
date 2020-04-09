---
title: ASP.NET Core için istemci IP güvenli listesi
author: damienbod
description: Uzak IP adreslerini onaylanmış IP adresleri listesine göre doğrulamak için ara yazılım veya eylem filtreleri nasıl yazılanın.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/12/2020
uid: security/ip-safelist
ms.openlocfilehash: 2db879a6918245cbacff8b1a5dc15786ffab6a34
ms.sourcegitcommit: 196e4a36df5be5b04fedcff484a4261f8046ec57
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80471809"
---
# <a name="client-ip-safelist-for-aspnet-core"></a>ASP.NET Core için istemci IP güvenli listesi

Yazar: [Damien Bowden](https://twitter.com/damien_bod) ve [Tom Dykstra](https://github.com/tdykstra)
 
Bu makalede, bir ASP.NET Core uygulamasında bir IP adresi güvenli listesi (izin listesi olarak da bilinir) uygulamanın üç yolu gösterilmektedir. Eşlik eden bir örnek uygulama her üç yaklaşımı da gösterir. Şunu kullanabilirsiniz:

* Middleware her isteğin uzak IP adresini kontrol etmek için.
* MVC eylem filtreleri belirli denetleyicileri veya eylem yöntemleri için isteklerin uzak IP adresini kontrol etmek için.
* Razor Sayfaları, Razor sayfaları için isteklerin uzak IP adresini kontrol etmek için filtreler.

Her durumda, onaylı istemci IP adreslerini içeren bir dize bir uygulama ayarında depolanır. Ara yazılım veya filtre:

* Dizeyi bir diziye ayrışdırır. 
* Dizide uzak IP adresinin olup olmadığını denetler.

Dizi IP adresini içeriyorsa erişime izin verilir. Aksi takdirde, bir HTTP 403 Yasak durum kodu döndürülür.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/ip-safelist/samples) ( nasıl[indirilir](xref:index#how-to-download-a-sample))

## <a name="ip-address-safelist"></a>IP adresi güvenli listesi

Örnek uygulamada IP adresi güvenli listesi:

* `AdminSafeList` *appsettings.json* dosyasındaki özellik tarafından tanımlanır.
* Hem [Internet Protokolü sürüm 4 (IPv4)](https://wikipedia.org/wiki/IPv4) hem de Internet Protocol sürüm 6 [(IPv6)](https://wikipedia.org/wiki/IPv6) adreslerini içerebilecek yarı sütunlu dize.

[!code-json[](ip-safelist/samples/3.x/ClientIpAspNetCore/appsettings.json?range=1-3&highlight=2)]

Önceki örnekte, IPv4 adresleri `127.0.0.1` ve `192.168.1.5` IPv6 döngü adresi `::1` (sıkıştırılmış biçimi `0:0:0:0:0:0:0:1`için) izin verilir.

## <a name="middleware"></a>Ara yazılım

Yöntem, `Startup.Configure` uygulamanın `AdminSafeListMiddleware` istek ardışık hattına özel ara yazılım türünü ekler. Safelist .NET Core yapılandırma sağlayıcısı ile alınır ve bir oluşturucu parametresi olarak geçirilir.

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureAddMiddleware)]

Ara yazılım dizeyi bir diziye dönüştürür ve dizideki uzak IP adresini arar. Uzak IP adresi bulunamazsa, ara yazılım HTTP 403 Forbidden'ı döndürür. Bu doğrulama işlemi HTTP GET istekleri için atlanır.

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Middlewares/AdminSafeListMiddleware.cs?name=snippet_ClassOnly)]

## <a name="action-filter"></a>İşlem filtresi

Belirli MVC denetleyicileri veya eylem yöntemleri için safelist tabanlı erişim denetimi istiyorsanız, bir eylem filtresi kullanın. Örneğin:

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Filters/ClientIpCheckActionFilter.cs?name=snippet_ClassOnly)]

Içinde `Startup.ConfigureServices`, MVC filtreleri koleksiyonuna eylem filtresi ekleyin. Aşağıdaki örnekte, `ClientIpCheckActionFilter` bir eylem filtresi eklenir. Bir safelist ve bir konsol logger örneği oluşturucu parametreleri olarak geçirilir.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesActionFilter)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesActionFilter)]

::: moniker-end

Eylem filtresi daha sonra [[ServiceFilter]](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute) özniteliği ile bir denetleyici veya eylem yöntemi uygulanabilir:

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Controllers/ValuesController.cs?name=snippet_ActionFilter&highlight=1)]

Örnek uygulamada, eylem filtresi denetleyicinin `Get` eylem yöntemine uygulanır. Uygulamayı aşağıdaki leri göndererek test ettiğinizde:

* Bir HTTP GET `[ServiceFilter]` isteği, öznitelik istemci IP adresini doğrular. `Get` Eylem yöntemine erişime izin verilirse, eylem filtresi ve eylem yöntemi tarafından aşağıdaki konsol çıktısının bir varyasyonu üretilir:

    ```
    dbug: ClientIpSafelistComponents.Filters.ClientIpCheckActionFilter[0]
          Remote IpAddress: ::1
    dbug: ClientIpAspNetCore.Controllers.ValuesController[0]
          successful HTTP GET    
    ```

* GET dışında bir HTTP istek `AdminSafeListMiddleware` fiili, middleware istemci IP adresini doğrular.

## <a name="razor-pages-filter"></a>Jilet Sayfaları filtresi

Bir Razor Pages uygulaması için güvenli liste tabanlı erişim denetimi istiyorsanız, Bir Jilet Sayfaları filtresi kullanın. Örneğin:

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Filters/ClientIpCheckPageFilter.cs?name=snippet_ClassOnly)]

Içinde `Startup.ConfigureServices`, MVC filtreleri koleksiyonuna ekleyerek Razor Pages filtresi etkinleştirin. Aşağıdaki örnekte, `ClientIpCheckPageFilter` bir Jilet Sayfaları filtresi eklenir. Bir safelist ve bir konsol logger örneği oluşturucu parametreleri olarak geçirilir.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesPageFilter)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesPageFilter)]

::: moniker-end

Örnek uygulamanın *Index* Razor sayfası istendiğinde, Razor Pages filtresi istemci IP adresini doğrular. Filtre, aşağıdaki konsol çıktısının bir varyasyonunu üretir:

```
dbug: ClientIpSafelistComponents.Filters.ClientIpCheckPageFilter[0]
      Remote IpAddress: ::1
```

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:fundamentals/middleware/index>
* [İşlem filtreleri](xref:mvc/controllers/filters#action-filters)
* <xref:razor-pages/filter>
