---
title: Çeşitli ASP.NET Core veri koruma API 'Leri
author: rick-anderson
description: ASP.NET Core Data Protection ısecret arabirimi hakkında bilgi edinin.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/extensibility/misc-apis
ms.openlocfilehash: a07ccc3645a9a8132fd5290e7c43f353f74aca05
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776987"
---
# <a name="miscellaneous-aspnet-core-data-protection-apis"></a>Çeşitli ASP.NET Core veri koruma API 'Leri

<a name="data-protection-extensibility-mics-apis"></a>

>[!WARNING]
> Aşağıdaki arabirimlerin herhangi birini uygulayan türler birden çok çağıranlar için iş parçacığı açısından güvenli olmalıdır.

## <a name="isecret"></a>Isecret

`ISecret` Arabirim, şifreleme anahtar malzemesi gibi bir gizli değeri temsil eder. Aşağıdaki API yüzeyini içerir:

* `Length`: `int`

* `Dispose()`: `void`

* `WriteSecretIntoBuffer(ArraySegment<byte> buffer)`: `void`

`WriteSecretIntoBuffer` Yöntemi, sağlanan arabelleği ham gizli değer ile doldurur. Bu API 'nin arabelleği `byte[]` doğrudan döndürmek yerine bir parametre olarak aldığı neden, bu, çağrıyı yapana, yönetilen çöp toplayıcısıyla gizli dizi ile sınırlama sağlayan bir arabellek nesnesini sabitleme fırsatı verir.

`Secret` Türü, gizli değerinin işlem içi bellekte `ISecret` depolandığı somut bir uygulamasıdır. Windows platformlarında gizli değeri [CryptProtectMemory](https://msdn.microsoft.com/library/windows/desktop/aa380262(v=vs.85).aspx)aracılığıyla şifrelenir.
