---
title: ASP.NET Core kimliği doğrulanmış şifreleme ayrıntıları
author: rick-anderson
description: ASP.NET Core veri koruma kimliği doğrulanmış şifrelemenin uygulama ayrıntılarını öğrenin.
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
uid: security/data-protection/implementation/authenticated-encryption-details
ms.openlocfilehash: ab3be90d212c0cb7159a04694e7101c275157f00
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408636"
---
# <a name="authenticated-encryption-details-in-aspnet-core"></a><span data-ttu-id="a8ac3-103">ASP.NET Core kimliği doğrulanmış şifreleme ayrıntıları</span><span class="sxs-lookup"><span data-stu-id="a8ac3-103">Authenticated encryption details in ASP.NET Core</span></span>

<a name="data-protection-implementation-authenticated-encryption-details"></a>

<span data-ttu-id="a8ac3-104">Idataprotector. Protect çağrısı kimliği doğrulanmış şifreleme operasyonlardır.</span><span class="sxs-lookup"><span data-stu-id="a8ac3-104">Calls to IDataProtector.Protect are authenticated encryption operations.</span></span> <span data-ttu-id="a8ac3-105">Koru yöntemi hem gizlilik hem de özgünlük sağlar ve bu belirli ıdataprotector örneğini kök ıdataprotectionprovider 'dan türetmede kullanılan amaç zincirine bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="a8ac3-105">The Protect method offers both confidentiality and authenticity, and it's tied to the purpose chain that was used to derive this particular IDataProtector instance from its root IDataProtectionProvider.</span></span>

