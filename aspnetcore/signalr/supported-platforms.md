---
title: SignalRDesteklenen platformları ASP.NET Core
author: bradygaster
description: ASP.NET Core için desteklenen platformlar hakkında bilgi edinin SignalR .
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc, devx-track-js
ms.date: 01/16/2020
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
uid: signalr/supported-platforms
ms.openlocfilehash: 761edbbe7bab28d2340207a0adea0718b37c7ec1
ms.sourcegitcommit: 2e3a967331b2c69f585dd61e9ad5c09763615b44
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92690342"
---
# <a name="aspnet-core-no-locsignalr-supported-platforms"></a>SignalRDesteklenen platformları ASP.NET Core

## <a name="server-system-requirements"></a>Sunucu sistem gereksinimleri

SignalR ASP.NET Core için ASP.NET Core desteklediği tüm sunucu platformunu destekler.

## <a name="javascript-client"></a>JavaScript istemcisi

[JavaScript Istemcisi](xref:signalr/javascript-client) NodeJS 8 ve sonraki sürümlerde ve aşağıdaki tarayıcılarda çalışır:

| Tarayıcı                          | Sürüm         |
| -------------------------------- | --------------- |
| İOS dahil Apple Safari      | Geçerli&dagger; |
| Android dahil Google Chrome | Geçerli&dagger; |
| Microsoft Edge                   | Geçerli&dagger; |
| Mozilla Firefox                  | Geçerli&dagger; |

&dagger;*Geçerli* , tarayıcının en son sürümünü ifade eder.

## <a name="net-client"></a>.NET istemcisi

[.Net Client](xref:signalr/dotnet-client) , ASP.NET Core tarafından desteklenen herhangi bir platformda çalışır. Örneğin, [Xamarin geliştiricileri SignalR ](https://github.com/aspnet/Announcements/issues/305) Xamarin. iOS 8.4.0.1 ve üzeri ve Android uygulamalarını kullanarak Xamarin. iOS 11.14.0.4 ve üstünü kullanarak Android uygulamaları oluşturmak için kullanabilir.

Sunucu IIS çalıştırıyorsa, WebSockets aktarımı Windows Server 2012 veya üzeri sürümlerde IIS 8,0 veya sonraki bir sürümü gerektirir. Diğer aktarımlar tüm platformlarda desteklenir.

## <a name="java-client"></a>Java istemcisi

[Java istemcisi](xref:signalr/java-client) , Java 8 ve sonraki sürümlerini destekler.

## <a name="unsupported-clients"></a>Desteklenmeyen istemciler

Aşağıdaki istemciler mevcuttur, ancak deneysel veya resmi olmayan bir. Şu anda desteklenmemektedir ve hiçbir şekilde bulunmayabilir.

* [C++ istemcisi](https://github.com/aspnet/SignalR-Client-Cpp)

* [Swift istemcisi](https://github.com/moozzyk/SignalR-Client-Swift)
