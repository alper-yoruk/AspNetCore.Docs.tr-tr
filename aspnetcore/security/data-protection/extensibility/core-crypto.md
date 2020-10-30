---
title: ASP.NET Core temel şifreleme genişletilebilirliği
author: rick-anderson
description: Iauthenticatedencryptor, ıauthenticatedencryptordescriptor, ıauthenticatedencryptordescriptordeserializer ve en üst düzey fabrika hakkında bilgi edinin.
ms.author: riande
ms.date: 08/11/2017
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
uid: security/data-protection/extensibility/core-crypto
ms.openlocfilehash: 99ce283d56a6010ddd846f21e0ca9a9a324d55fc
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060787"
---
# <a name="core-cryptography-extensibility-in-aspnet-core"></a>ASP.NET Core temel şifreleme genişletilebilirliği

<a name="data-protection-extensibility-core-crypto"></a>

>[!WARNING]
> Aşağıdaki arabirimlerin herhangi birini uygulayan türler birden çok çağıranlar için iş parçacığı açısından güvenli olmalıdır.

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptor"></a>

## <a name="iauthenticatedencryptor"></a>Iauthenticatedencryptor

**Iauthenticatedencryptor** arabirimi, şifreleme alt sisteminin temel yapı taşıdır. Anahtar başına genellikle bir ıauthenticatedencryptor vardır ve ıauthenticatedencryptor örneği, şifreleme işlemlerini gerçekleştirmek için gereken tüm şifreleme anahtarı malzemesini ve algoritmik bilgilerini sarmalar.

Adından da anlaşılacağı gibi, tür kimliği doğrulanmış şifreleme ve şifre çözme hizmetleri sağlamaktan sorumludur. Aşağıdaki iki API 'yi kullanıma sunar.

* `Decrypt(ArraySegment<byte> ciphertext, ArraySegment<byte> additionalAuthenticatedData) : byte[]`

* `Encrypt(ArraySegment<byte> plaintext, ArraySegment<byte> additionalAuthenticatedData) : byte[]`

Encrypt yöntemi, şifreli düz metin ve bir kimlik doğrulama etiketi içeren bir blobu döndürür. Kimlik doğrulama etiketi, ek kimliği doğrulanmış verileri (AAD) içermelidir, ancak AAD 'nin son yükün geri kurtarılması gerekmez. Şifre çözme yöntemi, kimlik doğrulama etiketini doğrular ve çözülemez yükü döndürür. Tüm başarısızlıklar (ArgumentNullException ve benzeri hariç) CryptographicException için hogenlanmış olmalıdır.

> [!NOTE]
> Iauthenticatedencryptor örneğinin kendisi aslında anahtar malzemesini içermesi gerekmez. Örneğin, uygulama tüm işlemler için bir HSM 'ye temsilci verebilir.

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptorfactory"></a>
<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor"></a>

## <a name="how-to-create-an-iauthenticatedencryptor"></a>Iauthenticatedencryptor oluşturma

# <a name="aspnet-core-2x"></a>[ASP.NET Core 2. x](#tab/aspnetcore2x)

**Iauthenticatedencryptorfactory** arabirimi, bir [ıauthenticatedencryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) örneğinin nasıl oluşturulacağını bilen bir türü temsil eder. API 'SI aşağıdaki gibidir.

* Createencryptorınstance (Ikey anahtarı): ıauthenticatedencryptor

Belirli bir Ikey örneği için, Createencryptorınstance yöntemi tarafından oluşturulan tüm kimliği doğrulanmış şifreleyiciler, aşağıdaki kod örneğinde olduğu gibi eşdeğer olarak kabul edilmelidir.

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

# <a name="aspnet-core-1x"></a>[ASP.NET Core 1. x](#tab/aspnetcore1x)

**Iauthenticatedencryptordescriptor** arabirimi bir [ıauthenticatedencryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) örneğinin nasıl oluşturulacağını bilen bir türü temsil eder. API 'SI aşağıdaki gibidir.

* Createencryptorınstance (): ıauthenticatedencryptor

* ExportToXml (): Xmlserializeddescriptorınfo

Iauthenticatedencryptor gibi bir ıauthenticatedencryptordescriptor örneği, belirli bir anahtarı kaydırmak için kabul edilir. Yani, belirtilen ıauthenticatedencryptordescriptor örneği için, aşağıdaki kod örneğinde olduğu gibi, Createencryptorınstance yöntemi tarafından oluşturulan tüm kimliği doğrulanmış şifreleyiciler eşdeğer olarak kabul edilmelidir.

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

