---
title: ASP.NET Core içindeki istek özellikleri
author: ardalis
description: ASP.NET Core arabirimlerde tanımlanan HTTP istekleri ve yanıtları ile ilgili Web sunucusu uygulama ayrıntıları hakkında bilgi edinin.
ms.author: riande
ms.custom: mvc
ms.date: 10/20/2020
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
uid: fundamentals/request-features
ms.openlocfilehash: 88e97d88341789a76a79da8d92098c2e00396fe7
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/04/2021
ms.locfileid: "95870431"
---
# <a name="request-features-in-aspnet-core"></a>ASP.NET Core içindeki istek özellikleri

[Steve Smith](https://ardalis.com/) tarafından

`HttpContext`Uygulamaların ve ara yazılımların istekleri işlemek için kullandığı API 'nin altında, *özellik arabirimleri* olarak adlandırılan bir soyutlama katmanı vardır. Her özellik arabirimi tarafından sunulan işlevlerin ayrıntılı bir alt kümesini sağlar `HttpContext` . Bu arabirimler, isteğin tamamı yeniden uygulanması gerekmeden işlenirken, sunucu veya ara yazılım tarafından eklenebilir, değiştirilebilir, sarmalanabilir, değiştirilebilir veya kaldırılabilir `HttpContext` . Ayrıca, test sırasında işlevselliği sahte bir şekilde kullanılabilir.

## <a name="feature-collections"></a>Özellik koleksiyonları

<xref:Microsoft.AspNetCore.Http.HttpContext.Features>Özelliği, `HttpContext` geçerli istek için özellik arabirimleri koleksiyonuna erişim sağlar. Özellik koleksiyonu bir istek bağlamı içinde bile değişebilir olduğundan, ara yazılım, koleksiyonu değiştirmek ve ek özellikler için destek eklemek üzere kullanılabilir. Bazı gelişmiş özellikler yalnızca özellik koleksiyonu aracılığıyla ilişkili arabirime erişerek kullanılabilir.

## <a name="feature-interfaces"></a>Özellik arabirimleri

ASP.NET Core, ' de <xref:Microsoft.AspNetCore.Http.Features?displayProperty=fullName> destekledikleri özellikleri belirlemek için çeşitli sunucular ve ara yazılım tarafından paylaşılan bir dizi ortak http özelliği arabirimini tanımlar. Sunucular ve ara yazılım, ek işlevlere sahip kendi arabirimlerini de sağlayabilir.

Çoğu özellik arabirimi isteğe bağlı, açık işlevsellik sağlar ve ilgili `HttpContext` API 'leri, özellik mevcut değilse varsayılan değerleri sağlar. Çekirdek istek ve yanıt işlevselliği sağladığından ve isteği işlemek için uygulanması gerektiğinden, aşağıdaki içerikte bazı arabirimler belirtilmiştir.

Aşağıdaki özellik arabirimleri şunlardır <xref:Microsoft.AspNetCore.Http.Features?displayProperty=fullName> :

<xref:Microsoft.AspNetCore.Http.Features.IHttpRequestFeature>: Protokol, yol, sorgu dizesi, üst bilgiler ve gövde dahil olmak üzere bir HTTP isteğinin yapısını tanımlar. İstekleri işlemek için bu özellik gereklidir.

<xref:Microsoft.AspNetCore.Http.Features.IHttpResponseFeature>: Durum kodu, üst bilgiler ve yanıtın gövdesi dahil olmak üzere bir HTTP yanıtının yapısını tanımlar. İstekleri işlemek için bu özellik gereklidir.

::: moniker range=">= aspnetcore-3.0"

<xref:Microsoft.AspNetCore.Http.Features.IHttpResponseBodyFeature>: Bir `Stream` , a veya bir dosya kullanarak yanıt gövdesini yazmanın farklı yollarını tanımlar `PipeWriter` . İstekleri işlemek için bu özellik gereklidir. Bu ve ' nin yerini alır `IHttpResponseFeature.Body` `IHttpSendFileFeature` .

::: moniker-end

<xref:Microsoft.AspNetCore.Http.Features.Authentication.IHttpAuthenticationFeature>: <xref:System.Security.Claims.ClaimsPrincipal> Şu anda istekle ilişkili olan öğesini tutar.

<xref:Microsoft.AspNetCore.Http.Features.IFormFeature>: Gelen HTTP ve çok parçalı form gönderilerini ayrıştırmak ve önbelleğe almak için kullanılır.

::: moniker range=">= aspnetcore-2.0"

<xref:Microsoft.AspNetCore.Http.Features.IHttpBodyControlFeature>: İstek veya Yanıt gövdeleri için zaman uyumlu GÇ işlemlerine izin verilip verilmeyeceğini denetlemek için kullanılır.

::: moniker-end
   
::: moniker range="< aspnetcore-3.0"

<xref:Microsoft.AspNetCore.Http.Features.IHttpBufferingFeature>: İsteklerin ve/veya yanıtlarının arabelleğe alınması devre dışı bırakmak için yöntemleri tanımlar.

::: moniker-end

<xref:Microsoft.AspNetCore.Http.Features.IHttpConnectionFeature>: Bağlantı kimliği ve yerel ve uzak adresler ve bağlantı noktaları için özellikleri tanımlar.

::: moniker range=">= aspnetcore-2.0"

<xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>: Geçerli istek için izin verilen en fazla istek gövdesi boyutunu denetler.

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

`IHttpRequestBodyDetectionFeature`: İsteğin bir gövdeye sahip olup olmayacağını gösterir.

::: moniker-end

<xref:Microsoft.AspNetCore.Http.Features.IHttpRequestIdentifierFeature>: İstekleri benzersiz şekilde tanımlamak için uygulanabilecek bir özellik ekler.

<xref:Microsoft.AspNetCore.Http.Features.IHttpRequestLifetimeFeature>: Bağlantı iptal etme veya bir isteğin erken sonlandırılıp sonlandırılmayacağını (örneğin, bir istemcinin bağlantısının kesilmesi) için desteği tanımlar.

::: moniker range=">= aspnetcore-3.0"

<xref:Microsoft.AspNetCore.Http.Features.IHttpRequestTrailersFeature>: Varsa istek artbilgisi üst bilgilerine erişim sağlar.

<xref:Microsoft.AspNetCore.Http.Features.IHttpResetFeature>: HTTP/2 veya HTTP/3 gibi bunları destekleyen protokoller için sıfırlama iletileri göndermek için kullanılır.

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<xref:Microsoft.AspNetCore.Http.Features.IHttpResponseTrailersFeature>: Destekleniyorsa yanıt artbilgisi bilgilerini sağlar.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature>: Dosyaları zaman uyumsuz olarak göndermek için bir yöntem tanımlar.

::: moniker-end

<xref:Microsoft.AspNetCore.Http.Features.IHttpUpgradeFeature>: Sunucu protokolleri değiştirmek isterse, istemcinin kullanmak istediğiniz ek protokolleri belirtmesini sağlayan [http yükseltmeleri](https://tools.ietf.org/html/rfc2616.html#section-14.42)için desteği tanımlar.

<xref:Microsoft.AspNetCore.Http.Features.IHttpWebSocketFeature>: Web yuvalarını desteklemek için bir API tanımlar.

::: moniker range=">= aspnetcore-3.0"

<xref:Microsoft.AspNetCore.Http.Features.IHttpsCompressionFeature>: Yanıt sıkıştırmasının HTTPS bağlantıları üzerinden kullanılması gerekip gerekmediğini denetler.

::: moniker-end

<xref:Microsoft.AspNetCore.Http.Features.IItemsFeature>: <xref:Microsoft.AspNetCore.Http.Features.IItemsFeature.Items> İstek başına uygulama durumu için koleksiyonu depolar.

<xref:Microsoft.AspNetCore.Http.Features.IQueryFeature>: Sorgu dizesini ayrıştırır ve önbelleğe alır.
   
::: moniker range=">= aspnetcore-3.0"

<xref:Microsoft.AspNetCore.Http.Features.IRequestBodyPipeFeature>: İstek gövdesini bir olarak temsil eder <xref:System.IO.Pipelines.PipeReader> .
 
::: moniker-end

<xref:Microsoft.AspNetCore.Http.Features.IRequestCookiesFeature>: İstek üst bilgisi değerlerini ayrıştırır ve önbelleğe alır `Cookie` .

<xref:Microsoft.AspNetCore.Http.Features.IResponseCookiesFeature>: Yanıtın üstbilgiye nasıl cookie uygulanacağını denetler `Set-Cookie` .

::: moniker range=">= aspnetcore-2.2"

<xref:Microsoft.AspNetCore.Http.Features.IServerVariablesFeature>: Bu özellik, IIS tarafından sağlananlar gibi istek sunucusu değişkenlerine erişim sağlar.

::: moniker-end
   
<xref:Microsoft.AspNetCore.Http.Features.IServiceProvidersFeature>: <xref:System.IServiceProvider> Kapsamlı bir istek hizmetleriyle erişim sağlar.

<xref:Microsoft.AspNetCore.Http.Features.ISessionFeature>: `ISessionFactory` <xref:Microsoft.AspNetCore.Http.ISession> Kullanıcı oturumlarını desteklemek için tanımlar ve soyutlamalar tanımlar. `ISessionFeature` , <xref:Microsoft.AspNetCore.Session.SessionMiddleware> (bkz <xref:fundamentals/app-state> .) tarafından uygulanır.

<xref:Microsoft.AspNetCore.Http.Features.ITlsConnectionFeature>: İstemci sertifikalarını almak için bir API tanımlar.

<xref:Microsoft.AspNetCore.Http.Features.ITlsTokenBindingFeature>: TLS belirteci bağlama parametreleriyle çalışma yöntemlerini tanımlar.
   
::: moniker range=">= aspnetcore-2.2"
   
<xref:Microsoft.AspNetCore.Http.Features.ITrackingConsentFeature>: Site etkinliğiyle ve işlevlerle ilgili Kullanıcı bilgilerinin depolanması ile ilgili Kullanıcı onayını sorgulamak, vermek ve geri çekmek için kullanılır.
   
::: moniker-end

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:fundamentals/servers/index>
* <xref:fundamentals/middleware/index>
