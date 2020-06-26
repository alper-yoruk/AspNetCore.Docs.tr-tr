---
title: ASP.NET Core bağlam üst bilgileri
author: rick-anderson
description: ASP.NET Core veri koruma bağlamı üst bilgilerinin uygulama ayrıntılarını öğrenin.
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
uid: security/data-protection/implementation/context-headers
ms.openlocfilehash: 078392662281253b8b6cfc0d50fddc8d66482b63
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85406907"
---
# <a name="context-headers-in-aspnet-core"></a><span data-ttu-id="c61cb-103">ASP.NET Core bağlam üst bilgileri</span><span class="sxs-lookup"><span data-stu-id="c61cb-103">Context headers in ASP.NET Core</span></span>

<a name="data-protection-implementation-context-headers"></a>

## <a name="background-and-theory"></a><span data-ttu-id="c61cb-104">Arka plan ve teorik</span><span class="sxs-lookup"><span data-stu-id="c61cb-104">Background and theory</span></span>

<span data-ttu-id="c61cb-105">Veri koruma sisteminde, "anahtar" kimliği doğrulanmış şifreleme hizmetleri sağlayabilen bir nesne anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="c61cb-105">In the data protection system, a "key" means an object that can provide authenticated encryption services.</span></span> <span data-ttu-id="c61cb-106">Her anahtar benzersiz bir kimlik (GUID) tarafından tanımlanır ve IT algoritmik Information ve entropıc malzemesiyle birlikte bulunur.</span><span class="sxs-lookup"><span data-stu-id="c61cb-106">Each key is identified by a unique id (a GUID), and it carries with it algorithmic information and entropic material.</span></span> <span data-ttu-id="c61cb-107">Her bir anahtarın benzersiz entropi taşıması ve sistem bunu zorunlu kılamaz ve anahtar halkasının mevcut bir anahtarın algoritmik bilgilerini değiştirerek anahtar halkasını el ile değiştirebilen geliştiriciler için de hesap yapmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="c61cb-107">It's intended that each key carry unique entropy, but the system cannot enforce that, and we also need to account for developers who might change the key ring manually by modifying the algorithmic information of an existing key in the key ring.</span></span> <span data-ttu-id="c61cb-108">Bu durumlarda güvenlik gereksinimlerimize ulaşmak için, veri koruma sisteminin bir [şifreleme çevikliği](https://www.microsoft.com/en-us/research/publication/cryptographic-agility-and-its-relation-to-circular-encryption/)vardır. Bu, birden çok şifreleme algoritmasında tek bir entropıc değeri güvenli bir şekilde kullanılmasına olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="c61cb-108">To achieve our security requirements given these cases the data protection system has a concept of [cryptographic agility](https://www.microsoft.com/en-us/research/publication/cryptographic-agility-and-its-relation-to-circular-encryption/), which allows securely using a single entropic value across multiple cryptographic algorithms.</span></span>

<span data-ttu-id="c61cb-109">Şifreleme çevikliği destekleyen çoğu sistem, yük içindeki algoritmayla ilgili bazı tanımlayıcı bilgileri de ekleyerek bunu destekler.</span><span class="sxs-lookup"><span data-stu-id="c61cb-109">Most systems which support cryptographic agility do so by including some identifying information about the algorithm inside the payload.</span></span> <span data-ttu-id="c61cb-110">Algoritmanın OID 'si genellikle bunun için iyi bir adaydır.</span><span class="sxs-lookup"><span data-stu-id="c61cb-110">The algorithm's OID is generally a good candidate for this.</span></span> <span data-ttu-id="c61cb-111">Ancak, çalıştırdığımız bir sorun aynı algoritmayı belirtmenizin birden çok yolu vardır: "AES" (CNG) ve yönetilen AES, AES, AesCryptoServiceProvider, AesCng ve Rijndadelmanaged (verilen belirli parametreler) sınıflarının hepsi aslında aynı şeydir ve bunların tümünün doğru OID 'ye eşlenmesinin devam etmemiz gerekir.</span><span class="sxs-lookup"><span data-stu-id="c61cb-111">However, one problem that we ran into is that there are multiple ways to specify the same algorithm: "AES" (CNG) and the managed Aes, AesManaged, AesCryptoServiceProvider, AesCng, and RijndaelManaged (given specific parameters) classes are all actually the same thing, and we'd need to maintain a mapping of all of these to the correct OID.</span></span> <span data-ttu-id="c61cb-112">Bir geliştirici özel bir algoritma (ya da başka bir AES uygulama) sağlamak istiyorlarsa, onun OID 'sini söylemeleri gerekir.</span><span class="sxs-lookup"><span data-stu-id="c61cb-112">If a developer wanted to provide a custom algorithm (or even another implementation of AES!), they'd have to tell us its OID.</span></span> <span data-ttu-id="c61cb-113">Bu ek kayıt adımı, sistem yapılandırmasını özellikle sorunsuz hale getirir.</span><span class="sxs-lookup"><span data-stu-id="c61cb-113">This extra registration step makes system configuration particularly painful.</span></span>

