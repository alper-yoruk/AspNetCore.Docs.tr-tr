---
title: ASP.NET Core içinde ObjectPool ile nesne yeniden kullanımı
author: rick-anderson
description: ObjectPool kullanan ASP.NET Core uygulamalarında performansı artırmaya yönelik ipuçları.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.date: 04/11/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: performance/ObjectPool
ms.openlocfilehash: f29d15fc1e2d2ad84526598be14638110f08614e
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774788"
---
# <a name="object-reuse-with-objectpool-in-aspnet-core"></a>ASP.NET Core içinde ObjectPool ile nesne yeniden kullanımı

, [Steve Gordon](https://twitter.com/stevejgordon), [Ryan şimdi ak](https://github.com/rynowak)ve [Rick Anderson](https://twitter.com/RickAndMSFT)

<xref:Microsoft.Extensions.ObjectPool>, nesnelerin çöp toplanmasına izin vermek yerine, bir nesne grubunu yeniden kullanım için bellekte tutmayı destekleyen ASP.NET Core altyapısının bir parçasıdır.

Yönetilebilecek nesneler şunları içeriyorsa, nesne havuzunu kullanmak isteyebilirsiniz:

- Ayırma/başlatma pahalıdır.
- Sınırlı bir kaynağı temsil eder.
- Tahmin edilebilir ve sık kullanılır.

Örneğin, ASP.NET Core Framework örnekleri yeniden kullanmak <xref:System.Text.StringBuilder> için bazı yerlerde nesne havuzunu kullanır. `StringBuilder`karakter verilerini tutmak için kendi arabelleğini ayırır ve yönetir. ASP.NET Core özellikler uygulamak `StringBuilder` için düzenli olarak kullanımlar ve bunları yeniden kullanmak bir performans avantajı sağlar.

Nesne havuzu her zaman performansı iyileştirmez:

- Bir nesnenin başlatma maliyeti yüksek değilse, havuzdan nesneyi almak genellikle daha yavaştır.
- Havuz tarafından yönetilen nesneler, havuz serbest olarak ayrılana kadar ayrılmış değildir.

Yalnızca uygulamanız veya kitaplığınız için gerçekçi senaryolar kullanarak performans verilerini topladıktan sonra nesne havuzunu kullanın.

**Uyarı: `ObjectPool` uygulamaz `IDisposable`. Bunu, aktiften çıkarma gerektiren türlerle kullanmanızı önermiyoruz.**

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

Bir <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1> nesne almak ve <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1.Return*> nesneyi döndürmek için çağırın.  Her nesneyi döndürmenizde gereksinim yoktur. Bir nesne döndürmezseniz atık olarak toplanacaktır.

## <a name="objectpool-sample"></a>ObjectPool örneği

Aşağıdaki kod:

* `ObjectPoolProvider` [Bağımlılık ekleme](xref:fundamentals/dependency-injection) (dı) kapsayıcısına ekler.
* Dı kapsayıcısına ekler `ObjectPool<StringBuilder>` ve yapılandırır.
* Öğesini ekler `BirthdayMiddleware`.

[!code-csharp[](ObjectPool/ObjectPoolSample/Startup.cs?name=snippet)]

Aşağıdaki kod şunu uygular`BirthdayMiddleware`

[!code-csharp[](ObjectPool/ObjectPoolSample/BirthdayMiddleware.cs?name=snippet)]
