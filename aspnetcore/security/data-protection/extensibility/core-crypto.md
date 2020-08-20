---
title: ASP.NET Core temel şifreleme genişletilebilirliği
author: rick-anderson
description: Iauthenticatedencryptor, ıauthenticatedencryptordescriptor, ıauthenticatedencryptordescriptordeserializer ve en üst düzey fabrika hakkında bilgi edinin.
ms.author: riande
ms.date: 08/11/2017
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
uid: security/data-protection/extensibility/core-crypto
ms.openlocfilehash: 4c802bc4beb1f1fde812e6c3f55fc43b5d569b66
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88635326"
---
# <a name="core-cryptography-extensibility-in-aspnet-core"></a><span data-ttu-id="836e7-103">ASP.NET Core temel şifreleme genişletilebilirliği</span><span class="sxs-lookup"><span data-stu-id="836e7-103">Core cryptography extensibility in ASP.NET Core</span></span>

<a name="data-protection-extensibility-core-crypto"></a>

>[!WARNING]
> <span data-ttu-id="836e7-104">Aşağıdaki arabirimlerin herhangi birini uygulayan türler birden çok çağıranlar için iş parçacığı açısından güvenli olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="836e7-104">Types that implement any of the following interfaces should be thread-safe for multiple callers.</span></span>

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptor"></a>

## <a name="iauthenticatedencryptor"></a><span data-ttu-id="836e7-105">Iauthenticatedencryptor</span><span class="sxs-lookup"><span data-stu-id="836e7-105">IAuthenticatedEncryptor</span></span>

<span data-ttu-id="836e7-106">**Iauthenticatedencryptor** arabirimi, şifreleme alt sisteminin temel yapı taşıdır.</span><span class="sxs-lookup"><span data-stu-id="836e7-106">The **IAuthenticatedEncryptor** interface is the basic building block of the cryptographic subsystem.</span></span> <span data-ttu-id="836e7-107">Anahtar başına genellikle bir ıauthenticatedencryptor vardır ve ıauthenticatedencryptor örneği, şifreleme işlemlerini gerçekleştirmek için gereken tüm şifreleme anahtarı malzemesini ve algoritmik bilgilerini sarmalar.</span><span class="sxs-lookup"><span data-stu-id="836e7-107">There's generally one IAuthenticatedEncryptor per key, and the IAuthenticatedEncryptor instance wraps all cryptographic key material and algorithmic information necessary to perform cryptographic operations.</span></span>

<span data-ttu-id="836e7-108">Adından da anlaşılacağı gibi, tür kimliği doğrulanmış şifreleme ve şifre çözme hizmetleri sağlamaktan sorumludur.</span><span class="sxs-lookup"><span data-stu-id="836e7-108">As its name suggests, the type is responsible for providing authenticated encryption and decryption services.</span></span> <span data-ttu-id="836e7-109">Aşağıdaki iki API 'yi kullanıma sunar.</span><span class="sxs-lookup"><span data-stu-id="836e7-109">It exposes the following two APIs.</span></span>

* `Decrypt(ArraySegment<byte> ciphertext, ArraySegment<byte> additionalAuthenticatedData) : byte[]`

* `Encrypt(ArraySegment<byte> plaintext, ArraySegment<byte> additionalAuthenticatedData) : byte[]`

