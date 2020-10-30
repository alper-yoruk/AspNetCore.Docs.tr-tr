---
title: ASP.NET Core 'de veri koruma makineye özel ilke desteği
author: rick-anderson
description: ASP.NET Core veri koruma kullanan tüm uygulamalar için varsayılan makine genelindeki bir ilke ayarlamaya yönelik destek hakkında bilgi edinin.
ms.author: riande
ms.date: 10/14/2016
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
uid: security/data-protection/configuration/machine-wide-policy
ms.openlocfilehash: f6f258e193bf964cb9b4cbd24da2740c5ed89a91
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053000"
---
# <a name="data-protection-machine-wide-policy-support-in-aspnet-core"></a>ASP.NET Core 'de veri koruma makineye özel ilke desteği

Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)

Windows üzerinde çalışırken, veri koruma sistemi, ASP.NET Core veri korumasını kullanan tüm uygulamalar için varsayılan makine genelinde bir ilke ayarlamak için sınırlı desteğe sahiptir. Genel fikir, bir yöneticinin makinedeki her uygulamayı el ile güncelleştirmek gerekmeden kullanılan algoritmalar veya anahtar yaşam süresi gibi varsayılan bir ayarı değiştirmek isteyebilir.

> [!WARNING]
> Sistem Yöneticisi varsayılan ilkeyi ayarlayabilir, ancak zorunlu kılamaz. Uygulama geliştiricisi her zaman herhangi bir değeri kendi seçmekten biriyle geçersiz kılabilir. Varsayılan ilke yalnızca geliştiricinin bir ayar için açık bir değer belirtmemiş olduğu uygulamaları etkiler.

## <a name="setting-default-policy"></a>Varsayılan ilke ayarlanıyor

Varsayılan ilkeyi ayarlamak için, bir yönetici aşağıdaki kayıt defteri anahtarı altındaki sistem kayıt defterinde bilinen değerleri ayarlayabilir:

**HKLM\SOFTWARE\Microsoft\DotNetPackages\Microsoft.AspNetCore.DataProtection**

64 bitlik bir işletim sistemdeyse ve 32 bit uygulamaların davranışını etkilemek istiyorsanız Yukarıdaki anahtarın Wow6432Node eşdeğerini yapılandırmayı unutmayın.

Desteklenen değerler aşağıda gösterilmiştir.

| Değer              | Tür   | Açıklama |
| ------------------ | :----: | ----------- |
| EncryptionType     | string | Veri koruma için hangi algoritmaların kullanılması gerektiğini belirtir. Değer CNG-CBC, CNG-GCM veya yönetilen olmalıdır ve aşağıda daha ayrıntılı olarak açıklanmıştır. |
| DefaultKeyLifetime | DWORD  | Yeni oluşturulan anahtarların yaşam süresini belirtir. Değer, gün cinsinden belirtilir ve >= 7 olmalıdır. |
| Keyescrowhavuzları     | string | Anahtar Emanet için kullanılan türleri belirtir. Değer, listedeki her öğe [ıkeyescrowsink](/dotnet/api/microsoft.aspnetcore.dataprotection.keymanagement.ikeyescrowsink)uygulayan bir türün derleme nitelikli adı olduğunda, noktalı virgülle ayrılmış anahtar Emanet listesidir. |

## <a name="encryption-types"></a>Şifreleme türleri

