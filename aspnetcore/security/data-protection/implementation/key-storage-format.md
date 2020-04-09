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
# <a name="key-storage-format-in-aspnet-core"></a><span data-ttu-id="49073-103">ASP.NET Core'da anahtar depolama biçimi</span><span class="sxs-lookup"><span data-stu-id="49073-103">Key storage format in ASP.NET Core</span></span>

<a name="data-protection-implementation-key-storage-format"></a>

<span data-ttu-id="49073-104">Nesneler XML gösteriminde istirahatte depolanır.</span><span class="sxs-lookup"><span data-stu-id="49073-104">Objects are stored at rest in XML representation.</span></span> <span data-ttu-id="49073-105">Anahtar depolama için varsayılan dizin:</span><span class="sxs-lookup"><span data-stu-id="49073-105">The default directory for key storage is:</span></span>

* <span data-ttu-id="49073-106">Windows: \*%LOCALAPPDATA%\ASP.NET\DataProtection-Keys\*</span><span class="sxs-lookup"><span data-stu-id="49073-106">Windows: \*%LOCALAPPDATA%\ASP.NET\DataProtection-Keys\*</span></span>
* <span data-ttu-id="49073-107">macOS / Linux: *$HOME/.aspnet/DataProtection-Keys*</span><span class="sxs-lookup"><span data-stu-id="49073-107">macOS / Linux: *$HOME/.aspnet/DataProtection-Keys*</span></span>

## <a name="the-key-element"></a><span data-ttu-id="49073-108">Anahtar \<> öğesi</span><span class="sxs-lookup"><span data-stu-id="49073-108">The \<key> element</span></span>

<span data-ttu-id="49073-109">Anahtarlar, anahtar deposundaki üst düzey nesneler olarak bulunur.</span><span class="sxs-lookup"><span data-stu-id="49073-109">Keys exist as top-level objects in the key repository.</span></span> <span data-ttu-id="49073-110">Kural kuralları tuşlarına göre dosya adı **tuşu-{guid}.xml**, {guid} tuşunun kimliğidir.</span><span class="sxs-lookup"><span data-stu-id="49073-110">By convention keys have the filename **key-{guid}.xml**, where {guid} is the id of the key.</span></span> <span data-ttu-id="49073-111">Bu tür her dosya tek bir anahtar içerir.</span><span class="sxs-lookup"><span data-stu-id="49073-111">Each such file contains a single key.</span></span> <span data-ttu-id="49073-112">Dosyanın biçimi aşağıdaki gibidir.</span><span class="sxs-lookup"><span data-stu-id="49073-112">The format of the file is as follows.</span></span>

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

<span data-ttu-id="49073-113">Anahtar \<> öğesi aşağıdaki öznitelikleri ve alt öğeleri içerir:</span><span class="sxs-lookup"><span data-stu-id="49073-113">The \<key> element contains the following attributes and child elements:</span></span>

* <span data-ttu-id="49073-114">Anahtar kimliği. Bu değer yetkili olarak kabul edilir; dosya adı sadece insan okunabilirlik için bir incelik olduğunu.</span><span class="sxs-lookup"><span data-stu-id="49073-114">The key id. This value is treated as authoritative; the filename is simply a nicety for human readability.</span></span>

* <span data-ttu-id="49073-115">\<Anahtar> öğesinin sürümü, şu anda 1 sabit.</span><span class="sxs-lookup"><span data-stu-id="49073-115">The version of the \<key> element, currently fixed at 1.</span></span>

* <span data-ttu-id="49073-116">Anahtarın oluşturulması, etkinleştirme ve son kullanma tarihleri.</span><span class="sxs-lookup"><span data-stu-id="49073-116">The key's creation, activation, and expiration dates.</span></span>

* <span data-ttu-id="49073-117">Bu \<anahtar içinde bulunan kimlik doğrulama şifreleme uygulaması hakkında bilgi içeren bir tanımlayıcı> öğesi.</span><span class="sxs-lookup"><span data-stu-id="49073-117">A \<descriptor> element, which contains information on the authenticated encryption implementation contained within this key.</span></span>