<span data-ttu-id="836e7-110">Encrypt yöntemi, şifreli düz metin ve bir kimlik doğrulama etiketi içeren bir blobu döndürür.</span><span class="sxs-lookup"><span data-stu-id="836e7-110">The Encrypt method returns a blob that includes the enciphered plaintext and an authentication tag.</span></span> <span data-ttu-id="836e7-111">Kimlik doğrulama etiketi, ek kimliği doğrulanmış verileri (AAD) içermelidir, ancak AAD 'nin son yükün geri kurtarılması gerekmez.</span><span class="sxs-lookup"><span data-stu-id="836e7-111">The authentication tag must encompass the additional authenticated data (AAD), though the AAD itself need not be recoverable from the final payload.</span></span> <span data-ttu-id="836e7-112">Şifre çözme yöntemi, kimlik doğrulama etiketini doğrular ve çözülemez yükü döndürür.</span><span class="sxs-lookup"><span data-stu-id="836e7-112">The Decrypt method validates the authentication tag and returns the deciphered payload.</span></span> <span data-ttu-id="836e7-113">Tüm başarısızlıklar (ArgumentNullException ve benzeri hariç) CryptographicException için hogenlanmış olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="836e7-113">All failures (except ArgumentNullException and similar) should be homogenized to CryptographicException.</span></span>

> [!NOTE]
> <span data-ttu-id="836e7-114">Iauthenticatedencryptor örneğinin kendisi aslında anahtar malzemesini içermesi gerekmez.</span><span class="sxs-lookup"><span data-stu-id="836e7-114">The IAuthenticatedEncryptor instance itself doesn't actually need to contain the key material.</span></span> <span data-ttu-id="836e7-115">Örneğin, uygulama tüm işlemler için bir HSM 'ye temsilci verebilir.</span><span class="sxs-lookup"><span data-stu-id="836e7-115">For example, the implementation could delegate to an HSM for all operations.</span></span>

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptorfactory"></a>
<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor"></a>

## <a name="how-to-create-an-iauthenticatedencryptor"></a><span data-ttu-id="836e7-116">Iauthenticatedencryptor oluşturma</span><span class="sxs-lookup"><span data-stu-id="836e7-116">How to create an IAuthenticatedEncryptor</span></span>

# <a name="aspnet-core-2x"></a>[<span data-ttu-id="836e7-117">ASP.NET Core 2. x</span><span class="sxs-lookup"><span data-stu-id="836e7-117">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="836e7-118">**Iauthenticatedencryptorfactory** arabirimi, bir [ıauthenticatedencryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) örneğinin nasıl oluşturulacağını bilen bir türü temsil eder.</span><span class="sxs-lookup"><span data-stu-id="836e7-118">The **IAuthenticatedEncryptorFactory** interface represents a type that knows how to create an [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance.</span></span> <span data-ttu-id="836e7-119">API 'SI aşağıdaki gibidir.</span><span class="sxs-lookup"><span data-stu-id="836e7-119">Its API is as follows.</span></span>

* <span data-ttu-id="836e7-120">Createencryptorınstance (Ikey anahtarı): ıauthenticatedencryptor</span><span class="sxs-lookup"><span data-stu-id="836e7-120">CreateEncryptorInstance(IKey key) : IAuthenticatedEncryptor</span></span>

<span data-ttu-id="836e7-121">Belirli bir Ikey örneği için, Createencryptorınstance yöntemi tarafından oluşturulan tüm kimliği doğrulanmış şifreleyiciler, aşağıdaki kod örneğinde olduğu gibi eşdeğer olarak kabul edilmelidir.</span><span class="sxs-lookup"><span data-stu-id="836e7-121">For any given IKey instance, any authenticated encryptors created by its CreateEncryptorInstance method should be considered equivalent, as in the below code sample.</span></span>

```csharp
// we have an IAuthenticatedEncryptorFactory instance and an IKey instance
IAuthenticatedEncryptorFactory factory = ...;
IKey key = ...;

// get an encryptor instance and perform an authenticated encryption operation
ArraySegment<byte> plaintext = new ArraySegment<byte>(Encoding.UTF8.GetBytes("plaintext"));
ArraySegment<byte> aad = new ArraySegment<byte>(Encoding.UTF8.GetBytes("AAD"));
var encryptor1 = factory.CreateEncryptorInstance(key);
byte[] ciphertext = encryptor1.Encrypt(plaintext, aad);

// get another encryptor instance and perform an authenticated decryption operation
var encryptor2 = factory.CreateEncryptorInstance(key);
byte[] roundTripped = encryptor2.Decrypt(new ArraySegment<byte>(ciphertext), aad);


// the 'roundTripped' and 'plaintext' buffers should be equivalent
```

