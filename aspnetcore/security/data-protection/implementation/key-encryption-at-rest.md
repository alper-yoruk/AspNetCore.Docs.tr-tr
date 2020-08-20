---
title: ASP.NET Core kullanarak Windows ve Azure 'da bekleyen anahtar şifreleme
author: rick-anderson
description: Bekleyen ASP.NET Core veri koruma anahtarı şifrelemesinin uygulama ayrıntılarını öğrenin.
ms.author: riande
ms.date: 07/16/2018
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
uid: security/data-protection/implementation/key-encryption-at-rest
ms.openlocfilehash: 4ca2d998141639406a8283c4c756c05a93251928
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88633688"
---
# <a name="key-encryption-at-rest-in-windows-and-azure-using-aspnet-core"></a>ASP.NET Core kullanarak Windows ve Azure 'da bekleyen anahtar şifreleme

Veri koruma sistemi, şifreleme anahtarlarının bekleyen bir şekilde nasıl şifrelendiğini belirlemekte [Varsayılan olarak bir bulma mekanizması kullanır](xref:security/data-protection/configuration/default-settings) . Geliştirici bulma mekanizmasını geçersiz kılabilir ve anahtarların Rest 'te nasıl şifrelendiğini el ile belirtebilir.

> [!WARNING]
> Açık [anahtar kalıcılığı konumu](xref:security/data-protection/implementation/key-storage-providers)belirtirseniz, veri koruma sistemi REST mekanizmasında varsayılan anahtar şifrelemesini kaldırır. Sonuç olarak, anahtarlar artık Rest 'de şifrelenmez. Üretim dağıtımları için [bir açık anahtar şifreleme mekanizması belirtmenizi](xref:security/data-protection/implementation/key-encryption-at-rest) öneririz. Bu konuda, bekleyen şifreleme mekanizması seçenekleri açıklanmaktadır.

::: moniker range=">= aspnetcore-2.1"

## <a name="azure-key-vault"></a>Azure Key Vault

Anahtarları [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)içinde depolamak Için, [ProtectKeysWithAzureKeyVault](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault) ile sistemi yapılandırın `Startup` :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blobUriWithSasToken>"))
        .ProtectKeysWithAzureKeyVault("<keyIdentifier>", "<clientId>", "<clientSecret>");
}
```

Daha fazla bilgi için bkz. [Configure ASP.NET Core Data Protection: ProtectKeysWithAzureKeyVault](xref:security/data-protection/configuration/overview#protectkeyswithazurekeyvault).

::: moniker-end

## <a name="windows-dpapi"></a>Windows DPAPI

**Yalnızca Windows dağıtımları için geçerlidir.**

Windows DPAPI kullanıldığında, depolama alanına kalıcı olmadan önce anahtar malzeme [CryptProtectData](/windows/desktop/api/dpapi/nf-dpapi-cryptprotectdata) ile şifrelenir. DPAPI, geçerli makinenin dışında hiçbir şekilde okunmayacak olan veriler için uygun bir şifreleme mekanizmasıdır (ancak, bu anahtarları Active Directory için geri yüklemek mümkün olsa da, bkz. [DPAPI ve dolaşım profilleri](https://support.microsoft.com/kb/309408/#6)). DPAPI anahtar Rest şifrelemesini yapılandırmak için [ProtectKeysWithDpapi](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithdpapi) uzantı yöntemlerinden birini çağırın:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Only the local user account can decrypt the keys
    services.AddDataProtection()
        .ProtectKeysWithDpapi();
}
```

