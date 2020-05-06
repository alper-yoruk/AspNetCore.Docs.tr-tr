---
title: ASP.NET Core bekleyen anahtar şifreleme
author: rick-anderson
description: Bekleyen ASP.NET Core veri koruma anahtarı şifrelemesinin uygulama ayrıntılarını öğrenin.
ms.author: riande
ms.date: 07/16/2018
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/implementation/key-encryption-at-rest
ms.openlocfilehash: e68b8e09dbd876c6f0d37242ebaa415994b3b808
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776935"
---
# <a name="key-encryption-at-rest-in-aspnet-core"></a><span data-ttu-id="c52cd-103">ASP.NET Core bekleyen anahtar şifreleme</span><span class="sxs-lookup"><span data-stu-id="c52cd-103">Key encryption at rest in ASP.NET Core</span></span>

<span data-ttu-id="c52cd-104">Veri koruma sistemi, şifreleme anahtarlarının bekleyen bir şekilde nasıl şifrelendiğini belirlemekte [Varsayılan olarak bir bulma mekanizması kullanır](xref:security/data-protection/configuration/default-settings) .</span><span class="sxs-lookup"><span data-stu-id="c52cd-104">The data protection system [employs a discovery mechanism by default](xref:security/data-protection/configuration/default-settings) to determine how cryptographic keys should be encrypted at rest.</span></span> <span data-ttu-id="c52cd-105">Geliştirici bulma mekanizmasını geçersiz kılabilir ve anahtarların Rest 'te nasıl şifrelendiğini el ile belirtebilir.</span><span class="sxs-lookup"><span data-stu-id="c52cd-105">The developer can override the discovery mechanism and manually specify how keys should be encrypted at rest.</span></span>

> [!WARNING]
> <span data-ttu-id="c52cd-106">Açık [anahtar kalıcılığı konumu](xref:security/data-protection/implementation/key-storage-providers)belirtirseniz, veri koruma sistemi REST mekanizmasında varsayılan anahtar şifrelemesini kaldırır.</span><span class="sxs-lookup"><span data-stu-id="c52cd-106">If you specify an explicit [key persistence location](xref:security/data-protection/implementation/key-storage-providers), the data protection system deregisters the default key encryption at rest mechanism.</span></span> <span data-ttu-id="c52cd-107">Sonuç olarak, anahtarlar artık Rest 'de şifrelenmez.</span><span class="sxs-lookup"><span data-stu-id="c52cd-107">Consequently, keys are no longer encrypted at rest.</span></span> <span data-ttu-id="c52cd-108">Üretim dağıtımları için [bir açık anahtar şifreleme mekanizması belirtmenizi](xref:security/data-protection/implementation/key-encryption-at-rest) öneririz.</span><span class="sxs-lookup"><span data-stu-id="c52cd-108">We recommend that you [specify an explicit key encryption mechanism](xref:security/data-protection/implementation/key-encryption-at-rest) for production deployments.</span></span> <span data-ttu-id="c52cd-109">Bu konuda, bekleyen şifreleme mekanizması seçenekleri açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="c52cd-109">The encryption-at-rest mechanism options are described in this topic.</span></span>

::: moniker range=">= aspnetcore-2.1"

## <a name="azure-key-vault"></a><span data-ttu-id="c52cd-110">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="c52cd-110">Azure Key Vault</span></span>

