---
title: ASP.NET Core 'de anahtar depolama biçimi
author: rick-anderson
description: ASP.NET Core veri koruma anahtarı depolama biçiminin uygulama ayrıntılarını öğrenin.
ms.author: riande
ms.date: 04/08/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/implementation/key-storage-format
ms.openlocfilehash: 032b3f9ccea2ae361a8f2fd12538ffb901310247
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408909"
---
# <a name="key-storage-format-in-aspnet-core"></a><span data-ttu-id="b76a1-103">ASP.NET Core 'de anahtar depolama biçimi</span><span class="sxs-lookup"><span data-stu-id="b76a1-103">Key storage format in ASP.NET Core</span></span>

<a name="data-protection-implementation-key-storage-format"></a>

<span data-ttu-id="b76a1-104">Nesneler, XML gösteriminde Rest olarak depolanır.</span><span class="sxs-lookup"><span data-stu-id="b76a1-104">Objects are stored at rest in XML representation.</span></span> <span data-ttu-id="b76a1-105">Anahtar depolaması için varsayılan dizin:</span><span class="sxs-lookup"><span data-stu-id="b76a1-105">The default directory for key storage is:</span></span>

* <span data-ttu-id="b76a1-106">Windows: \*%LOCALAPPDATA%\ASP.NET\DataProtection-Keys\*</span><span class="sxs-lookup"><span data-stu-id="b76a1-106">Windows: \*%LOCALAPPDATA%\ASP.NET\DataProtection-Keys\*</span></span>
* <span data-ttu-id="b76a1-107">macOS/Linux: *$Home/PST ASPNET/DataProtection-Keys*</span><span class="sxs-lookup"><span data-stu-id="b76a1-107">macOS / Linux: *$HOME/.aspnet/DataProtection-Keys*</span></span>

## <a name="the-key-element"></a><span data-ttu-id="b76a1-108">\<key>Öğe</span><span class="sxs-lookup"><span data-stu-id="b76a1-108">The \<key> element</span></span>

<span data-ttu-id="b76a1-109">Anahtarlar, anahtar deposundaki üst düzey nesneler olarak mevcuttur.</span><span class="sxs-lookup"><span data-stu-id="b76a1-109">Keys exist as top-level objects in the key repository.</span></span> <span data-ttu-id="b76a1-110">Kural anahtarlarına göre anahtar adı **-{Guid}. xml**, burada {GUID} anahtar kimliğidir.</span><span class="sxs-lookup"><span data-stu-id="b76a1-110">By convention keys have the filename **key-{guid}.xml**, where {guid} is the id of the key.</span></span> <span data-ttu-id="b76a1-111">Bu tür dosyalar tek bir anahtar içerir.</span><span class="sxs-lookup"><span data-stu-id="b76a1-111">Each such file contains a single key.</span></span> <span data-ttu-id="b76a1-112">Dosya biçimi aşağıdaki gibidir.</span><span class="sxs-lookup"><span data-stu-id="b76a1-112">The format of the file is as follows.</span></span>

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

<span data-ttu-id="b76a1-113">\<key>Öğesi aşağıdaki öznitelikleri ve alt öğeleri içerir:</span><span class="sxs-lookup"><span data-stu-id="b76a1-113">The \<key> element contains the following attributes and child elements:</span></span>

* <span data-ttu-id="b76a1-114">Anahtar kimliği. Bu değer yetkili olarak değerlendirilir; dosya adı, insan okunabilirlik için yalnızca bir nicsan.</span><span class="sxs-lookup"><span data-stu-id="b76a1-114">The key id. This value is treated as authoritative; the filename is simply a nicety for human readability.</span></span>

* <span data-ttu-id="b76a1-115">\<key>Şu anda 1 ' de düzeltilen öğenin sürümü.</span><span class="sxs-lookup"><span data-stu-id="b76a1-115">The version of the \<key> element, currently fixed at 1.</span></span>

* <span data-ttu-id="b76a1-116">Anahtarın oluşturulma, etkinleştirme ve sona erme tarihleri.</span><span class="sxs-lookup"><span data-stu-id="b76a1-116">The key's creation, activation, and expiration dates.</span></span>

* <span data-ttu-id="b76a1-117">\<descriptor>Bu anahtarda yer alan kimliği doğrulanmış şifreleme uygulamasıyla ilgili bilgileri içeren bir öğesi.</span><span class="sxs-lookup"><span data-stu-id="b76a1-117">A \<descriptor> element, which contains information on the authenticated encryption implementation contained within this key.</span></span>

