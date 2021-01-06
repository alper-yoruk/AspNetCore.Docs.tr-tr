---
title: BlazorDesteklenen platformları ASP.NET Core
author: guardrex
description: ASP.NET Core için desteklenen platformlar hakkında bilgi edinin Blazor .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/01/2020
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
uid: blazor/supported-platforms
ms.openlocfilehash: fe0734dbf6eb2647fa6c9b6f336063b9ec091139
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93054963"
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