# <a name="aspnet-core-1x"></a>[<span data-ttu-id="836e7-122">ASP.NET Core 1. x</span><span class="sxs-lookup"><span data-stu-id="836e7-122">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="836e7-123">**Iauthenticatedencryptordescriptor** arabirimi bir [ıauthenticatedencryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) örneğinin nasıl oluşturulacağını bilen bir türü temsil eder.</span><span class="sxs-lookup"><span data-stu-id="836e7-123">The **IAuthenticatedEncryptorDescriptor** interface represents a type that knows how to create an [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance.</span></span> <span data-ttu-id="836e7-124">API 'SI aşağıdaki gibidir.</span><span class="sxs-lookup"><span data-stu-id="836e7-124">Its API is as follows.</span></span>

* <span data-ttu-id="836e7-125">Createencryptorınstance (): ıauthenticatedencryptor</span><span class="sxs-lookup"><span data-stu-id="836e7-125">CreateEncryptorInstance() : IAuthenticatedEncryptor</span></span>

* <span data-ttu-id="836e7-126">ExportToXml (): Xmlserializeddescriptorınfo</span><span class="sxs-lookup"><span data-stu-id="836e7-126">ExportToXml() : XmlSerializedDescriptorInfo</span></span>

<span data-ttu-id="836e7-127">Iauthenticatedencryptor gibi bir ıauthenticatedencryptordescriptor örneği, belirli bir anahtarı kaydırmak için kabul edilir.</span><span class="sxs-lookup"><span data-stu-id="836e7-127">Like IAuthenticatedEncryptor, an instance of IAuthenticatedEncryptorDescriptor is assumed to wrap one specific key.</span></span> <span data-ttu-id="836e7-128">Yani, belirtilen ıauthenticatedencryptordescriptor örneği için, aşağıdaki kod örneğinde olduğu gibi, Createencryptorınstance yöntemi tarafından oluşturulan tüm kimliği doğrulanmış şifreleyiciler eşdeğer olarak kabul edilmelidir.</span><span class="sxs-lookup"><span data-stu-id="836e7-128">This means that for any given IAuthenticatedEncryptorDescriptor instance, any authenticated encryptors created by its CreateEncryptorInstance method should be considered equivalent, as in the below code sample.</span></span>

```csharp
// we have an IAuthenticatedEncryptorDescriptor instance
IAuthenticatedEncryptorDescriptor descriptor = ...;

// get an encryptor instance and perform an authenticated encryption operation
ArraySegment<byte> plaintext = new ArraySegment<byte>(Encoding.UTF8.GetBytes("plaintext"));
ArraySegment<byte> aad = new ArraySegment<byte>(Encoding.UTF8.GetBytes("AAD"));
var encryptor1 = descriptor.CreateEncryptorInstance();
byte[] ciphertext = encryptor1.Encrypt(plaintext, aad);

// get another encryptor instance and perform an authenticated decryption operation
var encryptor2 = descriptor.CreateEncryptorInstance();
byte[] roundTripped = encryptor2.Decrypt(new ArraySegment<byte>(ciphertext), aad);


// the 'roundTripped' and 'plaintext' buffers should be equivalent
```

---

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor"></a>

## <a name="iauthenticatedencryptordescriptor-aspnet-core-2x-only"></a><span data-ttu-id="836e7-129">Iauthenticatedencryptordescriptor (yalnızca ASP.NET Core 2. x)</span><span class="sxs-lookup"><span data-stu-id="836e7-129">IAuthenticatedEncryptorDescriptor (ASP.NET Core 2.x only)</span></span>

