---
title: ASP.NET Core anahtar imlebilirlik ve anahtar ayarları
author: rick-anderson
description: ASP.NET Core veri koruma anahtarı imlebilirlik API 'Lerinin uygulama ayrıntılarını öğrenin.
ms.author: riande
ms.date: 10/14/2016
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
uid: security/data-protection/implementation/key-immutability
ms.openlocfilehash: 8efca1d96945cb7af0132b27801b23a45714e08a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061255"
---
# <a name="key-immutability-and-key-settings-in-aspnet-core"></a>ASP.NET Core anahtar imlebilirlik ve anahtar ayarları

Bir nesne, yedekleme deposuna kalıcı olduktan sonra, temsili süresiz olarak düzeltilir. Yeni veriler, yedekleme deposuna eklenebilir, ancak mevcut veriler hiçbir şekilde değiştirilemez. Bu davranışın birincil amacı, verilerin bozulmasını önlemektir.

Bu davranışın bir sonucu, yedekleme deposuna bir anahtar yazıldıktan sonra sabittir. Oluşturma, etkinleştirme ve sona erme tarihleri hiçbir zaman değiştirilemez, ancak kullanılarak iptal edilebilir `IKeyManager` . Ayrıca, temel alınan algoritmik bilgileri, ana anahtar malzemeleri ve REST özelliklerindeki şifreleme de sabittir.

Geliştirici, anahtar kalıcılığını etkileyen herhangi bir ayarı değiştirirse, bu değişiklikler, bir sonraki anahtar üretilene kadar, `IKeyManager.CreateNewKey` veri koruma sisteminin kendi [otomatik anahtar oluşturma](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) davranışına veya aracılığıyla açık bir çağrı aracılığıyla geçerli olmayacaktır. Anahtar kalıcılığını etkileyen ayarlar şunlardır:

* [Varsayılan anahtar yaşam süresi](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management)

* [REST mekanizmasında anahtar şifreleme](xref:security/data-protection/implementation/key-encryption-at-rest)

* [Algoritmik bilgileri, anahtar içinde yer alır](xref:security/data-protection/configuration/overview#changing-algorithms-with-usecryptographicalgorithms)

Bu ayarların sonraki otomatik anahtar alma zamanından daha önce kullanıma sunulmasını istiyorsanız, `IKeyManager.CreateNewKey` Yeni bir anahtarın oluşturulmasını zorlamak için açık bir çağrı yapmayı düşünün. Açık bir etkinleştirme tarihi ({Now + 2 gün}), çağrının, değişikliğin yayması için zaman kullanılmasına izin veren iyi bir kural ve çağrının sona erme tarihi olduğunu unutmayın.

>[!TIP]
> Depoya dokunmadan tüm uygulamalar, uzantı yöntemleriyle aynı ayarları belirtmelidir `IDataProtectionBuilder` . Aksi takdirde, kalıcı anahtarın özellikleri, anahtar oluşturma yordamlarını çağıran belirli bir uygulamaya bağımlıdır.