<span data-ttu-id="c52cd-111">Anahtarları [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)içinde depolamak Için, [ProtectKeysWithAzureKeyVault](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault) `Startup` ile sistemi yapılandırın:</span><span class="sxs-lookup"><span data-stu-id="c52cd-111">To store keys in [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), configure the system with [ProtectKeysWithAzureKeyVault](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault) in the `Startup` class:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blobUriWithSasToken>"))
        .ProtectKeysWithAzureKeyVault("<keyIdentifier>", "<clientId>", "<clientSecret>");
}
```

<span data-ttu-id="c52cd-112">Daha fazla bilgi için bkz. [Configure ASP.NET Core Data Protection: ProtectKeysWithAzureKeyVault](xref:security/data-protection/configuration/overview#protectkeyswithazurekeyvault).</span><span class="sxs-lookup"><span data-stu-id="c52cd-112">For more information, see [Configure ASP.NET Core Data Protection: ProtectKeysWithAzureKeyVault](xref:security/data-protection/configuration/overview#protectkeyswithazurekeyvault).</span></span>

::: moniker-end

## <a name="windows-dpapi"></a><span data-ttu-id="c52cd-113">Windows DPAPI</span><span class="sxs-lookup"><span data-stu-id="c52cd-113">Windows DPAPI</span></span>

<span data-ttu-id="c52cd-114">**Yalnızca Windows dağıtımları için geçerlidir.**</span><span class="sxs-lookup"><span data-stu-id="c52cd-114">**Only applies to Windows deployments.**</span></span>

<span data-ttu-id="c52cd-115">Windows DPAPI kullanıldığında, depolama alanına kalıcı olmadan önce anahtar malzeme [CryptProtectData](/windows/desktop/api/dpapi/nf-dpapi-cryptprotectdata) ile şifrelenir.</span><span class="sxs-lookup"><span data-stu-id="c52cd-115">When Windows DPAPI is used, key material is encrypted with [CryptProtectData](/windows/desktop/api/dpapi/nf-dpapi-cryptprotectdata) before being persisted to storage.</span></span> <span data-ttu-id="c52cd-116">DPAPI, geçerli makinenin dışında hiçbir şekilde okunmayacak olan veriler için uygun bir şifreleme mekanizmasıdır (ancak, bu anahtarları Active Directory için geri yüklemek mümkün olsa da, bkz. [DPAPI ve dolaşım profilleri](https://support.microsoft.com/kb/309408/#6)).</span><span class="sxs-lookup"><span data-stu-id="c52cd-116">DPAPI is an appropriate encryption mechanism for data that's never read outside of the current machine (though it's possible to back these keys up to Active Directory; see [DPAPI and Roaming Profiles](https://support.microsoft.com/kb/309408/#6)).</span></span> <span data-ttu-id="c52cd-117">DPAPI anahtar Rest şifrelemesini yapılandırmak için [ProtectKeysWithDpapi](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithdpapi) uzantı yöntemlerinden birini çağırın:</span><span class="sxs-lookup"><span data-stu-id="c52cd-117">To configure DPAPI key-at-rest encryption, call one of the [ProtectKeysWithDpapi](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithdpapi) extension methods:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Only the local user account can decrypt the keys
    services.AddDataProtection()
        .ProtectKeysWithDpapi();
}
```

<span data-ttu-id="c52cd-118">`ProtectKeysWithDpapi` Parametresi olmadan çağrılırsa, yalnızca geçerli Windows Kullanıcı hesabı kalıcı anahtar halkasını çözebilir.</span><span class="sxs-lookup"><span data-stu-id="c52cd-118">If `ProtectKeysWithDpapi` is called with no parameters, only the current Windows user account can decipher the persisted key ring.</span></span> <span data-ttu-id="c52cd-119">İsteğe bağlı olarak, makinedeki herhangi bir kullanıcı hesabının (yalnızca geçerli kullanıcı hesabı değil) anahtar halkasını çözebilmesini sağlayabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="c52cd-119">You can optionally specify that any user account on the machine (not just the current user account) be able to decipher the key ring:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // All user accounts on the machine can decrypt the keys
    services.AddDataProtection()
        .ProtectKeysWithDpapi(protectToLocalMachine: true);
}
```

::: moniker range=">= aspnetcore-2.0"

## <a name="x509-certificate"></a><span data-ttu-id="c52cd-120">X. 509.440 sertifikası</span><span class="sxs-lookup"><span data-stu-id="c52cd-120">X.509 certificate</span></span>

<span data-ttu-id="c52cd-121">Uygulama birden çok makineye yayılıyorsa, makineler arasında paylaşılan bir X. 509.952 sertifikası dağıtmak ve barındırılan uygulamaları, bekleyen anahtarların şifrelenmesi için sertifikayı kullanacak şekilde yapılandırmak kullanışlı olabilir:</span><span class="sxs-lookup"><span data-stu-id="c52cd-121">If the app is spread across multiple machines, it may be convenient to distribute a shared X.509 certificate across the machines and configure the hosted apps to use the certificate for encryption of keys at rest:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .ProtectKeysWithCertificate("3BCE558E2AD3E0E34A7743EAB5AEA2A9BD2575A0");
}
```

