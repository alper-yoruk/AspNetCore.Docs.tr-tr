---
title: ASP.NET Core SignalR istemcileri
author: bradygaster
description: Çeşitli ASP.NET Core istemcileri tarafından desteklenen özellikleri öğrenin SignalR .
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/client-features
ms.openlocfilehash: a759e473ff7ffaebd0eb9309f37a959d0e06a466
ms.sourcegitcommit: e20653091c30e0768c4f960343e2c3dd658bba13
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/16/2020
ms.locfileid: "83438973"
---
# <a name="aspnet-core-signalr-clients"></a>ASP.NET Core SignalR istemcileri

## <a name="versioning-support-and-compatibility"></a>Sürüm oluşturma, destek ve uyumluluk

SignalR istemcileri sunucu bileşenleriyle birlikte dağıtılır ve eşleşmesi için sürümlüdür. Desteklenen her türlü istemci, desteklenen herhangi bir sunucuya güvenli bir şekilde bağlanabilir ve tüm uyumluluk sorunları düzeltilme hataları olarak kabul edilebilir. SignalR istemcileri, .NET Core 'un geri kalanı ile aynı destek yaşam döngüsü içinde desteklenir. Ayrıntılar için bkz. [.NET Core destek ilkesi](https://dotnet.microsoft.com/platform/support/policy/dotnet-core) .

Birçok özellik uyumlu bir istemci **ve** sunucu gerektirir. Çeşitli özelliklerin en düşük sürümlerinin gösterildiği tablo için aşağıya bakın.

SignalR 'nin 1. x sürümleri 2,1 ve 2,2 .NET Core yayınlarına eşlenir ve aynı yaşam süresine sahiptir. Sürüm 3. x ve üzeri için, SignalR sürümü .NET 'in geri kalanıyla tamamen eşleşir ve aynı destek yaşam döngüsüne sahiptir.

| SignalR sürümü | .NET Core sürümü | Destek düzeyi | Destek sonu |
| - | - | - | - |
| 1.0. x | 2.1. x | Uzun süreli destek | 21 Ağustos 2021 |
| 1.1. x | 2.2. x | Yaşam sonu | 23 Aralık 2019 |
| 3. x veya üzeri | *SignalR sürümü ile aynı* | Bkz. [.NET Core destek ilkesi](https://dotnet.microsoft.com/platform/support/policy/dotnet-core) |

**Note:** ASP.NET Core 3,0 ' de, JavaScript istemcisi *moved* `@microsoft/signalr` NPM paketine taşınır.

## <a name="feature-distribution"></a>Özellik dağıtımı

Aşağıdaki tabloda, gerçek zamanlı destek sunan istemciler için özellikler ve destek gösterilmektedir. Her özellik için, bu özelliği destekleyen *En düşük* sürüm listelenir. Listelenen bir sürüm yoksa özellik desteklenmez.

| Öne çıkan özelliği | Sunucu | .NET istemcisi | JavaScript istemcisi | Java istemcisi |
| ---- | :-: | :-: | :-: | :-: |
| Azure SignalR hizmeti desteği |2.1.0|1.0.0|1.0.0|1.0.0|
| [Sunucudan istemciye akış](xref:signalr/streaming)          |2.1.0|1.0.0|1.0.0|1.0.0|
| [İstemciden sunucuya akış](xref:signalr/streaming)          |3.0.0|3.0.0|3.0.0|3.0.0|
| Otomatik yeniden bağlanma ([.net](/aspnet/core/signalr/dotnet-client?view=aspnetcore-3.0&tabs=visual-studio#handle-lost-connection), [JavaScript](/aspnet/core/signalr/javascript-client?view=aspnetcore-3.0#reconnect-clients))          |3.0.0|3.0.0|3.0.0|❌|
| WebSockets taşıma |2.1.0|1.0.0|1.0.0|1.0.0|
| Sunucu tarafından gönderilen olay aktarımı |2.1.0|1.0.0|1.0.0|❌|
| Uzun yoklama taşıması |2.1.0|1.0.0|1.0.0|3.0.0|
| JSON hub 'ı Protokolü |2.1.0|1.0.0|1.0.0|1.0.0|
| MessagePack Hub Protokolü |2.1.0|1.0.0|1.0.0|❌|

[Sorun izleyici](https://github.com/dotnet/AspNetCore/issues)'de ek istemci özelliklerinin etkinleştirilmesi için destek izlenir.

## <a name="additional-resources"></a>Ek kaynaklar

* [SignalRASP.NET Core için kullanmaya başlama](xref:tutorials/signalr)
* [Desteklenen platformlar](xref:signalr/supported-platforms)
* [Merkezler](xref:signalr/hubs)
* [JavaScript istemcisi](xref:signalr/javascript-client)