<span data-ttu-id="a8ac3-106">Idataprotector. Protect bir Byte [] düz metin parametresi alır ve biçimi aşağıda açıklanan bir Byte [] korumalı yük üretir.</span><span class="sxs-lookup"><span data-stu-id="a8ac3-106">IDataProtector.Protect takes a byte[] plaintext parameter and produces a byte[] protected payload, whose format is described below.</span></span> <span data-ttu-id="a8ac3-107">(Ayrıca bir dize düz metin parametresi alan ve bir dize korumalı yük döndüren bir genişletme yöntemi aşırı yüklemesi de vardır.</span><span class="sxs-lookup"><span data-stu-id="a8ac3-107">(There's also an extension method overload which takes a string plaintext parameter and returns a string protected payload.</span></span> <span data-ttu-id="a8ac3-108">Bu API kullanılıyorsa, korumalı yük biçimi yine de aşağıdaki yapıya sahip olur, ancak [base64url-Encoded](https://tools.ietf.org/html/rfc4648#section-5)olur.)</span><span class="sxs-lookup"><span data-stu-id="a8ac3-108">If this API is used the protected payload format will still have the below structure, but it will be [base64url-encoded](https://tools.ietf.org/html/rfc4648#section-5).)</span></span>

## <a name="protected-payload-format"></a><span data-ttu-id="a8ac3-109">Korumalı yük biçimi</span><span class="sxs-lookup"><span data-stu-id="a8ac3-109">Protected payload format</span></span>

<span data-ttu-id="a8ac3-110">Korumalı yük biçimi üç ana bileşenden oluşur:</span><span class="sxs-lookup"><span data-stu-id="a8ac3-110">The protected payload format consists of three primary components:</span></span>

* <span data-ttu-id="a8ac3-111">Veri koruma sisteminin sürümünü tanımlayan 32 bitlik bir sihirli üst bilgi.</span><span class="sxs-lookup"><span data-stu-id="a8ac3-111">A 32-bit magic header that identifies the version of the data protection system.</span></span>

* <span data-ttu-id="a8ac3-112">Bu belirli yükü korumak için kullanılan anahtarı tanımlayan 128 bitlik bir anahtar kimliği.</span><span class="sxs-lookup"><span data-stu-id="a8ac3-112">A 128-bit key id that identifies the key used to protect this particular payload.</span></span>

* <span data-ttu-id="a8ac3-113">Korunan yükün geri kalanı, [Bu anahtarla kapsüllenmiş Şifreleyici 'ye özgüdür](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation).</span><span class="sxs-lookup"><span data-stu-id="a8ac3-113">The remainder of the protected payload is [specific to the encryptor encapsulated by this key](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation).</span></span> <span data-ttu-id="a8ac3-114">Aşağıdaki örnekte, anahtar AES-256-CBC + HMACSHA256 Şifreleyici 'yi temsil eder ve yük daha sonra aşağıdaki gibi bölünür:</span><span class="sxs-lookup"><span data-stu-id="a8ac3-114">In the example below, the key represents an AES-256-CBC + HMACSHA256 encryptor, and the payload is further subdivided as follows:</span></span>
  * <span data-ttu-id="a8ac3-115">128 bitlik bir anahtar değiştiricisi.</span><span class="sxs-lookup"><span data-stu-id="a8ac3-115">A 128-bit key modifier.</span></span>
  * <span data-ttu-id="a8ac3-116">128 bitlik bir başlatma vektörü.</span><span class="sxs-lookup"><span data-stu-id="a8ac3-116">A 128-bit initialization vector.</span></span>
  * <span data-ttu-id="a8ac3-117">48 bayt AES-256-CBC çıkışı.</span><span class="sxs-lookup"><span data-stu-id="a8ac3-117">48 bytes of AES-256-CBC output.</span></span>
  * <span data-ttu-id="a8ac3-118">Bir HMACSHA256 kimlik doğrulama etiketi.</span><span class="sxs-lookup"><span data-stu-id="a8ac3-118">An HMACSHA256 authentication tag.</span></span>

<span data-ttu-id="a8ac3-119">Örnek korumalı yük aşağıda gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="a8ac3-119">A sample protected payload is illustrated below.</span></span>

```
09 F0 C9 F0 80 9C 81 0C 19 66 19 40 95 36 53 F8
AA FF EE 57 57 2F 40 4C 3F 7F CC 9D CC D9 32 3E
84 17 99 16 EC BA 1F 4A A1 18 45 1F 2D 13 7A 28
79 6B 86 9C F8 B7 84 F9 26 31 FC B1 86 0A F1 56
61 CF 14 58 D3 51 6F CF 36 50 85 82 08 2D 3F 73
5F B0 AD 9E 1A B2 AE 13 57 90 C8 F5 7C 95 4E 6A
8A AA 06 EF 43 CA 19 62 84 7C 11 B2 C8 71 9D AA
52 19 2E 5B 4C 1E 54 F0 55 BE 88 92 12 C1 4B 5E
52 C9 74 A0
```

<span data-ttu-id="a8ac3-120">İlk 32 bitin üzerindeki yük biçiminden veya 4 bayt sürümü tanımlayan sihirli üst bilgi (09 F0 C9 F0)</span><span class="sxs-lookup"><span data-stu-id="a8ac3-120">From the payload format above the first 32 bits, or 4 bytes are the magic header identifying the version (09 F0 C9 F0)</span></span>

<span data-ttu-id="a8ac3-121">Sonraki 128 bit veya 16 bayt anahtar tanımlayıcısıdır (80 9C 81 0C 19 66 19 40 95 36 53 F8 AA FF EE 57)</span><span class="sxs-lookup"><span data-stu-id="a8ac3-121">The next 128 bits, or 16 bytes is the key identifier (80 9C 81 0C 19 66 19 40 95 36 53 F8 AA FF EE 57)</span></span>

<span data-ttu-id="a8ac3-122">Kalan, yükü içerir ve kullanılan biçime özeldir.</span><span class="sxs-lookup"><span data-stu-id="a8ac3-122">The remainder contains the payload and is specific to the format used.</span></span>

> [!WARNING]
> <span data-ttu-id="a8ac3-123">Belirli bir anahtara korunan tüm yüklerin aynı 20 baytlık (sihirli değer, anahtar kimliği) üst bilgisi ile başlaması gerekir.</span><span class="sxs-lookup"><span data-stu-id="a8ac3-123">All payloads protected to a given key will begin with the same 20-byte (magic value, key id) header.</span></span> <span data-ttu-id="a8ac3-124">Yöneticiler bu olguyu, bir yükün ne zaman oluşturulduğunu yaklaşık olarak tanılama amacıyla kullanabilir.</span><span class="sxs-lookup"><span data-stu-id="a8ac3-124">Administrators can use this fact for diagnostic purposes to approximate when a payload was generated.</span></span> <span data-ttu-id="a8ac3-125">Örneğin, yukarıdaki yük {0c819c80-6619-4019-9536-53f8aaffee57} anahtarına karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="a8ac3-125">For example, the payload above corresponds to key {0c819c80-6619-4019-9536-53f8aaffee57}.</span></span> <span data-ttu-id="a8ac3-126">Anahtar deposunu denetledikten sonra, bu anahtarın etkinleştirme tarihinin 2015-01-01 olduğunu ve sona erme tarihi 2015-03-01 olduğunu fark ederseniz, bu pencerede yükün (üzerinde oynanmadıysa) Bu pencerede oluşturulduğunu varsaymak mantıklı olur.</span><span class="sxs-lookup"><span data-stu-id="a8ac3-126">If after checking the key repository you find that this specific key's activation date was 2015-01-01 and its expiration date was 2015-03-01, then it's reasonable to assume that the payload (if not tampered with) was generated within that window, give or take a small fudge factor on either side.</span></span>
