---
title: Çeşitli ASP.NET Core veri koruma API 'Leri
author: rick-anderson
description: ASP.NET Core Data Protection ısecret arabirimi hakkında bilgi edinin.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/extensibility/misc-apis
ms.openlocfilehash: 2e319cdcec1e005682555c4e03c52632e6d8521a
ms.sourcegitcommit: b0fa7ff0cb158277df61bcd08058a81222c3fe10
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/07/2020
ms.locfileid: "87913807"
---
# <a name="miscellaneous-aspnet-core-data-protection-apis"></a>Çeşitli ASP.NET Core veri koruma API 'Leri

<a name="data-protection-extensibility-mics-apis"></a>

>[!WARNING]
> Aşağıdaki arabirimlerin herhangi birini uygulayan türler birden çok çağıranlar için iş parçacığı açısından güvenli olmalıdır.

## <a name="isecret"></a>Isecret

`ISecret`Arabirim, şifreleme anahtar malzemesi gibi bir gizli değeri temsil eder. Aşağıdaki API yüzeyini içerir:

* `Length`: `int`

* `Dispose()`: `void`

* `WriteSecretIntoBuffer(ArraySegment<byte> buffer)`: `void`

`WriteSecretIntoBuffer`Yöntemi, sağlanan arabelleği ham gizli değer ile doldurur. Bu API 'nin arabelleği doğrudan döndürmek yerine bir parametre olarak aldığı neden, `byte[]` Bu, çağrıyı yapana, yönetilen çöp toplayıcısıyla gizli dizi ile sınırlama sağlayan bir arabellek nesnesini sabitleme fırsatı verir.

`Secret`Türü, `ISecret` gizli değerinin işlem içi bellekte depolandığı somut bir uygulamasıdır. Windows platformlarında gizli değeri [CryptProtectMemory](/windows/win32/api/dpapi/nf-dpapi-cryptprotectmemory)aracılığıyla şifrelenir.