# <a name="aspnet-core-2x"></a>[<span data-ttu-id="836e7-130">ASP.NET Core 2. x</span><span class="sxs-lookup"><span data-stu-id="836e7-130">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="836e7-131">**Iauthenticatedencryptordescriptor** arabirimi, kendisini XML 'e aktarmayı bilen bir türü temsil eder.</span><span class="sxs-lookup"><span data-stu-id="836e7-131">The **IAuthenticatedEncryptorDescriptor** interface represents a type that knows how to export itself to XML.</span></span> <span data-ttu-id="836e7-132">API 'SI aşağıdaki gibidir.</span><span class="sxs-lookup"><span data-stu-id="836e7-132">Its API is as follows.</span></span>

* <span data-ttu-id="836e7-133">ExportToXml (): Xmlserializeddescriptorınfo</span><span class="sxs-lookup"><span data-stu-id="836e7-133">ExportToXml() : XmlSerializedDescriptorInfo</span></span>

# <a name="aspnet-core-1x"></a>[<span data-ttu-id="836e7-134">ASP.NET Core 1. x</span><span class="sxs-lookup"><span data-stu-id="836e7-134">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

---

## <a name="xml-serialization"></a><span data-ttu-id="836e7-135">XML seri hale getirme</span><span class="sxs-lookup"><span data-stu-id="836e7-135">XML Serialization</span></span>

<span data-ttu-id="836e7-136">Iauthenticatedencryptor ve ıauthenticatedencryptordescriptor arasındaki birincil fark, tanımlayıcının Şifreleyici 'nin nasıl oluşturulacağını ve geçerli bağımsız değişkenlerle nasıl tedarik olduğunu bilmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="836e7-136">The primary difference between IAuthenticatedEncryptor and IAuthenticatedEncryptorDescriptor is that the descriptor knows how to create the encryptor and supply it with valid arguments.</span></span> <span data-ttu-id="836e7-137">Uygulaması SymmetricAlgorithm ve KeyedHashAlgorithm kullanan bir ıauthenticatedencryptor öğesini düşünün.</span><span class="sxs-lookup"><span data-stu-id="836e7-137">Consider an IAuthenticatedEncryptor whose implementation relies on SymmetricAlgorithm and KeyedHashAlgorithm.</span></span> <span data-ttu-id="836e7-138">Şifreleyici 'nin işi bu türleri tüketmek, ancak bu türlerin nereden geldiğini bilmesi gerekmez, bu nedenle uygulama yeniden başlatıldığında kendisini yeniden oluşturma işleminin doğru bir açıklamasını yazmamaktadır.</span><span class="sxs-lookup"><span data-stu-id="836e7-138">The encryptor's job is to consume these types, but it doesn't necessarily know where these types came from, so it can't really write out a proper description of how to recreate itself if the application restarts.</span></span> <span data-ttu-id="836e7-139">Tanımlayıcı, bunun üzerine daha yüksek bir düzey görevi görür.</span><span class="sxs-lookup"><span data-stu-id="836e7-139">The descriptor acts as a higher level on top of this.</span></span> <span data-ttu-id="836e7-140">Tanımlayıcı, bir Şifreleyici örneğinin nasıl oluşturulacağını bildiği için (örneğin, gerekli algoritmaların nasıl oluşturulacağını bilir), bu bilgileri XML biçiminde seri hale getirmek için, bir uygulama sıfırlandıktan sonra Şifreleyici örneğinin yeniden oluşturulabilir olmasını sağlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="836e7-140">Since the descriptor knows how to create the encryptor instance (e.g., it knows how to create the required algorithms), it can serialize that knowledge in XML form so that the encryptor instance can be recreated after an application reset.</span></span>

<a name="data-protection-extensibility-core-crypto-exporttoxml"></a>

