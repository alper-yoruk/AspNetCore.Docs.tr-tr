---
title: ASP.NET Core veri korumasını yapılandırma
author: rick-anderson
description: ASP.NET Core 'de veri korumayı yapılandırma hakkında bilgi edinin.
ms.author: riande
ms.custom: mvc
ms.date: 10/07/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/configuration/overview
ms.openlocfilehash: 096862b7149679c14e3d1ca17d38468a14e438a2
ms.sourcegitcommit: b0fa7ff0cb158277df61bcd08058a81222c3fe10
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/07/2020
ms.locfileid: "87913870"
---
# <a name="configure-aspnet-core-data-protection"></a>ASP.NET Core veri korumasını yapılandırma

Veri koruma sistemi başlatıldığında, işletimsel ortama göre [varsayılan ayarları](xref:security/data-protection/configuration/default-settings) uygular. Bu ayarlar, genellikle tek bir makinede çalışan uygulamalar için uygundur. Bir geliştiricinin varsayılan ayarları değiştirmek isteyebileceğiniz durumlar vardır:

* Uygulama birden çok makineye yayılır.
* Uyumluluk nedenleriyle.

Bu senaryolar için veri koruma sistemi, zengin bir yapılandırma API 'SI sunar.

> [!WARNING]
> Yapılandırma dosyalarına benzer şekilde, veri koruma anahtarı halkası uygun izinler kullanılarak korunmalıdır. Rest 'de anahtarları şifrelemeyi seçebilirsiniz, ancak bu, saldırganların yeni anahtar oluşturmasını engellemez. Sonuç olarak, uygulamanızın güvenliği etkilenir. Veri koruma ile yapılandırılan depolama konumu, yapılandırma dosyalarını korumanıza benzer şekilde uygulamanın kendisiyle sınırlı olmalıdır. Örneğin, anahtar halkasını diskte depolamayı seçerseniz, dosya sistemi izinleri ' ni kullanın. Yalnızca Web uygulamanızın çalıştırıldığı kimliğin bu dizine okuma, yazma ve oluşturma erişimi olduğundan emin olun. Azure Blob depolama kullanırsanız, yalnızca Web uygulaması Blob deposunda, vb. için yeni girişler okuma, yazma veya oluşturma yeteneğine sahip olmalıdır.
>
> [Adddataprotection](/dotnet/api/microsoft.extensions.dependencyinjection.dataprotectionservicecollectionextensions.adddataprotection) genişletme yöntemi bir [ıdataprotectionbuilder](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotectionbuilder)döndürür. `IDataProtectionBuilder`Veri koruma seçeneklerini yapılandırmak için birlikte zincirleyebilirsiniz uzantı yöntemleri sunar.

::: moniker range=">= aspnetcore-3.0"

Bu makalede kullanılan veri koruma uzantıları için aşağıdaki NuGet paketleri gereklidir:

* [Microsoft. AspNetCore. DataProtection. AzureStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.AzureStorage/)
* [Microsoft. AspNetCore. DataProtection. AzureKeyVault](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.AzureKeyVault/)

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

## <a name="protectkeyswithazurekeyvault"></a>ProtectKeysWithAzureKeyVault

Anahtarları [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)içinde depolamak Için, [ProtectKeysWithAzureKeyVault](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault) ile sistemi yapılandırın `Startup` :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blobUriWithSasToken>"))
        .ProtectKeysWithAzureKeyVault("<keyIdentifier>", "<clientId>", "<clientSecret>");
}
```

Anahtar halka depolama konumunu ayarlayın (örneğin, [PersistKeysToAzureBlobStorage](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage)). Çağırma, `ProtectKeysWithAzureKeyVault` anahtar halka depolama konumu da dahil olmak üzere otomatik veri koruma ayarlarını devre dışı bırakan bir [ıxmlencryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmlencryptor) uyguladığından, konum ayarlanmalıdır. Yukarıdaki örnek, anahtar halkasını sürdürmek için Azure Blob depolamayı kullanır. Daha fazla bilgi için bkz. [anahtar depolama sağlayıcıları: Azure Storage](xref:security/data-protection/implementation/key-storage-providers#azure-storage). Ayrıca, [Persistkeystofilesystem](xref:security/data-protection/implementation/key-storage-providers#file-system)ile anahtar halkasını yerel olarak kalıcı hale getirebilirsiniz.

`keyIdentifier`Anahtar şifreleme için kullanılan Anahtar Kasası anahtar tanımlayıcısıdır. Örneğin, içinde adlı anahtar kasasında oluşturulan bir anahtarın `dataprotection` `contosokeyvault` anahtar tanımlayıcısı vardır `https://contosokeyvault.vault.azure.net/keys/dataprotection/` . Anahtar Kasası için anahtar **sarmalama** ve **sarmalama** anahtarı izinlerini içeren uygulamayı belirtin.

