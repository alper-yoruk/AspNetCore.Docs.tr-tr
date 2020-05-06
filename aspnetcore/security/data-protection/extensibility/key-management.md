---
title: ASP.NET Core 'de anahtar yönetimi genişletilebilirliği
author: rick-anderson
description: ASP.NET Core Data Protection anahtar yönetimi genişletilebilirliği hakkında bilgi edinin.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 10/24/2018
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/extensibility/key-management
ms.openlocfilehash: f8af699344473510c5579c2f0e4d2920ada013f1
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775732"
---
# <a name="key-management-extensibility-in-aspnet-core"></a>ASP.NET Core 'de anahtar yönetimi genişletilebilirliği

> [!TIP]
> Bu API 'nin arkasındaki temel kavramların bazılarını açıklandığı için, bu bölümü okumadan önce [anahtar yönetimi](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) bölümünü okuyun.

> [!WARNING]
> Aşağıdaki arabirimlerin herhangi birini uygulayan türler birden çok çağıranlar için iş parçacığı açısından güvenli olmalıdır.

## <a name="key"></a>Anahtar

`IKey` Arabirim, Cryptosystem içindeki bir anahtarın temel gösterimidir. Bu anahtar, "şifreleme anahtar malzemesi" değerinde değil, Soyut anlamda burada kullanılır. Bir anahtar aşağıdaki özelliklere sahiptir:

* Etkinleştirme, oluşturma ve sona erme tarihleri

* İptal durumu

* Anahtar tanımlayıcı (GUID)

::: moniker range=">= aspnetcore-2.0"

Ayrıca, `IKey` bu anahtara `CreateEncryptor` bağlı bir [ıauthenticatedencryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) örneği oluşturmak için kullanılabilecek bir yöntem sunar.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Ayrıca, `IKey` bu anahtara `CreateEncryptorInstance` bağlı bir [ıauthenticatedencryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) örneği oluşturmak için kullanılabilecek bir yöntem sunar.

::: moniker-end

> [!NOTE]
> Bir `IKey` örnekten ham şifreleme malzemesini almak için API yok.

## <a name="ikeymanager"></a>Ikeymanager

`IKeyManager` Arabirim, genel anahtar depolama, alma ve düzenleme işleminden sorumlu bir nesneyi temsil eder. Üç üst düzey işlemi kullanıma sunar:

* Yeni bir anahtar oluşturun ve depolama alanına kalıcı hale getirin.

* Depolama alanından tüm anahtarları al.

* Bir veya daha fazla anahtarı iptal edin ve iptal bilgilerini depolamaya devam edin.

