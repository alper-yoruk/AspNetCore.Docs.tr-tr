---
title: ASP.NET Core anahtar imlebilirlik ve anahtar ayarları
author: rick-anderson
description: ASP.NET Core veri koruma anahtarı imlebilirlik API 'Lerinin uygulama ayrıntılarını öğrenin.
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
uid: security/data-protection/implementation/key-immutability
ms.openlocfilehash: 01fb3a155aefa34dcd9ede8e7d6ada8fe6bb751c
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85403826"
---
# <a name="key-immutability-and-key-settings-in-aspnet-core"></a><span data-ttu-id="ffe12-103">ASP.NET Core anahtar imlebilirlik ve anahtar ayarları</span><span class="sxs-lookup"><span data-stu-id="ffe12-103">Key immutability and key settings in ASP.NET Core</span></span>

<span data-ttu-id="ffe12-104">Bir nesne, yedekleme deposuna kalıcı olduktan sonra, temsili süresiz olarak düzeltilir.</span><span class="sxs-lookup"><span data-stu-id="ffe12-104">Once an object is persisted to the backing store, its representation is forever fixed.</span></span> <span data-ttu-id="ffe12-105">Yeni veriler, yedekleme deposuna eklenebilir, ancak mevcut veriler hiçbir şekilde değiştirilemez.</span><span class="sxs-lookup"><span data-stu-id="ffe12-105">New data can be added to the backing store, but existing data can never be mutated.</span></span> <span data-ttu-id="ffe12-106">Bu davranışın birincil amacı, verilerin bozulmasını önlemektir.</span><span class="sxs-lookup"><span data-stu-id="ffe12-106">The primary purpose of this behavior is to prevent data corruption.</span></span>

<span data-ttu-id="ffe12-107">Bu davranışın bir sonucu, yedekleme deposuna bir anahtar yazıldıktan sonra sabittir.</span><span class="sxs-lookup"><span data-stu-id="ffe12-107">One consequence of this behavior is that once a key is written to the backing store, it's immutable.</span></span> <span data-ttu-id="ffe12-108">Oluşturma, etkinleştirme ve sona erme tarihleri hiçbir zaman değiştirilemez, ancak kullanılarak iptal edilebilir `IKeyManager` .</span><span class="sxs-lookup"><span data-stu-id="ffe12-108">Its creation, activation, and expiration dates can never be changed, though it can revoked by using `IKeyManager`.</span></span> <span data-ttu-id="ffe12-109">Ayrıca, temel alınan algoritmik bilgileri, ana anahtar malzemeleri ve REST özelliklerindeki şifreleme de sabittir.</span><span class="sxs-lookup"><span data-stu-id="ffe12-109">Additionally, its underlying algorithmic information, master keying material, and encryption at rest properties are also immutable.</span></span>

<span data-ttu-id="ffe12-110">Geliştirici, anahtar kalıcılığını etkileyen herhangi bir ayarı değiştirirse, bu değişiklikler, bir sonraki anahtar üretilene kadar, `IKeyManager.CreateNewKey` veri koruma sisteminin kendi [otomatik anahtar oluşturma](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) davranışına veya aracılığıyla açık bir çağrı aracılığıyla geçerli olmayacaktır.</span><span class="sxs-lookup"><span data-stu-id="ffe12-110">If the developer changes any setting that affects key persistence, those changes won't go into effect until the next time a key is generated, either via an explicit call to `IKeyManager.CreateNewKey` or via the data protection system's own [automatic key generation](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) behavior.</span></span> <span data-ttu-id="ffe12-111">Anahtar kalıcılığını etkileyen ayarlar şunlardır:</span><span class="sxs-lookup"><span data-stu-id="ffe12-111">The settings that affect key persistence are as follows:</span></span>

* [<span data-ttu-id="ffe12-112">Varsayılan anahtar yaşam süresi</span><span class="sxs-lookup"><span data-stu-id="ffe12-112">The default key lifetime</span></span>](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management)

* [<span data-ttu-id="ffe12-113">REST mekanizmasında anahtar şifreleme</span><span class="sxs-lookup"><span data-stu-id="ffe12-113">The key encryption at rest mechanism</span></span>](xref:security/data-protection/implementation/key-encryption-at-rest)

* [<span data-ttu-id="ffe12-114">Algoritmik bilgileri, anahtar içinde yer alır</span><span class="sxs-lookup"><span data-stu-id="ffe12-114">The algorithmic information contained within the key</span></span>](xref:security/data-protection/configuration/overview#changing-algorithms-with-usecryptographicalgorithms)

<span data-ttu-id="ffe12-115">Bu ayarların sonraki otomatik anahtar alma zamanından daha önce kullanıma sunulmasını istiyorsanız, `IKeyManager.CreateNewKey` Yeni bir anahtarın oluşturulmasını zorlamak için açık bir çağrı yapmayı düşünün.</span><span class="sxs-lookup"><span data-stu-id="ffe12-115">If you need these settings to kick in earlier than the next automatic key rolling time, consider making an explicit call to `IKeyManager.CreateNewKey` to force the creation of a new key.</span></span> <span data-ttu-id="ffe12-116">Açık bir etkinleştirme tarihi ({Now + 2 gün}), çağrının, değişikliğin yayması için zaman kullanılmasına izin veren iyi bir kural ve çağrının sona erme tarihi olduğunu unutmayın.</span><span class="sxs-lookup"><span data-stu-id="ffe12-116">Remember to provide an explicit activation date ({ now + 2 days } is a good rule of thumb to allow time for the change to propagate) and expiration date in the call.</span></span>

>[!TIP]
> <span data-ttu-id="ffe12-117">Depoya dokunmadan tüm uygulamalar, uzantı yöntemleriyle aynı ayarları belirtmelidir `IDataProtectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="ffe12-117">All applications touching the repository should specify the same settings with the `IDataProtectionBuilder` extension methods.</span></span> <span data-ttu-id="ffe12-118">Aksi takdirde, kalıcı anahtarın özellikleri, anahtar oluşturma yordamlarını çağıran belirli bir uygulamaya bağımlıdır.</span><span class="sxs-lookup"><span data-stu-id="ffe12-118">Otherwise, the properties of the persisted key will be dependent on the particular application that invoked the key generation routines.</span></span>
