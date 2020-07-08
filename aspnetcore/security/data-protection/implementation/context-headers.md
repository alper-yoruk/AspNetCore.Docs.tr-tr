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
ms.openlocfilehash: 0995cd80c10f638c90a60630378518988ffb89ed
ms.sourcegitcommit: fa89d6553378529ae86b388689ac2c6f38281bb9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86060104"
---
# <a name="context-headers-in-aspnet-core"></a><span data-ttu-id="01c72-103">ASP.NET Core bağlam üst bilgileri</span><span class="sxs-lookup"><span data-stu-id="01c72-103">Context headers in ASP.NET Core</span></span>

<a name="data-protection-implementation-context-headers"></a>

## <a name="background-and-theory"></a><span data-ttu-id="01c72-104">Arka plan ve teorik</span><span class="sxs-lookup"><span data-stu-id="01c72-104">Background and theory</span></span>

<span data-ttu-id="01c72-105">Veri koruma sisteminde, "anahtar" kimliği doğrulanmış şifreleme hizmetleri sağlayabilen bir nesne anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="01c72-105">In the data protection system, a "key" means an object that can provide authenticated encryption services.</span></span> <span data-ttu-id="01c72-106">Her anahtar benzersiz bir kimlik (GUID) tarafından tanımlanır ve IT algoritmik Information ve entropıc malzemesiyle birlikte bulunur.</span><span class="sxs-lookup"><span data-stu-id="01c72-106">Each key is identified by a unique id (a GUID), and it carries with it algorithmic information and entropic material.</span></span> <span data-ttu-id="01c72-107">Her bir anahtarın benzersiz entropi taşıması ve sistem bunu zorunlu kılamaz ve anahtar halkasının mevcut bir anahtarın algoritmik bilgilerini değiştirerek anahtar halkasını el ile değiştirebilen geliştiriciler için de hesap yapmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="01c72-107">It's intended that each key carry unique entropy, but the system cannot enforce that, and we also need to account for developers who might change the key ring manually by modifying the algorithmic information of an existing key in the key ring.</span></span> <span data-ttu-id="01c72-108">Bu durumlarda güvenlik gereksinimlerimize ulaşmak için, veri koruma sisteminin bir [şifreleme çevikliği](https://www.microsoft.com/research/publication/cryptographic-agility-and-its-relation-to-circular-encryption)vardır. Bu, birden çok şifreleme algoritmasında tek bir entropıc değeri güvenli bir şekilde kullanılmasına olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="01c72-108">To achieve our security requirements given these cases the data protection system has a concept of [cryptographic agility](https://www.microsoft.com/research/publication/cryptographic-agility-and-its-relation-to-circular-encryption), which allows securely using a single entropic value across multiple cryptographic algorithms.</span></span>

<span data-ttu-id="01c72-109">Şifreleme çevikliği destekleyen çoğu sistem, yük içindeki algoritmayla ilgili bazı tanımlayıcı bilgileri de ekleyerek bunu destekler.</span><span class="sxs-lookup"><span data-stu-id="01c72-109">Most systems which support cryptographic agility do so by including some identifying information about the algorithm inside the payload.</span></span> <span data-ttu-id="01c72-110">Algoritmanın OID 'si genellikle bunun için iyi bir adaydır.</span><span class="sxs-lookup"><span data-stu-id="01c72-110">The algorithm's OID is generally a good candidate for this.</span></span> <span data-ttu-id="01c72-111">Ancak, çalıştırdığımız bir sorun aynı algoritmayı belirtmenizin birden çok yolu vardır: "AES" (CNG) ve yönetilen AES, AES, AesCryptoServiceProvider, AesCng ve Rijndadelmanaged (verilen belirli parametreler) sınıflarının hepsi aslında aynı şeydir ve bunların tümünün doğru OID 'ye eşlenmesinin devam etmemiz gerekir.</span><span class="sxs-lookup"><span data-stu-id="01c72-111">However, one problem that we ran into is that there are multiple ways to specify the same algorithm: "AES" (CNG) and the managed Aes, AesManaged, AesCryptoServiceProvider, AesCng, and RijndaelManaged (given specific parameters) classes are all actually the same thing, and we'd need to maintain a mapping of all of these to the correct OID.</span></span> <span data-ttu-id="01c72-112">Bir geliştirici özel bir algoritma (ya da başka bir AES uygulama) sağlamak istiyorlarsa, onun OID 'sini söylemeleri gerekir.</span><span class="sxs-lookup"><span data-stu-id="01c72-112">If a developer wanted to provide a custom algorithm (or even another implementation of AES!), they'd have to tell us its OID.</span></span> <span data-ttu-id="01c72-113">Bu ek kayıt adımı, sistem yapılandırmasını özellikle sorunsuz hale getirir.</span><span class="sxs-lookup"><span data-stu-id="01c72-113">This extra registration step makes system configuration particularly painful.</span></span>

