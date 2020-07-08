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
# <a name="context-headers-in-aspnet-core"></a>ASP.NET Core bağlam üst bilgileri

<a name="data-protection-implementation-context-headers"></a>

## <a name="background-and-theory"></a>Arka plan ve teorik

Veri koruma sisteminde, "anahtar" kimliği doğrulanmış şifreleme hizmetleri sağlayabilen bir nesne anlamına gelir. Her anahtar benzersiz bir kimlik (GUID) tarafından tanımlanır ve IT algoritmik Information ve entropıc malzemesiyle birlikte bulunur. Her bir anahtarın benzersiz entropi taşıması ve sistem bunu zorunlu kılamaz ve anahtar halkasının mevcut bir anahtarın algoritmik bilgilerini değiştirerek anahtar halkasını el ile değiştirebilen geliştiriciler için de hesap yapmanız gerekir. Bu durumlarda güvenlik gereksinimlerimize ulaşmak için, veri koruma sisteminin bir [şifreleme çevikliği](https://www.microsoft.com/research/publication/cryptographic-agility-and-its-relation-to-circular-encryption)vardır. Bu, birden çok şifreleme algoritmasında tek bir entropıc değeri güvenli bir şekilde kullanılmasına olanak tanır.

Şifreleme çevikliği destekleyen çoğu sistem, yük içindeki algoritmayla ilgili bazı tanımlayıcı bilgileri de ekleyerek bunu destekler. Algoritmanın OID 'si genellikle bunun için iyi bir adaydır. Ancak, çalıştırdığımız bir sorun aynı algoritmayı belirtmenizin birden çok yolu vardır: "AES" (CNG) ve yönetilen AES, AES, AesCryptoServiceProvider, AesCng ve Rijndadelmanaged (verilen belirli parametreler) sınıflarının hepsi aslında aynı şeydir ve bunların tümünün doğru OID 'ye eşlenmesinin devam etmemiz gerekir. Bir geliştirici özel bir algoritma (ya da başka bir AES uygulama) sağlamak istiyorlarsa, onun OID 'sini söylemeleri gerekir. Bu ek kayıt adımı, sistem yapılandırmasını özellikle sorunsuz hale getirir.

Geri adımla, sorunu yanlış yönden yaklaşdığımızda kararıyoruz. Bir OID, algoritmanın ne olduğunu söyler, ancak bunu gerçekten ilgilentik. İki farklı algoritmalarda tek bir entropıc değeri güvenli bir şekilde kullanılması gerekiyorsa, algoritmaların gerçekten ne olduğunu bilmemizi gerekli değildir. Ne kadar önemli olduğumuz. Her türlü simetrik blok şifre algoritması da güçlü bir pseudportaıdom permütasyon (PRP): girişleri düzeltin (anahtar, zincir oluşturma modu, IV, düz metin) ve şifreli çıktı, büyük olasılıkla aynı girişler verilen diğer herhangi bir simetrik blok şifre algoritmasından farklı olacaktır. Benzer şekilde, her türlü anahtarlı karma işlevi de güçlü bir pseudportaıdom işlevidir (PRF) ve sabit bir giriş kümesi, çıkış overwhelmingly başka bir anahtarlı karma işlevden farklı olacaktır.

Bu güçlü PRPs ve PRFs kavramını bir bağlam üst bilgisi oluşturmak için kullanırız. Bu bağlam üstbilgisi temel olarak, belirli bir işlem için kullanımdaki algoritmaların üzerinde kararlı bir parmak izi görevi görür ve veri koruma sistemi için gereken şifreleme çevikliğini sağlar. Bu üst bilgi tekrarlanabilir ve daha sonra [alt anahtar türetme işleminin](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation)bir parçası olarak kullanılır. Temel algoritmaların işlem modlarına bağlı olarak bağlam üst bilgisini oluşturmanın iki farklı yolu vardır.

## <a name="cbc-mode-encryption--hmac-authentication"></a>CBC modu şifreleme + HMAC kimlik doğrulaması

<a name="data-protection-implementation-context-headers-cbc-components"></a>

Bağlam üst bilgisi aşağıdaki bileşenlerden oluşur:

* [16 bit] Bir işaret olan 00 00 değeri, "CBC şifreleme + HMAC kimlik doğrulaması" anlamına gelir.

* [32 bit] Simetrik blok şifreleme algoritmasının anahtar uzunluğu (bayt cinsinden, Big-endian).

* [32 bit] Simetrik blok şifreleme algoritmasının blok boyutu (bayt cinsinden, Big-endian).

* [32 bit] HMAC algoritmasının anahtar uzunluğu (bayt, Big-endian). (Şu anda anahtar boyutu her zaman Özet boyutuyla eşleşir.)

* [32 bit] HMAC algoritmasının Özet boyutu (bayt, Big-endian).

* `EncCBC(K_E, IV, "")`, simetrik blok şifre algoritmasının çıktısı boş bir dize girişi, ve IV ' in hepsi sıfır vektörü olduğu anlamına gelir. Oluşturma `K_E` işlemi aşağıda açıklanmıştır.

* `MAC(K_H, "")`, bu, boş bir dize girişi verilen HMAC algoritmasının çıktısı. Oluşturma `K_H` işlemi aşağıda açıklanmıştır.

İdeal olarak, ve için tüm-sıfır vektörlerini geçirebiliriz `K_E` `K_H` . Bununla birlikte, temeldeki algoritmanın herhangi bir işlem gerçekleştirmeden önce zayıf anahtarların varlığını denetliyoruz (özellikle DES ve 3DES), hepsi sıfır vektörü gibi basit veya yinelenebilir bir model kullanarak,

Bunun yerine, temel alınan PRF olarak (bkz. [NıST SP800-108](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-108.pdf), Sec. 5,1), sıfır uzunluklu bir anahtar, etiket ve bağlam ve HMACSHA512 ile. Çıkışın baytlarını türetireceğiz `| K_E | + | K_H |` , sonra sonucun içine ve kendilerine ait olduğunu kaldırdık `K_E` `K_H` . Matematik olarak bu, aşağıdaki gibi gösterilir.

`( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")`

### <a name="example-aes-192-cbc--hmacsha256"></a>Örnek: AES-192-CBC + HMACSHA256

Örnek olarak, simetrik blok şifreleme algoritmasının AES-192-CBC ve doğrulama algoritmasının HMACSHA256 olduğu durumu göz önünde bulundurun. Aşağıdaki adımları kullanarak sistem bağlam üst bilgisini oluşturur.

İlk olarak, `( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")` `| K_E | = 192 bits` `| K_H | = 256 bits` Belirtilen algoritmalara göre ve. Bu `K_E = 5BB6..21DD` `K_H = A04A..00A9` , aşağıdaki örnekte ve ' a yol açar:

```
5B B6 C9 83 13 78 22 1D 8E 10 73 CA CF 65 8E B0
61 62 42 71 CB 83 21 DD A0 4A 05 00 5B AB C0 A2
49 6F A5 61 E3 E2 49 87 AA 63 55 CD 74 0A DA C4
B7 92 3D BF 59 90 00 A9
```

Ardından, `Enc_CBC (K_E, IV, "")` yukarıda BELIRTILEN AES-192-CBC için `IV = 0*` işlem `K_E` .

`result := F474B1872B3B53E4721DE19C0841DB6F`

Daha sonra, `MAC(K_H, "")` yukarıdaki gibi VERILEN HMACSHA256 için işlem `K_H` .

`result := D4791184B996092EE1202F36E8608FA8FBD98ABDFF5402F264B1D7211536220C`

Bu, aşağıdaki tam bağlam üstbilgisini üretir:

```
00 00 00 00 00 18 00 00 00 10 00 00 00 20 00 00
00 20 F4 74 B1 87 2B 3B 53 E4 72 1D E1 9C 08 41
DB 6F D4 79 11 84 B9 96 09 2E E1 20 2F 36 E8 60
8F A8 FB D9 8A BD FF 54 02 F2 64 B1 D7 21 15 36
22 0C
```

Bu bağlam üst bilgisi, kimliği doğrulanmış şifreleme algoritması çiftinin parmak izi (AES-192-CBC şifrelemesi + HMACSHA256 Validation). [Yukarıda](xref:security/data-protection/implementation/context-headers#data-protection-implementation-context-headers-cbc-components) açıklanan bileşenler şunlardır:

* işaretleyici`(00 00)`

* blok şifre anahtar uzunluğu`(00 00 00 18)`

* blok şifre blok boyutu`(00 00 00 10)`

* HMAC anahtar uzunluğu`(00 00 00 20)`

* HMAC Özet boyutu`(00 00 00 20)`

* blok şifre PRP çıkışı `(F4 74 - DB 6F)` ve

* HMAC PRF çıkışı `(D4 79 - end)` .

> [!NOTE]
> CBC modu şifreleme + HMAC kimlik doğrulama bağlamı üst bilgisi, algoritma uygulamalarının Windows CNG tarafından mı yoksa yönetilen SymmetricAlgorithm ve KeyedHashAlgorithm türleriyle mi sağlandığına bakılmaksızın aynı şekilde oluşturulmuştur. Bu, farklı işletim sistemlerinde çalışan uygulamaların, algoritmaların uygulamaları arasında farklı olmasına rağmen aynı bağlam üst bilgisini güvenilir bir şekilde üretmesine olanak tanır. (Uygulamada, KeyedHashAlgorithm 'nin doğru HMAC olması gerekmez. Bu, herhangi bir anahtarlı karma algoritma türü olabilir.)

### <a name="example-3des-192-cbc--hmacsha1"></a>Örnek: 3DES-192-CBC + HMACSHA1

İlk olarak, `( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")` `| K_E | = 192 bits` `| K_H | = 160 bits` Belirtilen algoritmalara göre ve. Bu `K_E = A219..E2BB` `K_H = DC4A..B464` , aşağıdaki örnekte ve ' a yol açar:

```
A2 19 60 2F 83 A9 13 EA B0 61 3A 39 B8 A6 7E 22
61 D9 F8 6C 10 51 E2 BB DC 4A 00 D7 03 A2 48 3E
D1 F7 5A 34 EB 28 3E D7 D4 67 B4 64
```

Daha sonra, `Enc_CBC (K_E, IV, "")` 3DES-192-CBC için `IV = 0*` ve `K_E` yukarıdaki gibi işlem.

`result := ABB100F81E53E10E`

Daha sonra, `MAC(K_H, "")` yukarıdaki gibi VERILEN HMACSHA1 için işlem `K_H` .

`result := 76EB189B35CF03461DDF877CD9F4B1B4D63A7555`

Bu, aşağıda gösterildiği gibi, kimliği doğrulanmış şifreleme algoritması çiftinin (3DES-192-CBC şifrelemesi + HMACSHA1 doğrulaması) bir parmak izi olan tam bağlam üstbilgisini üretir

```
00 00 00 00 00 18 00 00 00 08 00 00 00 14 00 00
00 14 AB B1 00 F8 1E 53 E1 0E 76 EB 18 9B 35 CF
03 46 1D DF 87 7C D9 F4 B1 B4 D6 3A 75 55
```

Bileşenler aşağıdaki gibi kesilir:

* işaretleyici`(00 00)`

* blok şifre anahtar uzunluğu`(00 00 00 18)`

* blok şifre blok boyutu`(00 00 00 08)`

* HMAC anahtar uzunluğu`(00 00 00 14)`

* HMAC Özet boyutu`(00 00 00 14)`

* blok şifre PRP çıkışı `(AB B1 - E1 0E)` ve

* HMAC PRF çıkışı `(76 EB - end)` .

## <a name="galoiscounter-mode-encryption--authentication"></a>Galoa/sayaç modu şifreleme + kimlik doğrulama

Bağlam üst bilgisi aşağıdaki bileşenlerden oluşur:

* [16 bit] Bir işaret olan 00 01 değeri, "GCM şifreleme + kimlik doğrulama" anlamına gelir.

* [32 bit] Simetrik blok şifreleme algoritmasının anahtar uzunluğu (bayt cinsinden, Big-endian).

* [32 bit] Kimliği doğrulanmış şifreleme işlemleri sırasında kullanılan nonce boyutu (bayt cinsinden, Big-endian). (Sistemimiz için bu, nonce boyut = 96 bitde düzeltilir.)

* [32 bit] Simetrik blok şifreleme algoritmasının blok boyutu (bayt cinsinden, Big-endian). (GCM için, bu, blok boyutu = 128 bitde düzeltilir.)

* [32 bit] Kimliği doğrulanmış şifreleme işlevi tarafından üretilen kimlik doğrulama etiketi boyutu (bayt, Big-endian). (Sistemimizde bu, etiket boyutu = 128 bitde düzeltilir.)

* [128 bit] `Enc_GCM (K_E, nonce, "")`Simetrik blok şifreleme algoritmasının çıkışı boş bir dize girişi, ve nonce 'nin 96 bitlik bir tamamen sıfır vektörü olduğu, ' nin etiketi.

`K_E`, CBC şifreleme + HMAC kimlik doğrulama senaryosunda aynı mekanizmayı kullanarak türetilir. Ancak burada hiçbir yürütme olmadığından, `K_H` temelde bu, `| K_H | = 0` algoritma aşağıdaki biçime daraltılır.

`K_E = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")`

### <a name="example-aes-256-gcm"></a>Örnek: AES-256-GCM

Birinci, Let `K_E = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")` , burada `| K_E | = 256 bits` .

`K_E := 22BC6F1B171C08C4AE2F27444AF8FC8B3087A90006CAEA91FDCFB47C1B8733B8`

Ardından, `Enc_GCM (K_E, nonce, "")` yukarıda BELIRTILEN AES-256-GCM için kimlik doğrulama etiketini hesaplar `nonce = 096` `K_E` .

`result := E7DCCE66DF855A323A6BB7BD7A59BE45`

Bu, aşağıdaki tam bağlam üstbilgisini üretir:

```
00 01 00 00 00 20 00 00 00 0C 00 00 00 10 00 00
00 10 E7 DC CE 66 DF 85 5A 32 3A 6B B7 BD 7A 59
BE 45
```

Bileşenler aşağıdaki gibi kesilir:

* işaretleyici`(00 01)`

* blok şifre anahtar uzunluğu`(00 00 00 20)`

* nonce boyutu`(00 00 00 0C)`

* blok şifre blok boyutu`(00 00 00 10)`

* kimlik doğrulama etiketi boyutu `(00 00 00 10)` ve

* blok şifresi çalıştıran kimlik doğrulama etiketi `(E7 DC - end)` .