## <a name="iauthenticatedencryptordescriptor-aspnet-core-2x-only"></a>Iauthenticatedencryptordescriptor (yalnızca ASP.NET Core 2. x)

# <a name="aspnet-core-2x"></a>[ASP.NET Core 2. x](#tab/aspnetcore2x)

**Iauthenticatedencryptordescriptor** arabirimi, kendisini XML 'e aktarmayı bilen bir türü temsil eder. API 'SI aşağıdaki gibidir.

* ExportToXml (): Xmlserializeddescriptorınfo

# <a name="aspnet-core-1x"></a>[ASP.NET Core 1. x](#tab/aspnetcore1x)

---

## <a name="xml-serialization"></a>XML seri hale getirme

Iauthenticatedencryptor ve ıauthenticatedencryptordescriptor arasındaki birincil fark, tanımlayıcının Şifreleyici 'nin nasıl oluşturulacağını ve geçerli bağımsız değişkenlerle nasıl tedarik olduğunu bilmesini sağlar. Uygulaması SymmetricAlgorithm ve KeyedHashAlgorithm kullanan bir ıauthenticatedencryptor öğesini düşünün. Şifreleyici 'nin işi bu türleri tüketmek, ancak bu türlerin nereden geldiğini bilmesi gerekmez, bu nedenle uygulama yeniden başlatıldığında kendisini yeniden oluşturma işleminin doğru bir açıklamasını yazmamaktadır. Tanımlayıcı, bunun üzerine daha yüksek bir düzey görevi görür. Tanımlayıcı, bir Şifreleyici örneğinin nasıl oluşturulacağını bildiği için (örneğin, gerekli algoritmaların nasıl oluşturulacağını bilir), bu bilgileri XML biçiminde seri hale getirmek için, bir uygulama sıfırlandıktan sonra Şifreleyici örneğinin yeniden oluşturulabilir olmasını sağlayabilirsiniz.

<a name="data-protection-extensibility-core-crypto-exporttoxml"></a>

Tanımlayıcı, ExportToXml yordamı aracılığıyla seri hale getirilebilir. Bu yordam iki özellik içeren bir Xmlserializeddescriptorınfo döndürür: Descriptor 'ın XElement temsili ve bu tanımlayıcıyı ilgili XElement 'e geri döndürmek için kullanılabilecek bir [ıauthenticatedencryptordescriptordeserializer](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer) temsil eden tür.

Serileştirilmiş tanımlayıcı, şifreleme anahtar malzemesi gibi hassas bilgiler içerebilir. Veri koruma sistemi, depolama alanına kalıcı olmadan önce bilgileri şifrelemek için yerleşik desteğe sahiptir. Bu özelliğin avantajlarından yararlanabilmek için tanımlayıcı, "requiresEncryption" (xmlns " <http://schemas.asp.net/2015/03/dataProtection> ") öznitelik adı, "true" özniteliği ile hassas bilgiler içeren öğeyi işaretlemelidir.

>[!TIP]
> Bu özniteliği ayarlamak için yardımcı bir API vardır. Ad alanı Microsoft.AspNetCore.DataProtection.AuthenticatedEncryption.ConfigurationModel içinde bulunan XElement. Markasrequiresencryptıon () uzantı yöntemini çağırın.

Ayrıca, serileştirilmiş tanımlayıcının gizli bilgiler içermediği durumlar da olabilir. Bir HSM 'de depolanan bir şifreleme anahtarının durumunu yeniden deneyin. HSM, malzemeyi düz metin biçiminde kullanıma sunmayadıklarından, tanımlayıcı, kendisini serileştirilirken anahtar malzemesini yazamaz. Bunun yerine, tanımlayıcı, anahtarın anahtar Sarmalanan sürümünü yazabilir (HSM bu şekilde dışarı aktarmaya izin veriyorsa) veya HSM 'nin anahtar için kendi benzersiz tanımlayıcısıdır.

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer"></a>

## <a name="iauthenticatedencryptordescriptordeserializer"></a>Iauthenticatedencryptordescriptordeserializer