`ProtectKeysWithAzureKeyVault`kullanabilen

* [ProtectKeysWithAzureKeyVault (ıdataprotectionbuilder, keyvaultclient, String)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_Microsoft_Azure_KeyVault_KeyVaultClient_System_String_) , veri koruma sisteminin anahtar kasasını kullanmasını sağlamak Için bir [keyvaultclient](/dotnet/api/microsoft.azure.keyvault.keyvaultclient) kullanılmasına izin verir.
* [ProtectKeysWithAzureKeyVault (ıdataprotectionbuilder, String, String, X509Certificate2)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_String_System_String_System_Security_Cryptography_X509Certificates_X509Certificate2_) , `ClientId` veri koruma sisteminin anahtar kasasını kullanmasını sağlamak Için a ve [X509Certificate](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) kullanılmasına izin verir.
* [ProtectKeysWithAzureKeyVault (ıdataprotectionbuilder, String, String, String)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_String_System_String_System_String_) , ve ' nin kullanılmasına izin verir `ClientId` ve `ClientSecret` veri koruma sisteminin anahtar kasasını kullanmasını sağlar.

Anahtarları depolamak ve korumak için bir keykasası ve Azure Storage birleşimi kullanırken, `System.UriFormatException` anahtarları depolamak için kullanılan blob henüz yoksa bir oluşturulur. Bu, uygulamayı çalıştırma öncesinde el ile oluşturulabilir veya `.ProtectKeysWithAzureKeyVault()` BLOB 'un yerinde oluşturulması ve ardından sonraki çalıştırmalar için üzerine eklenmesi için ilk çalıştırma için kaldırılabilirler. `.ProtectKeysWithAzureKeyVault()`Bu, dosyanın uygun şema ve değerlerle oluşturulduğundan emin olacağı için, bu önerilir.

```csharp
var storageAccount = CloudStorageAccount.Parse("<storage account connection string">);
var client = storageAccount.CreateCloudBlobClient();
var container = client.GetContainerReference("<key store container name>");

var azureServiceTokenProvider = new AzureServiceTokenProvider();
var keyVaultClient = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(
        azureServiceTokenProvider.KeyVaultTokenCallback));

services.AddDataProtection()
    //This blob must already exist before the application is run
    .PersistKeysToAzureBlobStorage(container, "<key store blob name>")
    //Removing this line below for an initial run will ensure the file is created correctly
    .ProtectKeysWithAzureKeyVault(keyVaultClient, "<keyIdentifier>");
```

::: moniker-end

## <a name="persistkeystofilesystem"></a>PersistKeysToFileSystem

Anahtarları *% LocalAppData%* varsayılan konumu yerıne bir UNC paylaşımında depolamak için, sistemi [Persistkeystofilesystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem)ile yapılandırın:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"));
}
```

> [!WARNING]
> Anahtar Kalıcılık konumunu değiştirirseniz, DPAPI 'nın uygun bir şifreleme mekanizması olup olmadığını bilmez olmadığından, sistem artık, bekleyen anahtarları otomatik olarak şifreler.

## <a name="protectkeyswith"></a>ProtectKeysWith\*

Sistemi, [ProtectKeysWith \* ](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions) Yapılandırma API 'lerinden herhangi birini çağırarak, bekleyen anahtarları koruyacak şekilde yapılandırabilirsiniz. Anahtarları bir UNC paylaşımında depolayan ve bu anahtarları belirli bir X. 509.952 sertifikasıyla bekleyen bir şekilde şifreleyen aşağıdaki örneği göz önünde bulundurun:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"))
        .ProtectKeysWithCertificate("thumbprint");
}
```

::: moniker range=">= aspnetcore-2.1"