<span data-ttu-id="836e7-141">Tanımlayıcı, ExportToXml yordamı aracılığıyla seri hale getirilebilir.</span><span class="sxs-lookup"><span data-stu-id="836e7-141">The descriptor can be serialized via its ExportToXml routine.</span></span> <span data-ttu-id="836e7-142">Bu yordam iki özellik içeren bir Xmlserializeddescriptorınfo döndürür: Descriptor 'ın XElement temsili ve bu tanımlayıcıyı ilgili XElement 'e geri döndürmek için kullanılabilecek bir [ıauthenticatedencryptordescriptordeserializer](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer) temsil eden tür.</span><span class="sxs-lookup"><span data-stu-id="836e7-142">This routine returns an XmlSerializedDescriptorInfo which contains two properties: the XElement representation of the descriptor and the Type which represents an [IAuthenticatedEncryptorDescriptorDeserializer](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer) which can be used to resurrect this descriptor given the corresponding XElement.</span></span>

<span data-ttu-id="836e7-143">Serileştirilmiş tanımlayıcı, şifreleme anahtar malzemesi gibi hassas bilgiler içerebilir.</span><span class="sxs-lookup"><span data-stu-id="836e7-143">The serialized descriptor may contain sensitive information such as cryptographic key material.</span></span> <span data-ttu-id="836e7-144">Veri koruma sistemi, depolama alanına kalıcı olmadan önce bilgileri şifrelemek için yerleşik desteğe sahiptir.</span><span class="sxs-lookup"><span data-stu-id="836e7-144">The data protection system has built-in support for encrypting information before it's persisted to storage.</span></span> <span data-ttu-id="836e7-145">Bu özelliğin avantajlarından yararlanabilmek için tanımlayıcı, "requiresEncryption" (xmlns " <http://schemas.asp.net/2015/03/dataProtection> ") öznitelik adı, "true" özniteliği ile hassas bilgiler içeren öğeyi işaretlemelidir.</span><span class="sxs-lookup"><span data-stu-id="836e7-145">To take advantage of this, the descriptor should mark the element which contains sensitive information with the attribute name "requiresEncryption" (xmlns "<http://schemas.asp.net/2015/03/dataProtection>"), value "true".</span></span>

>[!TIP]
> <span data-ttu-id="836e7-146">Bu özniteliği ayarlamak için yardımcı bir API vardır.</span><span class="sxs-lookup"><span data-stu-id="836e7-146">There's a helper API for setting this attribute.</span></span> <span data-ttu-id="836e7-147">Ad alanı Microsoft.AspNetCore.DataProtection.AuthenticatedEncryption.ConfigurationModel içinde bulunan XElement. Markasrequiresencryptıon () uzantı yöntemini çağırın.</span><span class="sxs-lookup"><span data-stu-id="836e7-147">Call the extension method XElement.MarkAsRequiresEncryption() located in namespace Microsoft.AspNetCore.DataProtection.AuthenticatedEncryption.ConfigurationModel.</span></span>

<span data-ttu-id="836e7-148">Ayrıca, serileştirilmiş tanımlayıcının gizli bilgiler içermediği durumlar da olabilir.</span><span class="sxs-lookup"><span data-stu-id="836e7-148">There can also be cases where the serialized descriptor doesn't contain sensitive information.</span></span> <span data-ttu-id="836e7-149">Bir HSM 'de depolanan bir şifreleme anahtarının durumunu yeniden deneyin.</span><span class="sxs-lookup"><span data-stu-id="836e7-149">Consider again the case of a cryptographic key stored in an HSM.</span></span> <span data-ttu-id="836e7-150">HSM, malzemeyi düz metin biçiminde kullanıma sunmayadıklarından, tanımlayıcı, kendisini serileştirilirken anahtar malzemesini yazamaz.</span><span class="sxs-lookup"><span data-stu-id="836e7-150">The descriptor cannot write out the key material when serializing itself since the HSM won't expose the material in plaintext form.</span></span> <span data-ttu-id="836e7-151">Bunun yerine, tanımlayıcı, anahtarın anahtar Sarmalanan sürümünü yazabilir (HSM bu şekilde dışarı aktarmaya izin veriyorsa) veya HSM 'nin anahtar için kendi benzersiz tanımlayıcısıdır.</span><span class="sxs-lookup"><span data-stu-id="836e7-151">Instead, the descriptor might write out the key-wrapped version of the key (if the HSM allows export in this fashion) or the HSM's own unique identifier for the key.</span></span>

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer"></a>