<span data-ttu-id="c61cb-114">Geri adımla, sorunu yanlış yönden yaklaşdığımızda kararıyoruz.</span><span class="sxs-lookup"><span data-stu-id="c61cb-114">Stepping back, we decided that we were approaching the problem from the wrong direction.</span></span> <span data-ttu-id="c61cb-115">Bir OID, algoritmanın ne olduğunu söyler, ancak bunu gerçekten ilgilentik.</span><span class="sxs-lookup"><span data-stu-id="c61cb-115">An OID tells you what the algorithm is, but we don't actually care about this.</span></span> <span data-ttu-id="c61cb-116">İki farklı algoritmalarda tek bir entropıc değeri güvenli bir şekilde kullanılması gerekiyorsa, algoritmaların gerçekten ne olduğunu bilmemizi gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="c61cb-116">If we need to use a single entropic value securely in two different algorithms, it's not necessary for us to know what the algorithms actually are.</span></span> <span data-ttu-id="c61cb-117">Ne kadar önemli olduğumuz.</span><span class="sxs-lookup"><span data-stu-id="c61cb-117">What we actually care about is how they behave.</span></span> <span data-ttu-id="c61cb-118">Her türlü simetrik blok şifre algoritması da güçlü bir pseudportaıdom permütasyon (PRP): girişleri düzeltin (anahtar, zincir oluşturma modu, IV, düz metin) ve şifreli çıktı, büyük olasılıkla aynı girişler verilen diğer herhangi bir simetrik blok şifre algoritmasından farklı olacaktır.</span><span class="sxs-lookup"><span data-stu-id="c61cb-118">Any decent symmetric block cipher algorithm is also a strong pseudorandom permutation (PRP): fix the inputs (key, chaining mode, IV, plaintext) and the ciphertext output will with overwhelming probability be distinct from any other symmetric block cipher algorithm given the same inputs.</span></span> <span data-ttu-id="c61cb-119">Benzer şekilde, her türlü anahtarlı karma işlevi de güçlü bir pseudportaıdom işlevidir (PRF) ve sabit bir giriş kümesi, çıkış overwhelmingly başka bir anahtarlı karma işlevden farklı olacaktır.</span><span class="sxs-lookup"><span data-stu-id="c61cb-119">Similarly, any decent keyed hash function is also a strong pseudorandom function (PRF), and given a fixed input set its output will overwhelmingly be distinct from any other keyed hash function.</span></span>

<span data-ttu-id="c61cb-120">Bu güçlü PRPs ve PRFs kavramını bir bağlam üst bilgisi oluşturmak için kullanırız.</span><span class="sxs-lookup"><span data-stu-id="c61cb-120">We use this concept of strong PRPs and PRFs to build up a context header.</span></span> <span data-ttu-id="c61cb-121">Bu bağlam üstbilgisi temel olarak, belirli bir işlem için kullanımdaki algoritmaların üzerinde kararlı bir parmak izi görevi görür ve veri koruma sistemi için gereken şifreleme çevikliğini sağlar.</span><span class="sxs-lookup"><span data-stu-id="c61cb-121">This context header essentially acts as a stable thumbprint over the algorithms in use for any given operation, and it provides the cryptographic agility needed by the data protection system.</span></span> <span data-ttu-id="c61cb-122">Bu üst bilgi tekrarlanabilir ve daha sonra [alt anahtar türetme işleminin](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation)bir parçası olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="c61cb-122">This header is reproducible and is used later as part of the [subkey derivation process](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation).</span></span> <span data-ttu-id="c61cb-123">Temel algoritmaların işlem modlarına bağlı olarak bağlam üst bilgisini oluşturmanın iki farklı yolu vardır.</span><span class="sxs-lookup"><span data-stu-id="c61cb-123">There are two different ways to build the context header depending on the modes of operation of the underlying algorithms.</span></span>

## <a name="cbc-mode-encryption--hmac-authentication"></a><span data-ttu-id="c61cb-124">CBC modu şifreleme + HMAC kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="c61cb-124">CBC-mode encryption + HMAC authentication</span></span>