ASP.NET Core 2,1 veya üzeri sürümlerde, bir dosyadan yüklenen sertifika gibi bir [X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) için [ProtectKeysWithCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithcertificate)sağlayabilirsiniz:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"))
        .ProtectKeysWithCertificate(
            new X509Certificate2("certificate.pfx", "password"));
}
```

::: moniker-end

Yerleşik anahtar şifreleme mekanizmalarından daha fazla örnek ve tartışma için bkz. [rest 'de anahtar şifreleme](xref:security/data-protection/implementation/key-encryption-at-rest) .

::: moniker range=">= aspnetcore-2.1"

## <a name="unprotectkeyswithanycertificate"></a>UnprotectKeysWithAnyCertificate

ASP.NET Core 2,1 veya sonraki sürümlerde, [UnprotectKeysWithAnyCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.unprotectkeyswithanycertificate)ile [X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) sertifikaları dizisini kullanarak sertifikaları döndürebilir ve anahtarların şifrelerini çözebilirsiniz:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"))
        .ProtectKeysWithCertificate(
            new X509Certificate2("certificate.pfx", "password"));
        .UnprotectKeysWithAnyCertificate(
            new X509Certificate2("certificate_old_1.pfx", "password_1"),
            new X509Certificate2("certificate_old_2.pfx", "password_2"));
}
```

::: moniker-end

## <a name="setdefaultkeylifetime"></a>SetDefaultKeyLifetime

Sistemi varsayılan 90 gün yerine 14 günlük bir anahtar yaşam süresi kullanacak şekilde yapılandırmak için [Setdefaultkeylifetime](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setdefaultkeylifetime)kullanın:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .SetDefaultKeyLifetime(TimeSpan.FromDays(14));
}
```

## <a name="setapplicationname"></a>SetApplicationName

Varsayılan olarak, veri koruma sistemi, aynı fiziksel anahtar deposunu paylaşsalar bile, uygulamaları [içerik kök](xref:fundamentals/index#content-root) yollarına göre birbirinden yalıtır. Bu, uygulamaların birbirlerinin korunan yüklerini anlamasına engel olur.

Korumalı yükleri uygulamalar arasında paylaşmak için:

* <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*>Aynı değere sahip her bir uygulamada yapılandırın.
* Uygulamalar genelinde veri koruma API yığınının aynı sürümünü kullanın. Uygulamaların proje **dosyalarında aşağıdakilerden birini yapın:**
  * [Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app)aracılığıyla aynı paylaşılan çerçeve sürümüne başvurun.
  * Aynı [veri koruma paketi](xref:security/data-protection/introduction#package-layout) sürümüne başvurun.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .SetApplicationName("shared app name");
}
```

## <a name="disableautomatickeygeneration"></a>DisableAutomaticKeyGeneration

