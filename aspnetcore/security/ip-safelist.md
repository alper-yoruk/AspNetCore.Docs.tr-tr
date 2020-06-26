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
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/ip-safelist
ms.openlocfilehash: 5b74205bc7b17d61edbb73cf309f6e24e4318391
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85409013"
---
# <a name="client-ip-safelist-for-aspnet-core"></a>ASP.NET Core için istemci IP SafeList

By [Davmıen Bowden](https://twitter.com/damien_bod) ve [Tom Dykstra](https://github.com/tdykstra)
 
Bu makalede bir ASP.NET Core uygulamasında bir IP adresi SafeList (izin verilenler listesi olarak da bilinir) uygulamanın üç yolu gösterilmektedir. Eşlik eden bir örnek uygulama, üç yaklaşımdan oluşan tüm örnekleri gösterir Şunu kullanabilirsiniz:

* Her isteğin uzak IP adresini denetlemek için ara yazılım.
* MVC eylemi belirli denetleyiciler veya eylem yöntemlerine yönelik isteklerin uzak IP adresini denetlemek için filtreler.
* RazorSayfa isteklerinin uzak IP adresini denetlemek için sayfalar filtreler Razor .

Her durumda, onaylanan istemci IP adreslerini içeren bir dize bir uygulama ayarında saklanır. Ara yazılım veya filtre:

* Dizeyi bir dizi olarak ayrıştırır. 
* Dizide uzak IP adresinin bulunup bulunmadığını denetler.

Dizi IP adresini içeriyorsa erişime izin verilir. Aksi takdirde, HTTP 403 yasaklanmış durum kodu döndürülür.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/ip-safelist/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="ip-address-safelist"></a>IP adresi SafeList

Örnek uygulamada, IP adresi SafeList şu şekilde olur:

* `AdminSafeList` *appsettings.js* dosyadaki özelliği tarafından tanımlanır.
* Hem [Internet Protokolü sürüm 4 (IPv4)](https://wikipedia.org/wiki/IPv4) hem de [İnternet Protokolü sürüm 6 (IPv6)](https://wikipedia.org/wiki/IPv6) adresleri içerebilen, noktalı virgülle ayrılmış bir dize.

[!code-json[](ip-safelist/samples/3.x/ClientIpAspNetCore/appsettings.json?range=1-3&highlight=2)]

Yukarıdaki örnekte, ve ' nin IPv4 adreslerine ve `127.0.0.1` `192.168.1.5` IPv6 Geridöngü adresine `::1` (için sıkıştırılmış biçim `0:0:0:0:0:0:0:1` ) izin verilir.

## <a name="middleware"></a>Ara yazılım

`Startup.Configure`Yöntemi, `AdminSafeListMiddleware` uygulamanın istek ardışık düzenine özel ara yazılım türünü ekler. SafeList, .NET Core yapılandırma sağlayıcısıyla alınır ve bir oluşturucu parametresi olarak geçirilir.

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureAddMiddleware)]

Ara yazılım, dizeyi bir dizi olarak ayrıştırır ve dizideki uzak IP adresini arar. Uzak IP adresi bulunamazsa, ara yazılım HTTP 403 Yasak değerini döndürür. Bu doğrulama işlemi HTTP GET istekleri için atlanır.

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Middlewares/AdminSafeListMiddleware.cs?name=snippet_ClassOnly)]

## <a name="action-filter"></a>Eylem filtresi

Belirli MVC denetleyicileri veya eylem yöntemleri için güvenli liste temelli erişim denetimi istiyorsanız, bir eylem filtresi kullanın. Örneğin:

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Filters/ClientIpCheckActionFilter.cs?name=snippet_ClassOnly)]

İçinde `Startup.ConfigureServices` , eylem FILTRESINI MVC filtreleri koleksiyonuna ekleyin. Aşağıdaki örnekte, bir `ClientIpCheckActionFilter` eylem filtresi eklenir. Bir SafeList ve konsol günlükçü örneği, Oluşturucu parametreleri olarak geçirilir.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesActionFilter)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesActionFilter)]

::: moniker-end

Eylem filtresi daha sonra [[Servicefilter]](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute) özniteliğiyle bir denetleyiciye veya eylem yöntemine uygulanabilir:

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Controllers/ValuesController.cs?name=snippet_ActionFilter&highlight=1)]

Örnek uygulamada, eylem filtresi denetleyicinin `Get` eylem yöntemine uygulanır. Uygulamayı şunu göndererek test ettiğinizde:

* HTTP GET isteği, `[ServiceFilter]` özniteliği ISTEMCI IP adresini doğrular. Eylem yöntemine erişime izin veriliyorsa `Get` , aşağıdaki konsol çıkışının bir çeşitlemesi eylem filtresi ve eylem yöntemi tarafından üretilir:

    ```
    dbug: ClientIpSafelistComponents.Filters.ClientIpCheckActionFilter[0]
          Remote IpAddress: ::1
    dbug: ClientIpAspNetCore.Controllers.ValuesController[0]
          successful HTTP GET    
    ```

* GET dışında bir HTTP istek fiili, `AdminSafeListMiddleware` Ara yazılım ISTEMCI IP adresini doğrular.

## <a name="razor-pages-filter"></a>RazorSayfa filtresi

Bir sayfalar uygulaması için güvenli liste temelli erişim denetimi istiyorsanız Razor , bir Razor sayfa filtresi kullanın. Örneğin:

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Filters/ClientIpCheckPageFilter.cs?name=snippet_ClassOnly)]

İçinde `Startup.ConfigureServices` , Razor MVC filtreleri koleksiyonuna ekleyerek sayfa filtresini etkinleştirin. Aşağıdaki örnekte, bir `ClientIpCheckPageFilter` Razor sayfa filtresi eklenir. Bir SafeList ve konsol günlükçü örneği, Oluşturucu parametreleri olarak geçirilir.

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
