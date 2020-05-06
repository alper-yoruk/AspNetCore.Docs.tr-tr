---
title: ASP.NET Core için istemci IP SafeList
author: damienbod
description: Uzak IP adreslerini onaylanan IP adresleri listesine göre doğrulamak için ara yazılım veya eylem filtreleri yazmayı öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/12/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/ip-safelist
ms.openlocfilehash: 7923a81e72124cfb0e11e3c1ac327c1e32194b21
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776506"
---
# <a name="client-ip-safelist-for-aspnet-core"></a>ASP.NET Core için istemci IP SafeList

By [Davmıen Bowden](https://twitter.com/damien_bod) ve [Tom Dykstra](https://github.com/tdykstra)
 
Bu makalede bir ASP.NET Core uygulamasında bir IP adresi SafeList (izin verilenler listesi olarak da bilinir) uygulamanın üç yolu gösterilmektedir. Eşlik eden bir örnek uygulama, üç yaklaşımdan oluşan tüm örnekleri gösterir Şunu kullanabilirsiniz:

* Her isteğin uzak IP adresini denetlemek için ara yazılım.
* MVC eylemi belirli denetleyiciler veya eylem yöntemlerine yönelik isteklerin uzak IP adresini denetlemek için filtreler.
* RazorSayfa isteklerinin Razor uzak IP adresini denetlemek için sayfalar filtreler.

Her durumda, onaylanan istemci IP adreslerini içeren bir dize bir uygulama ayarında saklanır. Ara yazılım veya filtre:

* Dizeyi bir dizi olarak ayrıştırır. 
* Dizide uzak IP adresinin bulunup bulunmadığını denetler.

Dizi IP adresini içeriyorsa erişime izin verilir. Aksi takdirde, HTTP 403 yasaklanmış durum kodu döndürülür.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/ip-safelist/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="ip-address-safelist"></a>IP adresi SafeList

Örnek uygulamada, IP adresi SafeList şu şekilde olur:

* `AdminSafeList` *AppSettings. JSON* dosyasındaki özelliği tarafından tanımlanır.
* Hem [Internet Protokolü sürüm 4 (IPv4)](https://wikipedia.org/wiki/IPv4) hem de [İnternet Protokolü sürüm 6 (IPv6)](https://wikipedia.org/wiki/IPv6) adresleri içerebilen, noktalı virgülle ayrılmış bir dize.

[!code-json[](ip-safelist/samples/3.x/ClientIpAspNetCore/appsettings.json?range=1-3&highlight=2)]

Yukarıdaki `127.0.0.1` örnekte, ve `192.168.1.5` ' nin IPv4 adreslerine ve IPv6 Geridöngü adresine `::1` (için `0:0:0:0:0:0:0:1`sıkıştırılmış biçim) izin verilir.

## <a name="middleware"></a>Ara yazılım

`Startup.Configure` Yöntemi, uygulamanın istek ardışık `AdminSafeListMiddleware` düzenine özel ara yazılım türünü ekler. SafeList, .NET Core yapılandırma sağlayıcısıyla alınır ve bir oluşturucu parametresi olarak geçirilir.

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureAddMiddleware)]

Ara yazılım, dizeyi bir dizi olarak ayrıştırır ve dizideki uzak IP adresini arar. Uzak IP adresi bulunamazsa, ara yazılım HTTP 403 Yasak değerini döndürür. Bu doğrulama işlemi HTTP GET istekleri için atlanır.

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Middlewares/AdminSafeListMiddleware.cs?name=snippet_ClassOnly)]

## <a name="action-filter"></a>Eylem filtresi

Belirli MVC denetleyicileri veya eylem yöntemleri için güvenli liste temelli erişim denetimi istiyorsanız, bir eylem filtresi kullanın. Örneğin:

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Filters/ClientIpCheckActionFilter.cs?name=snippet_ClassOnly)]

İçinde `Startup.ConfigureServices`, eylem filtresini MVC filtreleri koleksiyonuna ekleyin. Aşağıdaki örnekte, bir `ClientIpCheckActionFilter` eylem filtresi eklenir. Bir SafeList ve konsol günlükçü örneği, Oluşturucu parametreleri olarak geçirilir.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesActionFilter)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesActionFilter)]

::: moniker-end

Eylem filtresi daha sonra [[Servicefilter]](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute) özniteliğiyle bir denetleyiciye veya eylem yöntemine uygulanabilir:

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Controllers/ValuesController.cs?name=snippet_ActionFilter&highlight=1)]

Örnek uygulamada, eylem filtresi denetleyicinin `Get` eylem yöntemine uygulanır. Uygulamayı şunu göndererek test ettiğinizde:

* HTTP GET isteği, `[ServiceFilter]` ÖZNITELIĞI istemci IP adresini doğrular. `Get` Eylem yöntemine erişime izin veriliyorsa, aşağıdaki konsol çıkışının bir çeşitlemesi eylem filtresi ve eylem yöntemi tarafından üretilir:

    ```
    dbug: ClientIpSafelistComponents.Filters.ClientIpCheckActionFilter[0]
          Remote IpAddress: ::1
    dbug: ClientIpAspNetCore.Controllers.ValuesController[0]
          successful HTTP GET    
    ```

* GET dışında bir HTTP istek fiili, `AdminSafeListMiddleware` ara yazılım istemci IP adresini doğrular.

## <a name="razor-pages-filter"></a>RazorSayfa filtresi

Bir Razor sayfalar uygulaması için güvenli liste temelli erişim denetimi istiyorsanız, bir Razor sayfa filtresi kullanın. Örneğin:

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Filters/ClientIpCheckPageFilter.cs?name=snippet_ClassOnly)]

İçinde `Startup.ConfigureServices`, MVC filtreleri Razor koleksiyonuna ekleyerek sayfa filtresini etkinleştirin. Aşağıdaki örnekte, bir `ClientIpCheckPageFilter` Razor sayfa filtresi eklenir. Bir SafeList ve konsol günlükçü örneği, Oluşturucu parametreleri olarak geçirilir.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesPageFilter)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesPageFilter)]

::: moniker-end

Örnek uygulamanın *Dizin* Razor sayfası istendiğinde, Razor sayfa filtresi istemci IP adresini doğrular. Filtre, aşağıdaki konsol çıkışının bir çeşidini üretir:

```
dbug: ClientIpSafelistComponents.Filters.ClientIpCheckPageFilter[0]
      Remote IpAddress: ::1
```

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:fundamentals/middleware/index>
* [Eylem filtreleri](xref:mvc/controllers/filters#action-filters)
* <xref:razor-pages/filter>