Süre sonu yaklaşımında bir uygulamanın anahtarları otomatik olarak (yeni anahtar oluştur) oluşturmasını istemediğiniz bir senaryoya sahip olabilirsiniz. Bunun bir örneği, birincil/ikincil ilişkide ayarlanmış uygulamalar olabilir; burada yalnızca birincil uygulama önemli yönetim kaygılarıyla sorumludur ve ikincil uygulamalar yalnızca bir anahtar halkasının Salt okunabilir bir görünümüne sahip olur. İkincil uygulamalar, sistemi ile yapılandırarak anahtar halkasını salt okuma olarak işleyecek şekilde yapılandırılabilir <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.DisableAutomaticKeyGeneration*> :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .DisableAutomaticKeyGeneration();
}
```

## <a name="per-application-isolation"></a>Uygulama başına yalıtım

Veri koruma sistemi bir ASP.NET Core ana bilgisayar tarafından sağlandığında, bu uygulamalar aynı çalışan işlemi hesabı altında çalışıyor ve aynı ana anahtarlama malzemesini kullanıyor olsa bile, uygulamaları otomatik olarak birbirinden yalıtır. Bu, System. Web 'in öğesinden ısoteapps değiştiricisine biraz benzer `<machineKey>` .

Yalıtım mekanizması, yerel makinedeki her uygulamayı benzersiz bir kiracı olarak düşünürken çalışarak, <xref:Microsoft.AspNetCore.DataProtection.IDataProtector> belirli bir uygulama için kök olarak uygulama kimliğini Ayrıştırıcı olarak dahil eder. Uygulamanın benzersiz KIMLIĞI uygulamanın fiziksel yoludur:

* IIS 'de barındırılan uygulamalar için benzersiz KIMLIK, uygulamanın IIS fiziksel yoludur. Bir uygulama bir Web grubu ortamında dağıtılırsa, bu değer, IIS ortamlarının Web grubundaki tüm makinelerde benzer şekilde yapılandırıldığından kararlı bir şekilde yapılır.
* [Kestrel sunucusunda](xref:fundamentals/servers/index#kestrel)çalışan şirket içinde barındırılan uygulamalar IÇIN benzersiz kimlik, diskteki uygulamanın fiziksel yoludur.

Benzersiz tanımlayıcı, &mdash; her iki uygulamayı ve makinenin kendisini sıfırlamaları için tasarlanmıştır.

Bu yalıtım mekanizması, uygulamaların kötü amaçlı olmayan olduğunu varsayar. Kötü amaçlı bir uygulama, aynı çalışan işlem hesabı altında çalışan diğer uygulamaları her zaman etkileyebilir. Uygulamaların birbirini dışlayan bir paylaşılan barındırma ortamında, barındırma sağlayıcısı, uygulamaların temel alınan anahtar depolarını ayırmak dahil olmak üzere uygulamalar arasında işletim sistemi düzeyinde yalıtımlarını sağlamak için gerekli adımları almalıdır.

Veri koruma sistemi bir ASP.NET Core ana bilgisayar tarafından sağlanmazsa (örneğin, somut tür aracılığıyla örneğini oluşturursanız `DataProtectionProvider` ), uygulama yalıtımı varsayılan olarak devre dışıdır. Uygulama yalıtımı devre dışı bırakıldığında, aynı anahtarlama malzemesi tarafından desteklenen tüm uygulamalar, uygun [amaçları](xref:security/data-protection/consumer-apis/purpose-strings)sağladıkları sürece yükleri paylaşabilir. Bu ortamda uygulama yalıtımı sağlamak için, yapılandırma nesnesinde [Setapplicationname](#setapplicationname) metodunu çağırın ve her bir uygulama için benzersiz bir ad sağlayın.

## <a name="changing-algorithms-with-usecryptographicalgorithms"></a>Usecryptographicalgoritmalarıyla algoritmaları değiştirme

Veri koruma yığını, yeni oluşturulan anahtarlar tarafından kullanılan varsayılan algoritmayı değiştirmenize izin verir. Bunu yapmanın en kolay yolu, yapılandırma geri çağrısından [Usecryptographicalgoritmaların](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecryptographicalgorithms) çağırmasıdır:

::: moniker range=">= aspnetcore-2.0"

```csharp
services.AddDataProtection()
    .UseCryptographicAlgorithms(
        new AuthenticatedEncryptorConfiguration()
    {
        EncryptionAlgorithm = EncryptionAlgorithm.AES_256_CBC,
        ValidationAlgorithm = ValidationAlgorithm.HMACSHA256
    });
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
services.AddDataProtection()
    .UseCryptographicAlgorithms(
        new AuthenticatedEncryptionSettings()
    {
        EncryptionAlgorithm = EncryptionAlgorithm.AES_256_CBC,
        ValidationAlgorithm = ValidationAlgorithm.HMACSHA256
    });
```

::: moniker-end

Varsayılan EncryptionAlgorithm AES-256-CBC, varsayılan ValidationAlgorithm ise HMACSHA256. Varsayılan ilke, bir sistem yöneticisi tarafından [makine genelindeki bir ilke](xref:security/data-protection/configuration/machine-wide-policy)aracılığıyla ayarlanabilir, ancak açık bir çağrı `UseCryptographicAlgorithms` varsayılan ilkeyi geçersiz kılar.

Çağırma `UseCryptographicAlgorithms` , önceden tanımlanmış bir yerleşik listeden istenen algoritmayı belirtmenize olanak tanır. Algoritmanın uygulanması konusunda endişelenmeniz gerekmez. Yukarıdaki senaryoda, veri koruma sistemi Windows üzerinde çalışıyorsa AES 'nin CNG uygulamasını kullanmaya çalışır. Aksi takdirde, yönetilen [System. Security. Cryptography. AES](/dotnet/api/system.security.cryptography.aes) sınıfına geri döner.

Bir uygulamayı [Usecustomccryptotographicalgoritmalarına](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecustomcryptographicalgorithms)bir çağrı yoluyla el ile belirtebilirsiniz.

> [!TIP]
> Algoritmaların değiştirilmesi, anahtar halkasının varolan anahtarlarını etkilemez. Yalnızca yeni oluşturulan anahtarları etkiler.

### <a name="specifying-custom-managed-algorithms"></a>Özel yönetilen algoritmaları belirtme

::: moniker range=">= aspnetcore-2.0"

Özel yönetilen algoritmaları belirtmek için, uygulama türlerini işaret eden bir [Managedadıbir Catedencryptorconfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.managedauthenticatedencryptorconfiguration) örneği oluşturun:

```csharp
serviceCollection.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new ManagedAuthenticatedEncryptorConfiguration()
    {
        // A type that subclasses SymmetricAlgorithm
        EncryptionAlgorithmType = typeof(Aes),

        // Specified in bits
        EncryptionAlgorithmKeySize = 256,

        // A type that subclasses KeyedHashAlgorithm
        ValidationAlgorithmType = typeof(HMACSHA256)
    });
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Özel yönetilen algoritmaları belirtmek için, uygulama türlerini işaret eden bir [Managedavılationcatedencryptionsettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.managedauthenticatedencryptionsettings) örneği oluşturun:

