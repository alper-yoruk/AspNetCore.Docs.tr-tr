---
title: ASP.NET Core güvenlik konuları SignalR
author: bradygaster
description: ASP.NET Core 'da kimlik doğrulama ve yetkilendirmeyi nasıl kullanacağınızı öğrenin SignalR .
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: mvc
ms.date: 01/16/2020
no-loc:
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
uid: signalr/security
ms.openlocfilehash: 12293c5cb3dc49d505225f1b44e824e9273cfffc
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88630997"
---
# <a name="security-considerations-in-aspnet-core-no-locsignalr"></a>ASP.NET Core güvenlik konuları SignalR

, [Andrew Stanton-nurte](https://twitter.com/anurse)

Bu makalede, güvenliğini sağlama hakkında bilgi sağlanır SignalR .

## <a name="cross-origin-resource-sharing"></a>Çıkış noktaları arası kaynak paylaşma

[Çapraz kaynak kaynak paylaşımı (CORS)](https://www.w3.org/TR/cors/) , tarayıcıda çapraz kaynak bağlantılarına izin vermek için kullanılabilir SignalR . JavaScript kodu uygulamadan farklı bir etki alanında barındırılıyorsa SignalR , JavaScript 'in uygulamaya bağlanmasına izin vermek Için [CORS ara yazılımı](xref:security/cors) etkinleştirilmelidir SignalR . Yalnızca güvendiğiniz veya denetlediğiniz etki alanlarından çıkış noktaları arası isteklere izin verin. Örnek:

* Siteniz üzerinde barındırılıyor `http://www.example.com`
* SignalRUygulamanız üzerinde barındırılıyor`http://signalr.example.com`

CORS SignalR , uygulamada yalnızca kaynağa izin verecek şekilde yapılandırılmalıdır `www.example.com` .

CORS 'yi yapılandırma hakkında daha fazla bilgi için bkz. [çıkış noktaları arası istekleri (CORS) etkinleştirme](xref:security/cors). SignalRAşağıdaki CORS ilkelerini **gerektirir** :

* Beklenen belirli kaynaklardan izin verin. Herhangi bir kaynağa izin verilmesi mümkün olsa **da güvenli veya** önerilmez.
* HTTP yöntemlerine `GET` ve `POST` izin verilmelidir.
* cookieTabanlı yapışkan oturumların düzgün çalışması için kimlik bilgilerine izin verilmelidir. Kimlik doğrulaması kullanılmasa bile etkinleştirilmeleri gerekir.

::: moniker range=">= aspnetcore-5.0"

Ancak, 5,0 ' de, TypeScript istemcisinde kimlik bilgilerini kullanmayan bir seçenek sağladık.
Kimlik bilgilerini kullanma seçeneği yalnızca %100 ' i, Cookie uygulamanızda gerek duyulmayan kimlik bilgilerinin ( cookie Örneğin, yapışkan oturumlar için birden çok sunucu kullanırken Azure App Service tarafından kullanılır) gerekli olmadığını bilmeniz durumunda kullanılmalıdır.

::: moniker-end

Örneğin, aşağıdaki CORS ilkesi üzerinde barındırılan bir SignalR tarayıcı istemcisinin üzerinde `https://example.com` barındırılan uygulamaya erişmesine izin verir SignalR `https://signalr.example.com` :

::: moniker range=">= aspnetcore-3.0"

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    // ... other middleware ...

    // Make sure the CORS middleware is ahead of SignalR.
    app.UseCors(builder =>
    {
        builder.WithOrigins("https://example.com")
            .AllowAnyHeader()
            .WithMethods("GET", "POST")
            .AllowCredentials();
    });

    // ... other middleware ...
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chathub");
    });

    // ... other middleware ...
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[Main](security/sample/Startup.cs?name=snippet1)]

::: moniker-end

## <a name="websocket-origin-restriction"></a>WebSocket kaynak kısıtlaması

::: moniker range=">= aspnetcore-2.2"

