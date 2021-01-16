---
title: ASP.NET Core Kestrel Web sunucusuyla ters proxy kullanma
author: rick-anderson
description: ASP.NET Core için platformlar arası Web sunucusu olan Kestrel 'in önünde bir ters proxy 'nin ne zaman kullanılacağı hakkında bilgi edinin.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 01/14/2021
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
uid: fundamentals/servers/kestrel/when-to-use-a-reverse-proxy
ms.openlocfilehash: fc89a9f841403bbccedff0a9c0720a08c11abdd6
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253972"
---
# <a name="when-to-use-kestrel-with-a-reverse-proxy"></a>Ters ara sunucu ile Kestrel ne zaman kullanılır?

Kestrel, kendisi veya [Internet Information Services (IIS)](https://www.iis.net/), [NGINX](https://nginx.org)veya [Apache](https://httpd.apache.org/)gibi bir *ters ara sunucu* ile kullanılabilir. Ters proxy sunucusu, ağdan gelen HTTP isteklerini alır ve Kestrel 'e iletir.

Sınır (Internet 'e yönelik) Web sunucusu olarak kullanılan Kestrel:

![Kestrel, ters proxy sunucusu olmadan doğrudan Internet ile iletişim kurar](_static/kestrel-to-internet2.png)

Ters Proxy yapılandırmasında kullanılan Kestrel:

![Kestrel, IIS, NGINX veya Apache gibi bir ters ara sunucu üzerinden Internet ile dolaylı olarak iletişim kurar](_static/kestrel-to-internet.png)

İki yapılandırma de, ters ara sunucu sunucusuyla veya olmadan, desteklenen bir barındırma yapılandırması.

Kestrel, ters ara sunucu olmayan bir uç sunucu olarak kullanıldığında, birden çok işlem arasındaki aynı IP adresini ve bağlantı noktasını paylaşma desteklenmez. Kestrel bir bağlantı noktasını dinlemek üzere yapılandırıldığında, Kestrel isteklerin üst bilgilerinden bağımsız olarak bu bağlantı noktası için tüm trafiği işler `Host` . Bağlantı noktalarını paylaşabilen bir ters proxy, istekleri benzersiz bir IP ve bağlantı noktası üzerinde Kestrel 'e iletebilir.

Ters proxy sunucusu gerekli olmasa bile, ters proxy sunucu kullanılması iyi bir seçim olabilir.

Ters proxy:

* , Barındırdığı uygulamaların açığa çıkarılan genel yüzey alanını sınırlayabilir.
* Ek bir yapılandırma ve savunma katmanı sağlayın.
* , Mevcut altyapıyla daha iyi tümleşebilir.
* Yük dengelemeyi ve güvenli iletişim (HTTPS) yapılandırmasını kolaylaştırın. Yalnızca ters proxy sunucusu bir X. 509.440 sertifikası gerektirir ve bu sunucu, düz HTTP kullanarak, iç ağdaki uygulamanın sunucularıyla iletişim kurabilir.

> [!WARNING]
> Ters Proxy yapılandırmasında barındırma, [konak filtrelemeyi](xref:fundamentals/servers/kestrel/host-filtering)gerektirir.

## <a name="additional-resources"></a>Ek kaynaklar

<xref:host-and-deploy/proxy-load-balancer>