```csharp
serviceCollection.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new ManagedAuthenticatedEncryptionSettings()
    {
        // A type that subclasses SymmetricAlgorithm
        EncryptionAlgorithmType = typeof(Aes),

        // Specified in bits
        EncryptionAlgorithmKeySize = 256,

        // A type that subclasses KeyedHashAlgorithm
        ValidationAlgorithmType = typeof(HMACSHA256)
    });
```

::: moniker-end

Genellikle \* tür özelliklerinin [simetrik](/dotnet/api/system.security.cryptography.symmetricalgorithm) , instantiable (Ortak parametresiz ctor aracılığıyla) sağlaması gerekir, ancak sistem özel durumları, kolaylık sağlamak gibi [KeyedHashAlgorithm](/dotnet/api/system.security.cryptography.keyedhashalgorithm)bazı değerler sağlar `typeof(Aes)` .

> [!NOTE]
> SymmetricAlgorithm, ≥ 128 bitlerinin anahtar uzunluğuna ve ≥ 64 bit blok boyutuna sahip olmalıdır ve PKCS #7 dolgusu ile CBC modunda şifrelemeyi desteklemelidir. KeyedHashAlgorithm >= 128 bitlik bir Özet boyutuna sahip olmalıdır ve karma algoritmanın Özet uzunluğuna eşit olan anahtarların uzunluğunu desteklemelidir. KeyedHashAlgorithm, HMAC için kesinlikle gerekli değildir.

### <a name="specifying-custom-windows-cng-algorithms"></a>Özel Windows CNG algoritmaları belirtme

::: moniker range=">= aspnetcore-2.0"

HMAC doğrulama ile CBC modunda şifrelemeyi kullanarak özel bir Windows CNG algoritması belirtmek için algoritmik bilgilerini içeren bir [Cngcbcauthenticatedencryptorconfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration) örneği oluşturun:

```csharp
services.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new CngCbcAuthenticatedEncryptorConfiguration()
    {
        // Passed to BCryptOpenAlgorithmProvider
        EncryptionAlgorithm = "AES",
        EncryptionAlgorithmProvider = null,

        // Specified in bits
        EncryptionAlgorithmKeySize = 256,

        // Passed to BCryptOpenAlgorithmProvider
        HashAlgorithm = "SHA256",
        HashAlgorithmProvider = null
    });
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

HMAC doğrulama ile CBC modunda şifrelemeyi kullanarak özel bir Windows CNG algoritması belirtmek için algoritmik bilgilerini içeren bir [Cngcbcauthenticatedencryptionsettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cngcbcauthenticatedencryptionsettings) örneği oluşturun:

```csharp
services.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new CngCbcAuthenticatedEncryptionSettings()
    {
        // Passed to BCryptOpenAlgorithmProvider
        EncryptionAlgorithm = "AES",
        EncryptionAlgorithmProvider = null,

        // Specified in bits
        EncryptionAlgorithmKeySize = 256,

        // Passed to BCryptOpenAlgorithmProvider
        HashAlgorithm = "SHA256",
        HashAlgorithmProvider = null
    });
