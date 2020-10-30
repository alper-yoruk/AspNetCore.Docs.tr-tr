---
title: ASP.NET Core anahtarları iptal edilen yüklerin korumasını kaldır
author: rick-anderson
description: ASP.NET Core bir uygulamada, bu yana gelen anahtarlarla korunan verilerin korumasını kaldırma hakkında bilgi edinin.
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: security/data-protection/consumer-apis/dangerous-unprotect
ms.openlocfilehash: 5f13b53182f720ac8b58d90701561d381981308a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051102"
---
# <a name="unprotect-payloads-whose-keys-have-been-revoked-in-aspnet-core"></a><span data-ttu-id="318c2-103">ASP.NET Core anahtarları iptal edilen yüklerin korumasını kaldır</span><span class="sxs-lookup"><span data-stu-id="318c2-103">Unprotect payloads whose keys have been revoked in ASP.NET Core</span></span>

<a name="data-protection-consumer-apis-dangerous-unprotect"></a>

<span data-ttu-id="318c2-104">ASP.NET Core veri koruma API 'Leri öncelikle gizli yüklerin sınırsız kalıcılığı için tasarlanmamıştır.</span><span class="sxs-lookup"><span data-stu-id="318c2-104">The ASP.NET Core data protection APIs are not primarily intended for indefinite persistence of confidential payloads.</span></span> <span data-ttu-id="318c2-105">[WINDOWS CNG DPAPI](/windows/win32/seccng/cng-dpapi) ve [Azure Rights Management](/rights-management/) gibi diğer teknolojiler, sınırsız depolama senaryosuna daha uygundur ve bunlara karşılık olarak güçlü anahtar yönetim özelliklerine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="318c2-105">Other technologies like [Windows CNG DPAPI](/windows/win32/seccng/cng-dpapi) and [Azure Rights Management](/rights-management/) are more suited to the scenario of indefinite storage, and they have correspondingly strong key management capabilities.</span></span> <span data-ttu-id="318c2-106">Yani, bir geliştiricinin gizli verilerin uzun süreli korunması için ASP.NET Core veri koruma API 'Lerini kullanmasını engelleyen bir şey yoktur.</span><span class="sxs-lookup"><span data-stu-id="318c2-106">That said, there's nothing prohibiting a developer from using the ASP.NET Core data protection APIs for long-term protection of confidential data.</span></span> <span data-ttu-id="318c2-107">Anahtarlar hiçbir zaman anahtar halkadan kaldırılmaz, bu nedenle `IDataProtector.Unprotect` anahtarlar kullanılabilir ve geçerli olduğu sürece mevcut yükleri her zaman kurtarabilir.</span><span class="sxs-lookup"><span data-stu-id="318c2-107">Keys are never removed from the key ring, so `IDataProtector.Unprotect` can always recover existing payloads as long as the keys are available and valid.</span></span>

<span data-ttu-id="318c2-108">Ancak, geliştirici iptal edilmiş bir anahtarla korunan verilerin korumasını denediğinde, `IDataProtector.Unprotect` Bu durumda bir özel durum oluşturacak şekilde bir sorun ortaya çıkar.</span><span class="sxs-lookup"><span data-stu-id="318c2-108">However, an issue arises when the developer tries to unprotect data that has been protected with a revoked key, as `IDataProtector.Unprotect` will throw an exception in this case.</span></span> <span data-ttu-id="318c2-109">Bu yük türleri sistem tarafından kolayca yeniden oluşturulabilir ve en kötü site ziyaretçisinin tekrar oturum açması gerekebilecek için, kısa süreli veya geçici yüklere (kimlik doğrulama belirteçleri gibi) uygun olabilir.</span><span class="sxs-lookup"><span data-stu-id="318c2-109">This might be fine for short-lived or transient payloads (like authentication tokens), as these kinds of payloads can easily be recreated by the system, and at worst the site visitor might be required to log in again.</span></span> <span data-ttu-id="318c2-110">Ancak kalıcı yükleri için `Unprotect` throw 'un kabul edilemez veri kaybına neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="318c2-110">But for persisted payloads, having `Unprotect` throw could lead to unacceptable data loss.</span></span>

## <a name="ipersisteddataprotector"></a><span data-ttu-id="318c2-111">IPersistedDataProtector</span><span class="sxs-lookup"><span data-stu-id="318c2-111">IPersistedDataProtector</span></span>

<span data-ttu-id="318c2-112">İptal edilen anahtarların yanında bile yüklerin korumasız hale geçmesine izin verme senaryosunu desteklemek için, veri koruma sistemi bir `IPersistedDataProtector` tür içerir.</span><span class="sxs-lookup"><span data-stu-id="318c2-112">To support the scenario of allowing payloads to be unprotected even in the face of revoked keys, the data protection system contains an `IPersistedDataProtector` type.</span></span> <span data-ttu-id="318c2-113">Bir örneğini almak için `IPersistedDataProtector` , normal şekilde bir örneğini almanız ve ' ı `IDataProtector` ' a atamayı denemeniz yeterlidir `IDataProtector` `IPersistedDataProtector` .</span><span class="sxs-lookup"><span data-stu-id="318c2-113">To get an instance of `IPersistedDataProtector`, simply get an instance of `IDataProtector` in the normal fashion and try casting the `IDataProtector` to `IPersistedDataProtector`.</span></span>