EncryptionType değeri CNG-CBC ise, sistem, Windows CNG tarafından sunulan hizmetlere yönelik gizlilik ve HMAC için bir CBC modu simetrik blok şifresi kullanacak şekilde yapılandırılır (daha fazla ayrıntı için bkz. [özel WINDOWS CNG algoritmaları belirtme](xref:security/data-protection/configuration/overview#specifying-custom-windows-cng-algorithms) ). Her biri CngCbcAuthenticatedEncryptionSettings türündeki bir özelliğe karşılık gelen aşağıdaki ek değerler desteklenir.

| Değer                       | Tür   | Açıklama |
| --------------------------- | :----: | ----------- |
| EncryptionAlgorithm         | string | CNG tarafından anlaşılan simetrik bir blok şifre algoritması adı. Bu algoritma CBC modunda açılır. |
| EncryptionAlgorithmProvider | string | Algoritma Şifrelemealgoritması oluşturabilecek CNG sağlayıcısı uygulamasının adı. |
| EncryptionAlgorithmKeySize  | DWORD  | Simetrik blok şifre algoritması için türetmeye yönelik anahtarın uzunluğu (bit cinsinden). |
| HashAlgorithm               | string | CNG tarafından anlaşılan karma algoritmanın adı. Bu algoritma HMAC modunda açılır. |
| HashAlgorithmProvider       | string | Algoritma HashAlgorithm üreten CNG sağlayıcı uygulamasının adı. |

EncryptionType değeri CNG-GCM ise, sistem Windows CNG tarafından sunulan hizmetlerle gizlilik ve özgünlük için bir Galoa/sayaç modu simetrik blok şifresi kullanacak şekilde yapılandırılır (daha fazla ayrıntı için bkz. [özel WINDOWS CNG algoritmaları belirtme](xref:security/data-protection/configuration/overview#specifying-custom-windows-cng-algorithms) ). Aşağıdaki ek değerler desteklenir, her biri Cnggcmakimliksidencryptionsettings türündeki bir özelliğe karşılık gelir.

| Değer                       | Tür   | Açıklama |
| --------------------------- | :----: | ----------- |
| EncryptionAlgorithm         | string | CNG tarafından anlaşılan simetrik bir blok şifre algoritması adı. Bu algoritma Galoa/sayaç modunda açılır. |
| EncryptionAlgorithmProvider | string | Algoritma Şifrelemealgoritması oluşturabilecek CNG sağlayıcısı uygulamasının adı. |
| EncryptionAlgorithmKeySize  | DWORD  | Simetrik blok şifre algoritması için türetmeye yönelik anahtarın uzunluğu (bit cinsinden). |

EncryptionType yönetiliyorsa, sistem, özgünlük için Gizlilik ve KeyedHashAlgorithm için yönetilen bir SymmetricAlgorithm kullanacak şekilde yapılandırılır (daha fazla ayrıntı için bkz. [özel yönetilen algoritmalar belirtme](xref:security/data-protection/configuration/overview#specifying-custom-managed-algorithms) ). Aşağıdaki ek değerler desteklenir, her biri Managedavılationcatedencryptionsettings türündeki bir özelliğe karşılık gelir.

| Değer                      | Tür   | Açıklama |
| -------------------------- | :----: | ----------- |
| EncryptionAlgorithmType    | string | SymmetricAlgorithm uygulayan bir türün derleme nitelikli adı. |
| EncryptionAlgorithmKeySize | DWORD  | Simetrik şifreleme algoritması için türetmeye yönelik anahtarın uzunluğu (bit cinsinden). |
| ValidationAlgorithmType    | string | KeyedHashAlgorithm uygulayan bir türün derleme nitelikli adı. |

EncryptionType null ya da boş dışında başka bir değere sahipse, veri koruma sistemi başlangıçta bir özel durum oluşturur.

> [!WARNING]
> Tür adlarını içeren varsayılan bir ilke ayarı yapılandırırken (EncryptionAlgorithmType, ValidationAlgorithmType, Keyescrowhavuzları), bu tür uygulama için kullanılabilir olmalıdır. Bu, masaüstü CLR üzerinde çalışan uygulamalar için, bu türleri içeren derlemelerin genel derleme önbelleğinde (GAC) bulunması anlamına gelir. .NET Core üzerinde çalışan ASP.NET Core uygulamalar için, bu türleri içeren paketlerin yüklenmesi gerekir.