>[!WARNING]
> `IKeyManager` Yazmak çok gelişmiş bir görevdir ve geliştiricilerin çoğunluğu bunu denememelidir. Bunun yerine, çoğu Geliştirici [Xmlkeymanager](#xmlkeymanager) sınıfı tarafından sunulan tesislerin avantajlarından yararlanmalıdır.

## <a name="xmlkeymanager"></a>XmlKeyManager

`XmlKeyManager` Türü, öğesinin `IKeyManager`ın-Box somut uygulamasıdır. Bu, anahtar Emanet ve anahtarların şifrelenmesi dahil olmak üzere çeşitli yararlı olanaklar sağlar. Bu sistemdeki anahtarlar XML öğeleri (özellikle, [XElement](/dotnet/csharp/programming-guide/concepts/linq/xelement-class-overview)) olarak temsil edilir.

`XmlKeyManager`görevlerini yerine getirdikten sonra diğer birçok bileşene bağlıdır:

::: moniker range=">= aspnetcore-2.0"

* `AlgorithmConfiguration`Yeni anahtarlar tarafından kullanılan algoritmaları belirleyen.

* `IXmlRepository`Bu, anahtarların depolamada nerede kalıcı olduğunu denetler.

* `IXmlEncryptor`[isteğe bağlı], bekleyen anahtarları şifrelemeye izin verir.

* `IKeyEscrowSink`[isteğe bağlı], anahtar emanet hizmetleri sağlar.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

* `IXmlRepository`Bu, anahtarların depolamada nerede kalıcı olduğunu denetler.

* `IXmlEncryptor`[isteğe bağlı], bekleyen anahtarları şifrelemeye izin verir.

* `IKeyEscrowSink`[isteğe bağlı], anahtar emanet hizmetleri sağlar.

::: moniker-end

Aşağıda, bu bileşenlerin içinde `XmlKeyManager`nasıl birbirine bağlanacağını gösteren üst düzey diyagramlar bulunur.

::: moniker range=">= aspnetcore-2.0"

![Anahtar oluşturma](key-management/_static/keycreation2.png)

*Anahtar oluşturma/CreateNewKey*

Uygulamasında `CreateNewKey`, `AlgorithmConfiguration` BILEŞENI, daha sonra XML olarak seri hale getirilen benzersiz `IAuthenticatedEncryptorDescriptor`bir oluşturmak için kullanılır. Bir anahtar Emanet havuzu mevcutsa, ham (şifrelenmemiş) XML, uzun vadeli depolama için havuza sağlanır. Şifrelenmemiş XML, şifrelenen XML belgesi oluşturmak için `IXmlEncryptor` (gerekliyse) ile çalıştırılır. Bu şifrelenmiş belge, `IXmlRepository`ile uzun süreli depolamaya devam ediyor. `IXmlEncryptor` (Yapılandırılmamışsa, şifrelenmemiş belge içinde kalıcı hale getirilir `IXmlRepository`.)

![Anahtar alımı](key-management/_static/keyretrieval2.png)

::: moniker-end

::: moniker range="< aspnetcore-2.0"

![Anahtar oluşturma](key-management/_static/keycreation1.png)

*Anahtar oluşturma/CreateNewKey*

Uygulamasında `CreateNewKey`, `IAuthenticatedEncryptorConfiguration` BILEŞENI, daha sonra XML olarak seri hale getirilen benzersiz `IAuthenticatedEncryptorDescriptor`bir oluşturmak için kullanılır. Bir anahtar Emanet havuzu mevcutsa, ham (şifrelenmemiş) XML, uzun vadeli depolama için havuza sağlanır. Şifrelenmemiş XML, şifrelenen XML belgesi oluşturmak için `IXmlEncryptor` (gerekliyse) ile çalıştırılır. Bu şifrelenmiş belge, `IXmlRepository`ile uzun süreli depolamaya devam ediyor. `IXmlEncryptor` (Yapılandırılmamışsa, şifrelenmemiş belge içinde kalıcı hale getirilir `IXmlRepository`.)

![Anahtar alımı](key-management/_static/keyretrieval1.png)

::: moniker-end

*Anahtar alımı/GetAllKeys*

Uygulamasında `GetAllKeys`, anahtarları temsıl eden xml belgeleri ve geri alınamaz `IXmlRepository`. Bu belgeler şifrelenirse, sistem otomatik olarak şifresini çözer. `XmlKeyManager`belgelerin `IAuthenticatedEncryptorDescriptorDeserializer` `IAuthenticatedEncryptorDescriptor` serisini kaldırmak için uygun örnekleri oluşturur, daha sonra ayrı `IKey` örneklerde sarmalanır. Bu `IKey` örnek koleksiyonu çağırana döndürülür.

Belirli XML öğeleri hakkında daha fazla bilgi, [anahtar depolama biçimi belgesinde](xref:security/data-protection/implementation/key-storage-format#data-protection-implementation-key-storage-format)bulunabilir.

## <a name="ixmlrepository"></a>Ixmlrepository

Arabirim `IXmlRepository` , bir yedekleme deposundan XML 'i kalıcı hale getirebilen ve XML alabileceği bir türü temsil eder. İki API 'yi kullanıma sunar:

* `GetAllElements` :`IReadOnlyCollection<XElement>`

* `StoreElement(XElement element, string friendlyName)`

Uygulamalarının, `IXmlRepository` bu uygulamaların IÇINDEN geçen XML ayrıştırmasına gerek yoktur. XML belgelerini donuk olarak ele almalıdır ve daha yüksek katmanların belgeleri oluşturma ve ayrıştırma konusunda endişelenmesine imkan sağlar.

Aşağıdakileri uygulayan `IXmlRepository`dört yerleşik somut tür vardır:

::: moniker range=">= aspnetcore-2.2"

* [FileSystemXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.filesystemxmlrepository)
* [RegistryXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository)
* [AzureStorage. AzureBlobXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.azurestorage.azureblobxmlrepository)
* [RedisXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.stackexchangeredis.redisxmlrepository)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* [FileSystemXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.filesystemxmlrepository)
* [RegistryXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository)
* [AzureStorage. AzureBlobXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.azurestorage.azureblobxmlrepository)
* [RedisXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.redisxmlrepository)

::: moniker-end

Daha fazla bilgi için bkz. [anahtar depolama sağlayıcıları belgesi](xref:security/data-protection/implementation/key-storage-providers) .

Özel `IXmlRepository` kaydetme, farklı bir yedekleme deposu (örneğin, Azure Tablo Depolaması) kullanılırken uygundur.

Varsayılan depo uygulama genelinde değiştirmek için özel `IXmlRepository` bir örnek kaydedin:

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

## <a name="ixmlencryptor"></a>Ixmlencryptor

Arabirim `IXmlEncryptor` , düz metin xml öğesini şifreleyemeyen bir türü temsil eder. Tek bir API 'YI kullanıma sunar:

* Şifreleme (XElement plaintextElement): Encryptedxmlinınfo

Seri hale getirilen `IAuthenticatedEncryptorDescriptor` bir "şifreleme gerektirir `XmlKeyManager` " olarak işaretlenen herhangi bir öğe içeriyorsa, bu öğeleri yapılandırılmış `IXmlEncryptor` `Encrypt` yöntemi aracılığıyla çalıştırır ve düz metin öğesi yerine, `IXmlRepository`şifreli öğeyi kalıcı hale gelir. `Encrypt` Yönteminin çıktısı bir `EncryptedXmlInfo` nesnedir. Bu nesne, hem sonuç `XElement` veren bir sarmalayıcı hem de karşılık gelen öğeyi şifre altına almak Için kullanılabilecek türünü `IXmlDecryptor` temsil eden bir sarmalayıcı içerir.

Aşağıdakileri uygulayan `IXmlEncryptor`dört yerleşik somut tür vardır:

* [CertificateXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.certificatexmlencryptor)
* [DpapiNGXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.dpapingxmlencryptor)
* [Dpapikselmlencryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.dpapixmlencryptor)
* [NullXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.nullxmlencryptor)

Daha fazla bilgi için [rest belgesinde anahtar şifrelemeyi](xref:security/data-protection/implementation/key-encryption-at-rest) inceleyin.

Varsayılan anahtar şifreleme-Rest mekanizmasını uygulama genelinde değiştirmek için özel `IXmlEncryptor` bir örnek kaydedin:

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

## <a name="ixmldecryptor"></a>Ixmldecryptor

Arabirim `IXmlDecryptor` , bir `XElement` `IXmlEncryptor`ile şifrelenen bir dosyanın şifresini çözmeyi bilen bir türü temsil eder. Tek bir API 'YI kullanıma sunar:

* Şifre çözme (XElement encryptedElement): XElement

`Decrypt` Yöntemi tarafından `IXmlEncryptor.Encrypt`gerçekleştirilen şifrelemeyi geri alır. Genellikle, her somut `IXmlEncryptor` uygulamanın karşılık gelen somut `IXmlDecryptor` bir uygulamaya sahip olması gerekir.

Uygulayan `IXmlDecryptor` türler aşağıdaki iki ortak oluşturucudan birine sahip olmalıdır:

* . ctor (Iservvoiceprovider)
* . ctor ()

> [!NOTE]
> Oluşturucuya `IServiceProvider` geçirilen değer null olabilir.

## <a name="ikeyescrowsink"></a>Ikeyescrowsink

Arabirim `IKeyEscrowSink` , hassas bilgiler Emanet gerçekleştirebilen bir türü temsil eder. Seri hale getirilmiş tanımlayıcıların gizli bilgiler (örneğin, şifreleme malzemeleri) içerebileceğini ve bu, ilk yerde [ıxmlencryptor](#ixmlencryptor) türünün giriş ile ilgili olduğunu unutmayın. Ancak, kazalardan dolayı gerçekleşir ve anahtar halkaları silinebilir veya bozulabilir.

Emanet arabirimi, bir acil durum çıkış taraması sağlar ve bu, yapılandırılmış [ıxmlencryptor](#ixmlencryptor)tarafından dönüştürülmeden önce ham serileştirilmiş XML erişimine izin verir. Arabirim tek bir API 'YI kullanıma sunar:

* Mağaza (GUID keyId, XElement öğesi)

İş ilkesiyle güvenli bir şekilde `IKeyEscrowSink` tutarlı bir şekilde, belirtilen öğeyi işlemek için uygulamaya en uygun seçenektir. Mümkün olan bir uygulama,, sertifikanın özel anahtarının esutde olduğu bilinen bir kurumsal X. 509.440 sertifikası kullanarak XML öğesini şifrelemek üzere, Emanet havuzu için olabilir. tür `CertificateXmlEncryptor` , bu yardımcı olabilir. `IKeyEscrowSink` Uygulama, belirtilen öğeyi uygun şekilde kalıcı hale getirmekten de sorumludur.

Varsayılan olarak, sunucu yöneticileri [bunu genel olarak yapılandırabilse](xref:security/data-protection/configuration/machine-wide-policy)de bir emanet mekanizması etkinleştirilmez. Ayrıca, `IDataProtectionBuilder.AddKeyEscrowSink` aşağıdaki örnekte gösterildiği gibi yöntemi aracılığıyla programlı bir şekilde yapılandırılabilir. Örneklerin tekton olmasını `AddKeyEscrowSink` amaçlandığı için yöntem `IServiceCollection.AddSingleton` , ve `IServiceCollection.AddInstance` aşırı yüklemelerini yansıtır. `IKeyEscrowSink` Birden çok `IKeyEscrowSink` örnek kayıtlıysa, her biri anahtar oluşturma sırasında çağrılır, bu nedenle anahtarlar birden çok mekanizmayı eşzamanlı olarak gerçekleştirebilir.

Bir örnekten malzeme okumak için bir `IKeyEscrowSink` API yok. Bu, Emanet mekanizmalarının tasarım teorisiyle tutarlıdır: anahtar malzemesini güvenilen bir yetkili için erişilebilir hale getirmek ve uygulamanın kendisi güvenilir bir yetkili olmadığından, kendi escroçar malzemesine erişemez.

Aşağıdaki örnek kod, anahtarların yalnızca "CONTOSODomain `IKeyEscrowSink` Admins" üyelerinin kurtarabileceği şekilde, bir yerde oluşturulması ve kaydolduğunu gösterir.

> [!NOTE]
> Bu örneği çalıştırmak için, etki alanına katılmış bir Windows 8/Windows Server 2012 makinesinde olmanız ve etki alanı denetleyicisinin Windows Server 2012 veya üzeri olması gerekir.

[!code-csharp[](key-management/samples/key-management-extensibility.cs)]
