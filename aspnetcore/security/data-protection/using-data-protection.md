---
title: ASP.NET Core 'de veri koruma API 'Lerini kullanmaya başlayın
author: rick-anderson
description: Bir uygulamadaki verileri korumak ve korumayı kaldırmak için ASP.NET Core veri koruma API 'Lerini kullanmayı öğrenin.
ms.author: riande
ms.date: 11/12/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/using-data-protection
ms.openlocfilehash: d5e9e61db39a67e8ccb7b345dfa4c97353312857
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774268"
---
# <a name="get-started-with-the-data-protection-apis-in-aspnet-core"></a>ASP.NET Core 'de veri koruma API 'Lerini kullanmaya başlayın

<a name="security-data-protection-getting-started"></a>

En basit olan verileri korumak aşağıdaki adımlardan oluşur:

1. Bir veri koruma sağlayıcısından veri koruyucusu oluşturun.

2. Korumak istediğiniz `Protect` verilerle yöntemi çağırın.

3. Düz metin `Unprotect` haline döndürmek istediğiniz verilerle yöntemi çağırın.

ASP.NET Core veya SignalRgibi birçok çerçeve ve uygulama modeli, veri koruma sistemini zaten yapılandırıp bağımlılık ekleme yoluyla erişenbir hizmet kapsayıcısına ekler. Aşağıdaki örnek, bağımlılık ekleme ve veri koruma yığınını kaydetme, veri koruma sağlayıcısını dı aracılığıyla alma, bir koruyucu oluşturma ve verilerin korumasını kaldırma için bir hizmet kapsayıcısını yapılandırmayı gösterir.

[!code-csharp[](../../security/data-protection/using-data-protection/samples/protectunprotect.cs?highlight=26,34,35,36,37,38,39,40)]

Bir koruyucu oluşturduğunuzda bir veya daha fazla [Amaç dizesi](xref:security/data-protection/consumer-apis/purpose-strings)sağlamanız gerekir. Amaç dizesi, tüketiciler arasında yalıtım sağlar. Örneğin, "yeşil" bir amaç dizesiyle oluşturulan bir koruyucu, "mor" amacını taşıyan bir koruyucu tarafından belirtilen verilerin korumasını yapamaz.

>[!TIP]
> `IDataProtectionProvider` Ve `IDataProtector` örnekleri, birden çok çağıranlar için iş parçacığı güvenlidir. Bir `IDataProtector` bileşen bir öğesine `CreateProtector`çağrısıyla öğesine bir başvuru aldıktan sonra, `Protect` ve ' `Unprotect`a yönelik birden çok çağrı için bu başvuruyu kullanacaktır.
>
>Korumalı yük doğrulanamazsa veya çözümlenememişse, öğesine `Unprotect` yapılan bir çağrı CryptographicException oluşturur. Bazı bileşenler, kaldırma işlemleri sırasında hataları yoksaymak isteyebilir; kimlik doğrulama tanımlama bilgilerini okuyan bir bileşen bu hatayı işleyebilir ve isteği, isteğin hemen başarısız olması yerine hiç bir tanımlama bilgisine sahip olmamış gibi değerlendirir. Bu davranışın, tüm özel durumlara izin vermek yerine CryptographicException özel olarak yakalamalı bileşenler.