<a name="data-protection-implementation-context-headers-cbc-components"></a>

<span data-ttu-id="c61cb-125">Bağlam üst bilgisi aşağıdaki bileşenlerden oluşur:</span><span class="sxs-lookup"><span data-stu-id="c61cb-125">The context header consists of the following components:</span></span>

* <span data-ttu-id="c61cb-126">[16 bit] Bir işaret olan 00 00 değeri, "CBC şifreleme + HMAC kimlik doğrulaması" anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="c61cb-126">[16 bits] The value 00 00, which is a marker meaning "CBC encryption + HMAC authentication".</span></span>

* <span data-ttu-id="c61cb-127">[32 bit] Simetrik blok şifreleme algoritmasının anahtar uzunluğu (bayt cinsinden, Big-endian).</span><span class="sxs-lookup"><span data-stu-id="c61cb-127">[32 bits] The key length (in bytes, big-endian) of the symmetric block cipher algorithm.</span></span>

* <span data-ttu-id="c61cb-128">[32 bit] Simetrik blok şifreleme algoritmasının blok boyutu (bayt cinsinden, Big-endian).</span><span class="sxs-lookup"><span data-stu-id="c61cb-128">[32 bits] The block size (in bytes, big-endian) of the symmetric block cipher algorithm.</span></span>

* <span data-ttu-id="c61cb-129">[32 bit] HMAC algoritmasının anahtar uzunluğu (bayt, Big-endian).</span><span class="sxs-lookup"><span data-stu-id="c61cb-129">[32 bits] The key length (in bytes, big-endian) of the HMAC algorithm.</span></span> <span data-ttu-id="c61cb-130">(Şu anda anahtar boyutu her zaman Özet boyutuyla eşleşir.)</span><span class="sxs-lookup"><span data-stu-id="c61cb-130">(Currently the key size always matches the digest size.)</span></span>

* <span data-ttu-id="c61cb-131">[32 bit] HMAC algoritmasının Özet boyutu (bayt, Big-endian).</span><span class="sxs-lookup"><span data-stu-id="c61cb-131">[32 bits] The digest size (in bytes, big-endian) of the HMAC algorithm.</span></span>

* <span data-ttu-id="c61cb-132">K_E, IV, ""), simetrik blok şifreleme algoritmasının, boş bir dize girişi ve IV 'nin tamamen sıfır olan bir vektör olduğu anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="c61cb-132">EncCBC(K_E, IV, ""), which is the output of the symmetric block cipher algorithm given an empty string input and where IV is an all-zero vector.</span></span> <span data-ttu-id="c61cb-133">K_E oluşturulması aşağıda açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="c61cb-133">The construction of K_E is described below.</span></span>

* <span data-ttu-id="c61cb-134">MAC (K_H, ""), boş bir dize girişi verilen HMAC algoritmasının çıktısı.</span><span class="sxs-lookup"><span data-stu-id="c61cb-134">MAC(K_H, ""), which is the output of the HMAC algorithm given an empty string input.</span></span> <span data-ttu-id="c61cb-135">K_H oluşturulması aşağıda açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="c61cb-135">The construction of K_H is described below.</span></span>

<span data-ttu-id="c61cb-136">İdeal olarak, K_E ve K_H için tümüyle sıfır vektörler geçirebiliriz.</span><span class="sxs-lookup"><span data-stu-id="c61cb-136">Ideally, we could pass all-zero vectors for K_E and K_H.</span></span> <span data-ttu-id="c61cb-137">Bununla birlikte, temeldeki algoritmanın herhangi bir işlem gerçekleştirmeden önce zayıf anahtarların varlığını denetliyoruz (özellikle DES ve 3DES), hepsi sıfır vektörü gibi basit veya yinelenebilir bir model kullanarak,</span><span class="sxs-lookup"><span data-stu-id="c61cb-137">However, we want to avoid the situation where the underlying algorithm checks for the existence of weak keys before performing any operations (notably DES and 3DES), which precludes using a simple or repeatable pattern like an all-zero vector.</span></span>

