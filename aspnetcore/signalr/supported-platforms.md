---
title: Desteklenen SignalR platformları ASP.NET Core
author: bradygaster
description: ASP.NET Core SignalRiçin desteklenen platformlar hakkında bilgi edinin.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 01/16/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/supported-platforms
ms.openlocfilehash: 70a05dabb95aaf561aa78d5c8b24b430c51bd973
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82772611"
---
# <a name="aspnet-core-signalr-supported-platforms"></a>ASP.NET Core SignalR desteklenen platformlar

## <a name="server-system-requirements"></a>Sunucu sistem gereksinimleri

ASP.NET Core için SignalR, ASP.NET Core desteklediği tüm sunucu platformunu destekler.

## <a name="javascript-client"></a>JavaScript istemcisi

[JavaScript Istemcisi](xref:signalr/javascript-client) NodeJS 8 ve sonraki sürümlerde ve aşağıdaki tarayıcılarda çalışır:

| Tarayıcı                         | Sürüm         |
| ------------------------------- | --------------- |
| Microsoft Edge                  | Geçerli&dagger; |
| Mozilla Firefox                 | Geçerli&dagger; |
| Google Chrome; Android içerir | Geçerli&dagger; |
| Uygulamasını iOS içerir            | Geçerli&dagger; |
| Microsoft Internet Explorer     | 11              |

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
