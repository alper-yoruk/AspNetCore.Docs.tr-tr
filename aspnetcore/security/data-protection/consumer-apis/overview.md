---
title: ASP.NET Core için tüketici API 'Lerine genel bakış
author: rick-anderson
description: ASP.NET Core veri koruma kitaplığı 'nda bulunan çeşitli tüketici API 'Lerine ilişkin kısa bir genel bakış alın.
ms.author: riande
ms.date: 06/11/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/consumer-apis/overview
ms.openlocfilehash: e840111d702882a45e59cf89d679b87fa537d833
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82767863"
---
# <a name="consumer-apis-overview-for-aspnet-core"></a>ASP.NET Core için tüketici API 'Lerine genel bakış

Ve `IDataProtectionProvider` `IDataProtector` arabirimleri, tüketicilerinin veri koruma sistemini kullanan temel arabirimlerdir. Bunlar [Microsoft. AspNetCore. DataProtection. soyutlamalar](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Abstractions/) paketinde bulunur.

## <a name="idataprotectionprovider"></a>Idataprotectionprovider

Sağlayıcı arabirimi, veri koruma sisteminin kökünü temsil eder. Verileri korumak veya korumayı kaldırmak için doğrudan kullanılamaz. Bunun yerine, amaç, hedeflenen tüketici kullanım durumunu açıklayan `IDataProtector` bir dize `IDataProtectionProvider.CreateProtector(purpose)`olan çağırarak, çağırarak öğesine bir başvuru almalıdır. Bu parametrenin amacı hakkında daha fazla bilgi ve uygun bir değer seçme hakkında daha fazla bilgi için bkz. [Amaç dizeleri](xref:security/data-protection/consumer-apis/purpose-strings) .

## <a name="idataprotector"></a>Idataprotector

Koruyucu arabirimi, öğesine `CreateProtector`yapılan bir çağrı tarafından döndürülür ve bu arabirim, tüketicilerin koruma ve kaldırma işlemleri gerçekleştirmek için kullanabileceği bu arabirimdir.

Bir veri parçasını korumak için verileri `Protect` metoduna geçirin. Temel arabirim, Byte []-> Byte [] ' ı dönüştüren bir yöntemi tanımlar, ancak dize > dizesini dönüştüren aşırı yükleme (uzantı yöntemi olarak sağlanmış) de vardır. İki yöntem tarafından sunulan güvenlik aynıdır; geliştirici, kullanım durumu için hangi aşırı yükün en kullanışlı olduğunu seçmelidir. Seçilen aşırı yükleme ne olursa olsun, koruma yöntemi tarafından döndürülen değer artık korunur (şifreleme ve üzerinde oynanarak denetlenen) ve uygulama onu güvenilmeyen bir istemciye gönderebilir.

Daha önce korunan bir veri parçasının korumasını kaldırmak için korumalı verileri `Unprotect` yöntemine geçirin. (Geliştiriciye kolaylık olması için Byte [] tabanlı ve dize tabanlı aşırı yüklemeler vardır.) Korumalı yük bu aynı `Protect` `IDataProtector`üzerinde daha önceki bir çağrı tarafından oluşturulduysa, `Unprotect` yöntemi orijinal korumasız yükü döndürür. Korunan yük üzerinde oynanmış veya farklı `IDataProtector`bir ile üretildiyse, `Unprotect` Yöntem CryptographicException oluşturur.

Aynı ve kavramı, amaç kavramıyla `IDataProtector` farklıdır. Aynı köke `IDataProtector` `IDataProtectionProvider` sahip, ancak çağrısında `IDataProtectionProvider.CreateProtector`farklı amaç dizeleri aracılığıyla iki örnek oluşturulduklarında, [farklı koruyucuları](xref:security/data-protection/consumer-apis/purpose-strings)kabul edilir ve diğeri tarafından oluşturulan yüklerin korumasını kaldırılamaz.

## <a name="consuming-these-interfaces"></a>Bu arabirimleri kullanma

Dı kullanan bir bileşen için, istenen kullanım, bileşenin oluşturucusunda bir `IDataProtectionProvider` parametre aldığı ve BILEŞEN başlatıldığında dı sisteminin otomatik olarak bu hizmeti sağladığını sunmasıdır.

> [!NOTE]
> Bazı uygulamalar (konsol uygulamaları veya ASP.NET 4. x uygulamaları gibi), burada açıklanan mekanizmayı kullanamaz. Bu senaryolar için, ara ' ya geçmeden bir `IDataProtection` sağlayıcının örneğini alma hakkında daha fazla bilgi için, bu [olmayan duyarlı senaryolar](xref:security/data-protection/configuration/non-di-scenarios) belgesine başvurun.

Aşağıdaki örnek üç kavram göstermektedir:

1. [Veri koruma sistemini](xref:security/data-protection/configuration/overview) hizmet kapsayıcısına ekleme,

2. Bir `IDataProtectionProvider`örneğini almak için dı kullanma ve

3. Kaynağından bir `IDataProtector` oluşturup `IDataProtectionProvider` , verileri korumak ve korumayı kaldırmak için kullanarak.

[!code-csharp[](../using-data-protection/samples/protectunprotect.cs?highlight=26,34,35,36,37,38,39,40)]

Microsoft. AspNetCore. DataProtection. soyutlamalar paketi geliştirici kolaylığı olarak bir genişletme `IServiceProvider.GetDataProtector` yöntemi içerir. Hem hizmet sağlayıcısından hem de `IDataProtectionProvider` çağırarak `IDataProtectionProvider.CreateProtector`, tek bir işlem olarak kapsüller. Aşağıdaki örnek kullanımını gösterir.

[!code-csharp[](./overview/samples/getdataprotector.cs?highlight=15)]

>[!TIP]
> `IDataProtectionProvider` Ve `IDataProtector` örnekleri, birden çok çağıranlar için iş parçacığı güvenlidir. Bir `IDataProtector` bileşen bir öğesine `CreateProtector`çağrısıyla öğesine bir başvuru aldıktan sonra, `Protect` ve ' `Unprotect`a yönelik birden çok çağrı için bu başvuruyu kullanacaktır. Korumalı yük doğrulanamazsa veya çözümlenememişse, öğesine `Unprotect` yapılan bir çağrı CryptographicException oluşturur. Bazı bileşenler, kaldırma işlemleri sırasında hataları yoksaymak isteyebilir; kimlik doğrulama tanımlama bilgilerini okuyan bir bileşen bu hatayı işleyebilir ve isteği, isteğin hemen başarısız olması yerine hiç bir tanımlama bilgisine sahip olmamış gibi değerlendirir. Bu davranışın, tüm özel durumlara izin vermek yerine CryptographicException özel olarak yakalamalı bileşenler.