<span data-ttu-id="c61cb-138">Bunun yerine, temel alınan PRF olarak (bkz. [NıST SP800-108](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-108.pdf), Sec. 5,1), sıfır uzunluklu bir anahtar, etiket ve bağlam ve HMACSHA512 ile.</span><span class="sxs-lookup"><span data-stu-id="c61cb-138">Instead, we use the NIST SP800-108 KDF in Counter Mode (see [NIST SP800-108](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-108.pdf), Sec. 5.1) with a zero-length key, label, and context and HMACSHA512 as the underlying PRF.</span></span> <span data-ttu-id="c61cb-139">Türettik | K_E | + | K_H | çıkış baytları, ardından K_E ve K_H kendilerini parçalara ayırın.</span><span class="sxs-lookup"><span data-stu-id="c61cb-139">We derive | K_E | + | K_H | bytes of output, then decompose the result into K_E and K_H themselves.</span></span> <span data-ttu-id="c61cb-140">Matematik olarak bu, aşağıdaki gibi gösterilir.</span><span class="sxs-lookup"><span data-stu-id="c61cb-140">Mathematically, this is represented as follows.</span></span>

<span data-ttu-id="c61cb-141">(K_E | | K_H) = SP800_108_CTR (prf = HMACSHA512, Key = "", Label = "", Context = "")</span><span class="sxs-lookup"><span data-stu-id="c61cb-141">( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")</span></span>

### <a name="example-aes-192-cbc--hmacsha256"></a><span data-ttu-id="c61cb-142">Örnek: AES-192-CBC + HMACSHA256</span><span class="sxs-lookup"><span data-stu-id="c61cb-142">Example: AES-192-CBC + HMACSHA256</span></span>

<span data-ttu-id="c61cb-143">Örnek olarak, simetrik blok şifreleme algoritmasının AES-192-CBC ve doğrulama algoritmasının HMACSHA256 olduğu durumu göz önünde bulundurun.</span><span class="sxs-lookup"><span data-stu-id="c61cb-143">As an example, consider the case where the symmetric block cipher algorithm is AES-192-CBC and the validation algorithm is HMACSHA256.</span></span> <span data-ttu-id="c61cb-144">Aşağıdaki adımları kullanarak sistem bağlam üst bilgisini oluşturur.</span><span class="sxs-lookup"><span data-stu-id="c61cb-144">The system would generate the context header using the following steps.</span></span>

<span data-ttu-id="c61cb-145">İlk, Let (K_E | | K_H) = SP800_108_CTR (prf = HMACSHA512, Key = "", Label = "", Context = ""), burada | K_E | = 192 bit ve | K_H | = 256 bit Belirtilen algoritmalara göre.</span><span class="sxs-lookup"><span data-stu-id="c61cb-145">First, let ( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = ""), where | K_E | = 192 bits and | K_H | = 256 bits per the specified algorithms.</span></span> <span data-ttu-id="c61cb-146">Bu K_E = 5BB6 ' ya yol açar. 21DD ve K_H = A04A.. Aşağıdaki örnekte 00A9:</span><span class="sxs-lookup"><span data-stu-id="c61cb-146">This leads to K_E = 5BB6..21DD and K_H = A04A..00A9 in the example below:</span></span>

```
5B B6 C9 83 13 78 22 1D 8E 10 73 CA CF 65 8E B0
61 62 42 71 CB 83 21 DD A0 4A 05 00 5B AB C0 A2
49 6F A5 61 E3 E2 49 87 AA 63 55 CD 74 0A DA C4
B7 92 3D BF 59 90 00 A9
```

<span data-ttu-id="c61cb-147">Daha sonra, Enc_CBC (K_E, IV, "") AES-192-CBC verilen IV = 0 \* ve K_E yukarıdaki gibi hesaplama.</span><span class="sxs-lookup"><span data-stu-id="c61cb-147">Next, compute Enc_CBC (K_E, IV, "") for AES-192-CBC given IV = 0\* and K_E as above.</span></span>

<span data-ttu-id="c61cb-148">Sonuç: = F474B1872B3B53E4721DE19C0841DB6F</span><span class="sxs-lookup"><span data-stu-id="c61cb-148">result := F474B1872B3B53E4721DE19C0841DB6F</span></span>

<span data-ttu-id="c61cb-149">Daha sonra, HMACSHA256 verilen K_H için işlem MAC (K_H, "").</span><span class="sxs-lookup"><span data-stu-id="c61cb-149">Next, compute MAC(K_H, "") for HMACSHA256 given K_H as above.</span></span>

<span data-ttu-id="c61cb-150">Sonuç: = D4791184B996092EE1202F36E8608FA8FBD98ABDFF5402F264B1D7211536220C</span><span class="sxs-lookup"><span data-stu-id="c61cb-150">result := D4791184B996092EE1202F36E8608FA8FBD98ABDFF5402F264B1D7211536220C</span></span>

<span data-ttu-id="c61cb-151">Bu, aşağıdaki tam bağlam üstbilgisini üretir:</span><span class="sxs-lookup"><span data-stu-id="c61cb-151">This produces the full context header below:</span></span>

```
00 00 00 00 00 18 00 00 00 10 00 00 00 20 00 00
00 20 F4 74 B1 87 2B 3B 53 E4 72 1D E1 9C 08 41
DB 6F D4 79 11 84 B9 96 09 2E E1 20 2F 36 E8 60
8F A8 FB D9 8A BD FF 54 02 F2 64 B1 D7 21 15 36
22 0C
```

<span data-ttu-id="c61cb-152">Bu bağlam üst bilgisi, kimliği doğrulanmış şifreleme algoritması çiftinin parmak izi (AES-192-CBC şifrelemesi + HMACSHA256 Validation).</span><span class="sxs-lookup"><span data-stu-id="c61cb-152">This context header is the thumbprint of the authenticated encryption algorithm pair (AES-192-CBC encryption + HMACSHA256 validation).</span></span> <span data-ttu-id="c61cb-153">[Yukarıda](xref:security/data-protection/implementation/context-headers#data-protection-implementation-context-headers-cbc-components) açıklanan bileşenler şunlardır:</span><span class="sxs-lookup"><span data-stu-id="c61cb-153">The components, as described [above](xref:security/data-protection/implementation/context-headers#data-protection-implementation-context-headers-cbc-components) are:</span></span>

* <span data-ttu-id="c61cb-154">işaretleyici (00 00)</span><span class="sxs-lookup"><span data-stu-id="c61cb-154">the marker (00 00)</span></span>

* <span data-ttu-id="c61cb-155">blok şifresi anahtar uzunluğu (00 00 00 18)</span><span class="sxs-lookup"><span data-stu-id="c61cb-155">the block cipher key length (00 00 00 18)</span></span>

* <span data-ttu-id="c61cb-156">blok şifre blok boyutu (00 00 00 10)</span><span class="sxs-lookup"><span data-stu-id="c61cb-156">the block cipher block size (00 00 00 10)</span></span>

* <span data-ttu-id="c61cb-157">HMAC anahtar uzunluğu (00 00 00 20)</span><span class="sxs-lookup"><span data-stu-id="c61cb-157">the HMAC key length (00 00 00 20)</span></span>

* <span data-ttu-id="c61cb-158">HMAC Özet boyutu (00 00 00 20)</span><span class="sxs-lookup"><span data-stu-id="c61cb-158">the HMAC digest size (00 00 00 20)</span></span>

* <span data-ttu-id="c61cb-159">blok şifre PRP çıkışı (F4 74-DB 6F) ve</span><span class="sxs-lookup"><span data-stu-id="c61cb-159">the block cipher PRP output (F4 74 - DB 6F) and</span></span>

* <span data-ttu-id="c61cb-160">HMAC PRF çıkışı (D4 79-End).</span><span class="sxs-lookup"><span data-stu-id="c61cb-160">the HMAC PRF output (D4 79 - end).</span></span>

> [!NOTE]
> <span data-ttu-id="c61cb-161">CBC modu şifreleme + HMAC kimlik doğrulama bağlamı üst bilgisi, algoritma uygulamalarının Windows CNG tarafından mı yoksa yönetilen SymmetricAlgorithm ve KeyedHashAlgorithm türleriyle mi sağlandığına bakılmaksızın aynı şekilde oluşturulmuştur.</span><span class="sxs-lookup"><span data-stu-id="c61cb-161">The CBC-mode encryption + HMAC authentication context header is built the same way regardless of whether the algorithms implementations are provided by Windows CNG or by managed SymmetricAlgorithm and KeyedHashAlgorithm types.</span></span> <span data-ttu-id="c61cb-162">Bu, farklı işletim sistemlerinde çalışan uygulamaların, algoritmaların uygulamaları arasında farklı olmasına rağmen aynı bağlam üst bilgisini güvenilir bir şekilde üretmesine olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="c61cb-162">This allows applications running on different operating systems to reliably produce the same context header even though the implementations of the algorithms differ between OSes.</span></span> <span data-ttu-id="c61cb-163">(Uygulamada, KeyedHashAlgorithm 'nin doğru HMAC olması gerekmez.</span><span class="sxs-lookup"><span data-stu-id="c61cb-163">(In practice, the KeyedHashAlgorithm doesn't have to be a proper HMAC.</span></span> <span data-ttu-id="c61cb-164">Bu, herhangi bir anahtarlı karma algoritma türü olabilir.)</span><span class="sxs-lookup"><span data-stu-id="c61cb-164">It can be any keyed hash algorithm type.)</span></span>

### <a name="example-3des-192-cbc--hmacsha1"></a><span data-ttu-id="c61cb-165">Örnek: 3DES-192-CBC + HMACSHA1</span><span class="sxs-lookup"><span data-stu-id="c61cb-165">Example: 3DES-192-CBC + HMACSHA1</span></span>

<span data-ttu-id="c61cb-166">İlk, Let (K_E | | K_H) = SP800_108_CTR (prf = HMACSHA512, Key = "", Label = "", Context = ""), burada | K_E | = 192 bit ve | K_H | = 160 bit Belirtilen algoritmalara göre.</span><span class="sxs-lookup"><span data-stu-id="c61cb-166">First, let ( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = ""), where | K_E | = 192 bits and | K_H | = 160 bits per the specified algorithms.</span></span> <span data-ttu-id="c61cb-167">Bu K_E = A219... E2BB ve K_H = DC4A.. Aşağıdaki örnekte B464:</span><span class="sxs-lookup"><span data-stu-id="c61cb-167">This leads to K_E = A219..E2BB and K_H = DC4A..B464 in the example below:</span></span>

```
A2 19 60 2F 83 A9 13 EA B0 61 3A 39 B8 A6 7E 22
61 D9 F8 6C 10 51 E2 BB DC 4A 00 D7 03 A2 48 3E
D1 F7 5A 34 EB 28 3E D7 D4 67 B4 64
```

<span data-ttu-id="c61cb-168">Sonra, 3DES-192-CBC için işlem Enc_CBC (K_E, IV, "") yukarıdaki gibi IV = 0 \* ve K_E.</span><span class="sxs-lookup"><span data-stu-id="c61cb-168">Next, compute Enc_CBC (K_E, IV, "") for 3DES-192-CBC given IV = 0\* and K_E as above.</span></span>

<span data-ttu-id="c61cb-169">Sonuç: = ABB100F81E53E10E</span><span class="sxs-lookup"><span data-stu-id="c61cb-169">result := ABB100F81E53E10E</span></span>

<span data-ttu-id="c61cb-170">Daha sonra, HMACSHA1 verilen K_H için işlem MAC (K_H, "").</span><span class="sxs-lookup"><span data-stu-id="c61cb-170">Next, compute MAC(K_H, "") for HMACSHA1 given K_H as above.</span></span>

<span data-ttu-id="c61cb-171">Sonuç: = 76EB189B35CF03461DDF877CD9F4B1B4D63A7555</span><span class="sxs-lookup"><span data-stu-id="c61cb-171">result := 76EB189B35CF03461DDF877CD9F4B1B4D63A7555</span></span>

<span data-ttu-id="c61cb-172">Bu, aşağıda gösterildiği gibi, kimliği doğrulanmış şifreleme algoritması çiftinin (3DES-192-CBC şifrelemesi + HMACSHA1 doğrulaması) bir parmak izi olan tam bağlam üstbilgisini üretir</span><span class="sxs-lookup"><span data-stu-id="c61cb-172">This produces the full context header which is a thumbprint of the authenticated encryption algorithm pair (3DES-192-CBC encryption + HMACSHA1 validation), shown below:</span></span>

```
00 00 00 00 00 18 00 00 00 08 00 00 00 14 00 00
00 14 AB B1 00 F8 1E 53 E1 0E 76 EB 18 9B 35 CF
03 46 1D DF 87 7C D9 F4 B1 B4 D6 3A 75 55
```

<span data-ttu-id="c61cb-173">Bileşenler aşağıdaki gibi kesilir:</span><span class="sxs-lookup"><span data-stu-id="c61cb-173">The components break down as follows:</span></span>

* <span data-ttu-id="c61cb-174">işaretleyici (00 00)</span><span class="sxs-lookup"><span data-stu-id="c61cb-174">the marker (00 00)</span></span>

* <span data-ttu-id="c61cb-175">blok şifresi anahtar uzunluğu (00 00 00 18)</span><span class="sxs-lookup"><span data-stu-id="c61cb-175">the block cipher key length (00 00 00 18)</span></span>

* <span data-ttu-id="c61cb-176">blok şifre blok boyutu (00 00 00 08)</span><span class="sxs-lookup"><span data-stu-id="c61cb-176">the block cipher block size (00 00 00 08)</span></span>

* <span data-ttu-id="c61cb-177">HMAC anahtar uzunluğu (00 00 00 14)</span><span class="sxs-lookup"><span data-stu-id="c61cb-177">the HMAC key length (00 00 00 14)</span></span>

* <span data-ttu-id="c61cb-178">HMAC Özet boyutu (00 00 00 14)</span><span class="sxs-lookup"><span data-stu-id="c61cb-178">the HMAC digest size (00 00 00 14)</span></span>

* <span data-ttu-id="c61cb-179">blok şifre PRP çıkışı (AB B1-E1 0E) ve</span><span class="sxs-lookup"><span data-stu-id="c61cb-179">the block cipher PRP output (AB B1 - E1 0E) and</span></span>

* <span data-ttu-id="c61cb-180">HMAC PRF çıkışı (76 EB-End).</span><span class="sxs-lookup"><span data-stu-id="c61cb-180">the HMAC PRF output (76 EB - end).</span></span>

## <a name="galoiscounter-mode-encryption--authentication"></a><span data-ttu-id="c61cb-181">Galoa/sayaç modu şifreleme + kimlik doğrulama</span><span class="sxs-lookup"><span data-stu-id="c61cb-181">Galois/Counter Mode encryption + authentication</span></span>

<span data-ttu-id="c61cb-182">Bağlam üst bilgisi aşağıdaki bileşenlerden oluşur:</span><span class="sxs-lookup"><span data-stu-id="c61cb-182">The context header consists of the following components:</span></span>

* <span data-ttu-id="c61cb-183">[16 bit] Bir işaret olan 00 01 değeri, "GCM şifreleme + kimlik doğrulama" anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="c61cb-183">[16 bits] The value 00 01, which is a marker meaning "GCM encryption + authentication".</span></span>

* <span data-ttu-id="c61cb-184">[32 bit] Simetrik blok şifreleme algoritmasının anahtar uzunluğu (bayt cinsinden, Big-endian).</span><span class="sxs-lookup"><span data-stu-id="c61cb-184">[32 bits] The key length (in bytes, big-endian) of the symmetric block cipher algorithm.</span></span>

* <span data-ttu-id="c61cb-185">[32 bit] Kimliği doğrulanmış şifreleme işlemleri sırasında kullanılan nonce boyutu (bayt cinsinden, Big-endian).</span><span class="sxs-lookup"><span data-stu-id="c61cb-185">[32 bits] The nonce size (in bytes, big-endian) used during authenticated encryption operations.</span></span> <span data-ttu-id="c61cb-186">(Sistemimiz için bu, nonce boyut = 96 bitde düzeltilir.)</span><span class="sxs-lookup"><span data-stu-id="c61cb-186">(For our system, this is fixed at nonce size = 96 bits.)</span></span>

* <span data-ttu-id="c61cb-187">[32 bit] Simetrik blok şifreleme algoritmasının blok boyutu (bayt cinsinden, Big-endian).</span><span class="sxs-lookup"><span data-stu-id="c61cb-187">[32 bits] The block size (in bytes, big-endian) of the symmetric block cipher algorithm.</span></span> <span data-ttu-id="c61cb-188">(GCM için, bu, blok boyutu = 128 bitde düzeltilir.)</span><span class="sxs-lookup"><span data-stu-id="c61cb-188">(For GCM, this is fixed at block size = 128 bits.)</span></span>

* <span data-ttu-id="c61cb-189">[32 bit] Kimliği doğrulanmış şifreleme işlevi tarafından üretilen kimlik doğrulama etiketi boyutu (bayt, Big-endian).</span><span class="sxs-lookup"><span data-stu-id="c61cb-189">[32 bits] The authentication tag size (in bytes, big-endian) produced by the authenticated encryption function.</span></span> <span data-ttu-id="c61cb-190">(Sistemimizde bu, etiket boyutu = 128 bitde düzeltilir.)</span><span class="sxs-lookup"><span data-stu-id="c61cb-190">(For our system, this is fixed at tag size = 128 bits.)</span></span>

* <span data-ttu-id="c61cb-191">[128 bit] Enc_GCM (K_E, nonce, "") etiketi, simetrik blok şifreleme algoritmasının boş bir dize girişi verdiği ve nonce 'in 96 bitlik bir tamamen sıfır vektörü olduğu yerdir.</span><span class="sxs-lookup"><span data-stu-id="c61cb-191">[128 bits] The tag of Enc_GCM (K_E, nonce, ""), which is the output of the symmetric block cipher algorithm given an empty string input and where nonce is a 96-bit all-zero vector.</span></span>

<span data-ttu-id="c61cb-192">K_E, CBC şifreleme + HMAC kimlik doğrulama senaryosunda aynı mekanizmayı kullanarak türetilir.</span><span class="sxs-lookup"><span data-stu-id="c61cb-192">K_E is derived using the same mechanism as in the CBC encryption + HMAC authentication scenario.</span></span> <span data-ttu-id="c61cb-193">Bununla birlikte, burada Play 'de K_H olmadığından, aslında şunları yapmanız gerekir | K_H | = 0 ve algoritma aşağıdaki biçime daraltır.</span><span class="sxs-lookup"><span data-stu-id="c61cb-193">However, since there's no K_H in play here, we essentially have | K_H | = 0, and the algorithm collapses to the below form.</span></span>

<span data-ttu-id="c61cb-194">K_E = SP800_108_CTR (prf = HMACSHA512, Key = "", Label = "", Context = "")</span><span class="sxs-lookup"><span data-stu-id="c61cb-194">K_E = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")</span></span>

### <a name="example-aes-256-gcm"></a><span data-ttu-id="c61cb-195">Örnek: AES-256-GCM</span><span class="sxs-lookup"><span data-stu-id="c61cb-195">Example: AES-256-GCM</span></span>

<span data-ttu-id="c61cb-196">İlk olarak, izin K_E = SP800_108_CTR (prf = HMACSHA512, Key = "", Label = "", Context = ""), burada | K_E | = 256 bit.</span><span class="sxs-lookup"><span data-stu-id="c61cb-196">First, let K_E = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = ""), where | K_E | = 256 bits.</span></span>