<span data-ttu-id="01c72-114">Geri adımla, sorunu yanlış yönden yaklaşdığımızda kararıyoruz.</span><span class="sxs-lookup"><span data-stu-id="01c72-114">Stepping back, we decided that we were approaching the problem from the wrong direction.</span></span> <span data-ttu-id="01c72-115">Bir OID, algoritmanın ne olduğunu söyler, ancak bunu gerçekten ilgilentik.</span><span class="sxs-lookup"><span data-stu-id="01c72-115">An OID tells you what the algorithm is, but we don't actually care about this.</span></span> <span data-ttu-id="01c72-116">İki farklı algoritmalarda tek bir entropıc değeri güvenli bir şekilde kullanılması gerekiyorsa, algoritmaların gerçekten ne olduğunu bilmemizi gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="01c72-116">If we need to use a single entropic value securely in two different algorithms, it's not necessary for us to know what the algorithms actually are.</span></span> <span data-ttu-id="01c72-117">Ne kadar önemli olduğumuz.</span><span class="sxs-lookup"><span data-stu-id="01c72-117">What we actually care about is how they behave.</span></span> <span data-ttu-id="01c72-118">Her türlü simetrik blok şifre algoritması da güçlü bir pseudportaıdom permütasyon (PRP): girişleri düzeltin (anahtar, zincir oluşturma modu, IV, düz metin) ve şifreli çıktı, büyük olasılıkla aynı girişler verilen diğer herhangi bir simetrik blok şifre algoritmasından farklı olacaktır.</span><span class="sxs-lookup"><span data-stu-id="01c72-118">Any decent symmetric block cipher algorithm is also a strong pseudorandom permutation (PRP): fix the inputs (key, chaining mode, IV, plaintext) and the ciphertext output will with overwhelming probability be distinct from any other symmetric block cipher algorithm given the same inputs.</span></span> <span data-ttu-id="01c72-119">Benzer şekilde, her türlü anahtarlı karma işlevi de güçlü bir pseudportaıdom işlevidir (PRF) ve sabit bir giriş kümesi, çıkış overwhelmingly başka bir anahtarlı karma işlevden farklı olacaktır.</span><span class="sxs-lookup"><span data-stu-id="01c72-119">Similarly, any decent keyed hash function is also a strong pseudorandom function (PRF), and given a fixed input set its output will overwhelmingly be distinct from any other keyed hash function.</span></span>

<span data-ttu-id="01c72-120">Bu güçlü PRPs ve PRFs kavramını bir bağlam üst bilgisi oluşturmak için kullanırız.</span><span class="sxs-lookup"><span data-stu-id="01c72-120">We use this concept of strong PRPs and PRFs to build up a context header.</span></span> <span data-ttu-id="01c72-121">Bu bağlam üstbilgisi temel olarak, belirli bir işlem için kullanımdaki algoritmaların üzerinde kararlı bir parmak izi görevi görür ve veri koruma sistemi için gereken şifreleme çevikliğini sağlar.</span><span class="sxs-lookup"><span data-stu-id="01c72-121">This context header essentially acts as a stable thumbprint over the algorithms in use for any given operation, and it provides the cryptographic agility needed by the data protection system.</span></span> <span data-ttu-id="01c72-122">Bu üst bilgi tekrarlanabilir ve daha sonra [alt anahtar türetme işleminin](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation)bir parçası olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="01c72-122">This header is reproducible and is used later as part of the [subkey derivation process](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation).</span></span> <span data-ttu-id="01c72-123">Temel algoritmaların işlem modlarına bağlı olarak bağlam üst bilgisini oluşturmanın iki farklı yolu vardır.</span><span class="sxs-lookup"><span data-stu-id="01c72-123">There are two different ways to build the context header depending on the modes of operation of the underlying algorithms.</span></span>