<span data-ttu-id="c52cd-122">.NET Framework sınırlamaları nedeniyle, yalnızca CAPı özel anahtarlarına sahip sertifikalar desteklenir.</span><span class="sxs-lookup"><span data-stu-id="c52cd-122">Due to .NET Framework limitations, only certificates with CAPI private keys are supported.</span></span> <span data-ttu-id="c52cd-123">Bu sınırlamalar için olası geçici çözümler için aşağıdaki içeriğe bakın.</span><span class="sxs-lookup"><span data-stu-id="c52cd-123">See the content below for possible workarounds to these limitations.</span></span>

::: moniker-end

## <a name="windows-dpapi-ng"></a><span data-ttu-id="c52cd-124">Windows DPAPI-NG</span><span class="sxs-lookup"><span data-stu-id="c52cd-124">Windows DPAPI-NG</span></span>

<span data-ttu-id="c52cd-125">**Bu mekanizma yalnızca Windows 8/Windows Server 2012 veya üzeri sürümlerde kullanılabilir.**</span><span class="sxs-lookup"><span data-stu-id="c52cd-125">**This mechanism is available only on Windows 8/Windows Server 2012 or later.**</span></span>

<span data-ttu-id="c52cd-126">Windows 8 ' den itibaren, Windows işletim sistemi DPAPI-NG ' i (CNG DPAPI da denir) destekler.</span><span class="sxs-lookup"><span data-stu-id="c52cd-126">Beginning with Windows 8, Windows OS supports DPAPI-NG (also called CNG DPAPI).</span></span> <span data-ttu-id="c52cd-127">Daha fazla bilgi için bkz. [CNG DPAPI hakkında](/windows/desktop/SecCNG/cng-dpapi).</span><span class="sxs-lookup"><span data-stu-id="c52cd-127">For more information, see [About CNG DPAPI](/windows/desktop/SecCNG/cng-dpapi).</span></span>

<span data-ttu-id="c52cd-128">Sorumlu bir koruma tanımlayıcı kuralı olarak kodlanır.</span><span class="sxs-lookup"><span data-stu-id="c52cd-128">The principal is encoded as a protection descriptor rule.</span></span> <span data-ttu-id="c52cd-129">[ProtectKeysWithDpapiNG](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithdpaping)çağıran aşağıdaki örnekte, yalnızca belirtilen SID 'ye sahip etki alanına katılmış Kullanıcı anahtar halkasının şifresini çözebilir:</span><span class="sxs-lookup"><span data-stu-id="c52cd-129">In the following example that calls [ProtectKeysWithDpapiNG](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithdpaping), only the domain-joined user with the specified SID can decrypt the key ring:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Uses the descriptor rule "SID=S-1-5-21-..."
    services.AddDataProtection()
        .ProtectKeysWithDpapiNG("SID=S-1-5-21-...",
        flags: DpapiNGProtectionDescriptorFlags.None);
}
```

<span data-ttu-id="c52cd-130">Ayrıca, ' nin `ProtectKeysWithDpapiNG`Parametresiz aşırı yüklemesi de vardır.</span><span class="sxs-lookup"><span data-stu-id="c52cd-130">There's also a parameterless overload of `ProtectKeysWithDpapiNG`.</span></span> <span data-ttu-id="c52cd-131">"SID = {CURRENT_ACCOUNT_SID}" kuralını belirtmek için bu kullanışlı yöntemi kullanın; burada *CURRENT_ACCOUNT_SID* geçerli Windows Kullanıcı hesabının SID 'sidir:</span><span class="sxs-lookup"><span data-stu-id="c52cd-131">Use this convenience method to specify the rule "SID={CURRENT_ACCOUNT_SID}", where *CURRENT_ACCOUNT_SID* is the SID of the current Windows user account:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Use the descriptor rule "SID={current account SID}"
    services.AddDataProtection()
        .ProtectKeysWithDpapiNG();
}
```

