---
title: BlazorDesteklenen platformları ASP.NET Core
author: guardrex
description: ASP.NET Core için desteklenen platformlar hakkında bilgi edinin Blazor .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/supported-platforms
ms.openlocfilehash: 99f14486711c2dd2a634bc51b27a8e3891deee1a
ms.sourcegitcommit: 066d66ea150f8aab63f9e0e0668b06c9426296fd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/23/2020
ms.locfileid: "85243232"
---
# <a name="aspnet-core-blazor-supported-platforms"></a>BlazorDesteklenen platformları ASP.NET Core

[Luke Latham](https://github.com/guardrex) tarafından

## <a name="browser-requirements"></a>Tarayıcı gereksinimleri

### <a name="blazor-webassembly"></a>BlazorWebAssembly

| Tarayıcı                          | Sürüm               |
| -------------------------------- | :-------------------: |
| Microsoft Edge                   | Geçerli               |
| Mozilla Firefox                  | Geçerli               |
| Android dahil Google Chrome | Geçerli               |
| İOS dahil Safari            | Geçerli               |
| Microsoft Internet Explorer      | Desteklenmiyor&dagger; |

&dagger;Microsoft Internet Explorer [Webassembly](https://webassembly.org)'yi desteklemez.

### <a name="blazor-server"></a>BlazorServer

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