## <a name="cbc-mode-encryption--hmac-authentication"></a><span data-ttu-id="01c72-124">CBC modu şifreleme + HMAC kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="01c72-124">CBC-mode encryption + HMAC authentication</span></span>

<a name="data-protection-implementation-context-headers-cbc-components"></a>

<span data-ttu-id="01c72-125">Bağlam üst bilgisi aşağıdaki bileşenlerden oluşur:</span><span class="sxs-lookup"><span data-stu-id="01c72-125">The context header consists of the following components:</span></span>

* <span data-ttu-id="01c72-126">[16 bit] Bir işaret olan 00 00 değeri, "CBC şifreleme + HMAC kimlik doğrulaması" anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="01c72-126">[16 bits] The value 00 00, which is a marker meaning "CBC encryption + HMAC authentication".</span></span>

* <span data-ttu-id="01c72-127">[32 bit] Simetrik blok şifreleme algoritmasının anahtar uzunluğu (bayt cinsinden, Big-endian).</span><span class="sxs-lookup"><span data-stu-id="01c72-127">[32 bits] The key length (in bytes, big-endian) of the symmetric block cipher algorithm.</span></span>

* <span data-ttu-id="01c72-128">[32 bit] Simetrik blok şifreleme algoritmasının blok boyutu (bayt cinsinden, Big-endian).</span><span class="sxs-lookup"><span data-stu-id="01c72-128">[32 bits] The block size (in bytes, big-endian) of the symmetric block cipher algorithm.</span></span>

* <span data-ttu-id="01c72-129">[32 bit] HMAC algoritmasının anahtar uzunluğu (bayt, Big-endian).</span><span class="sxs-lookup"><span data-stu-id="01c72-129">[32 bits] The key length (in bytes, big-endian) of the HMAC algorithm.</span></span> <span data-ttu-id="01c72-130">(Şu anda anahtar boyutu her zaman Özet boyutuyla eşleşir.)</span><span class="sxs-lookup"><span data-stu-id="01c72-130">(Currently the key size always matches the digest size.)</span></span>

* <span data-ttu-id="01c72-131">[32 bit] HMAC algoritmasının Özet boyutu (bayt, Big-endian).</span><span class="sxs-lookup"><span data-stu-id="01c72-131">[32 bits] The digest size (in bytes, big-endian) of the HMAC algorithm.</span></span>

* <span data-ttu-id="01c72-132">`EncCBC(K_E, IV, "")`, simetrik blok şifre algoritmasının çıktısı boş bir dize girişi, ve IV ' in hepsi sıfır vektörü olduğu anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="01c72-132">`EncCBC(K_E, IV, "")`, which is the output of the symmetric block cipher algorithm given an empty string input and where IV is an all-zero vector.</span></span> <span data-ttu-id="01c72-133">Oluşturma `K_E` işlemi aşağıda açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="01c72-133">The construction of `K_E` is described below.</span></span>

* <span data-ttu-id="01c72-134">`MAC(K_H, "")`, bu, boş bir dize girişi verilen HMAC algoritmasının çıktısı.</span><span class="sxs-lookup"><span data-stu-id="01c72-134">`MAC(K_H, "")`, which is the output of the HMAC algorithm given an empty string input.</span></span> <span data-ttu-id="01c72-135">Oluşturma `K_H` işlemi aşağıda açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="01c72-135">The construction of `K_H` is described below.</span></span>

<span data-ttu-id="01c72-136">İdeal olarak, ve için tüm-sıfır vektörlerini geçirebiliriz `K_E` `K_H` .</span><span class="sxs-lookup"><span data-stu-id="01c72-136">Ideally, we could pass all-zero vectors for `K_E` and `K_H`.</span></span> <span data-ttu-id="01c72-137">Bununla birlikte, temeldeki algoritmanın herhangi bir işlem gerçekleştirmeden önce zayıf anahtarların varlığını denetliyoruz (özellikle DES ve 3DES), hepsi sıfır vektörü gibi basit veya yinelenebilir bir model kullanarak,</span><span class="sxs-lookup"><span data-stu-id="01c72-137">However, we want to avoid the situation where the underlying algorithm checks for the existence of weak keys before performing any operations (notably DES and 3DES), which precludes using a simple or repeatable pattern like an all-zero vector.</span></span>