## <a name="iauthenticatedencryptordescriptordeserializer"></a><span data-ttu-id="836e7-152">Iauthenticatedencryptordescriptordeserializer</span><span class="sxs-lookup"><span data-stu-id="836e7-152">IAuthenticatedEncryptorDescriptorDeserializer</span></span>

<span data-ttu-id="836e7-153">**Iauthenticatedencryptordescriptordeserializer** arabirimi bir ıauthenticatedencryptordescriptor örneğinin bir XElement öğesinden serisini nasıl çıkaracağını bilen bir türü temsil eder.</span><span class="sxs-lookup"><span data-stu-id="836e7-153">The **IAuthenticatedEncryptorDescriptorDeserializer** interface represents a type that knows how to deserialize an IAuthenticatedEncryptorDescriptor instance from an XElement.</span></span> <span data-ttu-id="836e7-154">Tek bir yöntem sunar:</span><span class="sxs-lookup"><span data-stu-id="836e7-154">It exposes a single method:</span></span>

* <span data-ttu-id="836e7-155">ImportFromXML (XElement öğesi): ıauthenticatedencryptordescriptor</span><span class="sxs-lookup"><span data-stu-id="836e7-155">ImportFromXml(XElement element) : IAuthenticatedEncryptorDescriptor</span></span>

<span data-ttu-id="836e7-156">ImportFromXML yöntemi [ıauthenticatedencryptordescriptor. ExportToXml](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-exporttoxml) tarafından döndürülen XElement değerini alır ve özgün ıauthenticatedencryptordescriptor 'ın eşdeğerini oluşturur.</span><span class="sxs-lookup"><span data-stu-id="836e7-156">The ImportFromXml method takes the XElement that was returned by [IAuthenticatedEncryptorDescriptor.ExportToXml](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-exporttoxml) and creates an equivalent of the original IAuthenticatedEncryptorDescriptor.</span></span>

<span data-ttu-id="836e7-157">Iauthenticatedencryptordescriptordeserializer uygulayan türler aşağıdaki iki ortak oluşturucudan birine sahip olmalıdır:</span><span class="sxs-lookup"><span data-stu-id="836e7-157">Types which implement IAuthenticatedEncryptorDescriptorDeserializer should have one of the following two public constructors:</span></span>

* <span data-ttu-id="836e7-158">. ctor (Iservvoiceprovider)</span><span class="sxs-lookup"><span data-stu-id="836e7-158">.ctor(IServiceProvider)</span></span>

* <span data-ttu-id="836e7-159">. ctor ()</span><span class="sxs-lookup"><span data-stu-id="836e7-159">.ctor()</span></span>

> [!NOTE]
> <span data-ttu-id="836e7-160">Oluşturucuya geçirilen IServiceProvider null olabilir.</span><span class="sxs-lookup"><span data-stu-id="836e7-160">The IServiceProvider passed to the constructor may be null.</span></span>

## <a name="the-top-level-factory"></a><span data-ttu-id="836e7-161">En üst düzey fabrika</span><span class="sxs-lookup"><span data-stu-id="836e7-161">The top-level factory</span></span>

# <a name="aspnet-core-2x"></a>[<span data-ttu-id="836e7-162">ASP.NET Core 2. x</span><span class="sxs-lookup"><span data-stu-id="836e7-162">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="836e7-163">**AlgorithmConfiguration** sınıfı, [ıauthenticatedencryptordescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) örneklerinin nasıl oluşturulacağını bilen bir türü temsil eder.</span><span class="sxs-lookup"><span data-stu-id="836e7-163">The **AlgorithmConfiguration** class represents a type which knows how to create [IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) instances.</span></span> <span data-ttu-id="836e7-164">Tek bir API sunar.</span><span class="sxs-lookup"><span data-stu-id="836e7-164">It exposes a single API.</span></span>

