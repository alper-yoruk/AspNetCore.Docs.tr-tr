---
title: ASP.NET Core veri korumasını yapılandırma
author: rick-anderson
description: ASP.NET Core 'de veri korumayı yapılandırma hakkında bilgi edinin.
ms.author: riande
ms.custom: mvc
ms.date: 11/02/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: security/data-protection/configuration/overview
ms.openlocfilehash: 048f6d6d57d3cc5d64004e18b18a3347ee92e450
ms.sourcegitcommit: d64bf0cbe763beda22a7728c7f10d07fc5e19262
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93234575"
---
# <a name="configure-aspnet-core-data-protection"></a><span data-ttu-id="c16e0-103">ASP.NET Core veri korumasını yapılandırma</span><span class="sxs-lookup"><span data-stu-id="c16e0-103">Configure ASP.NET Core Data Protection</span></span>

<span data-ttu-id="c16e0-104">Veri koruma sistemi başlatıldığında, işletimsel ortama göre [varsayılan ayarları](xref:security/data-protection/configuration/default-settings) uygular.</span><span class="sxs-lookup"><span data-stu-id="c16e0-104">When the Data Protection system is initialized, it applies [default settings](xref:security/data-protection/configuration/default-settings) based on the operational environment.</span></span> <span data-ttu-id="c16e0-105">Bu ayarlar, genellikle tek bir makinede çalışan uygulamalar için uygundur.</span><span class="sxs-lookup"><span data-stu-id="c16e0-105">These settings are generally appropriate for apps running on a single machine.</span></span> <span data-ttu-id="c16e0-106">Bir geliştiricinin varsayılan ayarları değiştirmek isteyebileceğiniz durumlar vardır:</span><span class="sxs-lookup"><span data-stu-id="c16e0-106">There are cases where a developer may want to change the default settings:</span></span>

* <span data-ttu-id="c16e0-107">Uygulama birden çok makineye yayılır.</span><span class="sxs-lookup"><span data-stu-id="c16e0-107">The app is spread across multiple machines.</span></span>
* <span data-ttu-id="c16e0-108">Uyumluluk nedenleriyle.</span><span class="sxs-lookup"><span data-stu-id="c16e0-108">For compliance reasons.</span></span>

<span data-ttu-id="c16e0-109">Bu senaryolar için veri koruma sistemi, zengin bir yapılandırma API 'SI sunar.</span><span class="sxs-lookup"><span data-stu-id="c16e0-109">For these scenarios, the Data Protection system offers a rich configuration API.</span></span>

> [!WARNING]
> <span data-ttu-id="c16e0-110">Yapılandırma dosyalarına benzer şekilde, veri koruma anahtarı halkası uygun izinler kullanılarak korunmalıdır.</span><span class="sxs-lookup"><span data-stu-id="c16e0-110">Similar to configuration files, the data protection key ring should be protected using appropriate permissions.</span></span> <span data-ttu-id="c16e0-111">Rest 'de anahtarları şifrelemeyi seçebilirsiniz, ancak bu, saldırganların yeni anahtar oluşturmasını engellemez.</span><span class="sxs-lookup"><span data-stu-id="c16e0-111">You can choose to encrypt keys at rest, but this doesn't prevent attackers from creating new keys.</span></span> <span data-ttu-id="c16e0-112">Sonuç olarak, uygulamanızın güvenliği etkilenir.</span><span class="sxs-lookup"><span data-stu-id="c16e0-112">Consequently, your app's security is impacted.</span></span> <span data-ttu-id="c16e0-113">Veri koruma ile yapılandırılan depolama konumu, yapılandırma dosyalarını korumanıza benzer şekilde uygulamanın kendisiyle sınırlı olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="c16e0-113">The storage location configured with Data Protection should have its access limited to the app itself, similar to the way you would protect configuration files.</span></span> <span data-ttu-id="c16e0-114">Örneğin, anahtar halkasını diskte depolamayı seçerseniz, dosya sistemi izinleri ' ni kullanın.</span><span class="sxs-lookup"><span data-stu-id="c16e0-114">For example, if you choose to store your key ring on disk, use file system permissions.</span></span> <span data-ttu-id="c16e0-115">Yalnızca Web uygulamanızın çalıştırıldığı kimliğin bu dizine okuma, yazma ve oluşturma erişimi olduğundan emin olun.</span><span class="sxs-lookup"><span data-stu-id="c16e0-115">Ensure only the identity under which your web app runs has read, write, and create access to that directory.</span></span> <span data-ttu-id="c16e0-116">Azure Blob depolama kullanırsanız, yalnızca Web uygulaması Blob deposunda, vb. için yeni girişler okuma, yazma veya oluşturma yeteneğine sahip olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="c16e0-116">If you use Azure Blob Storage, only the web app should have the ability to read, write, or create new entries in the blob store, etc.</span></span>
>
> <span data-ttu-id="c16e0-117">[Adddataprotection](/dotnet/api/microsoft.extensions.dependencyinjection.dataprotectionservicecollectionextensions.adddataprotection) genişletme yöntemi bir [ıdataprotectionbuilder](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotectionbuilder)döndürür.</span><span class="sxs-lookup"><span data-stu-id="c16e0-117">The extension method [AddDataProtection](/dotnet/api/microsoft.extensions.dependencyinjection.dataprotectionservicecollectionextensions.adddataprotection) returns an [IDataProtectionBuilder](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotectionbuilder).</span></span> <span data-ttu-id="c16e0-118">`IDataProtectionBuilder` Veri koruma seçeneklerini yapılandırmak için birlikte zincirleyebilirsiniz uzantı yöntemleri sunar.</span><span class="sxs-lookup"><span data-stu-id="c16e0-118">`IDataProtectionBuilder` exposes extension methods that you can chain together to configure Data Protection options.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c16e0-119">Bu makalede kullanılan veri koruma uzantıları için aşağıdaki NuGet paketleri gereklidir:</span><span class="sxs-lookup"><span data-stu-id="c16e0-119">The following NuGet packages are required for the Data Protection extensions used in this article:</span></span>