<span data-ttu-id="01c72-138">Bunun yerine, temel alınan PRF olarak (bkz. [NıST SP800-108](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-108.pdf), Sec. 5,1), sıfır uzunluklu bir anahtar, etiket ve bağlam ve HMACSHA512 ile.</span><span class="sxs-lookup"><span data-stu-id="01c72-138">Instead, we use the NIST SP800-108 KDF in Counter Mode (see [NIST SP800-108](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-108.pdf), Sec. 5.1) with a zero-length key, label, and context and HMACSHA512 as the underlying PRF.</span></span> <span data-ttu-id="01c72-139">Çıkışın baytlarını türetireceğiz `| K_E | + | K_H |` , sonra sonucun içine ve kendilerine ait olduğunu kaldırdık `K_E` `K_H` .</span><span class="sxs-lookup"><span data-stu-id="01c72-139">We derive `| K_E | + | K_H |` bytes of output, then decompose the result into `K_E` and `K_H` themselves.</span></span> <span data-ttu-id="01c72-140">Matematik olarak bu, aşağıdaki gibi gösterilir.</span><span class="sxs-lookup"><span data-stu-id="01c72-140">Mathematically, this is represented as follows.</span></span>

`( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")`

### <a name="example-aes-192-cbc--hmacsha256"></a><span data-ttu-id="01c72-141">Örnek: AES-192-CBC + HMACSHA256</span><span class="sxs-lookup"><span data-stu-id="01c72-141">Example: AES-192-CBC + HMACSHA256</span></span>

<span data-ttu-id="01c72-142">Örnek olarak, simetrik blok şifreleme algoritmasının AES-192-CBC ve doğrulama algoritmasının HMACSHA256 olduğu durumu göz önünde bulundurun.</span><span class="sxs-lookup"><span data-stu-id="01c72-142">As an example, consider the case where the symmetric block cipher algorithm is AES-192-CBC and the validation algorithm is HMACSHA256.</span></span> <span data-ttu-id="01c72-143">Aşağıdaki adımları kullanarak sistem bağlam üst bilgisini oluşturur.</span><span class="sxs-lookup"><span data-stu-id="01c72-143">The system would generate the context header using the following steps.</span></span>

<span data-ttu-id="01c72-144">İlk olarak, `( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")` `| K_E | = 192 bits` `| K_H | = 256 bits` Belirtilen algoritmalara göre ve.</span><span class="sxs-lookup"><span data-stu-id="01c72-144">First, let `( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")`, where `| K_E | = 192 bits` and `| K_H | = 256 bits` per the specified algorithms.</span></span> <span data-ttu-id="01c72-145">Bu `K_E = 5BB6..21DD` `K_H = A04A..00A9` , aşağıdaki örnekte ve ' a yol açar:</span><span class="sxs-lookup"><span data-stu-id="01c72-145">This leads to `K_E = 5BB6..21DD` and `K_H = A04A..00A9` in the example below:</span></span>

```
5B B6 C9 83 13 78 22 1D 8E 10 73 CA CF 65 8E B0
61 62 42 71 CB 83 21 DD A0 4A 05 00 5B AB C0 A2
49 6F A5 61 E3 E2 49 87 AA 63 55 CD 74 0A DA C4
B7 92 3D BF 59 90 00 A9
```

<span data-ttu-id="01c72-146">Ardından, `Enc_CBC (K_E, IV, "")` yukarıda BELIRTILEN AES-192-CBC için `IV = 0*` işlem `K_E` .</span><span class="sxs-lookup"><span data-stu-id="01c72-146">Next, compute `Enc_CBC (K_E, IV, "")` for AES-192-CBC given `IV = 0*` and `K_E` as above.</span></span>

`result := F474B1872B3B53E4721DE19C0841DB6F`

<span data-ttu-id="01c72-147">Daha sonra, `MAC(K_H, "")` yukarıdaki gibi VERILEN HMACSHA256 için işlem `K_H` .</span><span class="sxs-lookup"><span data-stu-id="01c72-147">Next, compute `MAC(K_H, "")` for HMACSHA256 given `K_H` as above.</span></span>

`result := D4791184B996092EE1202F36E8608FA8FBD98ABDFF5402F264B1D7211536220C`