```

::: moniker-end

> [!NOTE]
> Simetrik blok şifreleme algoritmasının anahtar uzunluğu >= 128 bit, >= 64 bit blok boyutunda olmalıdır ve PKCS #7 dolgusu ile CBC modunda şifrelemeyi desteklemesi gerekir. Karma algoritmasının >= 128 bitlik bir Özet boyutu olmalıdır ve BCRYPT \_ alg \_ tanıtıcısı \_ HMAC \_ bayrağı bayrağıyla açılmakta olması gerekir. \*Sağlayıcı özellikleri, belirtilen algoritma için varsayılan sağlayıcıyı kullanmak üzere null olarak ayarlanabilir. Daha fazla bilgi için [BCryptOpenAlgorithmProvider](/windows/win32/api/bcrypt/nf-bcrypt-bcryptopenalgorithmprovider) belgelerine bakın.

::: moniker range=">= aspnetcore-2.0"

Doğrulama ile Galoa/sayaç modu şifrelemesini kullanarak özel bir Windows CNG algoritması belirtmek için, algoritmik bilgilerini içeren bir [Cnggcmagıncatedencryptorconfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cnggcmauthenticatedencryptorconfiguration) örneği oluşturun:

```csharp
services.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new CngGcmAuthenticatedEncryptorConfiguration()
    {
        // Passed to BCryptOpenAlgorithmProvider
        EncryptionAlgorithm = "AES",
        EncryptionAlgorithmProvider = null,

        // Specified in bits
        EncryptionAlgorithmKeySize = 256
    });
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Doğrulama ile Galoa/sayaç modu şifrelemesini kullanarak özel bir Windows CNG algoritması belirtmek için algoritmik bilgilerini içeren bir [Cnggcmagıncatedencryptionsettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cnggcmauthenticatedencryptionsettings) örneği oluşturun:

```csharp
services.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new CngGcmAuthenticatedEncryptionSettings()
    {
        // Passed to BCryptOpenAlgorithmProvider
        EncryptionAlgorithm = "AES",
        EncryptionAlgorithmProvider = null,

        // Specified in bits
        EncryptionAlgorithmKeySize = 256
    });
```

::: moniker-end

> [!NOTE]
> Simetrik blok şifreleme algoritmasının anahtar uzunluğu >= 128 bit, tam olarak 128 bit blok boyutu ve GCM şifrelemesini desteklemesi gerekir. [EncryptionAlgorithmProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration.encryptionalgorithmprovider) özelliğini, belirtilen algoritma için varsayılan sağlayıcıyı kullanacak şekilde null olarak ayarlayabilirsiniz. Daha fazla bilgi için [BCryptOpenAlgorithmProvider](/windows/win32/api/bcrypt/nf-bcrypt-bcryptopenalgorithmprovider) belgelerine bakın.

### <a name="specifying-other-custom-algorithms"></a>Diğer özel algoritmaları belirtme

Birinci sınıf bir API olarak gösterilmese de, veri koruma sistemi neredeyse her türlü algoritmanın belirtilmesine izin verecek kadar genişletilebilir. Örneğin, bir donanım güvenlik modülü (HSM) içinde yer alan tüm anahtarları tutmak ve çekirdek şifreleme ve şifre çözme yordamlarının özel bir uygulamasını sağlamak mümkündür. Daha fazla bilgi için bkz. [çekirdek şifreleme genişletilebilirliği](xref:security/data-protection/extensibility/core-crypto) Içindeki [ıauthenticatedencryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.iauthenticatedencryptor) .

## <a name="persisting-keys-when-hosting-in-a-docker-container"></a>Docker kapsayıcısında barındırırken kalıcı anahtarlar

Bir [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/) kapsayıcısında barındırırken anahtarların şu şekilde tutulması gerekir:

* Paylaşılan bir birim veya ana bilgisayar ile takılan bir birim gibi kapsayıcının yaşam süresinden sonra devam eden bir Docker birimi olan bir klasör.
* [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) veya [redin](https://redis.io/)gibi bir dış sağlayıcı.

## <a name="persisting-keys-with-redis"></a>Redsıs ile kalıcı anahtarlar

Anahtarları depolamak için yalnızca redsıs [veri kalıcılığını](/azure/azure-cache-for-redis/cache-how-to-premium-persistence) destekleyen redsıs sürümleri kullanılmalıdır. [Azure Blob depolama](/azure/storage/blobs/storage-blobs-introduction) kalıcıdır ve anahtarları depolamak için kullanılabilir. Daha fazla bilgi için [Bu GitHub sorununa](https://github.com/dotnet/AspNetCore/issues/13476)bakın.

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:security/data-protection/configuration/non-di-scenarios>
* <xref:security/data-protection/configuration/machine-wide-policy>
* <xref:host-and-deploy/web-farm>
* <xref:security/data-protection/implementation/key-storage-providers>
