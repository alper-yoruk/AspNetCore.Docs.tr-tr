---
title: Desteklenen Blazor platformları ASP.NET Core
author: guardrex
description: ASP.NET Core Blazoriçin desteklenen platformlar hakkında bilgi edinin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/supported-platforms
ms.openlocfilehash: 1c78803e6468f924bf8c8e9403a34565b114006f
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82771122"
---
# <a name="aspnet-core-blazor-supported-platforms"></a>ASP.NET Core Blazor desteklenen platformlar

[Luke Latham](https://github.com/guardrex) tarafından

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

## <a name="browser-requirements"></a>Tarayıcı gereksinimleri

### <a name="blazor-webassembly"></a>Blazor WebAssembly

| Tarayıcı                          | Sürüm               |
| -------------------------------- | :-------------------: |
| Microsoft Edge                   | Geçerli               |
| Mozilla Firefox                  | Geçerli               |
| Android dahil Google Chrome | Geçerli               |
| İOS dahil Safari            | Geçerli               |
| Microsoft Internet Explorer      | Desteklenmiyor&dagger; |

&dagger;Microsoft Internet Explorer [Webassembly](https://webassembly.org)'yi desteklemez.

### <a name="blazor-server"></a>Blazor Server

| Tarayıcı                          | Sürüm    |
| -------------------------------- | :--------: |
| Microsoft Edge                   | Geçerli    |
| Mozilla Firefox                  | Geçerli    |
| Android dahil Google Chrome | Geçerli    |
| İOS dahil Safari            | Geçerli    |
| Microsoft Internet Explorer      | üst&dagger; |

&dagger;Ek polydolgular gereklidir (örneğin, [Polyfill.io](https://polyfill.io/v3/) bir paket aracılığıyla taahhüt eklenebilir).

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:blazor/hosting-models>
