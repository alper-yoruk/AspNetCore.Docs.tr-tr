---
title: Çeşitli ASP.NET Core veri koruma API 'Leri
author: rick-anderson
description: ASP.NET Core Data Protection ısecret arabirimi hakkında bilgi edinin.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/extensibility/misc-apis
ms.openlocfilehash: 5ab8226779fb4209a7254b95eccac4be2d26b10d
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88019047"
---
# <a name="miscellaneous-aspnet-core-data-protection-apis"></a><span data-ttu-id="d5a07-103">Çeşitli ASP.NET Core veri koruma API 'Leri</span><span class="sxs-lookup"><span data-stu-id="d5a07-103">Miscellaneous ASP.NET Core Data Protection APIs</span></span>

<a name="data-protection-extensibility-mics-apis"></a>

>[!WARNING]
> <span data-ttu-id="d5a07-104">Aşağıdaki arabirimlerin herhangi birini uygulayan türler birden çok çağıranlar için iş parçacığı açısından güvenli olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="d5a07-104">Types that implement any of the following interfaces should be thread-safe for multiple callers.</span></span>

## <a name="isecret"></a><span data-ttu-id="d5a07-105">Isecret</span><span class="sxs-lookup"><span data-stu-id="d5a07-105">ISecret</span></span>

<span data-ttu-id="d5a07-106">`ISecret`Arabirim, şifreleme anahtar malzemesi gibi bir gizli değeri temsil eder.</span><span class="sxs-lookup"><span data-stu-id="d5a07-106">The `ISecret` interface represents a secret value, such as cryptographic key material.</span></span> <span data-ttu-id="d5a07-107">Aşağıdaki API yüzeyini içerir:</span><span class="sxs-lookup"><span data-stu-id="d5a07-107">It contains the following API surface:</span></span>

* <span data-ttu-id="d5a07-108">`Length`: `int`</span><span class="sxs-lookup"><span data-stu-id="d5a07-108">`Length`: `int`</span></span>

* <span data-ttu-id="d5a07-109">`Dispose()`: `void`</span><span class="sxs-lookup"><span data-stu-id="d5a07-109">`Dispose()`: `void`</span></span>

* <span data-ttu-id="d5a07-110">`WriteSecretIntoBuffer(ArraySegment<byte> buffer)`: `void`</span><span class="sxs-lookup"><span data-stu-id="d5a07-110">`WriteSecretIntoBuffer(ArraySegment<byte> buffer)`: `void`</span></span>

<span data-ttu-id="d5a07-111">`WriteSecretIntoBuffer`Yöntemi, sağlanan arabelleği ham gizli değer ile doldurur.</span><span class="sxs-lookup"><span data-stu-id="d5a07-111">The `WriteSecretIntoBuffer` method populates the supplied buffer with the raw secret value.</span></span> <span data-ttu-id="d5a07-112">Bu API 'nin arabelleği doğrudan döndürmek yerine bir parametre olarak aldığı neden, `byte[]` Bu, çağrıyı yapana, yönetilen çöp toplayıcısıyla gizli dizi ile sınırlama sağlayan bir arabellek nesnesini sabitleme fırsatı verir.</span><span class="sxs-lookup"><span data-stu-id="d5a07-112">The reason this API takes the buffer as a parameter rather than returning a `byte[]` directly is that this gives the caller the opportunity to pin the buffer object, limiting secret exposure to the managed garbage collector.</span></span>

<span data-ttu-id="d5a07-113">`Secret`Türü, `ISecret` gizli değerinin işlem içi bellekte depolandığı somut bir uygulamasıdır.</span><span class="sxs-lookup"><span data-stu-id="d5a07-113">The `Secret` type is a concrete implementation of `ISecret` where the secret value is stored in in-process memory.</span></span> <span data-ttu-id="d5a07-114">Windows platformlarında gizli değeri [CryptProtectMemory](/windows/win32/api/dpapi/nf-dpapi-cryptprotectmemory)aracılığıyla şifrelenir.</span><span class="sxs-lookup"><span data-stu-id="d5a07-114">On Windows platforms, the secret value is encrypted via [CryptProtectMemory](/windows/win32/api/dpapi/nf-dpapi-cryptprotectmemory).</span></span>
