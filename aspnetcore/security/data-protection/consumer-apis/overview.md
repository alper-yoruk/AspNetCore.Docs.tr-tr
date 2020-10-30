---
title: ASP.NET Core için tüketici API 'Lerine genel bakış
author: rick-anderson
description: ASP.NET Core veri koruma kitaplığı 'nda bulunan çeşitli tüketici API 'Lerine ilişkin kısa bir genel bakış alın.
ms.author: riande
ms.date: 06/11/2019
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
uid: security/data-protection/consumer-apis/overview
ms.openlocfilehash: 485ea3f669b518f2979d04493b281bd116b05f65
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051882"
---
# <a name="consumer-apis-overview-for-aspnet-core"></a>ASP.NET Core için tüketici API 'Lerine genel bakış

`IDataProtectionProvider`Ve `IDataProtector` arabirimleri, tüketicilerinin veri koruma sistemini kullanan temel arabirimlerdir. Bunlar [Microsoft. AspNetCore. DataProtection. soyutlamalar](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Abstractions/) paketinde bulunur.

## <a name="idataprotectionprovider"></a>Idataprotectionprovider

Sağlayıcı arabirimi, veri koruma sisteminin kökünü temsil eder. Verileri korumak veya korumayı kaldırmak için doğrudan kullanılamaz. Bunun yerine, `IDataProtector` `IDataProtectionProvider.CreateProtector(purpose)` Amaç, hedeflenen tüketici kullanım durumunu açıklayan bir dize olan çağırarak, çağırarak öğesine bir başvuru almalıdır. Bu parametrenin amacı hakkında daha fazla bilgi ve uygun bir değer seçme hakkında daha fazla bilgi için bkz. [Amaç dizeleri](xref:security/data-protection/consumer-apis/purpose-strings) .

## <a name="idataprotector"></a>Idataprotector

Koruyucu arabirimi, öğesine yapılan bir çağrı tarafından döndürülür `CreateProtector` ve bu arabirim, tüketicilerin koruma ve kaldırma işlemleri gerçekleştirmek için kullanabileceği bu arabirimdir.

Bir veri parçasını korumak için verileri `Protect` metoduna geçirin. Temel arabirim, Byte []-> Byte [] ' ı dönüştüren bir yöntemi tanımlar, ancak dize > dizesini dönüştüren aşırı yükleme (uzantı yöntemi olarak sağlanmış) de vardır. İki yöntem tarafından sunulan güvenlik aynıdır; geliştirici, kullanım durumu için hangi aşırı yükün en kullanışlı olduğunu seçmelidir. Seçilen aşırı yükleme ne olursa olsun, koruma yöntemi tarafından döndürülen değer artık korunur (şifreleme ve üzerinde oynanarak denetlenen) ve uygulama onu güvenilmeyen bir istemciye gönderebilir.

Daha önce korunan bir veri parçasının korumasını kaldırmak için korumalı verileri `Unprotect` yöntemine geçirin. (Geliştiriciye kolaylık olması için Byte [] tabanlı ve dize tabanlı aşırı yüklemeler vardır.) Korumalı yük bu aynı üzerinde daha önceki bir çağrı tarafından oluşturulduysa `Protect` `IDataProtector` , `Unprotect` yöntemi orijinal korumasız yükü döndürür. Korunan yük üzerinde oynanmış veya farklı bir ile üretildiyse `IDataProtector` , `Unprotect` Yöntem CryptographicException oluşturur.

Aynı ve kavramı, `IDataProtector` Amaç kavramıyla farklıdır. `IDataProtector`Aynı köke sahip, `IDataProtectionProvider` ancak çağrısında farklı amaç dizeleri aracılığıyla iki örnek `IDataProtectionProvider.CreateProtector` oluşturulduklarında, [farklı koruyucuları](xref:security/data-protection/consumer-apis/purpose-strings)kabul edilir ve diğeri tarafından oluşturulan yüklerin korumasını kaldırılamaz.

## <a name="consuming-these-interfaces"></a>Bu arabirimleri kullanma

Dı kullanan bir bileşen için, istenen kullanım, bileşenin `IDataProtectionProvider` oluşturucusunda bir parametre aldığı ve bileşen BAŞLATıLDıĞıNDA dı sisteminin otomatik olarak bu hizmeti sağladığını sunmasıdır.

> [!NOTE]
> Bazı uygulamalar (konsol uygulamaları veya ASP.NET 4. x uygulamaları gibi), burada açıklanan mekanizmayı kullanamaz. Bu senaryolar için, ara ' ya geçmeden bir sağlayıcının örneğini alma hakkında daha fazla bilgi için, bu [olmayan duyarlı senaryolar](xref:security/data-protection/configuration/non-di-scenarios) belgesine başvurun `IDataProtection` .

Aşağıdaki örnek üç kavram göstermektedir:

1. [Veri koruma sistemini](xref:security/data-protection/configuration/overview) hizmet kapsayıcısına ekleme,

2. Bir örneğini almak için DI kullanma `IDataProtectionProvider` ve

3. Kaynağından bir oluşturup `IDataProtector` `IDataProtectionProvider` , verileri korumak ve korumayı kaldırmak için kullanarak.

[!code-csharp[](../using-data-protection/samples/protectunprotect.cs?highlight=26,34,35,36,37,38,39,40)]

Microsoft. AspNetCore. DataProtection. soyutlamalar paketi geliştirici kolaylığı olarak bir genişletme yöntemi içerir `IServiceProvider.GetDataProtector` . Hem hizmet sağlayıcısından hem de çağırarak, tek bir işlem olarak Kapsüller `IDataProtectionProvider` `IDataProtectionProvider.CreateProtector` . Aşağıdaki örnek kullanımını gösterir.

[!code-csharp[](./overview/samples/getdataprotector.cs?highlight=15)]

>[!TIP]
> Ve örnekleri `IDataProtectionProvider` , `IDataProtector` birden çok çağıranlar için iş parçacığı güvenlidir. Bir bileşen bir öğesine çağrısıyla öğesine bir başvuru aldıktan sonra `IDataProtector` `CreateProtector` , ve ' a yönelik birden çok çağrı için bu başvuruyu kullanacaktır `Protect` `Unprotect` . `Unprotect`Korumalı yük doğrulanamazsa veya çözümlenememişse, öğesine yapılan bir çağrı CryptographicException oluşturur. Bazı bileşenler, kaldırma işlemleri sırasında hataları yoksaymak isteyebilir; kimlik doğrulamasını okuyan bir bileşen cookie Bu hatayı işleyebilir ve isteği cookie , isteğin hemen başarısız olması yerine hiç olmadığı gibi ele alabilir. Bu davranışın, tüm özel durumlara izin vermek yerine CryptographicException özel olarak yakalamalı bileşenler.