`ProtectKeysWithDpapi`Parametresi olmadan çağrılırsa, yalnızca geçerli Windows Kullanıcı hesabı kalıcı anahtar halkasını çözebilir. İsteğe bağlı olarak, makinedeki herhangi bir kullanıcı hesabının (yalnızca geçerli kullanıcı hesabı değil) anahtar halkasını çözebilmesini sağlayabilirsiniz:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // All user accounts on the machine can decrypt the keys
    services.AddDataProtection()
        .ProtectKeysWithDpapi(protectToLocalMachine: true);
}
```

::: moniker range=">= aspnetcore-2.0"

## <a name="x509-certificate"></a>X. 509.440 sertifikası

Uygulama birden çok makineye yayılıyorsa, makineler arasında paylaşılan bir X. 509.952 sertifikası dağıtmak ve barındırılan uygulamaları, bekleyen anahtarların şifrelenmesi için sertifikayı kullanacak şekilde yapılandırmak kullanışlı olabilir:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .ProtectKeysWithCertificate("3BCE558E2AD3E0E34A7743EAB5AEA2A9BD2575A0");
}
```

.NET Framework sınırlamaları nedeniyle, yalnızca CAPı özel anahtarlarına sahip sertifikalar desteklenir. Bu sınırlamalar için olası geçici çözümler için aşağıdaki içeriğe bakın.

::: moniker-end

## <a name="windows-dpapi-ng"></a>Windows DPAPI-NG

**Bu mekanizma yalnızca Windows 8/Windows Server 2012 veya üzeri sürümlerde kullanılabilir.**

Windows 8 ' den itibaren, Windows işletim sistemi DPAPI-NG ' i (CNG DPAPI da denir) destekler. Daha fazla bilgi için bkz. [CNG DPAPI hakkında](/windows/desktop/SecCNG/cng-dpapi).

Sorumlu bir koruma tanımlayıcı kuralı olarak kodlanır. [ProtectKeysWithDpapiNG](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithdpaping)çağıran aşağıdaki örnekte, yalnızca belirtilen SID 'ye sahip etki alanına katılmış Kullanıcı anahtar halkasının şifresini çözebilir:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Uses the descriptor rule "SID=S-1-5-21-..."
    services.AddDataProtection()
        .ProtectKeysWithDpapiNG("SID=S-1-5-21-...",
        flags: DpapiNGProtectionDescriptorFlags.None);
}
```

Ayrıca, ' nin Parametresiz aşırı yüklemesi de vardır `ProtectKeysWithDpapiNG` . "SID = {CURRENT_ACCOUNT_SID}" kuralını belirtmek için bu kullanışlı yöntemi kullanın; burada *CURRENT_ACCOUNT_SID* geçerli Windows Kullanıcı hesabının SID 'sidir:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Use the descriptor rule "SID={current account SID}"
    services.AddDataProtection()
        .ProtectKeysWithDpapiNG();
}
```

Bu senaryoda, AD etki alanı denetleyicisi, DPAPI-NG işlemleri tarafından kullanılan şifreleme anahtarlarını dağıtmaktan sorumludur. Hedef Kullanıcı, etki alanına katılmış herhangi bir makineden şifrelenmiş yükün şifresini çözebilir (işlemin kimlikleri altında çalışıyor olması şartıyla).

## <a name="certificate-based-encryption-with-windows-dpapi-ng"></a>Windows DPAPI-NG ile sertifika tabanlı şifreleme

Uygulama Windows 8.1/Windows Server 2012 R2 veya sonraki sürümlerde çalışıyorsa, sertifika tabanlı şifrelemeyi gerçekleştirmek için Windows DPAPI-NG kullanabilirsiniz. "CERTIFICATE = Hashıd: parmak ızı" kural tanımlayıcı dizesini kullanın; burada *parmak izi* , sertifikanın onaltılı olarak kodlanmış SHA1 parmak izdir:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .ProtectKeysWithDpapiNG("CERTIFICATE=HashId:3BCE558E2...B5AEA2A9BD2575A0",
            flags: DpapiNGProtectionDescriptorFlags.None);
}
```

Anahtarların şifre açmak için bu havuzda işaret edilen tüm uygulamaların Windows 8.1/Windows Server 2012 R2 veya üzeri üzerinde çalışıyor olması gerekir.

## <a name="custom-key-encryption"></a>Özel anahtar şifrelemesi

Yerleşik mekanizmalar uygun değilse, geliştirici özel bir [ıxmlencryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmlencryptor)sağlayarak kendi anahtar şifreleme mekanizmalarını belirtebilir.