* <span data-ttu-id="836e7-165">CreateNewDescriptor (): ıauthenticatedencryptordescriptor</span><span class="sxs-lookup"><span data-stu-id="836e7-165">CreateNewDescriptor() : IAuthenticatedEncryptorDescriptor</span></span>

<span data-ttu-id="836e7-166">En üst düzey fabrika olarak AlgorithmConfiguration düşünün.</span><span class="sxs-lookup"><span data-stu-id="836e7-166">Think of AlgorithmConfiguration as the top-level factory.</span></span> <span data-ttu-id="836e7-167">Yapılandırma bir şablon işlevi görür.</span><span class="sxs-lookup"><span data-stu-id="836e7-167">The configuration serves as a template.</span></span> <span data-ttu-id="836e7-168">Algoritmik bilgilerini sarmalar (örn. bu yapılandırma bir AES-128-GCM ana anahtarıyla tanımlayıcılar üretir), ancak henüz belirli bir anahtarla ilişkilendirilmemiş.</span><span class="sxs-lookup"><span data-stu-id="836e7-168">It wraps algorithmic information (e.g., this configuration produces descriptors with an AES-128-GCM master key), but it's not yet associated with a specific key.</span></span>

<span data-ttu-id="836e7-169">CreateNewDescriptor çağrıldığında, yalnızca bu çağrı için yeni anahtar malzemesi oluşturulur ve bu anahtar malzemesini ve malzemeyi tüketmek için gereken algoritmik bilgilerini sarmalayan yeni bir ıauthenticatedencryptordescriptor üretilir.</span><span class="sxs-lookup"><span data-stu-id="836e7-169">When CreateNewDescriptor is called, fresh key material is created solely for this call, and a new IAuthenticatedEncryptorDescriptor is produced which wraps this key material and the algorithmic information required to consume the material.</span></span> <span data-ttu-id="836e7-170">Ana malzeme yazılımda (ve bellekte tutulan) oluşturulabilir, bir HSM içinde oluşturulup tutulabilir ve bu şekilde devam eder.</span><span class="sxs-lookup"><span data-stu-id="836e7-170">The key material could be created in software (and held in memory), it could be created and held within an HSM, and so on.</span></span> <span data-ttu-id="836e7-171">Önemli nokta, CreateNewDescriptor 'a yapılan iki çağrının asla eşdeğer ıauthenticatedencryptordescriptor örnekleri oluşturmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="836e7-171">The crucial point is that any two calls to CreateNewDescriptor should never create equivalent IAuthenticatedEncryptorDescriptor instances.</span></span>

<span data-ttu-id="836e7-172">AlgorithmConfiguration türü, [otomatik anahtar yuvarlama](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling)gibi anahtar oluşturma yordamları için giriş noktası olarak görev yapar.</span><span class="sxs-lookup"><span data-stu-id="836e7-172">The AlgorithmConfiguration type serves as the entry point for key creation routines such as [automatic key rolling](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling).</span></span> <span data-ttu-id="836e7-173">Tüm gelecek anahtarların uygulamasını değiştirmek için KeyManagementOptions içindeki AuthenticatedEncryptorConfiguration özelliğini ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="836e7-173">To change the implementation for all future keys, set the AuthenticatedEncryptorConfiguration property in KeyManagementOptions.</span></span>

