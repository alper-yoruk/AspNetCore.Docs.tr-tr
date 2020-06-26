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
ms.openlocfilehash: e9de92233468e9e07791df608b1c37ffb3b29949
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408506"
---
# <a name="miscellaneous-aspnet-core-data-protection-apis"></a><span data-ttu-id="33316-103">Çeşitli ASP.NET Core veri koruma API 'Leri</span><span class="sxs-lookup"><span data-stu-id="33316-103">Miscellaneous ASP.NET Core Data Protection APIs</span></span>

<a name="data-protection-extensibility-mics-apis"></a>

>[!WARNING]
> <span data-ttu-id="33316-104">Aşağıdaki arabirimlerin herhangi birini uygulayan türler birden çok çağıranlar için iş parçacığı açısından güvenli olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="33316-104">Types that implement any of the following interfaces should be thread-safe for multiple callers.</span></span>

## <a name="isecret"></a><span data-ttu-id="33316-105">Isecret</span><span class="sxs-lookup"><span data-stu-id="33316-105">ISecret</span></span>

<span data-ttu-id="33316-106">`ISecret`Arabirim, şifreleme anahtar malzemesi gibi bir gizli değeri temsil eder.</span><span class="sxs-lookup"><span data-stu-id="33316-106">The `ISecret` interface represents a secret value, such as cryptographic key material.</span></span> <span data-ttu-id="33316-107">Aşağıdaki API yüzeyini içerir:</span><span class="sxs-lookup"><span data-stu-id="33316-107">It contains the following API surface:</span></span>

* <span data-ttu-id="33316-108">`Length`: `int`</span><span class="sxs-lookup"><span data-stu-id="33316-108">`Length`: `int`</span></span>

* <span data-ttu-id="33316-109">`Dispose()`: `void`</span><span class="sxs-lookup"><span data-stu-id="33316-109">`Dispose()`: `void`</span></span>

* <span data-ttu-id="33316-110">`WriteSecretIntoBuffer(ArraySegment<byte> buffer)`: `void`</span><span class="sxs-lookup"><span data-stu-id="33316-110">`WriteSecretIntoBuffer(ArraySegment<byte> buffer)`: `void`</span></span>

<span data-ttu-id="33316-111">`WriteSecretIntoBuffer`Yöntemi, sağlanan arabelleği ham gizli değer ile doldurur.</span><span class="sxs-lookup"><span data-stu-id="33316-111">The `WriteSecretIntoBuffer` method populates the supplied buffer with the raw secret value.</span></span> <span data-ttu-id="33316-112">Bu API 'nin arabelleği doğrudan döndürmek yerine bir parametre olarak aldığı neden, `byte[]` Bu, çağrıyı yapana, yönetilen çöp toplayıcısıyla gizli dizi ile sınırlama sağlayan bir arabellek nesnesini sabitleme fırsatı verir.</span><span class="sxs-lookup"><span data-stu-id="33316-112">The reason this API takes the buffer as a parameter rather than returning a `byte[]` directly is that this gives the caller the opportunity to pin the buffer object, limiting secret exposure to the managed garbage collector.</span></span>

<span data-ttu-id="33316-113">`Secret`Türü, `ISecret` gizli değerinin işlem içi bellekte depolandığı somut bir uygulamasıdır.</span><span class="sxs-lookup"><span data-stu-id="33316-113">The `Secret` type is a concrete implementation of `ISecret` where the secret value is stored in in-process memory.</span></span> <span data-ttu-id="33316-114">Windows platformlarında gizli değeri [CryptProtectMemory](https://msdn.microsoft.com/library/windows/desktop/aa380262(v=vs.85).aspx)aracılığıyla şifrelenir.</span><span class="sxs-lookup"><span data-stu-id="33316-114">On Windows platforms, the secret value is encrypted via [CryptProtectMemory](https://msdn.microsoft.com/library/windows/desktop/aa380262(v=vs.85).aspx).</span></span>
