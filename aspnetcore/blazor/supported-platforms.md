---
title: BlazorDesteklenen platformları ASP.NET Core
author: guardrex
description: ASP.NET Core için desteklenen platformlar hakkında bilgi edinin Blazor .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/supported-platforms
ms.openlocfilehash: 67896bcdd5d99ff2c9cf22e3902262f9513eb4f6
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88013534"
---
# <a name="aspnet-core-no-locblazor-supported-platforms"></a>BlazorDesteklenen platformları ASP.NET Core

[Luke Latham](https://github.com/guardrex) tarafından

## <a name="browser-requirements"></a>Tarayıcı gereksinimleri

### Blazor WebAssembly

| Tarayıcı                          | Sürüm               |
| -------------------------------- | :-------------------: |
| Microsoft Edge                   | Geçerli               |
| Mozilla Firefox                  | Geçerli               |
| Android dahil Google Chrome | Geçerli               |
| İOS dahil Safari            | Geçerli               |
| Microsoft Internet Explorer      | Desteklenmiyor&dagger; |

&dagger;Microsoft Internet Explorer [Webassembly](https://webassembly.org)'yi desteklemez.

### Blazor Server

| Tarayıcı                          | Sürüm    |
| -------------------------------- | :--------: |
| Microsoft Edge                   | Geçerli    |
| Mozilla Firefox                  | Geçerli    |
| Android dahil Google Chrome | Geçerli    |
| İOS dahil Safari            | Geçerli    |
| Microsoft Internet Explorer      | üst&dagger; |

&dagger;Ek polydolgular gereklidir (örneğin, bu, bir paket ile eklenebilir [`Polyfill.io`](https://polyfill.io/v3/) ).

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:blazor/hosting-models>