CORS tarafından sunulan korumalar WebSockets için geçerlidir. WebSockets üzerindeki kaynak kısıtlaması için [WebSockets kaynak kısıtlaması](xref:fundamentals/websockets#websocket-origin-restriction)' nı okuyun.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

CORS tarafından sunulan korumalar WebSockets için geçerlidir. Tarayıcılar şunları **desteklemez**:

* CORS ön uçuş istekleri gerçekleştirin.
* `Access-Control`WebSocket istekleri yapılırken üst bilgilerde belirtilen kısıtlamalara saygı.

Ancak, tarayıcılar `Origin` WebSocket istekleri verirken üstbilgiyi gönderir. Yalnızca beklenen kaynaklardan gelen WebSockets izin verildiğinden emin olmak için uygulamalar bu üstbilgileri doğrulamak üzere yapılandırılmalıdır.

ASP.NET Core 2,1 ve üzeri sürümlerde, daha önce yerleştirilmiş özel bir ara yazılım ** `UseSignalR` ve içindeki kimlik doğrulama ara yazılımı** kullanılarak üst bilgi doğrulaması elde edilebilir `Configure` :

[!code-csharp[Main](security/sample/Startup.cs?name=snippet2)]

> [!NOTE]
> `Origin`Üst bilgi istemci tarafından denetlenir ve `Referer` üst bilgi gibi erişilebilir. Bu üst bilgiler bir kimlik doğrulama mekanizması **olarak kullanılmamalıdır.**

::: moniker-end

## <a name="connectionid"></a>ConnectionID

`ConnectionId` SignalR Sunucu veya istemci ASP.NET Core sürümü 2,2 veya daha önceki bir sürümdeyse, kullanıma sunma kötü amaçlı kimliğe bürünmeye yol açabilir. SignalRSunucu ve istemci sürümü ASP.NET Core 3,0 veya üzeri ise, `ConnectionToken` bunun yerine `ConnectionId` gizli tutulması gerekir. , Özellikle `ConnectionToken` herhangi BIR API 'de gösterilmez.  Eski SignalR istemcilerin sunucuya bağlanmadığından emin olmak zor olabilir, bu nedenle SignalR sunucu sürümünüz ASP.NET Core 3,0 veya sonraki bir sürümse bile `ConnectionId` gösterilmemelidir.

## <a name="access-token-logging"></a>Erişim belirteci günlüğe kaydetme

WebSockets veya sunucu tarafından gönderilen olaylar kullanılırken, tarayıcı istemcisi erişim belirtecini sorgu dizesinde gönderir. Sorgu dizesi aracılığıyla erişim belirtecinin alınması genellikle standart `Authorization` üst bilgi kullanılarak güvenlidir. İstemci ve sunucu arasında güvenli bir uçtan uca bağlantı sağlamak için her zaman HTTPS kullanın. Birçok Web sunucusu, sorgu dizesi dahil olmak üzere her bir isteğin URL 'sini günlüğe kaydeder. URL 'Leri günlüğe kaydetme erişim belirtecini günlüğe alabilir. ASP.NET Core, her isteğin URL 'sini varsayılan olarak günlüğe kaydeder ve bu sorgu dizesini içerir. Örnek:

```
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:5000/chathub?access_token=1234
```

Bu verilerin sunucu günlüklerinizi günlüğe kaydetmekle ilgili endişeleriniz varsa, `Microsoft.AspNetCore.Hosting` günlükçü 'yi `Warning` düzeye veya yukarıya yapılandırarak (Bu iletiler düzeyinde yazılır) bu günlüğü tamamen devre dışı bırakabilirsiniz `Info` . Daha fazla bilgi için bkz. [günlük filtreleme](xref:fundamentals/logging/index#log-filtering) . Hala belirli istek bilgilerini günlüğe kaydetmek istiyorsanız, ihtiyacınız olan verileri günlüğe kaydetmek için [bir ara yazılım yazabilir](xref:fundamentals/middleware/write) ve `access_token` sorgu dizesi değerini (varsa) filtreleyebilirsiniz.

## <a name="exceptions"></a>Özel durumlar

Özel durum iletileri genellikle bir istemciye görüntülenmemelidir gizli veriler olarak değerlendirilir. Varsayılan olarak, SignalR bir hub yöntemi tarafından istemciye oluşturulan bir özel durumun ayrıntılarını istemciye göndermez. Bunun yerine, istemci bir hata oluştuğunu belirten genel bir ileti alır. İstemciye özel durum iletisi teslimi, [Enabledetailederrors](xref:signalr/configuration#configure-server-options)ile geçersiz kılınabilir (örneğin, geliştirme veya test). Özel durum iletileri, üretim uygulamalarındaki istemciye gösterilmemelidir.

## <a name="buffer-management"></a>Arabellek Yönetimi

SignalR gelen ve giden iletileri yönetmek için bağlantı başına arabellekleri kullanır. Varsayılan olarak, SignalR Bu arabellekleri 32 KB ile sınırlandırır. Bir istemcinin veya sunucunun gönderecan en büyük ileti 32 KB 'tır. İleti bağlantısı tarafından tüketilen maksimum bellek 32 KB 'tır. İletileriniz her zaman 32 KB 'den küçükse, sınırı azaltabilirsiniz ve şunları yapabilirsiniz:

* Bir istemcinin daha büyük bir ileti gönderebilmesini engeller.
* Sunucu, iletileri kabul etmek için hiçbir şekilde büyük arabellekler ayırmayı gerektirmez.

İletileriniz 32 KB 'tan büyükse, limiti artırabilirsiniz. Bu sınırı artırmak şu anlama gelir:

* İstemci, sunucunun büyük bellek arabellekleri ayırmasına neden olabilir.
* Büyük arabelleklerin sunucu ayırması, eşzamanlı bağlantı sayısını azaltabilir.

Gelen ve giden iletiler için, her ikisi de yapılandırılan [Httpconnectiondispatcheroptions](xref:signalr/configuration#configure-server-options) nesnesinde yapılandırılabilir `MapHub` :

* `ApplicationMaxBufferSize` istemciden sunucunun arabelleğe aldığı en fazla bayt sayısını temsil eder. İstemci bu sınırdan daha büyük bir ileti göndermemeyi denerse bağlantı kapatılabilir.
* `TransportMaxBufferSize` sunucunun gönderemediği en fazla bayt sayısını temsil eder. Sunucu, bu sınırdan daha büyük bir ileti (hub metotlarından dönüş değerleri dahil) gönderilmeye çalışırsa, bir özel durum oluşturulur.

Limit ayarı `0` sınırı devre dışı bırakır. Sınırı kaldırmak, bir istemcinin herhangi bir boyutta ileti göndermesini sağlar. Büyük iletiler gönderen kötü amaçlı istemciler fazla belleğin ayrılmasına neden olabilir. Aşırı bellek kullanımı, eşzamanlı bağlantı sayısını önemli ölçüde azaltabilir.