<span data-ttu-id="c61cb-197">K_E: = 22BC6F1B171C08C4AE2F27444AF8FC8B3087A90006CAEA91FDCFB47C1B8733B8</span><span class="sxs-lookup"><span data-stu-id="c61cb-197">K_E := 22BC6F1B171C08C4AE2F27444AF8FC8B3087A90006CAEA91FDCFB47C1B8733B8</span></span>

<span data-ttu-id="c61cb-198">Daha sonra, Enc_GCM (K_E, nonce, "") kimlik doğrulama etiketini AES-256-GCM verilen nonce = 096 ve yukarıdaki gibi K_E için hesaplayın.</span><span class="sxs-lookup"><span data-stu-id="c61cb-198">Next, compute the authentication tag of Enc_GCM (K_E, nonce, "") for AES-256-GCM given nonce = 096 and K_E as above.</span></span>

<span data-ttu-id="c61cb-199">Sonuç: = E7DCCE66DF855A323A6BB7BD7A59BE45</span><span class="sxs-lookup"><span data-stu-id="c61cb-199">result := E7DCCE66DF855A323A6BB7BD7A59BE45</span></span>

<span data-ttu-id="c61cb-200">Bu, aşağıdaki tam bağlam üstbilgisini üretir:</span><span class="sxs-lookup"><span data-stu-id="c61cb-200">This produces the full context header below:</span></span>