<span data-ttu-id="01c72-148">Bu, aşağıdaki tam bağlam üstbilgisini üretir:</span><span class="sxs-lookup"><span data-stu-id="01c72-148">This produces the full context header below:</span></span>

```
00 00 00 00 00 18 00 00 00 10 00 00 00 20 00 00
00 20 F4 74 B1 87 2B 3B 53 E4 72 1D E1 9C 08 41
DB 6F D4 79 11 84 B9 96 09 2E E1 20 2F 36 E8 60
8F A8 FB D9 8A BD FF 54 02 F2 64 B1 D7 21 15 36
22 0C
```

<span data-ttu-id="01c72-149">Bu bağlam üst bilgisi, kimliği doğrulanmış şifreleme algoritması çiftinin parmak izi (AES-192-CBC şifrelemesi + HMACSHA256 Validation).</span><span class="sxs-lookup"><span data-stu-id="01c72-149">This context header is the thumbprint of the authenticated encryption algorithm pair (AES-192-CBC encryption + HMACSHA256 validation).</span></span> <span data-ttu-id="01c72-150">[Yukarıda](xref:security/data-protection/implementation/context-headers#data-protection-implementation-context-headers-cbc-components) açıklanan bileşenler şunlardır:</span><span class="sxs-lookup"><span data-stu-id="01c72-150">The components, as described [above](xref:security/data-protection/implementation/context-headers#data-protection-implementation-context-headers-cbc-components) are:</span></span>

* <span data-ttu-id="01c72-151">işaretleyici`(00 00)`</span><span class="sxs-lookup"><span data-stu-id="01c72-151">the marker `(00 00)`</span></span>

* <span data-ttu-id="01c72-152">blok şifre anahtar uzunluğu`(00 00 00 18)`</span><span class="sxs-lookup"><span data-stu-id="01c72-152">the block cipher key length `(00 00 00 18)`</span></span>

* <span data-ttu-id="01c72-153">blok şifre blok boyutu`(00 00 00 10)`</span><span class="sxs-lookup"><span data-stu-id="01c72-153">the block cipher block size `(00 00 00 10)`</span></span>

* <span data-ttu-id="01c72-154">HMAC anahtar uzunluğu`(00 00 00 20)`</span><span class="sxs-lookup"><span data-stu-id="01c72-154">the HMAC key length `(00 00 00 20)`</span></span>

* <span data-ttu-id="01c72-155">HMAC Özet boyutu`(00 00 00 20)`</span><span class="sxs-lookup"><span data-stu-id="01c72-155">the HMAC digest size `(00 00 00 20)`</span></span>

* <span data-ttu-id="01c72-156">blok şifre PRP çıkışı `(F4 74 - DB 6F)` ve</span><span class="sxs-lookup"><span data-stu-id="01c72-156">the block cipher PRP output `(F4 74 - DB 6F)` and</span></span>

* <span data-ttu-id="01c72-157">HMAC PRF çıkışı `(D4 79 - end)` .</span><span class="sxs-lookup"><span data-stu-id="01c72-157">the HMAC PRF output `(D4 79 - end)`.</span></span>

> [!NOTE]
> <span data-ttu-id="01c72-158">CBC modu şifreleme + HMAC kimlik doğrulama bağlamı üst bilgisi, algoritma uygulamalarının Windows CNG tarafından mı yoksa yönetilen SymmetricAlgorithm ve KeyedHashAlgorithm türleriyle mi sağlandığına bakılmaksızın aynı şekilde oluşturulmuştur.</span><span class="sxs-lookup"><span data-stu-id="01c72-158">The CBC-mode encryption + HMAC authentication context header is built the same way regardless of whether the algorithms implementations are provided by Windows CNG or by managed SymmetricAlgorithm and KeyedHashAlgorithm types.</span></span> <span data-ttu-id="01c72-159">Bu, farklı işletim sistemlerinde çalışan uygulamaların, algoritmaların uygulamaları arasında farklı olmasına rağmen aynı bağlam üst bilgisini güvenilir bir şekilde üretmesine olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="01c72-159">This allows applications running on different operating systems to reliably produce the same context header even though the implementations of the algorithms differ between OSes.</span></span> <span data-ttu-id="01c72-160">(Uygulamada, KeyedHashAlgorithm 'nin doğru HMAC olması gerekmez.</span><span class="sxs-lookup"><span data-stu-id="01c72-160">(In practice, the KeyedHashAlgorithm doesn't have to be a proper HMAC.</span></span> <span data-ttu-id="01c72-161">Bu, herhangi bir anahtarlı karma algoritma türü olabilir.)</span><span class="sxs-lookup"><span data-stu-id="01c72-161">It can be any keyed hash algorithm type.)</span></span>