# <a name="aspnet-core-1x"></a>[<span data-ttu-id="836e7-174">ASP.NET Core 1. x</span><span class="sxs-lookup"><span data-stu-id="836e7-174">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="836e7-175">**Iauthenticatedencryptorconfiguration** arabirimi, [ıauthenticatedencryptordescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) örneklerinin nasıl oluşturulacağını bilen bir türü temsil eder.</span><span class="sxs-lookup"><span data-stu-id="836e7-175">The **IAuthenticatedEncryptorConfiguration** interface represents a type which knows how to create [IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) instances.</span></span> <span data-ttu-id="836e7-176">Tek bir API sunar.</span><span class="sxs-lookup"><span data-stu-id="836e7-176">It exposes a single API.</span></span>

* <span data-ttu-id="836e7-177">CreateNewDescriptor (): ıauthenticatedencryptordescriptor</span><span class="sxs-lookup"><span data-stu-id="836e7-177">CreateNewDescriptor() : IAuthenticatedEncryptorDescriptor</span></span>

<span data-ttu-id="836e7-178">Iauthenticatedencryptorconfiguration öğesini en üst düzey fabrika olarak düşünün.</span><span class="sxs-lookup"><span data-stu-id="836e7-178">Think of IAuthenticatedEncryptorConfiguration as the top-level factory.</span></span> <span data-ttu-id="836e7-179">Yapılandırma bir şablon işlevi görür.</span><span class="sxs-lookup"><span data-stu-id="836e7-179">The configuration serves as a template.</span></span> <span data-ttu-id="836e7-180">Algoritmik bilgilerini sarmalar (örn. bu yapılandırma bir AES-128-GCM ana anahtarıyla tanımlayıcılar üretir), ancak henüz belirli bir anahtarla ilişkilendirilmemiş.</span><span class="sxs-lookup"><span data-stu-id="836e7-180">It wraps algorithmic information (e.g., this configuration produces descriptors with an AES-128-GCM master key), but it's not yet associated with a specific key.</span></span>

<span data-ttu-id="836e7-181">CreateNewDescriptor çağrıldığında, yalnızca bu çağrı için yeni anahtar malzemesi oluşturulur ve bu anahtar malzemesini ve malzemeyi tüketmek için gereken algoritmik bilgilerini sarmalayan yeni bir ıauthenticatedencryptordescriptor üretilir.</span><span class="sxs-lookup"><span data-stu-id="836e7-181">When CreateNewDescriptor is called, fresh key material is created solely for this call, and a new IAuthenticatedEncryptorDescriptor is produced which wraps this key material and the algorithmic information required to consume the material.</span></span> <span data-ttu-id="836e7-182">Ana malzeme yazılımda (ve bellekte tutulan) oluşturulabilir, bir HSM içinde oluşturulup tutulabilir ve bu şekilde devam eder.</span><span class="sxs-lookup"><span data-stu-id="836e7-182">The key material could be created in software (and held in memory), it could be created and held within an HSM, and so on.</span></span> <span data-ttu-id="836e7-183">Önemli nokta, CreateNewDescriptor 'a yapılan iki çağrının asla eşdeğer ıauthenticatedencryptordescriptor örnekleri oluşturmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="836e7-183">The crucial point is that any two calls to CreateNewDescriptor should never create equivalent IAuthenticatedEncryptorDescriptor instances.</span></span>

<span data-ttu-id="836e7-184">Iauthenticatedencryptorconfiguration türü, [otomatik anahtar yuvarlama](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling)gibi anahtar oluşturma yordamları için giriş noktası olarak görev yapar.</span><span class="sxs-lookup"><span data-stu-id="836e7-184">The IAuthenticatedEncryptorConfiguration type serves as the entry point for key creation routines such as [automatic key rolling](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling).</span></span> <span data-ttu-id="836e7-185">Tüm gelecek anahtarların uygulamasını değiştirmek için, hizmet kapsayıcısına tek bir ıauthenticatedencryptorconfiguration kaydedin.</span><span class="sxs-lookup"><span data-stu-id="836e7-185">To change the implementation for all future keys, register a singleton IAuthenticatedEncryptorConfiguration in the service container.</span></span>

---
