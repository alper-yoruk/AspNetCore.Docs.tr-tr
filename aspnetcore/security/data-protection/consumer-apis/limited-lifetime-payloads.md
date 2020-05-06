---
title: ASP.NET Core korumalı yüklerin ömrünü sınırlayın
author: rick-anderson
description: ASP.NET Core veri koruma API 'Lerini kullanarak korumalı yükün ömrünü sınırlamayı öğrenin.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/consumer-apis/limited-lifetime-payloads
ms.openlocfilehash: bc1597f75d8c5f786d46e59ac027d01ffca077c0
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82768617"
---
# <a name="limit-the-lifetime-of-protected-payloads-in-aspnet-core"></a>ASP.NET Core korumalı yüklerin ömrünü sınırlayın

Uygulama geliştiricisinin, belirlenen süre sonunda süresi dolan korumalı bir yük oluşturmak istediği senaryolar vardır. Örneğin, korumalı yük yalnızca bir saat için geçerli olması gereken bir parola sıfırlama belirtecini temsil edebilir. Geliştiricinin, gömülü bir sona erme tarihi içeren kendi yük biçimini oluşturması kesinlikle olasıdır ve gelişmiş geliştiriciler bunu yapmak isteyebilir, ancak bu süre sonlarını yöneten geliştiricilerin çoğunluğu sıkıcı bir şekilde büyüyebilir.

Geliştirici izleyicilerimizin bu kadar kolay olması için, [Microsoft. AspNetCore. DataProtection. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) paketi, belirlenen süre sonunda otomatik olarak süresi dolan yük oluşturma API 'lerini içerir. Bu API 'Ler, `ITimeLimitedDataProtector` türden kapanır.

## <a name="api-usage"></a>API kullanımı

`ITimeLimitedDataProtector` Arabirim, zaman sınırlı/kendi kendine süresi dolan yükleri korumak ve korumayı kaldırmak için çekirdek arabirimdir. Bir `ITimeLimitedDataProtector`örneğini oluşturmak için öncelikle belirli bir amaca göre oluşturulan normal bir [ıdataprotector](xref:security/data-protection/consumer-apis/overview) örneğine ihtiyacınız vardır. `IDataProtector` Örnek kullanılabilir olduğunda, yerleşik süre sonu özelliklerine `IDataProtector.ToTimeLimitedDataProtector` sahip bir koruyucu geri almak için genişletme yöntemini çağırın.

`ITimeLimitedDataProtector`Aşağıdaki API yüzeyini ve genişletme yöntemlerini gösterir:

* CreateProtector (dize amacı): ıomelimiteddataprotector-bu API, bir kök zaman sınırlı `IDataProtectionProvider.CreateProtector` koruyucudan [Amaç zincirlerini](xref:security/data-protection/consumer-apis/purpose-strings) oluşturmak için kullanılabilecek mevcut öğesine benzerdir.

* Koruma (Byte [] düz metin, DateTimeOffset süre sonu): Byte []

* Koruma (Byte [] düz metin, TimeSpan yaşam süresi): Byte []

* Koruma (Byte [] düz metin): Byte []

* Koruma (dize düz metin, DateTimeOffset süre sonu): dize

* Koruma (dize düz metin, zaman aralığı ömrü): dize

* Koru (dize düz metin): dize

Yalnızca düz metin olan temel `Protect` yöntemlere ek olarak, yükün sona erme tarihinin belirtilmesine izin veren yeni aşırı yüklemeler vardır. Sona erme tarihi mutlak bir tarih (bir `DateTimeOffset`ile) veya göreli bir süre (geçerli sistem saatinden bir `TimeSpan`ile) olarak belirtilebilir. Süre sonu olmayan bir aşırı yükleme çağrıldığında yükün süresinin dolmayacağı varsayılır.

* Korumasını kaldır (Byte [] protectedData, Out DateTimeOffset süre sonu): Byte []

* Korumasını kaldır (Byte [] protectedData): Byte []

* Korumasını kaldırma (dize protectedData, Out DateTimeOffset Expiration): dize

* Korumasını kaldır (dize protectedData): dize

Yöntemler `Unprotect` , orijinal korumasız verileri döndürür. Yükün süresi dolmamışsa, mutlak süre sonu, özgün korumasız verilerle birlikte isteğe bağlı bir out parametresi olarak döndürülür. Yükün geçerliliği dolmuşsa, korumasını kaldırma yönteminin tüm aşırı yüklemeleri CryptographicException oluşturur.

>[!WARNING]
> Uzun süreli veya sonsuz Kalıcılık gerektiren yükleri korumak için bu API 'Lerin kullanılması önerilmez. "Korumalı yüklerin bir aydan sonra kalıcı olarak kurtarılamadığında emin olabilir mi?" , Thumb 'in iyi bir kuralı olarak işlev görebilir; Yanıt yoksa, geliştiricilerin alternatif API 'Leri dikkate almalıdır.

Aşağıdaki örnek, veri koruma sisteminin örneğini oluşturmak için, [dı olmayan kod yollarını](xref:security/data-protection/configuration/non-di-scenarios) kullanır. Bu örneği çalıştırmak için, ilk olarak Microsoft. AspNetCore. DataProtection. Extensions paketine bir başvuru eklemiş olduğunuzdan emin olun.

[!code-csharp[](limited-lifetime-payloads/samples/limitedlifetimepayloads.cs)]