<span data-ttu-id="49073-118">Yukarıdaki örnekte, anahtarın kimliği {80732141-ec8f-4b80-af9c-c4d2d1ff8901}, 19 Mart 2015 tarihinde oluşturuldu ve etkinleştirildi ve 90 günlük bir ömrü vardır.</span><span class="sxs-lookup"><span data-stu-id="49073-118">In the above example, the key's id is {80732141-ec8f-4b80-af9c-c4d2d1ff8901}, it was created and activated on March 19, 2015, and it has a lifetime of 90 days.</span></span> <span data-ttu-id="49073-119">(Bazen etkinleştirme tarihi bu örnekte olduğu gibi oluşturma tarihinden biraz önce olabilir.</span><span class="sxs-lookup"><span data-stu-id="49073-119">(Occasionally the activation date might be slightly before the creation date as in this example.</span></span> <span data-ttu-id="49073-120">Bunun nedeni, API'lerin nasıl çalıştığı ve uygulamada zararsız olduğu bir nit'dir.)</span><span class="sxs-lookup"><span data-stu-id="49073-120">This is due to a nit in how the APIs work and is harmless in practice.)</span></span>

## <a name="the-descriptor-element"></a><span data-ttu-id="49073-121">\<Tanımlayıcı> öğesi</span><span class="sxs-lookup"><span data-stu-id="49073-121">The \<descriptor> element</span></span>

<span data-ttu-id="49073-122">Dış \<tanımlayıcı> öğesi iAuthenticatedEncryptorDescriptizer uygulayan bir türün montaj nitelikli adı olan bir öznitelik deserializerType içerir.</span><span class="sxs-lookup"><span data-stu-id="49073-122">The outer \<descriptor> element contains an attribute deserializerType, which is the assembly-qualified name of a type which implements IAuthenticatedEncryptorDescriptorDeserializer.</span></span> <span data-ttu-id="49073-123">Bu tür iç \<tanımlayıcı> öğesi okuma ve içerdiği bilgileri ayrıştırma sorumludur.</span><span class="sxs-lookup"><span data-stu-id="49073-123">This type is responsible for reading the inner \<descriptor> element and for parsing the information contained within.</span></span>

<span data-ttu-id="49073-124">\<Tanımlayıcı> öğesinin belirli biçimi, anahtar tarafından kapsüllenen kimlik doğrulamaşifreleme uygulamasına bağlıdır ve her deserializer türü bunun için biraz farklı bir biçim bekler.</span><span class="sxs-lookup"><span data-stu-id="49073-124">The particular format of the \<descriptor> element depends on the authenticated encryptor implementation encapsulated by the key, and each deserializer type expects a slightly different format for this.</span></span> <span data-ttu-id="49073-125">Genel olarak, ancak, bu öğe algoritmik bilgi (adlar, türleri, OSB veya benzeri) ve gizli anahtar malzeme içerir.</span><span class="sxs-lookup"><span data-stu-id="49073-125">In general, though, this element will contain algorithmic information (names, types, OIDs, or similar) and secret key material.</span></span> <span data-ttu-id="49073-126">Yukarıdaki örnekte, tanımlayıcı bu anahtarın AES-256-CBC şifreleme + HMACSHA256 doğrulaması sarar belirtir.</span><span class="sxs-lookup"><span data-stu-id="49073-126">In the above example, the descriptor specifies that this key wraps AES-256-CBC encryption + HMACSHA256 validation.</span></span>

## <a name="the-encryptedsecret-element"></a><span data-ttu-id="49073-127">ŞifreliGizli \<> öğesi</span><span class="sxs-lookup"><span data-stu-id="49073-127">The \<encryptedSecret> element</span></span>

<span data-ttu-id="49073-128">Gizli anahtar materyalinin şifreli biçimini içeren bir \*\* &lt;şifreli Gizli&gt; \*\* [öğe, sırlara karşı şifreleme etkinleştirilmişse](xref:security/data-protection/implementation/key-encryption-at-rest)mevcut olabilir.</span><span class="sxs-lookup"><span data-stu-id="49073-128">An **&lt;encryptedSecret&gt;** element which contains the encrypted form of the secret key material may be present if [encryption of secrets at rest is enabled](xref:security/data-protection/implementation/key-encryption-at-rest).</span></span> <span data-ttu-id="49073-129">Öznitelik `decryptorType` [IXmlDecryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmldecryptor)uygulayan bir türün montaj nitelikli adıdır.</span><span class="sxs-lookup"><span data-stu-id="49073-129">The attribute `decryptorType` is the assembly-qualified name of a type which implements [IXmlDecryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmldecryptor).</span></span> <span data-ttu-id="49073-130">Bu tür, iç \*\* &lt;şifreli Anahtar&gt; \*\* öğesini okumaktan ve özgün düz metni kurtarmak için şifresini çözmekten sorumludur.</span><span class="sxs-lookup"><span data-stu-id="49073-130">This type is responsible for reading the inner **&lt;encryptedKey&gt;** element and decrypting it to recover the original plaintext.</span></span>

