---
title: ASP.NET Core 'de anahtar depolama biçimi
author: rick-anderson
description: ASP.NET Core veri koruma anahtarı depolama biçiminin uygulama ayrıntılarını öğrenin.
ms.author: riande
ms.date: 04/08/2020
no-loc:
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
uid: security/data-protection/implementation/key-storage-format
ms.openlocfilehash: daf86d3e3357d42ddad74d5e2f06e00e0e24db07
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88631998"
---
# <a name="key-storage-format-in-aspnet-core"></a>ASP.NET Core 'de anahtar depolama biçimi

<a name="data-protection-implementation-key-storage-format"></a>

Nesneler, XML gösteriminde Rest olarak depolanır. Anahtar depolaması için varsayılan dizin:

* Windows: *%LOCALAPPDATA%\ASP.NET\DataProtection-Keys\*
* macOS/Linux: *$Home/PST ASPNET/DataProtection-Keys*

## <a name="the-key-element"></a>\<key>Öğe

Anahtarlar, anahtar deposundaki üst düzey nesneler olarak mevcuttur. Kural anahtarlarına göre anahtar adı **-{Guid}. xml**, burada {GUID} anahtar kimliğidir. Bu tür dosyalar tek bir anahtar içerir. Dosya biçimi aşağıdaki gibidir.

```xml
<?xml version="1.0" encoding="utf-8"?>
<key id="80732141-ec8f-4b80-af9c-c4d2d1ff8901" version="1">
  <creationDate>2015-03-19T23:32:02.3949887Z</creationDate>
  <activationDate>2015-03-19T23:32:02.3839429Z</activationDate>
  <expirationDate>2015-06-17T23:32:02.3839429Z</expirationDate>
  <descriptor deserializerType="{deserializerType}">
    <descriptor>
      <encryption algorithm="AES_256_CBC" />
      <validation algorithm="HMACSHA256" />
      <enc:encryptedSecret decryptorType="{decryptorType}" xmlns:enc="...">
        <encryptedKey>
          <!-- This key is encrypted with Windows DPAPI. -->
          <value>AQAAANCM...8/zeP8lcwAg==</value>
        </encryptedKey>
      </enc:encryptedSecret>
    </descriptor>
  </descriptor>
</key>
```

\<key>Öğesi aşağıdaki öznitelikleri ve alt öğeleri içerir:

* Anahtar kimliği. Bu değer yetkili olarak değerlendirilir; dosya adı, insan okunabilirlik için yalnızca bir nicsan.

* \<key>Şu anda 1 ' de düzeltilen öğenin sürümü.

* Anahtarın oluşturulma, etkinleştirme ve sona erme tarihleri.

* \<descriptor>Bu anahtarda yer alan kimliği doğrulanmış şifreleme uygulamasıyla ilgili bilgileri içeren bir öğesi.

Yukarıdaki örnekte, anahtarın kimliği {80732141-EC8F-4b80-af9c-c4d2d1ff8901}, 19 Mart 2015 ' de oluşturulmuştur ve etkinleştirilir ve bu süre için gün 90 ömrü vardır. (Bazen etkinleştirme tarihi, bu örnekte olduğu gibi, oluşturma tarihinden biraz önce olabilir. Bunun nedeni, API 'Lerin çalıştığı ve çok zararsız olan bir NBT yöntemidir.)

## <a name="the-descriptor-element"></a>\<descriptor>Öğe

Dıştaki \<descriptor> öğe, ıauthenticatedencryptordescriptordeserializer uygulayan bir türün derleme nitelikli adı olan bir öznitelik deserializerType içerir. Bu tür, iç öğe okumanın \<descriptor> ve içinde yer alan bilgilerin ayrıştırılmasından sorumludur.

Öğesinin belirli biçimi, \<descriptor> anahtarla kapsüllenmiş kimliği doğrulanmış Şifreleyici uygulamasına bağlıdır ve her bir seri hale getirici türü bunun için biraz farklı bir biçim bekler. Genellikle, bu öğe algoritmik bilgilerini (adlar, türler, OID 'ler veya benzer) ve gizli anahtar malzemesini içerir. Yukarıdaki örnekte, tanımlayıcı bu anahtarın AES-256-CBC şifreleme + HMACSHA256 doğrulamasını kaydırılacağını belirtir.

## <a name="the-encryptedsecret-element"></a>\<encryptedSecret>Öğe

[Rest üzerinde gizli dizi şifrelemesi etkinse](xref:security/data-protection/implementation/key-encryption-at-rest)gizli anahtar malzemesinin şifrelenmiş biçimini içeren bir ** &lt; &gt; encryptedsecret** öğesi mevcut olabilir. Özniteliği, `decryptorType` [ıxmldecryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmldecryptor)uygulayan bir türün derleme nitelikli adıdır. Bu tür, iç ** &lt; EncryptedKey &gt; ** öğesini okumaktan ve özgün düz metin kurtarmak için şifresini çözmekten sorumludur.

' De olduğu gibi `<descriptor>` , öğesinin belirli biçimi `<encryptedSecret>` kullanımda olan Rest şifreleme mekanizmasına bağlıdır. Yukarıdaki örnekte, ana anahtar açıklama başına Windows DPAPI kullanılarak şifrelenir.

## <a name="the-revocation-element"></a>\<revocation>Öğe

İptal edilecek öğeler, anahtar deposundaki en üst düzey nesneler olarak mevcuttur. Kural iptalinde, dosya adı **iptali-{timestamp}. xml** (belirli bir tarihten önceki tüm anahtarları iptal etmek için) veya **iptal-{GUID}. xml** (belirli bir anahtarı iptal etmek için). Her dosya tek bir \<revocation> öğe içerir.

Tek tek anahtarların iptal edilmesi için dosya içerikleri aşağıdaki gibi olacaktır.

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T22:45:30.2616742Z</revocationDate>
  <key id="eb4fc299-8808-409d-8a34-23fc83d026c9" />
  <reason>human-readable reason</reason>
</revocation>
```

Bu durumda, yalnızca belirtilen anahtar iptal edilir. Anahtar kimliği "*" ise, ancak aşağıdaki örnekte olduğu gibi, oluşturma tarihi belirtilen iptal tarihi 'nden önce olan tüm anahtarlar iptal edilir.

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T15:45:45.7366491-07:00</revocationDate>
  <!-- All keys created before the revocation date are revoked. -->
  <key id="*" />
  <reason>human-readable reason</reason>
</revocation>
```

\<reason>Öğe hiçbir şekilde sistem tarafından okunamaz. Yalnızca bir iptal için okunabilir bir neden depolamak için kullanışlı bir yerdir.
