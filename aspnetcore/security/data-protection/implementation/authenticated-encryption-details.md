---
title: ASP.NET Core kimliği doğrulanmış şifreleme ayrıntıları
author: rick-anderson
description: ASP.NET Core veri koruma kimliği doğrulanmış şifrelemenin uygulama ayrıntılarını öğrenin.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/implementation/authenticated-encryption-details
ms.openlocfilehash: 3066cd505781ed2ddad46626dda9d9ce35307877
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776974"
---
# <a name="authenticated-encryption-details-in-aspnet-core"></a>ASP.NET Core kimliği doğrulanmış şifreleme ayrıntıları

<a name="data-protection-implementation-authenticated-encryption-details"></a>

Idataprotector. Protect çağrısı kimliği doğrulanmış şifreleme operasyonlardır. Koru yöntemi hem gizlilik hem de özgünlük sağlar ve bu belirli ıdataprotector örneğini kök ıdataprotectionprovider 'dan türetmede kullanılan amaç zincirine bağlıdır.

Idataprotector. Protect bir Byte [] düz metin parametresi alır ve biçimi aşağıda açıklanan bir Byte [] korumalı yük üretir. (Ayrıca bir dize düz metin parametresi alan ve bir dize korumalı yük döndüren bir genişletme yöntemi aşırı yüklemesi de vardır. Bu API kullanılıyorsa, korumalı yük biçimi yine de aşağıdaki yapıya sahip olur, ancak [base64url-Encoded](https://tools.ietf.org/html/rfc4648#section-5)olur.)

## <a name="protected-payload-format"></a>Korumalı yük biçimi

Korumalı yük biçimi üç ana bileşenden oluşur:

* Veri koruma sisteminin sürümünü tanımlayan 32 bitlik bir sihirli üst bilgi.

* Bu belirli yükü korumak için kullanılan anahtarı tanımlayan 128 bitlik bir anahtar kimliği.

* Korunan yükün geri kalanı, [Bu anahtarla kapsüllenmiş Şifreleyici 'ye özgüdür](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation). Aşağıdaki örnekte, anahtar AES-256-CBC + HMACSHA256 Şifreleyici 'yi temsil eder ve yük daha sonra aşağıdaki gibi bölünür:
  * 128 bitlik bir anahtar değiştiricisi.
  * 128 bitlik bir başlatma vektörü.
  * 48 bayt AES-256-CBC çıkışı.
  * Bir HMACSHA256 kimlik doğrulama etiketi.

Örnek korumalı yük aşağıda gösterilmiştir.

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

İlk 32 bitin üzerindeki yük biçiminden veya 4 bayt sürümü tanımlayan sihirli üst bilgi (09 F0 C9 F0)

Sonraki 128 bit veya 16 bayt anahtar tanımlayıcısıdır (80 9C 81 0C 19 66 19 40 95 36 53 F8 AA FF EE 57)

Kalan, yükü içerir ve kullanılan biçime özeldir.

> [!WARNING]
> Belirli bir anahtara korunan tüm yüklerin aynı 20 baytlık (sihirli değer, anahtar kimliği) üst bilgisi ile başlaması gerekir. Yöneticiler bu olguyu, bir yükün ne zaman oluşturulduğunu yaklaşık olarak tanılama amacıyla kullanabilir. Örneğin, yukarıdaki yük {0c819c80-6619-4019-9536-53f8aaffee57} anahtarına karşılık gelir. Anahtar deposunu denetledikten sonra, bu anahtarın etkinleştirme tarihinin 2015-01-01 olduğunu ve sona erme tarihi 2015-03-01 olduğunu fark ederseniz, bu pencerede yükün (üzerinde oynanmadıysa) Bu pencerede oluşturulduğunu varsaymak mantıklı olur.
