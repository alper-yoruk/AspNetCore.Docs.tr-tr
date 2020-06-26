---
title: ASP.NET Core veri koruma anahtarı yönetimi ve ömrü
author: rick-anderson
description: ASP.NET Core veri koruma anahtarı yönetimi ve ömrü hakkında bilgi edinin.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/configuration/default-settings
ms.openlocfilehash: f758c814280ee09a240d99cc59cdab2dc4590df6
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85407102"
---
# <a name="data-protection-key-management-and-lifetime-in-aspnet-core"></a>ASP.NET Core veri koruma anahtarı yönetimi ve ömrü

Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)

## <a name="key-management"></a>Anahtar yönetimi

Uygulama, işletimsel ortamı algılamaya çalışır ve anahtar yapılandırmasını kendi kendine işleyebilir.

1. Uygulama [Azure uygulamalarında](https://azure.microsoft.com/services/app-service/)barındırılıyorsa, anahtarlar *%Home%\ASP.NET\DataProtection-Keys* klasöründe kalıcı hale getirilir. Bu klasör, ağ depolama tarafından desteklenir ve uygulamayı barındıran tüm makinelerde eşitlenir.
   * Anahtarlar bekleyen bir şekilde korunmuyor.
   * *DataProtection-Keys* klasörü, bir uygulamanın tüm örneklerine tek bir dağıtım yuvasında anahtar halkasını sağlar.
   * Hazırlama ve üretim gibi ayrı dağıtım yuvaları, anahtar halkasını paylaşmaz. Dağıtım yuvaları arasında takas yaptığınızda (örneğin, hazırlama üretimini üretime değiştirme veya A/B testi kullanma), veri koruma kullanan tüm uygulamalar, önceki yuvanın içindeki anahtar halkasını kullanarak depolanan verilerin şifresini çözemez. Bu, kullanıcıların tanımlama bilgilerini korumak için veri koruma kullandığından, standart ASP.NET Core tanımlama bilgisi kimlik doğrulaması kullanan bir uygulamadan oturum açmış kullanıcılara yol açar. Yuvada bağımsız anahtar halkaları istiyorsanız, Azure Blob depolama, Azure Key Vault, bir SQL Mağazası veya Redsıs önbelleği gibi bir dış anahtar halka sağlayıcısı kullanın.

1. Kullanıcı profili varsa, anahtarlar *%LocalAppData%\ASP.NET\DataProtection-Keys* klasöründe kalıcı hale getirilir. İşletim sistemi Windows ise, anahtarlar DPAPI kullanılarak geri kalanıyla şifrelenir.

   Uygulama havuzunun [Setprofileenvironment özniteliğinin](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) de etkinleştirilmesi gerekir. Varsayılan değeri `setProfileEnvironment` `true` . Bazı senaryolarda (örneğin, Windows işletim sistemi), `setProfileEnvironment` olarak ayarlanır `false` . Anahtarlar beklenen şekilde Kullanıcı profili dizininde depolanmıyorsa:

   1. *% Windir%/system32/inetsrv/config* klasörüne gidin.
   1. *applicationHost.config* dosyasını açın.
   1. Öğesini bulun `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` .
   1. `setProfileEnvironment`Özniteliğinin mevcut olmadığını, değeri varsayılan olarak değerini, `true` veya özniteliğin değerini olarak olarak ayarlandığını doğrulayın `true` .

1. Uygulama IIS 'de barındırılıyorsa, anahtarlar yalnızca çalışan işlem hesabına karşılık gelen özel bir kayıt defteri anahtarındaki HKLM Kayıt defteri 'nde kalıcı hale getirilir. Anahtarlar DPAPI kullanılarak geri kalanıyla şifrelenir.

1. Bu koşulların hiçbiri eşleşmezse anahtarlar geçerli işlemin dışında kalıcı olmaz. İşlem kapandığında, oluşturulan tüm anahtarlar kaybedilir.

Geliştirici her zaman tam denetime sahiptir ve anahtarların nerede ve nerede depolandığını geçersiz kılabilir. Yukarıdaki ilk üç seçenek, ASP.NET **\<machineKey>** Otomatik Oluşturma yordamlarının geçmişte nasıl çalıştığı hakkında benzer uygulamalar için iyi varsayılanlar sağlamalıdır. Son, geri dönüş seçeneği, geliştiricinin anahtar kalıcılığı istiyorsa [yapılandırma](xref:security/data-protection/configuration/overview) ön ucu belirtmesini gerektiren tek senaryodur, ancak bu geri dönüş yalnızca nadir durumlarda oluşur.

Bir Docker kapsayıcısında barındırırken, anahtarların bir Docker birimi (bir paylaşılan birim veya kapsayıcının yaşam süresinin ötesinde devam eden konağa bağlı bir birim) veya [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) ya da [redin](https://redis.io/)gibi bir dış sağlayıcıda kalıcı olması gerekir. Uygulamalar paylaşılan bir ağ birimine erişebiliyorsa (daha fazla bilgi için bkz. [Persistkeystofilesystem](xref:security/data-protection/configuration/overview#persistkeystofilesystem) ) Web grubu senaryolarında bir dış sağlayıcı da yararlıdır.

> [!WARNING]
> Geliştirici yukarıda özetlenen kuralları geçersiz kılıyorsa ve veri koruma sistemine belirli bir anahtar deposunda işaret ediyorsa, bekleyen anahtarların otomatik şifrelenmesi devre dışı bırakılır. Rest koruması, [yapılandırma](xref:security/data-protection/configuration/overview)aracılığıyla yeniden etkinleştirilebilir.

## <a name="key-lifetime"></a>Anahtar ömrü

Anahtarların varsayılan olarak 90 günlük bir ömrü vardır. Anahtarın süresi dolarsa, uygulama otomatik olarak yeni bir anahtar oluşturur ve yeni anahtarı etkin anahtar olarak ayarlar. Devre dışı bırakılan anahtarlar sistemde kaldığı sürece, uygulamanız bunlarla korunan tüm verilerin şifresini çözebilir. Daha fazla bilgi için bkz. [anahtar yönetimi](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling) .

## <a name="default-algorithms"></a>Varsayılan algoritmalar

Kullanılan varsayılan yük koruma algoritması, gizlilik için AES-256-CBC ve özgünlük için HMACSHA256. Her 90 günde bir olan 512 bitlik bir ana anahtar, bu algoritmalar için kullanılan iki alt anahtarın yük başına temelinde türetileceğini kullanır. Daha fazla bilgi için bkz. [alt anahtar türetme](xref:security/data-protection/implementation/subkeyderivation#additional-authenticated-data-and-subkey-derivation) .

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:security/data-protection/extensibility/key-management>
* <xref:host-and-deploy/web-farm>
