---
title: ASP.NET Core 'de anahtar yönetimi genişletilebilirliği
author: rick-anderson
description: ASP.NET Core Data Protection anahtar yönetimi genişletilebilirliği hakkında bilgi edinin.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 10/24/2018
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/extensibility/key-management
ms.openlocfilehash: e319872799ef4994b55ba941956836f0848dd76d
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408545"
---
# <a name="key-management-extensibility-in-aspnet-core"></a><span data-ttu-id="49ecc-103">ASP.NET Core 'de anahtar yönetimi genişletilebilirliği</span><span class="sxs-lookup"><span data-stu-id="49ecc-103">Key management extensibility in ASP.NET Core</span></span>

> [!TIP]
> <span data-ttu-id="49ecc-104">Bu API 'nin arkasındaki temel kavramların bazılarını açıklandığı için, bu bölümü okumadan önce [anahtar yönetimi](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) bölümünü okuyun.</span><span class="sxs-lookup"><span data-stu-id="49ecc-104">Read the [key management](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) section before reading this section, as it explains some of the fundamental concepts behind these APIs.</span></span>

> [!WARNING]
> <span data-ttu-id="49ecc-105">Aşağıdaki arabirimlerin herhangi birini uygulayan türler birden çok çağıranlar için iş parçacığı açısından güvenli olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="49ecc-105">Types that implement any of the following interfaces should be thread-safe for multiple callers.</span></span>

## <a name="key"></a><span data-ttu-id="49ecc-106">Anahtar</span><span class="sxs-lookup"><span data-stu-id="49ecc-106">Key</span></span>

<span data-ttu-id="49ecc-107">`IKey`Arabirim, Cryptosystem içindeki bir anahtarın temel gösterimidir.</span><span class="sxs-lookup"><span data-stu-id="49ecc-107">The `IKey` interface is the basic representation of a key in cryptosystem.</span></span> <span data-ttu-id="49ecc-108">Bu anahtar, "şifreleme anahtar malzemesi" değerinde değil, Soyut anlamda burada kullanılır.</span><span class="sxs-lookup"><span data-stu-id="49ecc-108">The term key is used here in the abstract sense, not in the literal sense of "cryptographic key material".</span></span> <span data-ttu-id="49ecc-109">Bir anahtar aşağıdaki özelliklere sahiptir:</span><span class="sxs-lookup"><span data-stu-id="49ecc-109">A key has the following properties:</span></span>

* <span data-ttu-id="49ecc-110">Etkinleştirme, oluşturma ve sona erme tarihleri</span><span class="sxs-lookup"><span data-stu-id="49ecc-110">Activation, creation, and expiration dates</span></span>

* <span data-ttu-id="49ecc-111">İptal durumu</span><span class="sxs-lookup"><span data-stu-id="49ecc-111">Revocation status</span></span>