<span data-ttu-id="b76a1-118">Yukarıdaki örnekte, anahtarın kimliği {80732141-EC8F-4b80-af9c-c4d2d1ff8901}, 19 Mart 2015 ' de oluşturulmuştur ve etkinleştirilir ve bu süre için gün 90 ömrü vardır.</span><span class="sxs-lookup"><span data-stu-id="b76a1-118">In the above example, the key's id is {80732141-ec8f-4b80-af9c-c4d2d1ff8901}, it was created and activated on March 19, 2015, and it has a lifetime of 90 days.</span></span> <span data-ttu-id="b76a1-119">(Bazen etkinleştirme tarihi, bu örnekte olduğu gibi, oluşturma tarihinden biraz önce olabilir.</span><span class="sxs-lookup"><span data-stu-id="b76a1-119">(Occasionally the activation date might be slightly before the creation date as in this example.</span></span> <span data-ttu-id="b76a1-120">Bunun nedeni, API 'Lerin çalıştığı ve çok zararsız olan bir NBT yöntemidir.)</span><span class="sxs-lookup"><span data-stu-id="b76a1-120">This is due to a nit in how the APIs work and is harmless in practice.)</span></span>

## <a name="the-descriptor-element"></a><span data-ttu-id="b76a1-121">\<descriptor>Öğe</span><span class="sxs-lookup"><span data-stu-id="b76a1-121">The \<descriptor> element</span></span>

<span data-ttu-id="b76a1-122">Dıştaki \<descriptor> öğe, ıauthenticatedencryptordescriptordeserializer uygulayan bir türün derleme nitelikli adı olan bir öznitelik deserializerType içerir.</span><span class="sxs-lookup"><span data-stu-id="b76a1-122">The outer \<descriptor> element contains an attribute deserializerType, which is the assembly-qualified name of a type which implements IAuthenticatedEncryptorDescriptorDeserializer.</span></span> <span data-ttu-id="b76a1-123">Bu tür, iç öğe okumanın \<descriptor> ve içinde yer alan bilgilerin ayrıştırılmasından sorumludur.</span><span class="sxs-lookup"><span data-stu-id="b76a1-123">This type is responsible for reading the inner \<descriptor> element and for parsing the information contained within.</span></span>

<span data-ttu-id="b76a1-124">Öğesinin belirli biçimi, \<descriptor> anahtarla kapsüllenmiş kimliği doğrulanmış Şifreleyici uygulamasına bağlıdır ve her bir seri hale getirici türü bunun için biraz farklı bir biçim bekler.</span><span class="sxs-lookup"><span data-stu-id="b76a1-124">The particular format of the \<descriptor> element depends on the authenticated encryptor implementation encapsulated by the key, and each deserializer type expects a slightly different format for this.</span></span> <span data-ttu-id="b76a1-125">Genellikle, bu öğe algoritmik bilgilerini (adlar, türler, OID 'ler veya benzer) ve gizli anahtar malzemesini içerir.</span><span class="sxs-lookup"><span data-stu-id="b76a1-125">In general, though, this element will contain algorithmic information (names, types, OIDs, or similar) and secret key material.</span></span> <span data-ttu-id="b76a1-126">Yukarıdaki örnekte, tanımlayıcı bu anahtarın AES-256-CBC şifreleme + HMACSHA256 doğrulamasını kaydırılacağını belirtir.</span><span class="sxs-lookup"><span data-stu-id="b76a1-126">In the above example, the descriptor specifies that this key wraps AES-256-CBC encryption + HMACSHA256 validation.</span></span>

## <a name="the-encryptedsecret-element"></a><span data-ttu-id="b76a1-127">\<encryptedSecret>Öğe</span><span class="sxs-lookup"><span data-stu-id="b76a1-127">The \<encryptedSecret> element</span></span>

<span data-ttu-id="b76a1-128">[Rest üzerinde gizli dizi şifrelemesi etkinse](xref:security/data-protection/implementation/key-encryption-at-rest)gizli anahtar malzemesinin şifrelenmiş biçimini içeren bir \*\* &lt; &gt; encryptedsecret\*\* öğesi mevcut olabilir.</span><span class="sxs-lookup"><span data-stu-id="b76a1-128">An **&lt;encryptedSecret&gt;** element which contains the encrypted form of the secret key material may be present if [encryption of secrets at rest is enabled](xref:security/data-protection/implementation/key-encryption-at-rest).</span></span> <span data-ttu-id="b76a1-129">Özniteliği, `decryptorType` [ıxmldecryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmldecryptor)uygulayan bir türün derleme nitelikli adıdır.</span><span class="sxs-lookup"><span data-stu-id="b76a1-129">The attribute `decryptorType` is the assembly-qualified name of a type which implements [IXmlDecryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmldecryptor).</span></span> <span data-ttu-id="b76a1-130">Bu tür, iç \*\* &lt; EncryptedKey &gt; \*\* öğesini okumaktan ve özgün düz metin kurtarmak için şifresini çözmekten sorumludur.</span><span class="sxs-lookup"><span data-stu-id="b76a1-130">This type is responsible for reading the inner **&lt;encryptedKey&gt;** element and decrypting it to recover the original plaintext.</span></span>

<span data-ttu-id="b76a1-131">' De olduğu gibi `<descriptor>` , öğesinin belirli biçimi `<encryptedSecret>` kullanımda olan Rest şifreleme mekanizmasına bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="b76a1-131">As with `<descriptor>`, the particular format of the `<encryptedSecret>` element depends on the at-rest encryption mechanism in use.</span></span> <span data-ttu-id="b76a1-132">Yukarıdaki örnekte, ana anahtar açıklama başına Windows DPAPI kullanılarak şifrelenir.</span><span class="sxs-lookup"><span data-stu-id="b76a1-132">In the above example, the master key is encrypted using Windows DPAPI per the comment.</span></span>

## <a name="the-revocation-element"></a><span data-ttu-id="b76a1-133">\<revocation>Öğe</span><span class="sxs-lookup"><span data-stu-id="b76a1-133">The \<revocation> element</span></span>

<span data-ttu-id="b76a1-134">İptal edilecek öğeler, anahtar deposundaki en üst düzey nesneler olarak mevcuttur.</span><span class="sxs-lookup"><span data-stu-id="b76a1-134">Revocations exist as top-level objects in the key repository.</span></span> <span data-ttu-id="b76a1-135">Kural iptalinde, dosya adı **iptali-{timestamp}. xml** (belirli bir tarihten önceki tüm anahtarları iptal etmek için) veya **iptal-{GUID}. xml** (belirli bir anahtarı iptal etmek için).</span><span class="sxs-lookup"><span data-stu-id="b76a1-135">By convention revocations have the filename **revocation-{timestamp}.xml** (for revoking all keys before a specific date) or **revocation-{guid}.xml** (for revoking a specific key).</span></span> <span data-ttu-id="b76a1-136">Her dosya tek bir \<revocation> öğe içerir.</span><span class="sxs-lookup"><span data-stu-id="b76a1-136">Each file contains a single \<revocation> element.</span></span>

<span data-ttu-id="b76a1-137">Tek tek anahtarların iptal edilmesi için dosya içerikleri aşağıdaki gibi olacaktır.</span><span class="sxs-lookup"><span data-stu-id="b76a1-137">For revocations of individual keys, the file contents will be as below.</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T22:45:30.2616742Z</revocationDate>
  <key id="eb4fc299-8808-409d-8a34-23fc83d026c9" />
  <reason>human-readable reason</reason>
</revocation>
```

<span data-ttu-id="b76a1-138">Bu durumda, yalnızca belirtilen anahtar iptal edilir.</span><span class="sxs-lookup"><span data-stu-id="b76a1-138">In this case, only the specified key is revoked.</span></span> <span data-ttu-id="b76a1-139">Anahtar kimliği "\*" ise, ancak aşağıdaki örnekte olduğu gibi, oluşturma tarihi belirtilen iptal tarihi 'nden önce olan tüm anahtarlar iptal edilir.</span><span class="sxs-lookup"><span data-stu-id="b76a1-139">If the key id is "\*", however, as in the below example, all keys whose creation date is prior to the specified revocation date are revoked.</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T15:45:45.7366491-07:00</revocationDate>
  <!-- All keys created before the revocation date are revoked. -->
  <key id="*" />
  <reason>human-readable reason</reason>
</revocation>
```

<span data-ttu-id="b76a1-140">\<reason>Öğe hiçbir şekilde sistem tarafından okunamaz.</span><span class="sxs-lookup"><span data-stu-id="b76a1-140">The \<reason> element is never read by the system.</span></span> <span data-ttu-id="b76a1-141">Yalnızca bir iptal için okunabilir bir neden depolamak için kullanışlı bir yerdir.</span><span class="sxs-lookup"><span data-stu-id="b76a1-141">It's simply a convenient place to store a human-readable reason for revocation.</span></span>
