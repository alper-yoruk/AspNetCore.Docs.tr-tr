---
title: ASP.NET Core Kestrel Web sunucusu ile HTTP/2 kullanın
author: rick-anderson
description: ASP.NET Core için platformlar arası Web sunucusu olan Kestrel ile HTTP/2 kullanma hakkında bilgi edinin.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 05/04/2020
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
uid: fundamentals/servers/kestrel/http2
ms.openlocfilehash: 431459bb6ece1d054146558ef865e44845a22686
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253985"
---
# <a name="use-http2-with-the-aspnet-core-kestrel-web-server"></a>ASP.NET Core Kestrel Web sunucusu ile HTTP/2 kullanın

Aşağıdaki temel gereksinimler karşılanıyorsa, [http/2](https://httpwg.org/specs/rfc7540.html) ASP.NET Core uygulamalar için kullanılabilir:

* İşletim Sistemi&dagger;
  * Windows Server 2016/Windows 10 veya üzeri&Dagger;
  * OpenSSL 1.0.2 veya üzerini içeren Linux (örneğin, Ubuntu 16,04 veya üzeri)
* Hedef Framework: .NET Core 2,2 veya üzeri
* [Uygulama katmanı protokol anlaşması (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) bağlantısı
* TLS 1,2 veya üzeri bağlantı

&dagger;HTTP/2, gelecek sürümlerde macOS 'ta desteklenecektir.
&Dagger;Kestrel, Windows Server 2012 R2 ve Windows 8.1 'de HTTP/2 için sınırlı destek içerir. Bu işletim sistemlerinde kullanılabilir olan desteklenen TLS şifre paketlerinin listesi sınırlı olduğundan destek sınırlıdır. TLS bağlantılarının güvenliğini sağlamak için Eliptik Eğri dijital Imza algoritması (ECDSA) kullanılarak oluşturulan bir sertifika gerekli olabilir.

Bir HTTP/2 bağlantısı kurulduysa, [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol%2A) Reports `HTTP/2` .

.NET Core 3,0 ile başlayarak, HTTP/2 varsayılan olarak etkindir. Yapılandırma hakkında daha fazla bilgi için [Kestrel http/2 limitleri](xref:fundamentals/servers/kestrel/options#http2-limits) ve [Listenoptions. Protocols](xref:fundamentals/servers/kestrel/endpoints#listenoptionsprotocols) bölümlerine bakın.

## <a name="advanced-http2-features"></a>Gelişmiş HTTP/2 özellikleri

Kestrel ' deki ek HTTP/2 özellikleri, yanıt tanıtımları ve sıfırlama çerçeveleri gönderme desteği dahil olmak üzere gRPC 'yi destekler.

### <a name="trailers"></a>Larına

[!INCLUDE[](~/includes/trailers.md)]

### <a name="reset"></a>Sıfırla

[!INCLUDE[](~/includes/reset.md)]