**Iauthenticatedencryptordescriptordeserializer** arabirimi bir ıauthenticatedencryptordescriptor örneğinin bir XElement öğesinden serisini nasıl çıkaracağını bilen bir türü temsil eder. Tek bir yöntem sunar:

* ImportFromXML (XElement öğesi): ıauthenticatedencryptordescriptor

ImportFromXML yöntemi [ıauthenticatedencryptordescriptor. ExportToXml](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-exporttoxml) tarafından döndürülen XElement değerini alır ve özgün ıauthenticatedencryptordescriptor 'ın eşdeğerini oluşturur.

Iauthenticatedencryptordescriptordeserializer uygulayan türler aşağıdaki iki ortak oluşturucudan birine sahip olmalıdır:

* . ctor (Iservvoiceprovider)

* . ctor ()

> [!NOTE]
> Oluşturucuya geçirilen IServiceProvider null olabilir.

## <a name="the-top-level-factory"></a>En üst düzey fabrika

# <a name="aspnet-core-2x"></a>[ASP.NET Core 2. x](#tab/aspnetcore2x)

**AlgorithmConfiguration** sınıfı, [ıauthenticatedencryptordescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) örneklerinin nasıl oluşturulacağını bilen bir türü temsil eder. Tek bir API sunar.

* CreateNewDescriptor (): ıauthenticatedencryptordescriptor

En üst düzey fabrika olarak AlgorithmConfiguration düşünün. Yapılandırma bir şablon işlevi görür. Algoritmik bilgilerini sarmalar (örn. bu yapılandırma bir AES-128-GCM ana anahtarıyla tanımlayıcılar üretir), ancak henüz belirli bir anahtarla ilişkilendirilmemiş.

CreateNewDescriptor çağrıldığında, yalnızca bu çağrı için yeni anahtar malzemesi oluşturulur ve bu anahtar malzemesini ve malzemeyi tüketmek için gereken algoritmik bilgilerini sarmalayan yeni bir ıauthenticatedencryptordescriptor üretilir. Ana malzeme yazılımda (ve bellekte tutulan) oluşturulabilir, bir HSM içinde oluşturulup tutulabilir ve bu şekilde devam eder. Önemli nokta, CreateNewDescriptor 'a yapılan iki çağrının asla eşdeğer ıauthenticatedencryptordescriptor örnekleri oluşturmamalıdır.

AlgorithmConfiguration türü, [otomatik anahtar yuvarlama](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling)gibi anahtar oluşturma yordamları için giriş noktası olarak görev yapar. Tüm gelecek anahtarların uygulamasını değiştirmek için KeyManagementOptions içindeki AuthenticatedEncryptorConfiguration özelliğini ayarlayın.

# <a name="aspnet-core-1x"></a>[ASP.NET Core 1. x](#tab/aspnetcore1x)

**Iauthenticatedencryptorconfiguration** arabirimi, [ıauthenticatedencryptordescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) örneklerinin nasıl oluşturulacağını bilen bir türü temsil eder. Tek bir API sunar.

* CreateNewDescriptor (): ıauthenticatedencryptordescriptor

Iauthenticatedencryptorconfiguration öğesini en üst düzey fabrika olarak düşünün. Yapılandırma bir şablon işlevi görür. Algoritmik bilgilerini sarmalar (örn. bu yapılandırma bir AES-128-GCM ana anahtarıyla tanımlayıcılar üretir), ancak henüz belirli bir anahtarla ilişkilendirilmemiş.

CreateNewDescriptor çağrıldığında, yalnızca bu çağrı için yeni anahtar malzemesi oluşturulur ve bu anahtar malzemesini ve malzemeyi tüketmek için gereken algoritmik bilgilerini sarmalayan yeni bir ıauthenticatedencryptordescriptor üretilir. Ana malzeme yazılımda (ve bellekte tutulan) oluşturulabilir, bir HSM içinde oluşturulup tutulabilir ve bu şekilde devam eder. Önemli nokta, CreateNewDescriptor 'a yapılan iki çağrının asla eşdeğer ıauthenticatedencryptordescriptor örnekleri oluşturmamalıdır.

Iauthenticatedencryptorconfiguration türü, [otomatik anahtar yuvarlama](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling)gibi anahtar oluşturma yordamları için giriş noktası olarak görev yapar. Tüm gelecek anahtarların uygulamasını değiştirmek için, hizmet kapsayıcısına tek bir ıauthenticatedencryptorconfiguration kaydedin.

---
