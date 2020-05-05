---
title: ASP.NET Core 'de karma parolalar
author: rick-anderson
description: ASP.NET Core veri koruma API 'Lerini kullanarak parolaların karmasını öğrenin.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/consumer-apis/password-hashing
ms.openlocfilehash: 6a5e0e4378241671905f2a759aad88372901e7d2
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82769786"
---
# <a name="hash-passwords-in-aspnet-core"></a>ASP.NET Core 'de karma parolalar

Veri koruma kodu tabanı, şifreleme anahtar türetme işlevlerini içeren *Microsoft. AspNetCore. Cryptography. Keytüretme* paketini içerir. Bu paket tek başına bir bileşendir ve veri koruma sisteminin geri kalanı üzerinde hiçbir bağımlılığı yoktur. Tamamen bağımsız olarak kullanılabilir. Kaynak, bir kolaylık olarak veri koruma kodu tabanının yanında bulunur.

Paket şu anda [PBKDF2 algoritmasını](https://tools.ietf.org/html/rfc2898#section-5.2)kullanarak `KeyDerivation.Pbkdf2` bir parolayı karma olarak sağlayan bir yöntem sunmaktadır. Bu API .NET Framework var olan [Rfc2898DeriveBytes türüne](/dotnet/api/system.security.cryptography.rfc2898derivebytes)çok benzer, ancak üç önemli ayrımlar vardır:

1. `KeyDerivation.Pbkdf2` Yöntemi birden çok prfs ( `HMACSHA1`Şu anda, `HMACSHA256`ve `HMACSHA512`) kullanmayı destekler, ancak `Rfc2898DeriveBytes` tür yalnızca destekler. `HMACSHA1`

2. `KeyDerivation.Pbkdf2` Yöntemi, geçerli işletim sistemini algılar ve yordamın en iyi duruma getirilmiş uygulamasını seçme girişimlerini, belirli durumlarda çok daha iyi performans sağlar. (Windows 8 ' de, 10 ' un `Rfc2898DeriveBytes`aktarım hızını yaklaşık olarak sunar.)

3. `KeyDerivation.Pbkdf2` Yöntemi, çağıranın tüm parametreleri (anahtar, prf ve yineleme sayısı) belirtmesini gerektirir. `Rfc2898DeriveBytes` Türü bunlar için varsayılan değerleri sağlar.

[!code-csharp[](password-hashing/samples/passwordhasher.cs)]

Gerçek dünya kullanım örneği için ASP.NET Core Identity `PasswordHasher` türü için [kaynak koda](https://github.com/dotnet/AspNetCore/blob/master/src/Identity/Extensions.Core/src/PasswordHasher.cs) bakın.
