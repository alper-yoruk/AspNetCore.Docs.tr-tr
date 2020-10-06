---
title: BlazorDesteklenen platformları ASP.NET Core
author: guardrex
description: ASP.NET Core için desteklenen platformlar hakkında bilgi edinin Blazor .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/01/2020
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
uid: blazor/supported-platforms
ms.openlocfilehash: 1ffe98636ed200adbf00e89c2c3499eb69792d3f
ms.sourcegitcommit: d60bfd52bfb559e805abd654b87a2a0c7eb69cf8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/06/2020
ms.locfileid: "91754547"
---
# <a name="aspnet-core-no-locblazor-supported-platforms"></a>BlazorDesteklenen platformları ASP.NET Core

[Luke Latham](https://github.com/guardrex) tarafından

::: moniker range=">= aspnetcore-5.0"

Blazor WebAssembly ve Blazor Server Aşağıdaki tabloda gösterilen tarayıcılarda desteklenir.

| Tarayıcı                          | Sürüm         |
| -------------------------------- | --------------- |
| İOS dahil Apple Safari      | Geçerli&dagger; |
| Android dahil Google Chrome | Geçerli&dagger; |
| Microsoft Edge                   | Geçerli&dagger; |
| Mozilla Firefox                  | Geçerli&dagger; |  

&dagger;*Geçerli* , tarayıcının en son sürümünü ifade eder.  

::: moniker-end

::: moniker range="< aspnetcore-5.0"

## Blazor WebAssembly

| Tarayıcı                          | Sürüm               |
| -------------------------------- | --------------------- |
| İOS dahil Apple Safari      | Geçerli&dagger;       |
| Android dahil Google Chrome | Geçerli&dagger;       |
| Microsoft Edge                   | Geçerli&dagger;       |
| Microsoft Internet Explorer      | Desteklenmiyor&Dagger; |
| Mozilla Firefox                  | Geçerli&dagger;       |  

&dagger;*Geçerli* , tarayıcının en son sürümünü ifade eder.  
&Dagger;Microsoft Internet Explorer [Webassembly](https://webassembly.org)'yi desteklemez.

## Blazor Server

| Tarayıcı                          | Sürüm         |
| -------------------------------- | --------------- |
| İOS dahil Apple Safari      | Geçerli&dagger; |
| Android dahil Google Chrome | Geçerli&dagger; |
| Microsoft Edge                   | Geçerli&dagger; |
| Microsoft Internet Explorer      | üst&Dagger;      |
| Mozilla Firefox                  | Geçerli&dagger; |

&dagger;*Geçerli* , tarayıcının en son sürümünü ifade eder.  
&Dagger;Ek polydolgular gereklidir. Örneğin, bir paket aracılığıyla taahhüt eklenebilir [`Polyfill.io`](https://polyfill.io/v3/) .

::: moniker-end

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:blazor/hosting-models>
* <xref:signalr/supported-platforms>
