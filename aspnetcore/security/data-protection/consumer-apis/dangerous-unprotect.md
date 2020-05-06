---
title: ASP.NET Core anahtarları iptal edilen yüklerin korumasını kaldır
author: rick-anderson
description: ASP.NET Core bir uygulamada, bu yana gelen anahtarlarla korunan verilerin korumasını kaldırma hakkında bilgi edinin.
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/consumer-apis/dangerous-unprotect
ms.openlocfilehash: 062703fc72ab4e515a99558b3316070ce1f83f79
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776805"
---
# <a name="unprotect-payloads-whose-keys-have-been-revoked-in-aspnet-core"></a>ASP.NET Core anahtarları iptal edilen yüklerin korumasını kaldır

<a name="data-protection-consumer-apis-dangerous-unprotect"></a>

ASP.NET Core veri koruma API 'Leri öncelikle gizli yüklerin sınırsız kalıcılığı için tasarlanmamıştır. [WINDOWS CNG DPAPI](https://msdn.microsoft.com/library/windows/desktop/hh706794%28v=vs.85%29.aspx) ve [Azure Rights Management](/rights-management/) gibi diğer teknolojiler, sınırsız depolama senaryosuna daha uygundur ve bunlara karşılık olarak güçlü anahtar yönetim özelliklerine sahiptir. Yani, bir geliştiricinin gizli verilerin uzun süreli korunması için ASP.NET Core veri koruma API 'Lerini kullanmasını engelleyen bir şey yoktur. Anahtarlar hiçbir zaman anahtar halkadan kaldırılmaz, bu nedenle `IDataProtector.Unprotect` anahtarlar kullanılabilir ve geçerli olduğu sürece mevcut yükleri her zaman kurtarabilir.

Ancak, geliştirici iptal edilmiş bir anahtarla korunan verilerin korumasını denediğinde, bu durumda bir özel durum oluşturacak şekilde `IDataProtector.Unprotect` bir sorun ortaya çıkar. Bu yük türleri sistem tarafından kolayca yeniden oluşturulabilir ve en kötü site ziyaretçisinin tekrar oturum açması gerekebilecek için, kısa süreli veya geçici yüklere (kimlik doğrulama belirteçleri gibi) uygun olabilir. Ancak kalıcı yükleri için `Unprotect` throw 'un kabul edilemez veri kaybına neden olabilir.

## <a name="ipersisteddataprotector"></a>IPersistedDataProtector

İptal edilen anahtarların yanında bile yüklerin korumasız hale geçmesine izin verme senaryosunu desteklemek için, veri koruma sistemi bir `IPersistedDataProtector` tür içerir. Bir örneğini `IPersistedDataProtector`almak için, normal `IDataProtector` `IDataProtector` şekilde `IPersistedDataProtector`bir örneğini almanız ve ' ı ' a atamayı denemeniz yeterlidir.

> [!NOTE]
> Örneklerin hepsi `IDataProtector` öğesine `IPersistedDataProtector`atanamaz. Geliştiriciler, geçersiz çalışma zamanı özel durumlarının oluşmasını önlemek için C# ' ı işleci veya benzer şekilde kullanmalıdır ve hata durumunu uygun şekilde işleyecek şekilde hazırlanmalıdır.

`IPersistedDataProtector`Aşağıdaki API yüzeyini gösterir:

```csharp
DangerousUnprotect(byte[] protectedData, bool ignoreRevocationErrors,
     out bool requiresMigration, out bool wasRevoked) : byte[]
```

Bu API, korumalı yükü (bir bayt dizisi olarak) alır ve korumasız yükü döndürür. Dize tabanlı aşırı yükleme yoktur. İki out parametresi aşağıdaki gibidir.

* `requiresMigration`: Bu yükü korumak için kullanılan anahtar artık etkin varsayılan anahtar değilse, true olarak ayarlanacak. Örneğin, bu yükü korumak için kullanılan anahtar eski ve bir anahtar toplama işlemi gerçekleştiğinden daha sonra çalışır. Çağıran, iş ihtiyaçlarına bağlı olarak yükü yeniden korumayı düşünmek isteyebilir.

* `wasRevoked`: Bu yükü korumak için kullanılan anahtar iptal edildiyse, true olarak ayarlanır.

>[!WARNING]
> `DangerousUnprotect` Yöntemine geçiş yaparken `ignoreRevocationErrors: true` çok dikkatli olun. Bu yöntemi çağırdıktan sonra `wasRevoked` değer true ise, bu yükü korumak için kullanılan anahtar iptal edilir ve yükün kimlik doğrulaması şüpheli olarak değerlendirilmelidir. Bu durumda, gerçek bir güveninin, örneğin güvenilmeyen bir Web istemcisi tarafından gönderilmesi yerine güvenli bir veritabanından geldiği durumlarda, yalnızca korumasız yük üzerinde çalışmaya devam edin.

[!code-csharp[](dangerous-unprotect/samples/dangerous-unprotect.cs)]
