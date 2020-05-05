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
# <a name="miscellaneous-aspnet-core-data-protection-apis"></a><span data-ttu-id="c184d-103">Çeşitli ASP.NET Core veri koruma API 'Leri</span><span class="sxs-lookup"><span data-stu-id="c184d-103">Miscellaneous ASP.NET Core Data Protection APIs</span></span>

<a name="data-protection-extensibility-mics-apis"></a>

>[!WARNING]
> <span data-ttu-id="c184d-104">Aşağıdaki arabirimlerin herhangi birini uygulayan türler birden çok çağıranlar için iş parçacığı açısından güvenli olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="c184d-104">Types that implement any of the following interfaces should be thread-safe for multiple callers.</span></span>

## <a name="isecret"></a><span data-ttu-id="c184d-105">Isecret</span><span class="sxs-lookup"><span data-stu-id="c184d-105">ISecret</span></span>

<span data-ttu-id="c184d-106">`ISecret` Arabirim, şifreleme anahtar malzemesi gibi bir gizli değeri temsil eder.</span><span class="sxs-lookup"><span data-stu-id="c184d-106">The `ISecret` interface represents a secret value, such as cryptographic key material.</span></span> <span data-ttu-id="c184d-107">Aşağıdaki API yüzeyini içerir:</span><span class="sxs-lookup"><span data-stu-id="c184d-107">It contains the following API surface:</span></span>

* <span data-ttu-id="c184d-108">`Length`: `int`</span><span class="sxs-lookup"><span data-stu-id="c184d-108">`Length`: `int`</span></span>

* <span data-ttu-id="c184d-109">`Dispose()`: `void`</span><span class="sxs-lookup"><span data-stu-id="c184d-109">`Dispose()`: `void`</span></span>

* <span data-ttu-id="c184d-110">`WriteSecretIntoBuffer(ArraySegment<byte> buffer)`: `void`</span><span class="sxs-lookup"><span data-stu-id="c184d-110">`WriteSecretIntoBuffer(ArraySegment<byte> buffer)`: `void`</span></span>

<span data-ttu-id="c184d-111">`WriteSecretIntoBuffer` Yöntemi, sağlanan arabelleği ham gizli değer ile doldurur.</span><span class="sxs-lookup"><span data-stu-id="c184d-111">The `WriteSecretIntoBuffer` method populates the supplied buffer with the raw secret value.</span></span> <span data-ttu-id="c184d-112">Bu API 'nin arabelleği `byte[]` doğrudan döndürmek yerine bir parametre olarak aldığı neden, bu, çağrıyı yapana, yönetilen çöp toplayıcısıyla gizli dizi ile sınırlama sağlayan bir arabellek nesnesini sabitleme fırsatı verir.</span><span class="sxs-lookup"><span data-stu-id="c184d-112">The reason this API takes the buffer as a parameter rather than returning a `byte[]` directly is that this gives the caller the opportunity to pin the buffer object, limiting secret exposure to the managed garbage collector.</span></span>

<span data-ttu-id="c184d-113">`Secret` Türü, gizli değerinin işlem içi bellekte `ISecret` depolandığı somut bir uygulamasıdır.</span><span class="sxs-lookup"><span data-stu-id="c184d-113">The `Secret` type is a concrete implementation of `ISecret` where the secret value is stored in in-process memory.</span></span> <span data-ttu-id="c184d-114">Windows platformlarında gizli değeri [CryptProtectMemory](https://msdn.microsoft.com/library/windows/desktop/aa380262(v=vs.85).aspx)aracılığıyla şifrelenir.</span><span class="sxs-lookup"><span data-stu-id="c184d-114">On Windows platforms, the secret value is encrypted via [CryptProtectMemory](https://msdn.microsoft.com/library/windows/desktop/aa380262(v=vs.85).aspx).</span></span>