* <span data-ttu-id="49ecc-112">Anahtar tanımlayıcı (GUID)</span><span class="sxs-lookup"><span data-stu-id="49ecc-112">Key identifier (a GUID)</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="49ecc-113">Ayrıca, `IKey` `CreateEncryptor` Bu anahtara bağlı bir [ıauthenticatedencryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) örneği oluşturmak için kullanılabilecek bir yöntem sunar.</span><span class="sxs-lookup"><span data-stu-id="49ecc-113">Additionally, `IKey` exposes a `CreateEncryptor` method which can be used to create an [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance tied to this key.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="49ecc-114">Ayrıca, `IKey` `CreateEncryptorInstance` Bu anahtara bağlı bir [ıauthenticatedencryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) örneği oluşturmak için kullanılabilecek bir yöntem sunar.</span><span class="sxs-lookup"><span data-stu-id="49ecc-114">Additionally, `IKey` exposes a `CreateEncryptorInstance` method which can be used to create an [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance tied to this key.</span></span>

::: moniker-end

> [!NOTE]
> <span data-ttu-id="49ecc-115">Bir örnekten ham şifreleme malzemesini almak için API yok `IKey` .</span><span class="sxs-lookup"><span data-stu-id="49ecc-115">There's no API to retrieve the raw cryptographic material from an `IKey` instance.</span></span>

## <a name="ikeymanager"></a><span data-ttu-id="49ecc-116">Ikeymanager</span><span class="sxs-lookup"><span data-stu-id="49ecc-116">IKeyManager</span></span>

<span data-ttu-id="49ecc-117">`IKeyManager`Arabirim, genel anahtar depolama, alma ve düzenleme işleminden sorumlu bir nesneyi temsil eder.</span><span class="sxs-lookup"><span data-stu-id="49ecc-117">The `IKeyManager` interface represents an object responsible for general key storage, retrieval, and manipulation.</span></span> <span data-ttu-id="49ecc-118">Üç üst düzey işlemi kullanıma sunar:</span><span class="sxs-lookup"><span data-stu-id="49ecc-118">It exposes three high-level operations:</span></span>

* <span data-ttu-id="49ecc-119">Yeni bir anahtar oluşturun ve depolama alanına kalıcı hale getirin.</span><span class="sxs-lookup"><span data-stu-id="49ecc-119">Create a new key and persist it to storage.</span></span>

* <span data-ttu-id="49ecc-120">Depolama alanından tüm anahtarları al.</span><span class="sxs-lookup"><span data-stu-id="49ecc-120">Get all keys from storage.</span></span>

* <span data-ttu-id="49ecc-121">Bir veya daha fazla anahtarı iptal edin ve iptal bilgilerini depolamaya devam edin.</span><span class="sxs-lookup"><span data-stu-id="49ecc-121">Revoke one or more keys and persist the revocation information to storage.</span></span>

>[!WARNING]
> <span data-ttu-id="49ecc-122">Yazmak `IKeyManager` çok gelişmiş bir görevdir ve geliştiricilerin çoğunluğu bunu denememelidir.</span><span class="sxs-lookup"><span data-stu-id="49ecc-122">Writing an `IKeyManager` is a very advanced task, and the majority of developers shouldn't attempt it.</span></span> <span data-ttu-id="49ecc-123">Bunun yerine, çoğu Geliştirici [Xmlkeymanager](#xmlkeymanager) sınıfı tarafından sunulan tesislerin avantajlarından yararlanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="49ecc-123">Instead, most developers should take advantage of the facilities offered by the [XmlKeyManager](#xmlkeymanager) class.</span></span>

## <a name="xmlkeymanager"></a><span data-ttu-id="49ecc-124">XmlKeyManager</span><span class="sxs-lookup"><span data-stu-id="49ecc-124">XmlKeyManager</span></span>

<span data-ttu-id="49ecc-125">`XmlKeyManager`Türü, öğesinin ın-Box somut uygulamasıdır `IKeyManager` .</span><span class="sxs-lookup"><span data-stu-id="49ecc-125">The `XmlKeyManager` type is the in-box concrete implementation of `IKeyManager`.</span></span> <span data-ttu-id="49ecc-126">Bu, anahtar Emanet ve anahtarların şifrelenmesi dahil olmak üzere çeşitli yararlı olanaklar sağlar.</span><span class="sxs-lookup"><span data-stu-id="49ecc-126">It provides several useful facilities, including key escrow and encryption of keys at rest.</span></span> <span data-ttu-id="49ecc-127">Bu sistemdeki anahtarlar XML öğeleri (özellikle, [XElement](/dotnet/csharp/programming-guide/concepts/linq/xelement-class-overview)) olarak temsil edilir.</span><span class="sxs-lookup"><span data-stu-id="49ecc-127">Keys in this system are represented as XML elements (specifically, [XElement](/dotnet/csharp/programming-guide/concepts/linq/xelement-class-overview)).</span></span>

<span data-ttu-id="49ecc-128">`XmlKeyManager`görevlerini yerine getirdikten sonra diğer birçok bileşene bağlıdır:</span><span class="sxs-lookup"><span data-stu-id="49ecc-128">`XmlKeyManager` depends on several other components in the course of fulfilling its tasks:</span></span>

::: moniker range=">= aspnetcore-2.0"

* <span data-ttu-id="49ecc-129">`AlgorithmConfiguration`Yeni anahtarlar tarafından kullanılan algoritmaları belirleyen.</span><span class="sxs-lookup"><span data-stu-id="49ecc-129">`AlgorithmConfiguration`, which dictates the algorithms used by new keys.</span></span>

* <span data-ttu-id="49ecc-130">`IXmlRepository`Bu, anahtarların depolamada nerede kalıcı olduğunu denetler.</span><span class="sxs-lookup"><span data-stu-id="49ecc-130">`IXmlRepository`, which controls where keys are persisted in storage.</span></span>

* <span data-ttu-id="49ecc-131">`IXmlEncryptor`[isteğe bağlı], bekleyen anahtarları şifrelemeye izin verir.</span><span class="sxs-lookup"><span data-stu-id="49ecc-131">`IXmlEncryptor` [optional], which allows encrypting keys at rest.</span></span>

* <span data-ttu-id="49ecc-132">`IKeyEscrowSink`[isteğe bağlı], anahtar emanet hizmetleri sağlar.</span><span class="sxs-lookup"><span data-stu-id="49ecc-132">`IKeyEscrowSink` [optional], which provides key escrow services.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

* <span data-ttu-id="49ecc-133">`IXmlRepository`Bu, anahtarların depolamada nerede kalıcı olduğunu denetler.</span><span class="sxs-lookup"><span data-stu-id="49ecc-133">`IXmlRepository`, which controls where keys are persisted in storage.</span></span>

* <span data-ttu-id="49ecc-134">`IXmlEncryptor`[isteğe bağlı], bekleyen anahtarları şifrelemeye izin verir.</span><span class="sxs-lookup"><span data-stu-id="49ecc-134">`IXmlEncryptor` [optional], which allows encrypting keys at rest.</span></span>

* <span data-ttu-id="49ecc-135">`IKeyEscrowSink`[isteğe bağlı], anahtar emanet hizmetleri sağlar.</span><span class="sxs-lookup"><span data-stu-id="49ecc-135">`IKeyEscrowSink` [optional], which provides key escrow services.</span></span>

::: moniker-end

<span data-ttu-id="49ecc-136">Aşağıda, bu bileşenlerin içinde nasıl birbirine bağlanacağını gösteren üst düzey diyagramlar bulunur `XmlKeyManager` .</span><span class="sxs-lookup"><span data-stu-id="49ecc-136">Below are high-level diagrams which indicate how these components are wired together within `XmlKeyManager`.</span></span>

::: moniker range=">= aspnetcore-2.0"

![Anahtar oluşturma](key-management/_static/keycreation2.png)

<span data-ttu-id="49ecc-138">*Anahtar oluşturma/CreateNewKey*</span><span class="sxs-lookup"><span data-stu-id="49ecc-138">*Key Creation / CreateNewKey*</span></span>

<span data-ttu-id="49ecc-139">Uygulamasında `CreateNewKey` , `AlgorithmConfiguration` bileşeni, `IAuthenticatedEncryptorDescriptor` daha sonra XML olarak seri hale getirilen benzersiz bir oluşturmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="49ecc-139">In the implementation of `CreateNewKey`, the `AlgorithmConfiguration` component is used to create a unique `IAuthenticatedEncryptorDescriptor`, which is then serialized as XML.</span></span> <span data-ttu-id="49ecc-140">Bir anahtar Emanet havuzu mevcutsa, ham (şifrelenmemiş) XML, uzun vadeli depolama için havuza sağlanır.</span><span class="sxs-lookup"><span data-stu-id="49ecc-140">If a key escrow sink is present, the raw (unencrypted) XML is provided to the sink for long-term storage.</span></span> <span data-ttu-id="49ecc-141">Şifrelenmemiş XML, `IXmlEncryptor` ŞIFRELENEN XML belgesi oluşturmak için (gerekliyse) ile çalıştırılır.</span><span class="sxs-lookup"><span data-stu-id="49ecc-141">The unencrypted XML is then run through an `IXmlEncryptor` (if required) to generate the encrypted XML document.</span></span> <span data-ttu-id="49ecc-142">Bu şifrelenmiş belge, ile uzun süreli depolamaya devam ediyor `IXmlRepository` .</span><span class="sxs-lookup"><span data-stu-id="49ecc-142">This encrypted document is persisted to long-term storage via the `IXmlRepository`.</span></span> <span data-ttu-id="49ecc-143">( `IXmlEncryptor` Yapılandırılmamışsa, şifrelenmemiş belge içinde kalıcı hale getirilir `IXmlRepository` .)</span><span class="sxs-lookup"><span data-stu-id="49ecc-143">(If no `IXmlEncryptor` is configured, the unencrypted document is persisted in the `IXmlRepository`.)</span></span>

![Anahtar alımı](key-management/_static/keyretrieval2.png)

::: moniker-end

::: moniker range="< aspnetcore-2.0"

![Anahtar oluşturma](key-management/_static/keycreation1.png)

<span data-ttu-id="49ecc-146">*Anahtar oluşturma/CreateNewKey*</span><span class="sxs-lookup"><span data-stu-id="49ecc-146">*Key Creation / CreateNewKey*</span></span>

<span data-ttu-id="49ecc-147">Uygulamasında `CreateNewKey` , `IAuthenticatedEncryptorConfiguration` bileşeni, `IAuthenticatedEncryptorDescriptor` daha sonra XML olarak seri hale getirilen benzersiz bir oluşturmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="49ecc-147">In the implementation of `CreateNewKey`, the `IAuthenticatedEncryptorConfiguration` component is used to create a unique `IAuthenticatedEncryptorDescriptor`, which is then serialized as XML.</span></span> <span data-ttu-id="49ecc-148">Bir anahtar Emanet havuzu mevcutsa, ham (şifrelenmemiş) XML, uzun vadeli depolama için havuza sağlanır.</span><span class="sxs-lookup"><span data-stu-id="49ecc-148">If a key escrow sink is present, the raw (unencrypted) XML is provided to the sink for long-term storage.</span></span> <span data-ttu-id="49ecc-149">Şifrelenmemiş XML, `IXmlEncryptor` ŞIFRELENEN XML belgesi oluşturmak için (gerekliyse) ile çalıştırılır.</span><span class="sxs-lookup"><span data-stu-id="49ecc-149">The unencrypted XML is then run through an `IXmlEncryptor` (if required) to generate the encrypted XML document.</span></span> <span data-ttu-id="49ecc-150">Bu şifrelenmiş belge, ile uzun süreli depolamaya devam ediyor `IXmlRepository` .</span><span class="sxs-lookup"><span data-stu-id="49ecc-150">This encrypted document is persisted to long-term storage via the `IXmlRepository`.</span></span> <span data-ttu-id="49ecc-151">( `IXmlEncryptor` Yapılandırılmamışsa, şifrelenmemiş belge içinde kalıcı hale getirilir `IXmlRepository` .)</span><span class="sxs-lookup"><span data-stu-id="49ecc-151">(If no `IXmlEncryptor` is configured, the unencrypted document is persisted in the `IXmlRepository`.)</span></span>

![Anahtar alımı](key-management/_static/keyretrieval1.png)

::: moniker-end

<span data-ttu-id="49ecc-153">*Anahtar alımı/GetAllKeys*</span><span class="sxs-lookup"><span data-stu-id="49ecc-153">*Key Retrieval / GetAllKeys*</span></span>

<span data-ttu-id="49ecc-154">Uygulamasında `GetAllKeys` , anahtarları temsil eden xml belgeleri ve geri alınamaz `IXmlRepository` .</span><span class="sxs-lookup"><span data-stu-id="49ecc-154">In the implementation of `GetAllKeys`, the XML documents representing keys and revocations are read from the underlying `IXmlRepository`.</span></span> <span data-ttu-id="49ecc-155">Bu belgeler şifrelenirse, sistem otomatik olarak şifresini çözer.</span><span class="sxs-lookup"><span data-stu-id="49ecc-155">If these documents are encrypted, the system will automatically decrypt them.</span></span> <span data-ttu-id="49ecc-156">`XmlKeyManager``IAuthenticatedEncryptorDescriptorDeserializer`belgelerin serisini kaldırmak için uygun örnekleri oluşturur `IAuthenticatedEncryptorDescriptor` , daha sonra ayrı örneklerde sarmalanır `IKey` .</span><span class="sxs-lookup"><span data-stu-id="49ecc-156">`XmlKeyManager` creates the appropriate `IAuthenticatedEncryptorDescriptorDeserializer` instances to deserialize the documents back into `IAuthenticatedEncryptorDescriptor` instances, which are then wrapped in individual `IKey` instances.</span></span> <span data-ttu-id="49ecc-157">Bu örnek koleksiyonu `IKey` çağırana döndürülür.</span><span class="sxs-lookup"><span data-stu-id="49ecc-157">This collection of `IKey` instances is returned to the caller.</span></span>

<span data-ttu-id="49ecc-158">Belirli XML öğeleri hakkında daha fazla bilgi, [anahtar depolama biçimi belgesinde](xref:security/data-protection/implementation/key-storage-format#data-protection-implementation-key-storage-format)bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="49ecc-158">Further information on the particular XML elements can be found in the [key storage format document](xref:security/data-protection/implementation/key-storage-format#data-protection-implementation-key-storage-format).</span></span>

## <a name="ixmlrepository"></a><span data-ttu-id="49ecc-159">Ixmlrepository</span><span class="sxs-lookup"><span data-stu-id="49ecc-159">IXmlRepository</span></span>

<span data-ttu-id="49ecc-160">`IXmlRepository`Arabirim, bir yedekleme deposundan XML 'i kalıcı hale getirebilen ve XML alabileceği bir türü temsil eder.</span><span class="sxs-lookup"><span data-stu-id="49ecc-160">The `IXmlRepository` interface represents a type that can persist XML to and retrieve XML from a backing store.</span></span> <span data-ttu-id="49ecc-161">İki API 'yi kullanıma sunar:</span><span class="sxs-lookup"><span data-stu-id="49ecc-161">It exposes two APIs:</span></span>

* <span data-ttu-id="49ecc-162">`GetAllElements` :`IReadOnlyCollection<XElement>`</span><span class="sxs-lookup"><span data-stu-id="49ecc-162">`GetAllElements` :`IReadOnlyCollection<XElement>`</span></span>

* `StoreElement(XElement element, string friendlyName)`

<span data-ttu-id="49ecc-163">Uygulamalarının, bu uygulamaların `IXmlRepository` içinden geçen XML ayrıştırmasına gerek yoktur.</span><span class="sxs-lookup"><span data-stu-id="49ecc-163">Implementations of `IXmlRepository` don't need to parse the XML passing through them.</span></span> <span data-ttu-id="49ecc-164">XML belgelerini donuk olarak ele almalıdır ve daha yüksek katmanların belgeleri oluşturma ve ayrıştırma konusunda endişelenmesine imkan sağlar.</span><span class="sxs-lookup"><span data-stu-id="49ecc-164">They should treat the XML documents as opaque and let higher layers worry about generating and parsing the documents.</span></span>

<span data-ttu-id="49ecc-165">Aşağıdakileri uygulayan dört yerleşik somut tür vardır `IXmlRepository` :</span><span class="sxs-lookup"><span data-stu-id="49ecc-165">There are four built-in concrete types which implement `IXmlRepository`:</span></span>

::: moniker range=">= aspnetcore-2.2"

* [<span data-ttu-id="49ecc-166">FileSystemXmlRepository</span><span class="sxs-lookup"><span data-stu-id="49ecc-166">FileSystemXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.filesystemxmlrepository)
* [<span data-ttu-id="49ecc-167">RegistryXmlRepository</span><span class="sxs-lookup"><span data-stu-id="49ecc-167">RegistryXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository)
* [<span data-ttu-id="49ecc-168">AzureStorage. AzureBlobXmlRepository</span><span class="sxs-lookup"><span data-stu-id="49ecc-168">AzureStorage.AzureBlobXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.azurestorage.azureblobxmlrepository)
* [<span data-ttu-id="49ecc-169">RedisXmlRepository</span><span class="sxs-lookup"><span data-stu-id="49ecc-169">RedisXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.stackexchangeredis.redisxmlrepository)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* [<span data-ttu-id="49ecc-170">FileSystemXmlRepository</span><span class="sxs-lookup"><span data-stu-id="49ecc-170">FileSystemXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.filesystemxmlrepository)
* [<span data-ttu-id="49ecc-171">RegistryXmlRepository</span><span class="sxs-lookup"><span data-stu-id="49ecc-171">RegistryXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository)
* [<span data-ttu-id="49ecc-172">AzureStorage. AzureBlobXmlRepository</span><span class="sxs-lookup"><span data-stu-id="49ecc-172">AzureStorage.AzureBlobXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.azurestorage.azureblobxmlrepository)
* [<span data-ttu-id="49ecc-173">RedisXmlRepository</span><span class="sxs-lookup"><span data-stu-id="49ecc-173">RedisXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.redisxmlrepository)

::: moniker-end

<span data-ttu-id="49ecc-174">Daha fazla bilgi için bkz. [anahtar depolama sağlayıcıları belgesi](xref:security/data-protection/implementation/key-storage-providers) .</span><span class="sxs-lookup"><span data-stu-id="49ecc-174">See the [key storage providers document](xref:security/data-protection/implementation/key-storage-providers) for more information.</span></span>

<span data-ttu-id="49ecc-175">Özel kaydetme, `IXmlRepository` farklı bir yedekleme deposu (örneğin, Azure Tablo Depolaması) kullanılırken uygundur.</span><span class="sxs-lookup"><span data-stu-id="49ecc-175">Registering a custom `IXmlRepository` is appropriate when using a different backing store (for example, Azure Table Storage).</span></span>

<span data-ttu-id="49ecc-176">Varsayılan depo uygulama genelinde değiştirmek için özel bir `IXmlRepository` örnek kaydedin:</span><span class="sxs-lookup"><span data-stu-id="49ecc-176">To change the default repository application-wide, register a custom `IXmlRepository` instance:</span></span>

::: moniker range=">= aspnetcore-2.0"

```csharp
services.Configure<KeyManagementOptions>(options => options.XmlRepository = new MyCustomXmlRepository());
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
services.AddSingleton<IXmlRepository>(new MyCustomXmlRepository());
```

::: moniker-end

## <a name="ixmlencryptor"></a><span data-ttu-id="49ecc-177">Ixmlencryptor</span><span class="sxs-lookup"><span data-stu-id="49ecc-177">IXmlEncryptor</span></span>

<span data-ttu-id="49ecc-178">`IXmlEncryptor`Arabirim, düz metın XML öğesini şifreleyemeyen bir türü temsil eder.</span><span class="sxs-lookup"><span data-stu-id="49ecc-178">The `IXmlEncryptor` interface represents a type that can encrypt a plaintext XML element.</span></span> <span data-ttu-id="49ecc-179">Tek bir API 'YI kullanıma sunar:</span><span class="sxs-lookup"><span data-stu-id="49ecc-179">It exposes a single API:</span></span>

* <span data-ttu-id="49ecc-180">Şifreleme (XElement plaintextElement): Encryptedxmlinınfo</span><span class="sxs-lookup"><span data-stu-id="49ecc-180">Encrypt(XElement plaintextElement) : EncryptedXmlInfo</span></span>

<span data-ttu-id="49ecc-181">Seri hale getirilen bir `IAuthenticatedEncryptorDescriptor` "şifreleme gerektirir" olarak işaretlenen herhangi bir öğe içeriyorsa, `XmlKeyManager` Bu öğeleri yapılandırılmış yöntemi aracılığıyla çalıştırır `IXmlEncryptor` `Encrypt` ve düz metin öğesi yerine, şifreli öğeyi kalıcı hale gelir `IXmlRepository` .</span><span class="sxs-lookup"><span data-stu-id="49ecc-181">If a serialized `IAuthenticatedEncryptorDescriptor` contains any elements marked as "requires encryption", then `XmlKeyManager` will run those elements through the configured `IXmlEncryptor`'s `Encrypt` method, and it will persist the enciphered element rather than the plaintext element to the `IXmlRepository`.</span></span> <span data-ttu-id="49ecc-182">`Encrypt`Yönteminin çıktısı bir `EncryptedXmlInfo` nesnedir.</span><span class="sxs-lookup"><span data-stu-id="49ecc-182">The output of the `Encrypt` method is an `EncryptedXmlInfo` object.</span></span> <span data-ttu-id="49ecc-183">Bu nesne, hem sonuç veren bir sarmalayıcı hem de `XElement` `IXmlDecryptor` karşılık gelen öğeyi şifre altına almak Için kullanılabilecek türünü temsil eden bir sarmalayıcı içerir.</span><span class="sxs-lookup"><span data-stu-id="49ecc-183">This object is a wrapper which contains both the resultant enciphered `XElement` and the Type which represents an `IXmlDecryptor` which can be used to decipher the corresponding element.</span></span>

<span data-ttu-id="49ecc-184">Aşağıdakileri uygulayan dört yerleşik somut tür vardır `IXmlEncryptor` :</span><span class="sxs-lookup"><span data-stu-id="49ecc-184">There are four built-in concrete types which implement `IXmlEncryptor`:</span></span>

* [<span data-ttu-id="49ecc-185">CertificateXmlEncryptor</span><span class="sxs-lookup"><span data-stu-id="49ecc-185">CertificateXmlEncryptor</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.certificatexmlencryptor)
* [<span data-ttu-id="49ecc-186">DpapiNGXmlEncryptor</span><span class="sxs-lookup"><span data-stu-id="49ecc-186">DpapiNGXmlEncryptor</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.dpapingxmlencryptor)
* [<span data-ttu-id="49ecc-187">Dpapikselmlencryptor</span><span class="sxs-lookup"><span data-stu-id="49ecc-187">DpapiXmlEncryptor</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.dpapixmlencryptor)
* [<span data-ttu-id="49ecc-188">NullXmlEncryptor</span><span class="sxs-lookup"><span data-stu-id="49ecc-188">NullXmlEncryptor</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.nullxmlencryptor)

<span data-ttu-id="49ecc-189">Daha fazla bilgi için [rest belgesinde anahtar şifrelemeyi](xref:security/data-protection/implementation/key-encryption-at-rest) inceleyin.</span><span class="sxs-lookup"><span data-stu-id="49ecc-189">See the [key encryption at rest document](xref:security/data-protection/implementation/key-encryption-at-rest) for more information.</span></span>

<span data-ttu-id="49ecc-190">Varsayılan anahtar şifreleme-Rest mekanizmasını uygulama genelinde değiştirmek için özel bir `IXmlEncryptor` örnek kaydedin:</span><span class="sxs-lookup"><span data-stu-id="49ecc-190">To change the default key-encryption-at-rest mechanism application-wide, register a custom `IXmlEncryptor` instance:</span></span>

::: moniker range=">= aspnetcore-2.0"

```csharp
services.Configure<KeyManagementOptions>(options => options.XmlEncryptor = new MyCustomXmlEncryptor());
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
services.AddSingleton<IXmlEncryptor>(new MyCustomXmlEncryptor());
```

::: moniker-end

## <a name="ixmldecryptor"></a><span data-ttu-id="49ecc-191">Ixmldecryptor</span><span class="sxs-lookup"><span data-stu-id="49ecc-191">IXmlDecryptor</span></span>

<span data-ttu-id="49ecc-192">`IXmlDecryptor`Arabirim, `XElement` bir ile şifrelenen bir dosyanın şifresini çözmeyi bilen bir türü temsil eder `IXmlEncryptor` .</span><span class="sxs-lookup"><span data-stu-id="49ecc-192">The `IXmlDecryptor` interface represents a type that knows how to decrypt an `XElement` that was enciphered via an `IXmlEncryptor`.</span></span> <span data-ttu-id="49ecc-193">Tek bir API 'YI kullanıma sunar:</span><span class="sxs-lookup"><span data-stu-id="49ecc-193">It exposes a single API:</span></span>

* <span data-ttu-id="49ecc-194">Şifre çözme (XElement encryptedElement): XElement</span><span class="sxs-lookup"><span data-stu-id="49ecc-194">Decrypt(XElement encryptedElement) : XElement</span></span>

<span data-ttu-id="49ecc-195">`Decrypt`Yöntemi tarafından gerçekleştirilen şifrelemeyi geri alır `IXmlEncryptor.Encrypt` .</span><span class="sxs-lookup"><span data-stu-id="49ecc-195">The `Decrypt` method undoes the encryption performed by `IXmlEncryptor.Encrypt`.</span></span> <span data-ttu-id="49ecc-196">Genellikle, her somut `IXmlEncryptor` uygulamanın karşılık gelen somut bir uygulamaya sahip olması gerekir `IXmlDecryptor` .</span><span class="sxs-lookup"><span data-stu-id="49ecc-196">Generally, each concrete `IXmlEncryptor` implementation will have a corresponding concrete `IXmlDecryptor` implementation.</span></span>

<span data-ttu-id="49ecc-197">Uygulayan türler `IXmlDecryptor` aşağıdaki iki ortak oluşturucudan birine sahip olmalıdır:</span><span class="sxs-lookup"><span data-stu-id="49ecc-197">Types which implement `IXmlDecryptor` should have one of the following two public constructors:</span></span>

* <span data-ttu-id="49ecc-198">. ctor (Iservvoiceprovider)</span><span class="sxs-lookup"><span data-stu-id="49ecc-198">.ctor(IServiceProvider)</span></span>
* <span data-ttu-id="49ecc-199">. ctor ()</span><span class="sxs-lookup"><span data-stu-id="49ecc-199">.ctor()</span></span>

> [!NOTE]
> <span data-ttu-id="49ecc-200">`IServiceProvider`Oluşturucuya geçirilen değer null olabilir.</span><span class="sxs-lookup"><span data-stu-id="49ecc-200">The `IServiceProvider` passed to the constructor may be null.</span></span>

## <a name="ikeyescrowsink"></a><span data-ttu-id="49ecc-201">Ikeyescrowsink</span><span class="sxs-lookup"><span data-stu-id="49ecc-201">IKeyEscrowSink</span></span>

<span data-ttu-id="49ecc-202">`IKeyEscrowSink`Arabirim, hassas bilgiler Emanet gerçekleştirebilen bir türü temsil eder.</span><span class="sxs-lookup"><span data-stu-id="49ecc-202">The `IKeyEscrowSink` interface represents a type that can perform escrow of sensitive information.</span></span> <span data-ttu-id="49ecc-203">Seri hale getirilmiş tanımlayıcıların gizli bilgiler (örneğin, şifreleme malzemeleri) içerebileceğini ve bu, ilk yerde [ıxmlencryptor](#ixmlencryptor) türünün giriş ile ilgili olduğunu unutmayın.</span><span class="sxs-lookup"><span data-stu-id="49ecc-203">Recall that serialized descriptors might contain sensitive information (such as cryptographic material), and this is what led to the introduction of the [IXmlEncryptor](#ixmlencryptor) type in the first place.</span></span> <span data-ttu-id="49ecc-204">Ancak, kazalardan dolayı gerçekleşir ve anahtar halkaları silinebilir veya bozulabilir.</span><span class="sxs-lookup"><span data-stu-id="49ecc-204">However, accidents happen, and key rings can be deleted or become corrupted.</span></span>

<span data-ttu-id="49ecc-205">Emanet arabirimi, bir acil durum çıkış taraması sağlar ve bu, yapılandırılmış [ıxmlencryptor](#ixmlencryptor)tarafından dönüştürülmeden önce ham serileştirilmiş XML erişimine izin verir.</span><span class="sxs-lookup"><span data-stu-id="49ecc-205">The escrow interface provides an emergency escape hatch, allowing access to the raw serialized XML before it's transformed by any configured [IXmlEncryptor](#ixmlencryptor).</span></span> <span data-ttu-id="49ecc-206">Arabirim tek bir API 'YI kullanıma sunar:</span><span class="sxs-lookup"><span data-stu-id="49ecc-206">The interface exposes a single API:</span></span>

* <span data-ttu-id="49ecc-207">Mağaza (GUID keyId, XElement öğesi)</span><span class="sxs-lookup"><span data-stu-id="49ecc-207">Store(Guid keyId, XElement element)</span></span>

<span data-ttu-id="49ecc-208">`IKeyEscrowSink`İş ilkesiyle güvenli bir şekilde tutarlı bir şekilde, belirtilen öğeyi işlemek için uygulamaya en uygun seçenektir.</span><span class="sxs-lookup"><span data-stu-id="49ecc-208">It's up to the `IKeyEscrowSink` implementation to handle the provided element in a secure manner consistent with business policy.</span></span> <span data-ttu-id="49ecc-209">Mümkün olan bir uygulama,, sertifikanın özel anahtarının esutde olduğu bilinen bir kurumsal X. 509.440 sertifikası kullanarak XML öğesini şifrelemek üzere, Emanet havuzu için olabilir. `CertificateXmlEncryptor`tür, bu yardımcı olabilir.</span><span class="sxs-lookup"><span data-stu-id="49ecc-209">One possible implementation could be for the escrow sink to encrypt the XML element using a known corporate X.509 certificate where the certificate's private key has been escrowed; the `CertificateXmlEncryptor` type can assist with this.</span></span> <span data-ttu-id="49ecc-210">`IKeyEscrowSink`Uygulama, belirtilen öğeyi uygun şekilde kalıcı hale getirmekten de sorumludur.</span><span class="sxs-lookup"><span data-stu-id="49ecc-210">The `IKeyEscrowSink` implementation is also responsible for persisting the provided element appropriately.</span></span>

<span data-ttu-id="49ecc-211">Varsayılan olarak, sunucu yöneticileri [bunu genel olarak yapılandırabilse](xref:security/data-protection/configuration/machine-wide-policy)de bir emanet mekanizması etkinleştirilmez.</span><span class="sxs-lookup"><span data-stu-id="49ecc-211">By default no escrow mechanism is enabled, though server administrators can [configure this globally](xref:security/data-protection/configuration/machine-wide-policy).</span></span> <span data-ttu-id="49ecc-212">Ayrıca, `IDataProtectionBuilder.AddKeyEscrowSink` Aşağıdaki örnekte gösterildiği gibi yöntemi aracılığıyla programlı bir şekilde yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="49ecc-212">It can also be configured programmatically via the `IDataProtectionBuilder.AddKeyEscrowSink` method as shown in the sample below.</span></span> <span data-ttu-id="49ecc-213">`AddKeyEscrowSink` `IServiceCollection.AddSingleton` `IServiceCollection.AddInstance` `IKeyEscrowSink` Örneklerin tekton olmasını amaçlandığı için yöntem, ve aşırı yüklemelerini yansıtır.</span><span class="sxs-lookup"><span data-stu-id="49ecc-213">The `AddKeyEscrowSink` method overloads mirror the `IServiceCollection.AddSingleton` and `IServiceCollection.AddInstance` overloads, as `IKeyEscrowSink` instances are intended to be singletons.</span></span> <span data-ttu-id="49ecc-214">Birden çok `IKeyEscrowSink` örnek kayıtlıysa, her biri anahtar oluşturma sırasında çağrılır, bu nedenle anahtarlar birden çok mekanizmayı eşzamanlı olarak gerçekleştirebilir.</span><span class="sxs-lookup"><span data-stu-id="49ecc-214">If multiple `IKeyEscrowSink` instances are registered, each one will be called during key generation, so keys can be escrowed to multiple mechanisms simultaneously.</span></span>

<span data-ttu-id="49ecc-215">Bir örnekten malzeme okumak için bir API yok `IKeyEscrowSink` .</span><span class="sxs-lookup"><span data-stu-id="49ecc-215">There's no API to read material from an `IKeyEscrowSink` instance.</span></span> <span data-ttu-id="49ecc-216">Bu, Emanet mekanizmalarının tasarım teorisiyle tutarlıdır: anahtar malzemesini güvenilen bir yetkili için erişilebilir hale getirmek ve uygulamanın kendisi güvenilir bir yetkili olmadığından, kendi escroçar malzemesine erişemez.</span><span class="sxs-lookup"><span data-stu-id="49ecc-216">This is consistent with the design theory of the escrow mechanism: it's intended to make the key material accessible to a trusted authority, and since the application is itself not a trusted authority, it shouldn't have access to its own escrowed material.</span></span>

<span data-ttu-id="49ecc-217">Aşağıdaki örnek kod, `IKeyEscrowSink` anahtarların yalnızca "CONTOSODomain Admins" üyelerinin kurtarabileceği şekilde, bir yerde oluşturulması ve kaydolduğunu gösterir.</span><span class="sxs-lookup"><span data-stu-id="49ecc-217">The following sample code demonstrates creating and registering an `IKeyEscrowSink` where keys are escrowed such that only members of "CONTOSODomain Admins" can recover them.</span></span>

> [!NOTE]
> <span data-ttu-id="49ecc-218">Bu örneği çalıştırmak için, etki alanına katılmış bir Windows 8/Windows Server 2012 makinesinde olmanız ve etki alanı denetleyicisinin Windows Server 2012 veya üzeri olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="49ecc-218">To run this sample, you must be on a domain-joined Windows 8 / Windows Server 2012 machine, and the domain controller must be Windows Server 2012 or later.</span></span>

[!code-csharp[](key-management/samples/key-management-extensibility.cs)]
