---
title: ASP.NET Core içinde ObjectPool ile nesne yeniden kullanımı
author: rick-anderson
description: ObjectPool kullanan ASP.NET Core uygulamalarında performansı artırmaya yönelik ipuçları.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.date: 04/11/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: performance/ObjectPool
ms.openlocfilehash: 9df7f370eb550172493478bcd8d94a9541926fec
ms.sourcegitcommit: 895e952aec11c91d703fbdd3640a979307b8cc67
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/01/2020
ms.locfileid: "85793556"
---
# <a name="object-reuse-with-objectpool-in-aspnet-core"></a>ASP.NET Core içinde ObjectPool ile nesne yeniden kullanımı

[Steve Gordon](https://twitter.com/stevejgordon), [Ryan şimdi ak](https://github.com/rynowak)ve [Günther fodl](https://github.com/gfoidl)

<xref:Microsoft.Extensions.ObjectPool>, nesnelerin çöp toplanmasına izin vermek yerine, bir nesne grubunu yeniden kullanım için bellekte tutmayı destekleyen ASP.NET Core altyapısının bir parçasıdır.

Yönetilebilecek nesneler şunları içeriyorsa, nesne havuzunu kullanmak isteyebilirsiniz:

- Ayırma/başlatma pahalıdır.
- Sınırlı bir kaynağı temsil eder.
- Tahmin edilebilir ve sık kullanılır.

Örneğin, ASP.NET Core Framework örnekleri yeniden kullanmak için bazı yerlerde nesne havuzunu kullanır <xref:System.Text.StringBuilder> . `StringBuilder`karakter verilerini tutmak için kendi arabelleğini ayırır ve yönetir. ASP.NET Core `StringBuilder` Özellikler uygulamak için düzenli olarak kullanımlar ve bunları yeniden kullanmak bir performans avantajı sağlar.

Nesne havuzu her zaman performansı iyileştirmez:

- Bir nesnenin başlatma maliyeti yüksek değilse, havuzdan nesneyi almak genellikle daha yavaştır.
- Havuz tarafından yönetilen nesneler, havuz serbest olarak ayrılana kadar ayrılmış değildir.

Yalnızca uygulamanız veya kitaplığınız için gerçekçi senaryolar kullanarak performans verilerini topladıktan sonra nesne havuzunu kullanın.

::: moniker range="< aspnetcore-3.0"
**Uyarı: `ObjectPool` uygulamaz `IDisposable` . Bunu, aktiften çıkarma gerektiren türlerle kullanmanızı önermiyoruz.** `ObjectPool`ASP.NET Core 3,0 ve üzeri sürümlerde desteklenir `IDisposable` .
::: moniker-end

**UNUTMAYıN: ObjectPool, ayırabilecek nesne sayısına bir sınır yerleştirmez, saklanacak nesne sayısına bir sınır koyar.**

## <a name="concepts"></a>Kavramlar

<xref:Microsoft.Extensions.ObjectPool.ObjectPool`1>-temel nesne havuzu soyutlama. Nesneleri almak ve döndürmek için kullanılır.

<xref:Microsoft.Extensions.ObjectPool.PooledObjectPolicy%601>-bir nesnenin nasıl oluşturulduğunu ve havuza döndürüldüğünde nasıl *sıfırlandığını* özelleştirmek için bunu uygulayın. Bu, doğrudan oluşturduğunuz bir nesne havuzuna geçirilebilir.... VEYA

<xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider.Create*>nesne havuzları oluşturmak için bir fabrika işlevi görür.
<!-- REview, there is no ObjectPoolProvider<T> -->

ObjectPool bir uygulamada birden çok şekilde kullanılabilir:

* Havuz örneği oluşturuluyor.
* Bir havuzu bir örnek olarak [bağımlılık ekleme](xref:fundamentals/dependency-injection) (dı) içinde kaydetme.
* , `ObjectPoolProvider<>` Ve ' a kayıt yaptırın ve fabrika olarak kullanılıyor.

## <a name="how-to-use-objectpool"></a>ObjectPool kullanma

<xref:Microsoft.Extensions.ObjectPool.ObjectPool`1.Get*>Bir nesne almak ve <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1.Return*> nesneyi döndürmek için çağırın.  Her nesneyi döndürmenizde gereksinim yoktur. Bir nesne döndürmezseniz atık olarak toplanacaktır.

::: moniker range=">= aspnetcore-3.0"
Kullanıldığında <xref:Microsoft.Extensions.ObjectPool.DefaultObjectPoolProvider> ve `T` `IDisposable` şunları uygular:

* Havuza ***döndürülmüyor*** öğeler silinir.
* Havuz, DI tarafından atıldığında, havuzdaki tüm öğeler silinir.

NOTE: havuz atıldıktan sonra:

* Çağırma `Get` a oluşturur `ObjectDisposedException` .
* `return`verilen öğeyi atar.

::: moniker-end

## <a name="objectpool-sample"></a>ObjectPool örneği

Aşağıdaki kod:

* `ObjectPoolProvider` [Bağımlılık ekleme](xref:fundamentals/dependency-injection) (dı) kapsayıcısına ekler.
* Dı kapsayıcısına ekler ve yapılandırır `ObjectPool<StringBuilder>` .
* Öğesini ekler `BirthdayMiddleware` .

[!code-csharp[](ObjectPool/ObjectPoolSample/Startup.cs?name=snippet)]

Aşağıdaki kod şunu uygular`BirthdayMiddleware`

[!code-csharp[](ObjectPool/ObjectPoolSample/BirthdayMiddleware.cs?name=snippet)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]
