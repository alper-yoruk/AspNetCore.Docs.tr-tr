---
title: ASP.NET Core'da anahtar depolama biçimi
author: rick-anderson
description: ASP.NET Çekirdek Veri Koruması anahtar depolama biçiminin uygulama ayrıntılarını öğrenin.
ms.author: riande
ms.date: 04/08/2020
uid: security/data-protection/implementation/key-storage-format
ms.openlocfilehash: 3072c673791b589027a910b80eaba52052eb9311
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80976943"
---
# <a name="key-storage-format-in-aspnet-core"></a>ASP.NET Core'da anahtar depolama biçimi

<a name="data-protection-implementation-key-storage-format"></a>

Nesneler XML gösteriminde istirahatte depolanır. Anahtar depolama için varsayılan dizin:

* Windows: *%LOCALAPPDATA%\ASP.NET\DataProtection-Keys\*
* macOS / Linux: *$HOME/.aspnet/DataProtection-Keys*

## <a name="the-key-element"></a>Anahtar \<> öğesi

Anahtarlar, anahtar deposundaki üst düzey nesneler olarak bulunur. Kural kuralları tuşlarına göre dosya adı **tuşu-{guid}.xml**, {guid} tuşunun kimliğidir. Bu tür her dosya tek bir anahtar içerir. Dosyanın biçimi aşağıdaki gibidir.

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

Anahtar \<> öğesi aşağıdaki öznitelikleri ve alt öğeleri içerir:

* Anahtar kimliği. Bu değer yetkili olarak kabul edilir; dosya adı sadece insan okunabilirlik için bir incelik olduğunu.

* \<Anahtar> öğesinin sürümü, şu anda 1 sabit.

* Anahtarın oluşturulması, etkinleştirme ve son kullanma tarihleri.

* Bu \<anahtar içinde bulunan kimlik doğrulama şifreleme uygulaması hakkında bilgi içeren bir tanımlayıcı> öğesi.

Yukarıdaki örnekte, anahtarın kimliği {80732141-ec8f-4b80-af9c-c4d2d1ff8901}, 19 Mart 2015 tarihinde oluşturuldu ve etkinleştirildi ve 90 günlük bir ömrü vardır. (Bazen etkinleştirme tarihi bu örnekte olduğu gibi oluşturma tarihinden biraz önce olabilir. Bunun nedeni, API'lerin nasıl çalıştığı ve uygulamada zararsız olduğu bir nit'dir.)

## <a name="the-descriptor-element"></a>\<Tanımlayıcı> öğesi

Dış \<tanımlayıcı> öğesi iAuthenticatedEncryptorDescriptizer uygulayan bir türün montaj nitelikli adı olan bir öznitelik deserializerType içerir. Bu tür iç \<tanımlayıcı> öğesi okuma ve içerdiği bilgileri ayrıştırma sorumludur.

\<Tanımlayıcı> öğesinin belirli biçimi, anahtar tarafından kapsüllenen kimlik doğrulamaşifreleme uygulamasına bağlıdır ve her deserializer türü bunun için biraz farklı bir biçim bekler. Genel olarak, ancak, bu öğe algoritmik bilgi (adlar, türleri, OSB veya benzeri) ve gizli anahtar malzeme içerir. Yukarıdaki örnekte, tanımlayıcı bu anahtarın AES-256-CBC şifreleme + HMACSHA256 doğrulaması sarar belirtir.

## <a name="the-encryptedsecret-element"></a>ŞifreliGizli \<> öğesi

Gizli anahtar materyalinin şifreli biçimini içeren bir ** &lt;şifreli Gizli&gt; ** [öğe, sırlara karşı şifreleme etkinleştirilmişse](xref:security/data-protection/implementation/key-encryption-at-rest)mevcut olabilir. Öznitelik `decryptorType` [IXmlDecryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmldecryptor)uygulayan bir türün montaj nitelikli adıdır. Bu tür, iç ** &lt;şifreli Anahtar&gt; ** öğesini okumaktan ve özgün düz metni kurtarmak için şifresini çözmekten sorumludur.

Olduğu `<descriptor>`gibi, öğenin `<encryptedSecret>` belirli biçimi kullanılan istirahat şifreleme mekanizmasına bağlıdır. Yukarıdaki örnekte, ana anahtar yorum başına Windows DPAPI kullanılarak şifrelenir.

## <a name="the-revocation-element"></a>İptal \<> öğesi

İptaller, anahtar deposundaki üst düzey nesneler olarak bulunur. Sözleşme iptalleri tarafından dosya adı **iptali-{timestamp}.xml** (belirli bir tarihten önce tüm anahtarları iptal etmek için) veya **iptal-{guid}.xml** (belirli bir anahtarı iptal etmek için). Her dosya tek \<bir iptal> öğesi içerir.

Tek tek anahtarların iptali için dosya içeriği aşağıdaki gibi olacaktır.

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T22:45:30.2616742Z</revocationDate>
  <key id="eb4fc299-8808-409d-8a34-23fc83d026c9" />
  <reason>human-readable reason</reason>
</revocation>
```

Bu durumda, yalnızca belirtilen anahtar iptal edilir. Ancak, anahtar kimliği "*" ise, aşağıdaki örnekte olduğu gibi, oluşturma tarihi belirtilen iptal tarihinden önce olan tüm anahtarlar iptal edilir.

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T15:45:45.7366491-07:00</revocationDate>
  <!-- All keys created before the revocation date are revoked. -->
  <key id="*" />
  <reason>human-readable reason</reason>
</revocation>
```

> \<öğenin nedeni sistem tarafından asla okunmaz. Bu sadece iptal için insan tarafından okunabilir bir neden saklamak için uygun bir yerdir.