### <a name="example-3des-192-cbc--hmacsha1"></a><span data-ttu-id="01c72-162">Örnek: 3DES-192-CBC + HMACSHA1</span><span class="sxs-lookup"><span data-stu-id="01c72-162">Example: 3DES-192-CBC + HMACSHA1</span></span>

<span data-ttu-id="01c72-163">İlk olarak, `( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")` `| K_E | = 192 bits` `| K_H | = 160 bits` Belirtilen algoritmalara göre ve.</span><span class="sxs-lookup"><span data-stu-id="01c72-163">First, let `( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")`, where `| K_E | = 192 bits` and `| K_H | = 160 bits` per the specified algorithms.</span></span> <span data-ttu-id="01c72-164">Bu `K_E = A219..E2BB` `K_H = DC4A..B464` , aşağıdaki örnekte ve ' a yol açar:</span><span class="sxs-lookup"><span data-stu-id="01c72-164">This leads to `K_E = A219..E2BB` and `K_H = DC4A..B464` in the example below:</span></span>

```
A2 19 60 2F 83 A9 13 EA B0 61 3A 39 B8 A6 7E 22
61 D9 F8 6C 10 51 E2 BB DC 4A 00 D7 03 A2 48 3E
D1 F7 5A 34 EB 28 3E D7 D4 67 B4 64
```

<span data-ttu-id="01c72-165">Daha sonra, `Enc_CBC (K_E, IV, "")` 3DES-192-CBC için `IV = 0*` ve `K_E` yukarıdaki gibi işlem.</span><span class="sxs-lookup"><span data-stu-id="01c72-165">Next, compute `Enc_CBC (K_E, IV, "")` for 3DES-192-CBC given `IV = 0*` and `K_E` as above.</span></span>

`result := ABB100F81E53E10E`

<span data-ttu-id="01c72-166">Daha sonra, `MAC(K_H, "")` yukarıdaki gibi VERILEN HMACSHA1 için işlem `K_H` .</span><span class="sxs-lookup"><span data-stu-id="01c72-166">Next, compute `MAC(K_H, "")` for HMACSHA1 given `K_H` as above.</span></span>

`result := 76EB189B35CF03461DDF877CD9F4B1B4D63A7555`

<span data-ttu-id="01c72-167">Bu, aşağıda gösterildiği gibi, kimliği doğrulanmış şifreleme algoritması çiftinin (3DES-192-CBC şifrelemesi + HMACSHA1 doğrulaması) bir parmak izi olan tam bağlam üstbilgisini üretir</span><span class="sxs-lookup"><span data-stu-id="01c72-167">This produces the full context header which is a thumbprint of the authenticated encryption algorithm pair (3DES-192-CBC encryption + HMACSHA1 validation), shown below:</span></span>

```
00 00 00 00 00 18 00 00 00 08 00 00 00 14 00 00
00 14 AB B1 00 F8 1E 53 E1 0E 76 EB 18 9B 35 CF
03 46 1D DF 87 7C D9 F4 B1 B4 D6 3A 75 55
```

<span data-ttu-id="01c72-168">Bileşenler aşağıdaki gibi kesilir:</span><span class="sxs-lookup"><span data-stu-id="01c72-168">The components break down as follows:</span></span>

* <span data-ttu-id="01c72-169">işaretleyici`(00 00)`</span><span class="sxs-lookup"><span data-stu-id="01c72-169">the marker `(00 00)`</span></span>

* <span data-ttu-id="01c72-170">blok şifre anahtar uzunluğu`(00 00 00 18)`</span><span class="sxs-lookup"><span data-stu-id="01c72-170">the block cipher key length `(00 00 00 18)`</span></span>

* <span data-ttu-id="01c72-171">blok şifre blok boyutu`(00 00 00 08)`</span><span class="sxs-lookup"><span data-stu-id="01c72-171">the block cipher block size `(00 00 00 08)`</span></span>

