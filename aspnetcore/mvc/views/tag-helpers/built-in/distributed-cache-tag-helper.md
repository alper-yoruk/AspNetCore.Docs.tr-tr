---
title: ASP.NET Core dağıtılmış önbellek etiketi Yardımcısı
author: pkellner
description: Dağıtılmış önbellek etiketi yardımcısını nasıl kullanacağınızı öğrenin.
ms.author: riande
ms.custom: mvc
ms.date: 01/24/2020
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
uid: mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper
ms.openlocfilehash: 67e5b7ef09525063da6e6b7dfce6fd084d279869
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88633909"
---
# <a name="distributed-cache-tag-helper-in-aspnet-core"></a>ASP.NET Core dağıtılmış önbellek etiketi Yardımcısı

By [Peter Kellner](https://peterkellner.net)

Dağıtılmış önbellek etiketi Yardımcısı, içeriğini dağıtılmış bir önbellek kaynağına önbelleğe alarak ASP.NET Core uygulamanızın performansını önemli ölçüde iyileştirebilme olanağı sağlar.

Etiket Yardımcıları hakkında genel bilgi için bkz <xref:mvc/views/tag-helpers/intro> ..

Dağıtılmış önbellek etiketi Yardımcısı, önbellek etiketi Yardımcısı ile aynı temel sınıftan devralınır. Tüm [önbellek etiketi Yardımcısı](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper) öznitelikleri, dağıtılmış etiket Yardımcısı tarafından kullanılabilir.

Dağıtılmış önbellek etiketi Yardımcısı, [Oluşturucu Ekleme](xref:fundamentals/dependency-injection#constructor-injection-behavior)işlemini kullanır. <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>Arabirim, dağıtılmış önbellek etiketi Yardımcısı 'nın oluşturucusuna geçirilir. `IDistributedCache`(Startup.cs) içinde somut bir uygulama oluşturulmadıysa `Startup.ConfigureServices` , dağıtılmış önbellek etiketi Yardımcısı önbelleğe alınmış verileri [önbellek etiketi Yardımcısı](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper)olarak depolamak için aynı bellek içi sağlayıcıyı kullanır.*Startup.cs*

## <a name="distributed-cache-tag-helper-attributes"></a>Dağıtılmış önbellek etiketi Yardımcısı öznitelikleri

### <a name="attributes-shared-with-the-cache-tag-helper"></a>Önbellek etiketi Yardımcısı ile paylaşılan öznitelikler

* `enabled`
* `expires-on`
* `expires-after`
* `expires-sliding`
* `vary-by-header`
* `vary-by-query`
* `vary-by-route`
* `vary-by-cookie`
* `vary-by-user`
* `vary-by priority`

Dağıtılmış önbellek etiketi Yardımcısı, önbellek etiketi Yardımcısı ile aynı sınıftan devralınır. Bu özniteliklerin açıklamaları için [önbellek etiketi Yardımcısı](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper)' na bakın.

### <a name="name"></a>name

| Öznitelik türü | Örnek                               |
| -------------- | ------------------------------------- |
| Dize         | `my-distributed-cache-unique-key-101` |

`name` gereklidir. `name`Özniteliği, depolanan her önbellek örneği için bir anahtar olarak kullanılır. Sayfadaki sayfa adı ve konuma göre her örneğe bir önbellek anahtarı atayan önbellek etiketi Yardımcısı 'nın aksine Razor Razor , dağıtılmış önbellek etiketi Yardımcısı yalnızca anahtarını özniteliğinde temel alır `name` .

Örnek:

```cshtml
<distributed-cache name="my-distributed-cache-unique-key-101">
    Time Inside Cache Tag Helper: @DateTime.Now
</distributed-cache>
```

## <a name="distributed-cache-tag-helper-idistributedcache-implementations"></a>Dağıtılmış önbellek etiketi Yardımcısı ıdistributedönbellek uygulamaları

<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>ASP.NET Core için yerleşik iki uygulama vardır. Biri SQL Server tabanlıdır ve diğeri redin tabanlıdır. [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html)gibi üçüncü taraf uygulamalar da mevcuttur. Bu uygulamaların ayrıntıları adresinde bulunabilir <xref:performance/caching/distributed> . Her iki uygulama da bir örneğini ayarlamayı `IDistributedCache` içerir `Startup` .

Özellikle belirli bir uygulamasını kullanmayla ilişkili etiket özniteliği yok `IDistributedCache` .

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:fundamentals/dependency-injection>
* <xref:performance/caching/distributed>
* <xref:performance/caching/memory>
* <xref:security/authentication/identity>
