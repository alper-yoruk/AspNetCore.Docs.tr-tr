---
title: ASP.NET Core 'de karma parolalar
author: rick-anderson
description: ASP.NET Core veri koruma API 'Lerini kullanarak parolaların karmasını öğrenin.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/consumer-apis/password-hashing
ms.openlocfilehash: a970d44a1ca6b9f3534bddb34b037e7c2fdc5389
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051869"
---
# <a name="hash-passwords-in-aspnet-core"></a>ASP.NET Core 'de karma parolalar

Veri koruma kodu tabanı, şifreleme anahtar türetme işlevlerini içeren *Microsoft. AspNetCore. Cryptography. Keytüretme* paketini içerir. Bu paket tek başına bir bileşendir ve veri koruma sisteminin geri kalanı üzerinde hiçbir bağımlılığı yoktur. Tamamen bağımsız olarak kullanılabilir. Kaynak, bir kolaylık olarak veri koruma kodu tabanının yanında bulunur.

Paket şu anda `KeyDerivation.Pbkdf2` [PBKDF2 algoritmasını](https://tools.ietf.org/html/rfc2898#section-5.2)kullanarak bir parolayı karma olarak sağlayan bir yöntem sunmaktadır. Bu API .NET Framework var olan [Rfc2898DeriveBytes türüne](/dotnet/api/system.security.cryptography.rfc2898derivebytes)çok benzer, ancak üç önemli ayrımlar vardır:

1. `KeyDerivation.Pbkdf2`Yöntemi birden çok PRFs (Şu anda `HMACSHA1` , ve) kullanmayı destekler `HMACSHA256` `HMACSHA512` , ancak `Rfc2898DeriveBytes` tür yalnızca destekler `HMACSHA1` .

2. `KeyDerivation.Pbkdf2`Yöntemi, geçerli işletim sistemini algılar ve yordamın en iyi duruma getirilmiş uygulamasını seçme girişimlerini, belirli durumlarda çok daha iyi performans sağlar. (Windows 8 ' de, 10 ' un aktarım hızını yaklaşık olarak sunar `Rfc2898DeriveBytes` .)

3. `KeyDerivation.Pbkdf2`Yöntemi, çağıranın tüm parametreleri (anahtar, prf ve yineleme sayısı) belirtmesini gerektirir. `Rfc2898DeriveBytes`Türü bunlar için varsayılan değerleri sağlar.

[!code-csharp[](password-hashing/samples/passwordhasher.cs)]

[source code](https://github.com/dotnet/AspNetCore/blob/master/src/Identity/Extensions.Core/src/PasswordHasher.cs) ASP.NET Core Identity `PasswordHasher` Gerçek dünya kullanım örneği için bkz. kaynak kodu.
