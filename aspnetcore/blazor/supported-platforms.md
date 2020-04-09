---
title: ASP.NET Blazor Core destekli platformlar
author: guardrex
description: ASP.NET Core Blazoriçin desteklenen platformlar hakkında bilgi edinin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2019
no-loc:
- Blazor
- SignalR
uid: blazor/supported-platforms
ms.openlocfilehash: 505974280b5c96ec2bcae42c6e076ab67a15bb07
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78658860"
---
# <a name="aspnet-core-blazor-supported-platforms"></a>ASP.NET Core Blazor desteklenen platformlar

Yazar: [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

## <a name="browser-requirements"></a>Tarayıcı gereksinimleri

### <a name="blazor-webassembly"></a>Blazor WebAssembly

| Tarayıcı                          | Sürüm               |
| -------------------------------- | :-------------------: |
| Microsoft Edge                   | Geçerli               |
| Mozilla Firefox                  | Geçerli               |
| Google Chrome, Android dahil | Geçerli               |
| Safari, iOS dahil            | Geçerli               |
| Microsoft Internet Explorer      | Desteklenmiyor&dagger; |

&dagger;Microsoft Internet Explorer [WebAssembly'i](https://webassembly.org)desteklemez.

### <a name="blazor-server"></a>Blazor Server

| Tarayıcı                          | Sürüm    |
| -------------------------------- | :--------: |
| Microsoft Edge                   | Geçerli    |
| Mozilla Firefox                  | Geçerli    |
| Google Chrome, Android dahil | Geçerli    |
| Safari, iOS dahil            | Geçerli    |
| Microsoft Internet Explorer      | 11&dagger; |

&dagger;Ek polifills gereklidir (örneğin, vaatler [Polyfill.io](https://polyfill.io/v3/) bir paket üzerinden eklenebilir).

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:blazor/hosting-models>