> [!NOTE]
> <span data-ttu-id="318c2-114">Örneklerin hepsi `IDataProtector` öğesine atanamaz `IPersistedDataProtector` .</span><span class="sxs-lookup"><span data-stu-id="318c2-114">Not all `IDataProtector` instances can be cast to `IPersistedDataProtector`.</span></span> <span data-ttu-id="318c2-115">Geliştiriciler, geçersiz çalışma zamanı özel durumlarının oluşmasını önlemek için C# ' ı işleci veya benzer şekilde kullanmalıdır ve hata durumunu uygun şekilde işleyecek şekilde hazırlanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="318c2-115">Developers should use the C# as operator or similar to avoid runtime exceptions caused by invalid casts, and they should be prepared to handle the failure case appropriately.</span></span>

<span data-ttu-id="318c2-116">`IPersistedDataProtector` Aşağıdaki API yüzeyini gösterir:</span><span class="sxs-lookup"><span data-stu-id="318c2-116">`IPersistedDataProtector` exposes the following API surface:</span></span>

```csharp
DangerousUnprotect(byte[] protectedData, bool ignoreRevocationErrors,
     out bool requiresMigration, out bool wasRevoked) : byte[]
```

<span data-ttu-id="318c2-117">Bu API, korumalı yükü (bir bayt dizisi olarak) alır ve korumasız yükü döndürür.</span><span class="sxs-lookup"><span data-stu-id="318c2-117">This API takes the protected payload (as a byte array) and returns the unprotected payload.</span></span> <span data-ttu-id="318c2-118">Dize tabanlı aşırı yükleme yoktur.</span><span class="sxs-lookup"><span data-stu-id="318c2-118">There's no string-based overload.</span></span> <span data-ttu-id="318c2-119">İki out parametresi aşağıdaki gibidir.</span><span class="sxs-lookup"><span data-stu-id="318c2-119">The two out parameters are as follows.</span></span>

* <span data-ttu-id="318c2-120">`requiresMigration`: Bu yükü korumak için kullanılan anahtar artık etkin varsayılan anahtar değilse, true olarak ayarlanacak. Örneğin, bu yükü korumak için kullanılan anahtar eski ve bir anahtar toplama işlemi gerçekleştiğinden daha sonra çalışır.</span><span class="sxs-lookup"><span data-stu-id="318c2-120">`requiresMigration`: will be set to true if the key used to protect this payload is no longer the active default key, e.g., the key used to protect this payload is old and a key rolling operation has since taken place.</span></span> <span data-ttu-id="318c2-121">Çağıran, iş ihtiyaçlarına bağlı olarak yükü yeniden korumayı düşünmek isteyebilir.</span><span class="sxs-lookup"><span data-stu-id="318c2-121">The caller may wish to consider reprotecting the payload depending on their business needs.</span></span>

* <span data-ttu-id="318c2-122">`wasRevoked`: Bu yükü korumak için kullanılan anahtar iptal edildiyse, true olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="318c2-122">`wasRevoked`: will be set to true if the key used to protect this payload was revoked.</span></span>

>[!WARNING]
> <span data-ttu-id="318c2-123">Yöntemine geçiş yaparken çok dikkatli olun `ignoreRevocationErrors: true` `DangerousUnprotect` .</span><span class="sxs-lookup"><span data-stu-id="318c2-123">Exercise extreme caution when passing `ignoreRevocationErrors: true` to the `DangerousUnprotect` method.</span></span> <span data-ttu-id="318c2-124">Bu yöntemi çağırdıktan sonra `wasRevoked` değer true ise, bu yükü korumak için kullanılan anahtar iptal edilir ve yükün kimlik doğrulaması şüpheli olarak değerlendirilmelidir.</span><span class="sxs-lookup"><span data-stu-id="318c2-124">If after calling this method the `wasRevoked` value is true, then the key used to protect this payload was revoked, and the payload's authenticity should be treated as suspect.</span></span> <span data-ttu-id="318c2-125">Bu durumda, gerçek bir güveninin, örneğin güvenilmeyen bir Web istemcisi tarafından gönderilmesi yerine güvenli bir veritabanından geldiği durumlarda, yalnızca korumasız yük üzerinde çalışmaya devam edin.</span><span class="sxs-lookup"><span data-stu-id="318c2-125">In this case, only continue operating on the unprotected payload if you have some separate assurance that it's authentic, e.g. that it's coming from a secure database rather than being sent by an untrusted web client.</span></span>

[!code-csharp[](dangerous-unprotect/samples/dangerous-unprotect.cs)]
