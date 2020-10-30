---
title: IIS 'de HTTP/2 ile ASP.NET Core kullanma
author: rick-anderson
description: IIS ile HTTP/2 özelliklerini kullanmayı öğrenin.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
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
uid: host-and-deploy/iis/protocols
ms.openlocfilehash: 4d0dc1239467e92c4127f631709c2ffd6098bfc5
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057420"
---
# <a name="use-aspnet-core-with-http2-on-iis"></a>IIS 'de HTTP/2 ile ASP.NET Core kullanma

, [Kotin Kotalik](https://github.com/jkotalik) tarafından

[Http/2](https://httpwg.org/specs/rfc7540.html) aşağıdaki IIS dağıtım senaryolarında ASP.NET Core desteklenir:

* Windows Server 2016 veya üzeri/Windows 10 veya üzeri
* IIS 10 veya üzeri
* TLS 1,2 veya üzeri bağlantı
* [İşlem dışı barındırırken](xref:host-and-deploy/iis/index#out-of-process-hosting-model): genel kullanıma yönelik uç sunucu bağlantıları http/2 kullanır, ancak [Kestrel sunucusuyla](xref:fundamentals/servers/kestrel) ters proxy bağlantısı http/1.1 kullanır.

Bir HTTP/2 bağlantısı oluşturulduğunda, işlem içi dağıtım için [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) raporları `HTTP/2` . Bir HTTP/2 bağlantısı oluşturulduğunda, işlem dışı bir dağıtım için [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) raporları `HTTP/1.1` .

İşlem içi ve işlem dışı barındırma modelleri hakkında daha fazla bilgi için bkz <xref:host-and-deploy/aspnet-core-module> ..

HTTP/2, HTTPS/TLS bağlantıları için varsayılan olarak etkindir. HTTP/2 bağlantısı kurulmadıysa bağlantılar HTTP/1.1 'ye geri döner. IIS dağıtımları ile HTTP/2 yapılandırması hakkında daha fazla bilgi için bkz. [IIS 'de http/2](/iis/get-started/whats-new-in-iis-10/http2-on-iis).

## <a name="advanced-http2-features-to-support-grpc"></a>GRPC 'yi desteklemeye yönelik gelişmiş HTTP/2 özellikleri

IIS 'deki ek HTTP/2 özellikleri, yanıt fragmanları ve sıfırlama çerçeveleri gönderme desteği dahil olmak üzere gRPC 'yi destekler.

IIS üzerinde gRPC çalıştırmak için gereksinimler:

* İşlem içi barındırma.
* Windows 10, OS Build 20300,1000 veya üzeri. Windows Insider Derlemeleriyle kullanılması gerekebilir.
* TLS 1,2 veya üzeri bağlantı

### <a name="trailers"></a>Larına

[!INCLUDE[](~/includes/trailers.md)]

### <a name="reset"></a>Sıfırla

[!INCLUDE[](~/includes/reset.md)]