* [<span data-ttu-id="c16e0-120">Azure. Extensions. AspNetCore. DataProtection. blob 'Lar</span><span class="sxs-lookup"><span data-stu-id="c16e0-120">Azure.Extensions.AspNetCore.DataProtection.Blobs</span></span>](https://www.nuget.org/packages/Azure.Extensions.AspNetCore.DataProtection.Blobs)
* [<span data-ttu-id="c16e0-121">Azure. Extensions. AspNetCore. DataProtection. Keys</span><span class="sxs-lookup"><span data-stu-id="c16e0-121">Azure.Extensions.AspNetCore.DataProtection.Keys</span></span>](https://www.nuget.org/packages/Azure.Extensions.AspNetCore.DataProtection.Keys)

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

## <a name="protectkeyswithazurekeyvault"></a><span data-ttu-id="c16e0-122">ProtectKeysWithAzureKeyVault</span><span class="sxs-lookup"><span data-stu-id="c16e0-122">ProtectKeysWithAzureKeyVault</span></span>

<span data-ttu-id="c16e0-123">CLı kullanarak Azure 'da oturum açın, örneğin:</span><span class="sxs-lookup"><span data-stu-id="c16e0-123">Log in to Azure using the CLI, for example:</span></span>

```azurecli
az login
``` 

<span data-ttu-id="c16e0-124">Anahtarları [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)içinde depolamak için, sınıfı içinde [ProtectKeysWithAzureKeyVault](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault) ile yapılandırın `Startup` .</span><span class="sxs-lookup"><span data-stu-id="c16e0-124">To store keys in [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), configure the system with [ProtectKeysWithAzureKeyVault](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault) in the `Startup` class.</span></span> <span data-ttu-id="c16e0-125">`blobUriWithSasToken` , anahtar dosyasının depolanması gereken tam URI 'dir.</span><span class="sxs-lookup"><span data-stu-id="c16e0-125">`blobUriWithSasToken` is the full URI where the key file should be stored.</span></span> <span data-ttu-id="c16e0-126">URI, bir sorgu dizesi parametresi olarak SAS belirtecini içermelidir:</span><span class="sxs-lookup"><span data-stu-id="c16e0-126">The URI must contain the SAS token as a query string parameter:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blobUriWithSasToken>"))
        .ProtectKeysWithAzureKeyVault(new Uri("<keyIdentifier>"), new DefaultAzureCredential());
}
```

<span data-ttu-id="c16e0-127">Anahtar halka depolama konumunu ayarlayın (örneğin, [PersistKeysToAzureBlobStorage](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage)).</span><span class="sxs-lookup"><span data-stu-id="c16e0-127">Set the key ring storage location (for example, [PersistKeysToAzureBlobStorage](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage)).</span></span> <span data-ttu-id="c16e0-128">Çağırma, `ProtectKeysWithAzureKeyVault` anahtar halka depolama konumu da dahil olmak üzere otomatik veri koruma ayarlarını devre dışı bırakan bir [ıxmlencryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmlencryptor) uyguladığından, konum ayarlanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="c16e0-128">The location must be set because calling `ProtectKeysWithAzureKeyVault` implements an [IXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmlencryptor) that disables automatic data protection settings, including the key ring storage location.</span></span> <span data-ttu-id="c16e0-129">Yukarıdaki örnek, anahtar halkasını sürdürmek için Azure Blob depolamayı kullanır.</span><span class="sxs-lookup"><span data-stu-id="c16e0-129">The preceding example uses Azure Blob Storage to persist the key ring.</span></span> <span data-ttu-id="c16e0-130">Daha fazla bilgi için bkz. [anahtar depolama sağlayıcıları: Azure Storage](xref:security/data-protection/implementation/key-storage-providers#azure-storage).</span><span class="sxs-lookup"><span data-stu-id="c16e0-130">For more information, see [Key storage providers: Azure Storage](xref:security/data-protection/implementation/key-storage-providers#azure-storage).</span></span> <span data-ttu-id="c16e0-131">Ayrıca, [Persistkeystofilesystem](xref:security/data-protection/implementation/key-storage-providers#file-system)ile anahtar halkasını yerel olarak kalıcı hale getirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="c16e0-131">You can also persist the key ring locally with [PersistKeysToFileSystem](xref:security/data-protection/implementation/key-storage-providers#file-system).</span></span>

<span data-ttu-id="c16e0-132">`keyIdentifier`Anahtar şifreleme için kullanılan Anahtar Kasası anahtar tanımlayıcısıdır.</span><span class="sxs-lookup"><span data-stu-id="c16e0-132">The `keyIdentifier` is the key vault key identifier used for key encryption.</span></span> <span data-ttu-id="c16e0-133">Örneğin, içinde adlı anahtar kasasında oluşturulan bir anahtarın `dataprotection` `contosokeyvault` anahtar tanımlayıcısı vardır `https://contosokeyvault.vault.azure.net/keys/dataprotection/` .</span><span class="sxs-lookup"><span data-stu-id="c16e0-133">For example, a key created in key vault named `dataprotection` in the `contosokeyvault` has the key identifier `https://contosokeyvault.vault.azure.net/keys/dataprotection/`.</span></span> <span data-ttu-id="c16e0-134">Anahtar Kasası için anahtar **sarmalama** ve **sarmalama** anahtarı izinlerini içeren uygulamayı belirtin.</span><span class="sxs-lookup"><span data-stu-id="c16e0-134">Provide the app with **Unwrap Key** and **Wrap Key** permissions to the key vault.</span></span>

<span data-ttu-id="c16e0-135">`ProtectKeysWithAzureKeyVault` kullanabilen</span><span class="sxs-lookup"><span data-stu-id="c16e0-135">`ProtectKeysWithAzureKeyVault` overloads:</span></span>

* <span data-ttu-id="c16e0-136">[ProtectKeysWithAzureKeyVault (ıdataprotectionbuilder, Uri, TokenCredential)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionkeyvaultkeybuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionKeyVaultKeyBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_Uri_Azure_Core_TokenCredential_) , veri koruma sisteminin anahtar kasasını kullanmasını sağlamak Için bir KeyIdentifier URI 'si ve bir TokenCredential kullanılmasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="c16e0-136">[ProtectKeysWithAzureKeyVault(IDataProtectionBuilder, Uri, TokenCredential)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionkeyvaultkeybuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionKeyVaultKeyBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_Uri_Azure_Core_TokenCredential_) permits the use of a keyIdentifier Uri and a tokenCredential to enable the data protection system to use the key vault.</span></span>
* <span data-ttu-id="c16e0-137">[ProtectKeysWithAzureKeyVault (ıdataprotectionbuilder, String, ıkeyencryptionkeyresolver)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionkeyvaultkeybuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionKeyVaultKeyBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_Uri_Azure_Core_TokenCredential_) , veri koruma sisteminin anahtar kasasını kullanmasını sağlamak Için bir KeyIdentifier dizesinin ve ıkeyencryptionkeyresolver 'ın kullanılmasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="c16e0-137">[ProtectKeysWithAzureKeyVault(IDataProtectionBuilder, string, IKeyEncryptionKeyResolver)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionkeyvaultkeybuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionKeyVaultKeyBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_Uri_Azure_Core_TokenCredential_) permits the use of a keyIdentifier string and IKeyEncryptionKeyResolver to enable the data protection system to use the key vault.</span></span>

<span data-ttu-id="c16e0-138">Uygulama, [`Microsoft.AspNetCore.DataProtection.AzureStorage`](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.AzureStorage) [`Microsoft.AspNetCore.DataProtection.AzureKeyVault`](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.AzureKeyVault) anahtarları depolamak ve korumak Için önceki Azure paketlerini (ve) ve Azure Key Vault Ile Azure Storage birleşimini kullanıyorsa, <xref:System.UriFormatException?displayProperty=nameWithType> anahtar depolaması için blob yoksa oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="c16e0-138">If the app uses the prior Azure packages ([`Microsoft.AspNetCore.DataProtection.AzureStorage`](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.AzureStorage) and [`Microsoft.AspNetCore.DataProtection.AzureKeyVault`](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.AzureKeyVault)) and a combination of Azure Key Vault and Azure Storage to store and protect keys, <xref:System.UriFormatException?displayProperty=nameWithType> is thrown if the blob for key storage doesn't exist.</span></span> <span data-ttu-id="c16e0-139">Blob Azure portal uygulamayı çalıştırmadan önce el ile oluşturulabilir veya aşağıdaki yordamı kullanabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="c16e0-139">The blob can be manually created ahead of running the app in the Azure portal, or use the following procedure:</span></span>

1. <span data-ttu-id="c16e0-140">`ProtectKeysWithAzureKeyVault`BLOB 'u yerinde oluşturmak için ilk çalıştırmaya yönelik çağrıyı kaldırın.</span><span class="sxs-lookup"><span data-stu-id="c16e0-140">Remove the call to `ProtectKeysWithAzureKeyVault` for the first run to create the blob in place.</span></span>
1. <span data-ttu-id="c16e0-141">`ProtectKeysWithAzureKeyVault`Sonraki çalıştırmalar için çağrısını ekleyin.</span><span class="sxs-lookup"><span data-stu-id="c16e0-141">Add the call to `ProtectKeysWithAzureKeyVault` for subsequent runs.</span></span>

<span data-ttu-id="c16e0-142">`ProtectKeysWithAzureKeyVault`Dosyanın uygun şema ve değerlerle oluşturulduğundan emin olmak için ilk çalıştırma için kaldırma işlemi önerilir.</span><span class="sxs-lookup"><span data-stu-id="c16e0-142">Removing `ProtectKeysWithAzureKeyVault` for the first run is advised, as it ensures that the file is created with the proper schema and values in place.</span></span> 

<span data-ttu-id="c16e0-143">[Azure. Extensions. aspnetcore. DataProtection. Blobları](https://www.nuget.org/packages/Azure.Extensions.AspNetCore.DataProtection.Blobs) ve [Azure. Extensions. Aspnetcore. DataProtection. Keys](https://www.nuget.org/packages/Azure.Extensions.AspNetCore.DataProtection.Keys) paketlerine YÜKSELTMENIZ önerilir, çünkü API, mevcut değilse blob otomatik olarak oluşturur.</span><span class="sxs-lookup"><span data-stu-id="c16e0-143">We recommended upgrading to the [Azure.Extensions.AspNetCore.DataProtection.Blobs](https://www.nuget.org/packages/Azure.Extensions.AspNetCore.DataProtection.Blobs) and [Azure.Extensions.AspNetCore.DataProtection.Keys](https://www.nuget.org/packages/Azure.Extensions.AspNetCore.DataProtection.Keys) packages because the API provided automatically creates the blob if it doesn't exist.</span></span>

```csharp
services.AddDataProtection()
    //This blob must already exist before the application is run
    .PersistKeysToAzureBlobStorage("<storage account connection string", "<key store container name>", "<key store blob name>")
    //Removing this line below for an initial run will ensure the file is created correctly
    .ProtectKeysWithAzureKeyVault(new Uri("<keyIdentifier>"), new DefaultAzureCredential());
```

::: moniker-end

## <a name="persistkeystofilesystem"></a><span data-ttu-id="c16e0-144">PersistKeysToFileSystem</span><span class="sxs-lookup"><span data-stu-id="c16e0-144">PersistKeysToFileSystem</span></span>

<span data-ttu-id="c16e0-145">Anahtarları *% LocalAppData%* varsayılan konumu yerıne bir UNC paylaşımında depolamak için, sistemi [Persistkeystofilesystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem)ile yapılandırın:</span><span class="sxs-lookup"><span data-stu-id="c16e0-145">To store keys on a UNC share instead of at the *%LOCALAPPDATA%* default location, configure the system with [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem):</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"));
}
```

> [!WARNING]
> <span data-ttu-id="c16e0-146">Anahtar Kalıcılık konumunu değiştirirseniz, DPAPI 'nın uygun bir şifreleme mekanizması olup olmadığını bilmez olmadığından, sistem artık, bekleyen anahtarları otomatik olarak şifreler.</span><span class="sxs-lookup"><span data-stu-id="c16e0-146">If you change the key persistence location, the system no longer automatically encrypts keys at rest, since it doesn't know whether DPAPI is an appropriate encryption mechanism.</span></span>

## <a name="protectkeyswith"></a><span data-ttu-id="c16e0-147">ProtectKeysWith\*</span><span class="sxs-lookup"><span data-stu-id="c16e0-147">ProtectKeysWith\*</span></span>

<span data-ttu-id="c16e0-148">Sistemi, [ProtectKeysWith \* ](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions) Yapılandırma API 'lerinden herhangi birini çağırarak, bekleyen anahtarları koruyacak şekilde yapılandırabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="c16e0-148">You can configure the system to protect keys at rest by calling any of the [ProtectKeysWith\*](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions) configuration APIs.</span></span> <span data-ttu-id="c16e0-149">Anahtarları bir UNC paylaşımında depolayan ve bu anahtarları belirli bir X. 509.952 sertifikasıyla bekleyen bir şekilde şifreleyen aşağıdaki örneği göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="c16e0-149">Consider the example below, which stores keys on a UNC share and encrypts those keys at rest with a specific X.509 certificate:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"))
        .ProtectKeysWithCertificate("thumbprint");
}
```

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="c16e0-150">ASP.NET Core 2,1 veya üzeri sürümlerde, bir dosyadan yüklenen sertifika gibi bir [X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) için [ProtectKeysWithCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithcertificate)sağlayabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="c16e0-150">In ASP.NET Core 2.1 or later, you can provide an [X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) to [ProtectKeysWithCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithcertificate), such as a certificate loaded from a file:</span></span>

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

<span data-ttu-id="c16e0-151">Yerleşik anahtar şifreleme mekanizmalarından daha fazla örnek ve tartışma için bkz. [rest 'de anahtar şifreleme](xref:security/data-protection/implementation/key-encryption-at-rest) .</span><span class="sxs-lookup"><span data-stu-id="c16e0-151">See [Key Encryption At Rest](xref:security/data-protection/implementation/key-encryption-at-rest) for more examples and discussion on the built-in key encryption mechanisms.</span></span>

::: moniker range=">= aspnetcore-2.1"

## <a name="unprotectkeyswithanycertificate"></a><span data-ttu-id="c16e0-152">UnprotectKeysWithAnyCertificate</span><span class="sxs-lookup"><span data-stu-id="c16e0-152">UnprotectKeysWithAnyCertificate</span></span>

<span data-ttu-id="c16e0-153">ASP.NET Core 2,1 veya sonraki sürümlerde, [UnprotectKeysWithAnyCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.unprotectkeyswithanycertificate)ile [X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) sertifikaları dizisini kullanarak sertifikaları döndürebilir ve anahtarların şifrelerini çözebilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="c16e0-153">In ASP.NET Core 2.1 or later, you can rotate certificates and decrypt keys at rest using an array of [X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) certificates with [UnprotectKeysWithAnyCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.unprotectkeyswithanycertificate):</span></span>

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

## <a name="setdefaultkeylifetime"></a><span data-ttu-id="c16e0-154">SetDefaultKeyLifetime</span><span class="sxs-lookup"><span data-stu-id="c16e0-154">SetDefaultKeyLifetime</span></span>

<span data-ttu-id="c16e0-155">Sistemi varsayılan 90 gün yerine 14 günlük bir anahtar yaşam süresi kullanacak şekilde yapılandırmak için [Setdefaultkeylifetime](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setdefaultkeylifetime)kullanın:</span><span class="sxs-lookup"><span data-stu-id="c16e0-155">To configure the system to use a key lifetime of 14 days instead of the default 90 days, use [SetDefaultKeyLifetime](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setdefaultkeylifetime):</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .SetDefaultKeyLifetime(TimeSpan.FromDays(14));
}
```

## <a name="setapplicationname"></a><span data-ttu-id="c16e0-156">SetApplicationName</span><span class="sxs-lookup"><span data-stu-id="c16e0-156">SetApplicationName</span></span>

<span data-ttu-id="c16e0-157">Varsayılan olarak, veri koruma sistemi, aynı fiziksel anahtar deposunu paylaşsalar bile, uygulamaları [içerik kök](xref:fundamentals/index#content-root) yollarına göre birbirinden yalıtır.</span><span class="sxs-lookup"><span data-stu-id="c16e0-157">By default, the Data Protection system isolates apps from one another based on their [content root](xref:fundamentals/index#content-root) paths, even if they're sharing the same physical key repository.</span></span> <span data-ttu-id="c16e0-158">Bu, uygulamaların birbirlerinin korunan yüklerini anlamasına engel olur.</span><span class="sxs-lookup"><span data-stu-id="c16e0-158">This prevents the apps from understanding each other's protected payloads.</span></span>

<span data-ttu-id="c16e0-159">Korumalı yükleri uygulamalar arasında paylaşmak için:</span><span class="sxs-lookup"><span data-stu-id="c16e0-159">To share protected payloads among apps:</span></span>

* <span data-ttu-id="c16e0-160"><xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*>Aynı değere sahip her bir uygulamada yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="c16e0-160">Configure <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> in each app with the same value.</span></span>
* <span data-ttu-id="c16e0-161">Uygulamalar genelinde veri koruma API yığınının aynı sürümünü kullanın.</span><span class="sxs-lookup"><span data-stu-id="c16e0-161">Use the same version of the Data Protection API stack across the apps.</span></span> <span data-ttu-id="c16e0-162">Uygulamaların proje **dosyalarında aşağıdakilerden birini yapın:**</span><span class="sxs-lookup"><span data-stu-id="c16e0-162">Perform **either** of the following in the apps' project files:</span></span>
  * <span data-ttu-id="c16e0-163">[Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app)aracılığıyla aynı paylaşılan çerçeve sürümüne başvurun.</span><span class="sxs-lookup"><span data-stu-id="c16e0-163">Reference the same shared framework version via the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>
  * <span data-ttu-id="c16e0-164">Aynı [veri koruma paketi](xref:security/data-protection/introduction#package-layout) sürümüne başvurun.</span><span class="sxs-lookup"><span data-stu-id="c16e0-164">Reference the same [Data Protection package](xref:security/data-protection/introduction#package-layout) version.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .SetApplicationName("shared app name");
}
```

## <a name="disableautomatickeygeneration"></a><span data-ttu-id="c16e0-165">DisableAutomaticKeyGeneration</span><span class="sxs-lookup"><span data-stu-id="c16e0-165">DisableAutomaticKeyGeneration</span></span>

<span data-ttu-id="c16e0-166">Süre sonu yaklaşımında bir uygulamanın anahtarları otomatik olarak (yeni anahtar oluştur) oluşturmasını istemediğiniz bir senaryoya sahip olabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="c16e0-166">You may have a scenario where you don't want an app to automatically roll keys (create new keys) as they approach expiration.</span></span> <span data-ttu-id="c16e0-167">Bunun bir örneği, birincil/ikincil ilişkide ayarlanmış uygulamalar olabilir; burada yalnızca birincil uygulama önemli yönetim kaygılarıyla sorumludur ve ikincil uygulamalar yalnızca bir anahtar halkasının Salt okunabilir bir görünümüne sahip olur.</span><span class="sxs-lookup"><span data-stu-id="c16e0-167">One example of this might be apps set up in a primary/secondary relationship, where only the primary app is responsible for key management concerns and secondary apps simply have a read-only view of the key ring.</span></span> <span data-ttu-id="c16e0-168">İkincil uygulamalar, sistemi ile yapılandırarak anahtar halkasını salt okuma olarak işleyecek şekilde yapılandırılabilir <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.DisableAutomaticKeyGeneration*> :</span><span class="sxs-lookup"><span data-stu-id="c16e0-168">The secondary apps can be configured to treat the key ring as read-only by configuring the system with <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.DisableAutomaticKeyGeneration*>:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .DisableAutomaticKeyGeneration();
}
```

## <a name="per-application-isolation"></a><span data-ttu-id="c16e0-169">Uygulama başına yalıtım</span><span class="sxs-lookup"><span data-stu-id="c16e0-169">Per-application isolation</span></span>

<span data-ttu-id="c16e0-170">Veri koruma sistemi bir ASP.NET Core ana bilgisayar tarafından sağlandığında, bu uygulamalar aynı çalışan işlemi hesabı altında çalışıyor ve aynı ana anahtarlama malzemesini kullanıyor olsa bile, uygulamaları otomatik olarak birbirinden yalıtır.</span><span class="sxs-lookup"><span data-stu-id="c16e0-170">When the Data Protection system is provided by an ASP.NET Core host, it automatically isolates apps from one another, even if those apps are running under the same worker process account and are using the same master keying material.</span></span> <span data-ttu-id="c16e0-171">Bu, System. Web 'in öğesinden ısoteapps değiştiricisine biraz benzer `<machineKey>` .</span><span class="sxs-lookup"><span data-stu-id="c16e0-171">This is somewhat similar to the IsolateApps modifier from System.Web's `<machineKey>` element.</span></span>

<span data-ttu-id="c16e0-172">Yalıtım mekanizması, yerel makinedeki her uygulamayı benzersiz bir kiracı olarak düşünürken çalışarak, <xref:Microsoft.AspNetCore.DataProtection.IDataProtector> belirli bir uygulama için kök olarak uygulama kimliğini Ayrıştırıcı olarak dahil eder.</span><span class="sxs-lookup"><span data-stu-id="c16e0-172">The isolation mechanism works by considering each app on the local machine as a unique tenant, thus the <xref:Microsoft.AspNetCore.DataProtection.IDataProtector> rooted for any given app automatically includes the app ID as a discriminator.</span></span> <span data-ttu-id="c16e0-173">Uygulamanın benzersiz KIMLIĞI uygulamanın fiziksel yoludur:</span><span class="sxs-lookup"><span data-stu-id="c16e0-173">The app's unique ID is the app's physical path:</span></span>

* <span data-ttu-id="c16e0-174">IIS 'de barındırılan uygulamalar için benzersiz KIMLIK, uygulamanın IIS fiziksel yoludur.</span><span class="sxs-lookup"><span data-stu-id="c16e0-174">For apps hosted in IIS, the unique ID is the IIS physical path of the app.</span></span> <span data-ttu-id="c16e0-175">Bir uygulama bir Web grubu ortamında dağıtılırsa, bu değer, IIS ortamlarının Web grubundaki tüm makinelerde benzer şekilde yapılandırıldığından kararlı bir şekilde yapılır.</span><span class="sxs-lookup"><span data-stu-id="c16e0-175">If an app is deployed in a web farm environment, this value is stable assuming that the IIS environments are configured similarly across all machines in the web farm.</span></span>
* <span data-ttu-id="c16e0-176">[Kestrel sunucusunda](xref:fundamentals/servers/index#kestrel)çalışan şirket içinde barındırılan uygulamalar IÇIN benzersiz kimlik, diskteki uygulamanın fiziksel yoludur.</span><span class="sxs-lookup"><span data-stu-id="c16e0-176">For self-hosted apps running on the [Kestrel server](xref:fundamentals/servers/index#kestrel), the unique ID is the physical path to the app on disk.</span></span>

<span data-ttu-id="c16e0-177">Benzersiz tanımlayıcı, &mdash; her iki uygulamayı ve makinenin kendisini sıfırlamaları için tasarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="c16e0-177">The unique identifier is designed to survive resets&mdash;both of the individual app and of the machine itself.</span></span>

<span data-ttu-id="c16e0-178">Bu yalıtım mekanizması, uygulamaların kötü amaçlı olmayan olduğunu varsayar.</span><span class="sxs-lookup"><span data-stu-id="c16e0-178">This isolation mechanism assumes that the apps are not malicious.</span></span> <span data-ttu-id="c16e0-179">Kötü amaçlı bir uygulama, aynı çalışan işlem hesabı altında çalışan diğer uygulamaları her zaman etkileyebilir.</span><span class="sxs-lookup"><span data-stu-id="c16e0-179">A malicious app can always impact any other app running under the same worker process account.</span></span> <span data-ttu-id="c16e0-180">Uygulamaların birbirini dışlayan bir paylaşılan barındırma ortamında, barındırma sağlayıcısı, uygulamaların temel alınan anahtar depolarını ayırmak dahil olmak üzere uygulamalar arasında işletim sistemi düzeyinde yalıtımlarını sağlamak için gerekli adımları almalıdır.</span><span class="sxs-lookup"><span data-stu-id="c16e0-180">In a shared hosting environment where apps are mutually untrusted, the hosting provider should take steps to ensure OS-level isolation between apps, including separating the apps' underlying key repositories.</span></span>

<span data-ttu-id="c16e0-181">Veri koruma sistemi bir ASP.NET Core ana bilgisayar tarafından sağlanmazsa (örneğin, somut tür aracılığıyla örneğini oluşturursanız `DataProtectionProvider` ), uygulama yalıtımı varsayılan olarak devre dışıdır.</span><span class="sxs-lookup"><span data-stu-id="c16e0-181">If the Data Protection system isn't provided by an ASP.NET Core host (for example, if you instantiate it via the `DataProtectionProvider` concrete type) app isolation is disabled by default.</span></span> <span data-ttu-id="c16e0-182">Uygulama yalıtımı devre dışı bırakıldığında, aynı anahtarlama malzemesi tarafından desteklenen tüm uygulamalar, uygun [amaçları](xref:security/data-protection/consumer-apis/purpose-strings)sağladıkları sürece yükleri paylaşabilir.</span><span class="sxs-lookup"><span data-stu-id="c16e0-182">When app isolation is disabled, all apps backed by the same keying material can share payloads as long as they provide the appropriate [purposes](xref:security/data-protection/consumer-apis/purpose-strings).</span></span> <span data-ttu-id="c16e0-183">Bu ortamda uygulama yalıtımı sağlamak için, yapılandırma nesnesinde [Setapplicationname](#setapplicationname) metodunu çağırın ve her bir uygulama için benzersiz bir ad sağlayın.</span><span class="sxs-lookup"><span data-stu-id="c16e0-183">To provide app isolation in this environment, call the [SetApplicationName](#setapplicationname) method on the configuration object and provide a unique name for each app.</span></span>

## <a name="changing-algorithms-with-usecryptographicalgorithms"></a><span data-ttu-id="c16e0-184">Usecryptographicalgoritmalarıyla algoritmaları değiştirme</span><span class="sxs-lookup"><span data-stu-id="c16e0-184">Changing algorithms with UseCryptographicAlgorithms</span></span>

<span data-ttu-id="c16e0-185">Veri koruma yığını, yeni oluşturulan anahtarlar tarafından kullanılan varsayılan algoritmayı değiştirmenize izin verir.</span><span class="sxs-lookup"><span data-stu-id="c16e0-185">The Data Protection stack allows you to change the default algorithm used by newly-generated keys.</span></span> <span data-ttu-id="c16e0-186">Bunu yapmanın en kolay yolu, yapılandırma geri çağrısından [Usecryptographicalgoritmaların](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecryptographicalgorithms) çağırmasıdır:</span><span class="sxs-lookup"><span data-stu-id="c16e0-186">The simplest way to do this is to call [UseCryptographicAlgorithms](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecryptographicalgorithms) from the configuration callback:</span></span>

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

<span data-ttu-id="c16e0-187">Varsayılan EncryptionAlgorithm AES-256-CBC, varsayılan ValidationAlgorithm ise HMACSHA256.</span><span class="sxs-lookup"><span data-stu-id="c16e0-187">The default EncryptionAlgorithm is AES-256-CBC, and the default ValidationAlgorithm is HMACSHA256.</span></span> <span data-ttu-id="c16e0-188">Varsayılan ilke, bir sistem yöneticisi tarafından [makine genelindeki bir ilke](xref:security/data-protection/configuration/machine-wide-policy)aracılığıyla ayarlanabilir, ancak açık bir çağrı `UseCryptographicAlgorithms` varsayılan ilkeyi geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="c16e0-188">The default policy can be set by a system administrator via a [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy), but an explicit call to `UseCryptographicAlgorithms` overrides the default policy.</span></span>

<span data-ttu-id="c16e0-189">Çağırma `UseCryptographicAlgorithms` , önceden tanımlanmış bir yerleşik listeden istenen algoritmayı belirtmenize olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="c16e0-189">Calling `UseCryptographicAlgorithms` allows you to specify the desired algorithm from a predefined built-in list.</span></span> <span data-ttu-id="c16e0-190">Algoritmanın uygulanması konusunda endişelenmeniz gerekmez.</span><span class="sxs-lookup"><span data-stu-id="c16e0-190">You don't need to worry about the implementation of the algorithm.</span></span> <span data-ttu-id="c16e0-191">Yukarıdaki senaryoda, veri koruma sistemi Windows üzerinde çalışıyorsa AES 'nin CNG uygulamasını kullanmaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="c16e0-191">In the scenario above, the Data Protection system attempts to use the CNG implementation of AES if running on Windows.</span></span> <span data-ttu-id="c16e0-192">Aksi takdirde, yönetilen [System. Security. Cryptography. AES](/dotnet/api/system.security.cryptography.aes) sınıfına geri döner.</span><span class="sxs-lookup"><span data-stu-id="c16e0-192">Otherwise, it falls back to the managed [System.Security.Cryptography.Aes](/dotnet/api/system.security.cryptography.aes) class.</span></span>

<span data-ttu-id="c16e0-193">Bir uygulamayı [Usecustomccryptotographicalgoritmalarına](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecustomcryptographicalgorithms)bir çağrı yoluyla el ile belirtebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="c16e0-193">You can manually specify an implementation via a call to [UseCustomCryptographicAlgorithms](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecustomcryptographicalgorithms).</span></span>

> [!TIP]
> <span data-ttu-id="c16e0-194">Algoritmaların değiştirilmesi, anahtar halkasının varolan anahtarlarını etkilemez.</span><span class="sxs-lookup"><span data-stu-id="c16e0-194">Changing algorithms doesn't affect existing keys in the key ring.</span></span> <span data-ttu-id="c16e0-195">Yalnızca yeni oluşturulan anahtarları etkiler.</span><span class="sxs-lookup"><span data-stu-id="c16e0-195">It only affects newly-generated keys.</span></span>

### <a name="specifying-custom-managed-algorithms"></a><span data-ttu-id="c16e0-196">Özel yönetilen algoritmaları belirtme</span><span class="sxs-lookup"><span data-stu-id="c16e0-196">Specifying custom managed algorithms</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="c16e0-197">Özel yönetilen algoritmaları belirtmek için, uygulama türlerini işaret eden bir [Managedadıbir Catedencryptorconfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.managedauthenticatedencryptorconfiguration) örneği oluşturun:</span><span class="sxs-lookup"><span data-stu-id="c16e0-197">To specify custom managed algorithms, create a [ManagedAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.managedauthenticatedencryptorconfiguration) instance that points to the implementation types:</span></span>

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

<span data-ttu-id="c16e0-198">Özel yönetilen algoritmaları belirtmek için, uygulama türlerini işaret eden bir [Managedavılationcatedencryptionsettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.managedauthenticatedencryptionsettings) örneği oluşturun:</span><span class="sxs-lookup"><span data-stu-id="c16e0-198">To specify custom managed algorithms, create a [ManagedAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.managedauthenticatedencryptionsettings) instance that points to the implementation types:</span></span>

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

<span data-ttu-id="c16e0-199">Genellikle \* tür özelliklerinin [simetrik](/dotnet/api/system.security.cryptography.symmetricalgorithm) , instantiable (Ortak parametresiz ctor aracılığıyla) sağlaması gerekir, ancak sistem özel durumları, kolaylık sağlamak gibi [KeyedHashAlgorithm](/dotnet/api/system.security.cryptography.keyedhashalgorithm)bazı değerler sağlar `typeof(Aes)` .</span><span class="sxs-lookup"><span data-stu-id="c16e0-199">Generally the \*Type properties must point to concrete, instantiable (via a public parameterless ctor) implementations of [SymmetricAlgorithm](/dotnet/api/system.security.cryptography.symmetricalgorithm) and [KeyedHashAlgorithm](/dotnet/api/system.security.cryptography.keyedhashalgorithm), though the system special-cases some values like `typeof(Aes)` for convenience.</span></span>

> [!NOTE]
> <span data-ttu-id="c16e0-200">SymmetricAlgorithm, ≥ 128 bitlerinin anahtar uzunluğuna ve ≥ 64 bit blok boyutuna sahip olmalıdır ve PKCS #7 dolgusu ile CBC modunda şifrelemeyi desteklemelidir.</span><span class="sxs-lookup"><span data-stu-id="c16e0-200">The SymmetricAlgorithm must have a key length of ≥ 128 bits and a block size of ≥ 64 bits, and it must support CBC-mode encryption with PKCS #7 padding.</span></span> <span data-ttu-id="c16e0-201">KeyedHashAlgorithm >= 128 bitlik bir Özet boyutuna sahip olmalıdır ve karma algoritmanın Özet uzunluğuna eşit olan anahtarların uzunluğunu desteklemelidir.</span><span class="sxs-lookup"><span data-stu-id="c16e0-201">The KeyedHashAlgorithm must have a digest size of >= 128 bits, and it must support keys of length equal to the hash algorithm's digest length.</span></span> <span data-ttu-id="c16e0-202">KeyedHashAlgorithm, HMAC için kesinlikle gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="c16e0-202">The KeyedHashAlgorithm isn't strictly required to be HMAC.</span></span>

### <a name="specifying-custom-windows-cng-algorithms"></a><span data-ttu-id="c16e0-203">Özel Windows CNG algoritmaları belirtme</span><span class="sxs-lookup"><span data-stu-id="c16e0-203">Specifying custom Windows CNG algorithms</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="c16e0-204">HMAC doğrulama ile CBC modunda şifrelemeyi kullanarak özel bir Windows CNG algoritması belirtmek için algoritmik bilgilerini içeren bir [Cngcbcauthenticatedencryptorconfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration) örneği oluşturun:</span><span class="sxs-lookup"><span data-stu-id="c16e0-204">To specify a custom Windows CNG algorithm using CBC-mode encryption with HMAC validation, create a [CngCbcAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration) instance that contains the algorithmic information:</span></span>

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

<span data-ttu-id="c16e0-205">HMAC doğrulama ile CBC modunda şifrelemeyi kullanarak özel bir Windows CNG algoritması belirtmek için algoritmik bilgilerini içeren bir [Cngcbcauthenticatedencryptionsettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cngcbcauthenticatedencryptionsettings) örneği oluşturun:</span><span class="sxs-lookup"><span data-stu-id="c16e0-205">To specify a custom Windows CNG algorithm using CBC-mode encryption with HMAC validation, create a [CngCbcAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cngcbcauthenticatedencryptionsettings) instance that contains the algorithmic information:</span></span>

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
> <span data-ttu-id="c16e0-206">Simetrik blok şifreleme algoritmasının anahtar uzunluğu >= 128 bit, >= 64 bit blok boyutunda olmalıdır ve PKCS #7 dolgusu ile CBC modunda şifrelemeyi desteklemesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="c16e0-206">The symmetric block cipher algorithm must have a key length of >= 128 bits, a block size of >= 64 bits, and it must support CBC-mode encryption with PKCS #7 padding.</span></span> <span data-ttu-id="c16e0-207">Karma algoritmasının >= 128 bitlik bir Özet boyutu olmalıdır ve BCRYPT \_ alg \_ tanıtıcısı \_ HMAC \_ bayrağı bayrağıyla açılmakta olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="c16e0-207">The hash algorithm must have a digest size of >= 128 bits and must support being opened with the BCRYPT\_ALG\_HANDLE\_HMAC\_FLAG flag.</span></span> <span data-ttu-id="c16e0-208">\*Sağlayıcı özellikleri, belirtilen algoritma için varsayılan sağlayıcıyı kullanmak üzere null olarak ayarlanabilir.</span><span class="sxs-lookup"><span data-stu-id="c16e0-208">The \*Provider properties can be set to null to use the default provider for the specified algorithm.</span></span> <span data-ttu-id="c16e0-209">Daha fazla bilgi için [BCryptOpenAlgorithmProvider](/windows/win32/api/bcrypt/nf-bcrypt-bcryptopenalgorithmprovider) belgelerine bakın.</span><span class="sxs-lookup"><span data-stu-id="c16e0-209">See the [BCryptOpenAlgorithmProvider](/windows/win32/api/bcrypt/nf-bcrypt-bcryptopenalgorithmprovider) documentation for more information.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="c16e0-210">Doğrulama ile Galoa/sayaç modu şifrelemesini kullanarak özel bir Windows CNG algoritması belirtmek için, algoritmik bilgilerini içeren bir [Cnggcmagıncatedencryptorconfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cnggcmauthenticatedencryptorconfiguration) örneği oluşturun:</span><span class="sxs-lookup"><span data-stu-id="c16e0-210">To specify a custom Windows CNG algorithm using Galois/Counter Mode encryption with validation, create a [CngGcmAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cnggcmauthenticatedencryptorconfiguration) instance that contains the algorithmic information:</span></span>

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

<span data-ttu-id="c16e0-211">Doğrulama ile Galoa/sayaç modu şifrelemesini kullanarak özel bir Windows CNG algoritması belirtmek için algoritmik bilgilerini içeren bir [Cnggcmagıncatedencryptionsettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cnggcmauthenticatedencryptionsettings) örneği oluşturun:</span><span class="sxs-lookup"><span data-stu-id="c16e0-211">To specify a custom Windows CNG algorithm using Galois/Counter Mode encryption with validation, create a [CngGcmAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cnggcmauthenticatedencryptionsettings) instance that contains the algorithmic information:</span></span>

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
> <span data-ttu-id="c16e0-212">Simetrik blok şifreleme algoritmasının anahtar uzunluğu >= 128 bit, tam olarak 128 bit blok boyutu ve GCM şifrelemesini desteklemesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="c16e0-212">The symmetric block cipher algorithm must have a key length of >= 128 bits, a block size of exactly 128 bits, and it must support GCM encryption.</span></span> <span data-ttu-id="c16e0-213">[EncryptionAlgorithmProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration.encryptionalgorithmprovider) özelliğini, belirtilen algoritma için varsayılan sağlayıcıyı kullanacak şekilde null olarak ayarlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="c16e0-213">You can set the [EncryptionAlgorithmProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration.encryptionalgorithmprovider) property to null to use the default provider for the specified algorithm.</span></span> <span data-ttu-id="c16e0-214">Daha fazla bilgi için [BCryptOpenAlgorithmProvider](/windows/win32/api/bcrypt/nf-bcrypt-bcryptopenalgorithmprovider) belgelerine bakın.</span><span class="sxs-lookup"><span data-stu-id="c16e0-214">See the [BCryptOpenAlgorithmProvider](/windows/win32/api/bcrypt/nf-bcrypt-bcryptopenalgorithmprovider) documentation for more information.</span></span>

### <a name="specifying-other-custom-algorithms"></a><span data-ttu-id="c16e0-215">Diğer özel algoritmaları belirtme</span><span class="sxs-lookup"><span data-stu-id="c16e0-215">Specifying other custom algorithms</span></span>

<span data-ttu-id="c16e0-216">Birinci sınıf bir API olarak gösterilmese de, veri koruma sistemi neredeyse her türlü algoritmanın belirtilmesine izin verecek kadar genişletilebilir.</span><span class="sxs-lookup"><span data-stu-id="c16e0-216">Though not exposed as a first-class API, the Data Protection system is extensible enough to allow specifying almost any kind of algorithm.</span></span> <span data-ttu-id="c16e0-217">Örneğin, bir donanım güvenlik modülü (HSM) içinde yer alan tüm anahtarları tutmak ve çekirdek şifreleme ve şifre çözme yordamlarının özel bir uygulamasını sağlamak mümkündür.</span><span class="sxs-lookup"><span data-stu-id="c16e0-217">For example, it's possible to keep all keys contained within a Hardware Security Module (HSM) and to provide a custom implementation of the core encryption and decryption routines.</span></span> <span data-ttu-id="c16e0-218">Daha fazla bilgi için bkz. [çekirdek şifreleme genişletilebilirliği](xref:security/data-protection/extensibility/core-crypto) Içindeki [ıauthenticatedencryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.iauthenticatedencryptor) .</span><span class="sxs-lookup"><span data-stu-id="c16e0-218">See [IAuthenticatedEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.iauthenticatedencryptor) in [Core cryptography extensibility](xref:security/data-protection/extensibility/core-crypto) for more information.</span></span>

## <a name="persisting-keys-when-hosting-in-a-docker-container"></a><span data-ttu-id="c16e0-219">Docker kapsayıcısında barındırırken kalıcı anahtarlar</span><span class="sxs-lookup"><span data-stu-id="c16e0-219">Persisting keys when hosting in a Docker container</span></span>

<span data-ttu-id="c16e0-220">Bir [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/) kapsayıcısında barındırırken anahtarların şu şekilde tutulması gerekir:</span><span class="sxs-lookup"><span data-stu-id="c16e0-220">When hosting in a [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/) container, keys should be maintained in either:</span></span>

* <span data-ttu-id="c16e0-221">Paylaşılan bir birim veya ana bilgisayar ile takılan bir birim gibi kapsayıcının yaşam süresinden sonra devam eden bir Docker birimi olan bir klasör.</span><span class="sxs-lookup"><span data-stu-id="c16e0-221">A folder that's a Docker volume that persists beyond the container's lifetime, such as a shared volume or a host-mounted volume.</span></span>
* <span data-ttu-id="c16e0-222">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) veya [redin](https://redis.io/)gibi bir dış sağlayıcı.</span><span class="sxs-lookup"><span data-stu-id="c16e0-222">An external provider, such as [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) or [Redis](https://redis.io/).</span></span>

## <a name="persisting-keys-with-redis"></a><span data-ttu-id="c16e0-223">Redsıs ile kalıcı anahtarlar</span><span class="sxs-lookup"><span data-stu-id="c16e0-223">Persisting keys with Redis</span></span>

<span data-ttu-id="c16e0-224">Anahtarları depolamak için yalnızca redsıs [veri kalıcılığını](/azure/azure-cache-for-redis/cache-how-to-premium-persistence) destekleyen redsıs sürümleri kullanılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="c16e0-224">Only Redis versions supporting [Redis Data Persistence](/azure/azure-cache-for-redis/cache-how-to-premium-persistence) should be used to store keys.</span></span> <span data-ttu-id="c16e0-225">[Azure Blob depolama](/azure/storage/blobs/storage-blobs-introduction) kalıcıdır ve anahtarları depolamak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="c16e0-225">[Azure Blob storage](/azure/storage/blobs/storage-blobs-introduction) is persistent and can be used to store keys.</span></span> <span data-ttu-id="c16e0-226">Daha fazla bilgi için [Bu GitHub sorununa](https://github.com/dotnet/AspNetCore/issues/13476)bakın.</span><span class="sxs-lookup"><span data-stu-id="c16e0-226">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/13476).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c16e0-227">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="c16e0-227">Additional resources</span></span>

* <xref:security/data-protection/configuration/non-di-scenarios>
* <xref:security/data-protection/configuration/machine-wide-policy>
* <xref:host-and-deploy/web-farm>
* <xref:security/data-protection/implementation/key-storage-providers>