<span data-ttu-id="c52cd-132">Bu senaryoda, AD etki alanı denetleyicisi, DPAPI-NG işlemleri tarafından kullanılan şifreleme anahtarlarını dağıtmaktan sorumludur.</span><span class="sxs-lookup"><span data-stu-id="c52cd-132">In this scenario, the AD domain controller is responsible for distributing the encryption keys used by the DPAPI-NG operations.</span></span> <span data-ttu-id="c52cd-133">Hedef Kullanıcı, etki alanına katılmış herhangi bir makineden şifrelenmiş yükün şifresini çözebilir (işlemin kimlikleri altında çalışıyor olması şartıyla).</span><span class="sxs-lookup"><span data-stu-id="c52cd-133">The target user can decipher the encrypted payload from any domain-joined machine (provided that the process is running under their identity).</span></span>

## <a name="certificate-based-encryption-with-windows-dpapi-ng"></a><span data-ttu-id="c52cd-134">Windows DPAPI-NG ile sertifika tabanlı şifreleme</span><span class="sxs-lookup"><span data-stu-id="c52cd-134">Certificate-based encryption with Windows DPAPI-NG</span></span>

<span data-ttu-id="c52cd-135">Uygulama Windows 8.1/Windows Server 2012 R2 veya sonraki sürümlerde çalışıyorsa, sertifika tabanlı şifrelemeyi gerçekleştirmek için Windows DPAPI-NG kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="c52cd-135">If the app is running on Windows 8.1/Windows Server 2012 R2 or later, you can use Windows DPAPI-NG to perform certificate-based encryption.</span></span> <span data-ttu-id="c52cd-136">"CERTIFICATE = Hashıd: parmak ızı" kural tanımlayıcı dizesini kullanın; burada *parmak izi* , sertifikanın onaltılı olarak kodlanmış SHA1 parmak izdir:</span><span class="sxs-lookup"><span data-stu-id="c52cd-136">Use the rule descriptor string "CERTIFICATE=HashId:THUMBPRINT", where *THUMBPRINT* is the hex-encoded SHA1 thumbprint of the certificate:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .ProtectKeysWithDpapiNG("CERTIFICATE=HashId:3BCE558E2...B5AEA2A9BD2575A0",
            flags: DpapiNGProtectionDescriptorFlags.None);
}
```

<span data-ttu-id="c52cd-137">Anahtarların şifre açmak için bu havuzda işaret edilen tüm uygulamaların Windows 8.1/Windows Server 2012 R2 veya üzeri üzerinde çalışıyor olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="c52cd-137">Any app pointed at this repository must be running on Windows 8.1/Windows Server 2012 R2 or later to decipher the keys.</span></span>

## <a name="custom-key-encryption"></a><span data-ttu-id="c52cd-138">Özel anahtar şifrelemesi</span><span class="sxs-lookup"><span data-stu-id="c52cd-138">Custom key encryption</span></span>

<span data-ttu-id="c52cd-139">Yerleşik mekanizmalar uygun değilse, geliştirici özel bir [ıxmlencryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmlencryptor)sağlayarak kendi anahtar şifreleme mekanizmalarını belirtebilir.</span><span class="sxs-lookup"><span data-stu-id="c52cd-139">If the in-box mechanisms aren't appropriate, the developer can specify their own key encryption mechanism by providing a custom [IXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmlencryptor).</span></span>