* <span data-ttu-id="01c72-172">HMAC anahtar uzunluğu`(00 00 00 14)`</span><span class="sxs-lookup"><span data-stu-id="01c72-172">the HMAC key length `(00 00 00 14)`</span></span>

* <span data-ttu-id="01c72-173">HMAC Özet boyutu`(00 00 00 14)`</span><span class="sxs-lookup"><span data-stu-id="01c72-173">the HMAC digest size `(00 00 00 14)`</span></span>

* <span data-ttu-id="01c72-174">blok şifre PRP çıkışı `(AB B1 - E1 0E)` ve</span><span class="sxs-lookup"><span data-stu-id="01c72-174">the block cipher PRP output `(AB B1 - E1 0E)` and</span></span>

* <span data-ttu-id="01c72-175">HMAC PRF çıkışı `(76 EB - end)` .</span><span class="sxs-lookup"><span data-stu-id="01c72-175">the HMAC PRF output `(76 EB - end)`.</span></span>

## <a name="galoiscounter-mode-encryption--authentication"></a><span data-ttu-id="01c72-176">Galoa/sayaç modu şifreleme + kimlik doğrulama</span><span class="sxs-lookup"><span data-stu-id="01c72-176">Galois/Counter Mode encryption + authentication</span></span>

<span data-ttu-id="01c72-177">Bağlam üst bilgisi aşağıdaki bileşenlerden oluşur:</span><span class="sxs-lookup"><span data-stu-id="01c72-177">The context header consists of the following components:</span></span>

* <span data-ttu-id="01c72-178">[16 bit] Bir işaret olan 00 01 değeri, "GCM şifreleme + kimlik doğrulama" anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="01c72-178">[16 bits] The value 00 01, which is a marker meaning "GCM encryption + authentication".</span></span>

* <span data-ttu-id="01c72-179">[32 bit] Simetrik blok şifreleme algoritmasının anahtar uzunluğu (bayt cinsinden, Big-endian).</span><span class="sxs-lookup"><span data-stu-id="01c72-179">[32 bits] The key length (in bytes, big-endian) of the symmetric block cipher algorithm.</span></span>

* <span data-ttu-id="01c72-180">[32 bit] Kimliği doğrulanmış şifreleme işlemleri sırasında kullanılan nonce boyutu (bayt cinsinden, Big-endian).</span><span class="sxs-lookup"><span data-stu-id="01c72-180">[32 bits] The nonce size (in bytes, big-endian) used during authenticated encryption operations.</span></span> <span data-ttu-id="01c72-181">(Sistemimiz için bu, nonce boyut = 96 bitde düzeltilir.)</span><span class="sxs-lookup"><span data-stu-id="01c72-181">(For our system, this is fixed at nonce size = 96 bits.)</span></span>

* <span data-ttu-id="01c72-182">[32 bit] Simetrik blok şifreleme algoritmasının blok boyutu (bayt cinsinden, Big-endian).</span><span class="sxs-lookup"><span data-stu-id="01c72-182">[32 bits] The block size (in bytes, big-endian) of the symmetric block cipher algorithm.</span></span> <span data-ttu-id="01c72-183">(GCM için, bu, blok boyutu = 128 bitde düzeltilir.)</span><span class="sxs-lookup"><span data-stu-id="01c72-183">(For GCM, this is fixed at block size = 128 bits.)</span></span>

* <span data-ttu-id="01c72-184">[32 bit] Kimliği doğrulanmış şifreleme işlevi tarafından üretilen kimlik doğrulama etiketi boyutu (bayt, Big-endian).</span><span class="sxs-lookup"><span data-stu-id="01c72-184">[32 bits] The authentication tag size (in bytes, big-endian) produced by the authenticated encryption function.</span></span> <span data-ttu-id="01c72-185">(Sistemimizde bu, etiket boyutu = 128 bitde düzeltilir.)</span><span class="sxs-lookup"><span data-stu-id="01c72-185">(For our system, this is fixed at tag size = 128 bits.)</span></span>

* <span data-ttu-id="01c72-186">[128 bit] `Enc_GCM (K_E, nonce, "")`Simetrik blok şifreleme algoritmasının çıkışı boş bir dize girişi, ve nonce 'nin 96 bitlik bir tamamen sıfır vektörü olduğu, ' nin etiketi.</span><span class="sxs-lookup"><span data-stu-id="01c72-186">[128 bits] The tag of `Enc_GCM (K_E, nonce, "")`, which is the output of the symmetric block cipher algorithm given an empty string input and where nonce is a 96-bit all-zero vector.</span></span>