```
00 01 00 00 00 20 00 00 00 0C 00 00 00 10 00 00
00 10 E7 DC CE 66 DF 85 5A 32 3A 6B B7 BD 7A 59
BE 45
```

<span data-ttu-id="c61cb-201">Bileşenler aşağıdaki gibi kesilir:</span><span class="sxs-lookup"><span data-stu-id="c61cb-201">The components break down as follows:</span></span>

* <span data-ttu-id="c61cb-202">işaretleyici (00 01)</span><span class="sxs-lookup"><span data-stu-id="c61cb-202">the marker (00 01)</span></span>

* <span data-ttu-id="c61cb-203">blok şifresi anahtar uzunluğu (00 00 00 20)</span><span class="sxs-lookup"><span data-stu-id="c61cb-203">the block cipher key length (00 00 00 20)</span></span>

* <span data-ttu-id="c61cb-204">nonce boyutu (00 00 00 0C)</span><span class="sxs-lookup"><span data-stu-id="c61cb-204">the nonce size (00 00 00 0C)</span></span>

* <span data-ttu-id="c61cb-205">blok şifre blok boyutu (00 00 00 10)</span><span class="sxs-lookup"><span data-stu-id="c61cb-205">the block cipher block size (00 00 00 10)</span></span>

* <span data-ttu-id="c61cb-206">kimlik doğrulama etiketi boyutu (00 00 00 10) ve</span><span class="sxs-lookup"><span data-stu-id="c61cb-206">the authentication tag size (00 00 00 10) and</span></span>

* <span data-ttu-id="c61cb-207">blok şifresi (E7 DC-End) çalıştıran kimlik doğrulama etiketi.</span><span class="sxs-lookup"><span data-stu-id="c61cb-207">the authentication tag from running the block cipher (E7 DC - end).</span></span>