<span data-ttu-id="49073-131">Olduğu `<descriptor>`gibi, öğenin `<encryptedSecret>` belirli biçimi kullanılan istirahat şifreleme mekanizmasına bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="49073-131">As with `<descriptor>`, the particular format of the `<encryptedSecret>` element depends on the at-rest encryption mechanism in use.</span></span> <span data-ttu-id="49073-132">Yukarıdaki örnekte, ana anahtar yorum başına Windows DPAPI kullanılarak şifrelenir.</span><span class="sxs-lookup"><span data-stu-id="49073-132">In the above example, the master key is encrypted using Windows DPAPI per the comment.</span></span>

## <a name="the-revocation-element"></a><span data-ttu-id="49073-133">İptal \<> öğesi</span><span class="sxs-lookup"><span data-stu-id="49073-133">The \<revocation> element</span></span>

<span data-ttu-id="49073-134">İptaller, anahtar deposundaki üst düzey nesneler olarak bulunur.</span><span class="sxs-lookup"><span data-stu-id="49073-134">Revocations exist as top-level objects in the key repository.</span></span> <span data-ttu-id="49073-135">Sözleşme iptalleri tarafından dosya adı **iptali-{timestamp}.xml** (belirli bir tarihten önce tüm anahtarları iptal etmek için) veya **iptal-{guid}.xml** (belirli bir anahtarı iptal etmek için).</span><span class="sxs-lookup"><span data-stu-id="49073-135">By convention revocations have the filename **revocation-{timestamp}.xml** (for revoking all keys before a specific date) or **revocation-{guid}.xml** (for revoking a specific key).</span></span> <span data-ttu-id="49073-136">Her dosya tek \<bir iptal> öğesi içerir.</span><span class="sxs-lookup"><span data-stu-id="49073-136">Each file contains a single \<revocation> element.</span></span>

<span data-ttu-id="49073-137">Tek tek anahtarların iptali için dosya içeriği aşağıdaki gibi olacaktır.</span><span class="sxs-lookup"><span data-stu-id="49073-137">For revocations of individual keys, the file contents will be as below.</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T22:45:30.2616742Z</revocationDate>
  <key id="eb4fc299-8808-409d-8a34-23fc83d026c9" />
  <reason>human-readable reason</reason>
</revocation>
```

<span data-ttu-id="49073-138">Bu durumda, yalnızca belirtilen anahtar iptal edilir.</span><span class="sxs-lookup"><span data-stu-id="49073-138">In this case, only the specified key is revoked.</span></span> <span data-ttu-id="49073-139">Ancak, anahtar kimliği "\*" ise, aşağıdaki örnekte olduğu gibi, oluşturma tarihi belirtilen iptal tarihinden önce olan tüm anahtarlar iptal edilir.</span><span class="sxs-lookup"><span data-stu-id="49073-139">If the key id is "\*", however, as in the below example, all keys whose creation date is prior to the specified revocation date are revoked.</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T15:45:45.7366491-07:00</revocationDate>
  <!-- All keys created before the revocation date are revoked. -->
  <key id="*" />
  <reason>human-readable reason</reason>
</revocation>
```

<span data-ttu-id="49073-140">> \<öğenin nedeni sistem tarafından asla okunmaz.</span><span class="sxs-lookup"><span data-stu-id="49073-140">The \<reason> element is never read by the system.</span></span> <span data-ttu-id="49073-141">Bu sadece iptal için insan tarafından okunabilir bir neden saklamak için uygun bir yerdir.</span><span class="sxs-lookup"><span data-stu-id="49073-141">It's simply a convenient place to store a human-readable reason for revocation.</span></span>