<span data-ttu-id="01c72-187">`K_E`, CBC şifreleme + HMAC kimlik doğrulama senaryosunda aynı mekanizmayı kullanarak türetilir.</span><span class="sxs-lookup"><span data-stu-id="01c72-187">`K_E` is derived using the same mechanism as in the CBC encryption + HMAC authentication scenario.</span></span> <span data-ttu-id="01c72-188">Ancak burada hiçbir yürütme olmadığından, `K_H` temelde bu, `| K_H | = 0` algoritma aşağıdaki biçime daraltılır.</span><span class="sxs-lookup"><span data-stu-id="01c72-188">However, since there's no `K_H` in play here, we essentially have `| K_H | = 0`, and the algorithm collapses to the below form.</span></span>

`K_E = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")`

### <a name="example-aes-256-gcm"></a><span data-ttu-id="01c72-189">Örnek: AES-256-GCM</span><span class="sxs-lookup"><span data-stu-id="01c72-189">Example: AES-256-GCM</span></span>

<span data-ttu-id="01c72-190">Birinci, Let `K_E = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")` , burada `| K_E | = 256 bits` .</span><span class="sxs-lookup"><span data-stu-id="01c72-190">First, let `K_E = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")`, where `| K_E | = 256 bits`.</span></span>

`K_E := 22BC6F1B171C08C4AE2F27444AF8FC8B3087A90006CAEA91FDCFB47C1B8733B8`

<span data-ttu-id="01c72-191">Ardından, `Enc_GCM (K_E, nonce, "")` yukarıda BELIRTILEN AES-256-GCM için kimlik doğrulama etiketini hesaplar `nonce = 096` `K_E` .</span><span class="sxs-lookup"><span data-stu-id="01c72-191">Next, compute the authentication tag of `Enc_GCM (K_E, nonce, "")` for AES-256-GCM given `nonce = 096` and `K_E` as above.</span></span>

`result := E7DCCE66DF855A323A6BB7BD7A59BE45`

<span data-ttu-id="01c72-192">Bu, aşağıdaki tam bağlam üstbilgisini üretir:</span><span class="sxs-lookup"><span data-stu-id="01c72-192">This produces the full context header below:</span></span>

```
00 01 00 00 00 20 00 00 00 0C 00 00 00 10 00 00
00 10 E7 DC CE 66 DF 85 5A 32 3A 6B B7 BD 7A 59
BE 45
```

<span data-ttu-id="01c72-193">Bileşenler aşağıdaki gibi kesilir:</span><span class="sxs-lookup"><span data-stu-id="01c72-193">The components break down as follows:</span></span>

* <span data-ttu-id="01c72-194">işaretleyici`(00 01)`</span><span class="sxs-lookup"><span data-stu-id="01c72-194">the marker `(00 01)`</span></span>

* <span data-ttu-id="01c72-195">blok şifre anahtar uzunluğu`(00 00 00 20)`</span><span class="sxs-lookup"><span data-stu-id="01c72-195">the block cipher key length `(00 00 00 20)`</span></span>

* <span data-ttu-id="01c72-196">nonce boyutu`(00 00 00 0C)`</span><span class="sxs-lookup"><span data-stu-id="01c72-196">the nonce size `(00 00 00 0C)`</span></span>

* <span data-ttu-id="01c72-197">blok şifre blok boyutu`(00 00 00 10)`</span><span class="sxs-lookup"><span data-stu-id="01c72-197">the block cipher block size `(00 00 00 10)`</span></span>

* <span data-ttu-id="01c72-198">kimlik doğrulama etiketi boyutu `(00 00 00 10)` ve</span><span class="sxs-lookup"><span data-stu-id="01c72-198">the authentication tag size `(00 00 00 10)` and</span></span>

* <span data-ttu-id="01c72-199">blok şifresi çalıştıran kimlik doğrulama etiketi `(E7 DC - end)` .</span><span class="sxs-lookup"><span data-stu-id="01c72-199">the authentication tag from running the block cipher `(E7 DC - end)`.</span></span>
