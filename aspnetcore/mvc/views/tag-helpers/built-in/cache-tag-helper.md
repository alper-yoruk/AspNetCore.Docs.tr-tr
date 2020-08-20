---
title: ASP.NET Core MVC 'de önbellek etiketi Yardımcısı
author: pkellner
description: Önbellek etiketi yardımcısını kullanmayı öğrenin.
ms.author: riande
ms.custom: mvc
ms.date: 10/10/2018
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
uid: mvc/views/tag-helpers/builtin-th/cache-tag-helper
ms.openlocfilehash: b1cab7ab8b491529ee4208d92fb30082be795eda
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88635066"
---
# <a name="cache-tag-helper-in-aspnet-core-mvc"></a>ASP.NET Core MVC 'de önbellek etiketi Yardımcısı

By [Peter Kellner](https://peterkellner.net)

Önbellek etiketi Yardımcısı, içeriğini dahili ASP.NET Core önbellek sağlayıcısına önbelleğe alarak ASP.NET Core uygulamanızın performansını iyileştirebilme olanağı sağlar.

Etiket Yardımcıları hakkında genel bilgi için bkz <xref:mvc/views/tag-helpers/intro> ..

Aşağıdaki Razor biçimlendirme geçerli tarihi önbelleğe alır:

```cshtml
<cache>@DateTime.Now</cache>
```

Etiket Yardımcısını içeren sayfanın ilk isteği geçerli tarihi görüntüler. Önbellek süresi dolana kadar veya önbelleğe alınmış tarih önbellekten çıkarılana kadar ek istekler önbelleğe alınmış değeri gösterir.

## <a name="cache-tag-helper-attributes"></a>Önbellek etiketi yardımcı öznitelikleri

### <a name="enabled"></a>enabled

| Öznitelik türü  | Örnekler        | Varsayılan |
| --------------- | --------------- | ------- |
| Boole         | `true`, `false` | `true`  |

`enabled` Önbellek etiketi Yardımcısı tarafından eklenen içeriğin önbelleğe alınıp alınmayacağını belirler. Varsayılan değer: `true`. Olarak ayarlanırsa `false` , işlenen çıktı önbelleğe **alınmaz** .

Örnek:

```cshtml
<cache enabled="true">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="expires-on"></a>süre sonu-açık

| Öznitelik türü   | Örnek                            |
| ---------------- | ---------------------------------- |
| `DateTimeOffset` | `@new DateTime(2025,1,29,17,02,0)` |

`expires-on` önbelleğe alınmış öğe için mutlak bir sona erme tarihi ayarlar.

Aşağıdaki örnek, 29 Ocak 2025 ' de 5:02 PM 'e kadar önbellek etiketi Yardımcısı 'nın içeriğini önbelleğe alır:

```cshtml
<cache expires-on="@new DateTime(2025,1,29,17,02,0)">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="expires-after"></a>süre sonu-sonra

| Öznitelik türü | Örnek                      | Varsayılan    |
| -------------- | ---------------------------- | ---------- |
| `TimeSpan`     | `@TimeSpan.FromSeconds(120)` | 20 dakika |

`expires-after` içeriği önbelleğe almak için ilk istek saatinden geçen sürenin uzunluğunu ayarlar.

Örnek:

```cshtml
<cache expires-after="@TimeSpan.FromSeconds(120)">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

RazorGörünüm altyapısı varsayılan `expires-after` değeri yirmi dakika olarak ayarlar.

### <a name="expires-sliding"></a>süre sonu-kayan

| Öznitelik türü | Örnek                     |
| -------------- | --------------------------- |
| `TimeSpan`     | `@TimeSpan.FromSeconds(60)` |

Değerine erişilmediyse önbellek girişinin çıkarılme süresini ayarlar.

Örnek:

```cshtml
<cache expires-sliding="@TimeSpan.FromSeconds(60)">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-header"></a>üst bilgiye göre değişiklik

| Öznitelik türü | Örnekler                                    |
| -------------- | ------------------------------------------- |
| Dize         | `User-Agent`, `User-Agent,content-encoding` |

`vary-by-header` değişiklik yaparken önbellek yenilemeyi tetikleyen üst bilgi değerlerinin virgülle ayrılmış listesini kabul eder.

Aşağıdaki örnek, üst bilgi değerini izler `User-Agent` . Örnek, Web sunucusuna sunulan her farklı için içeriği önbelleğe alır `User-Agent` :

```cshtml
<cache vary-by-header="User-Agent">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-query"></a>sorguya göre değişiklik

| Öznitelik türü | Örnekler             |
| -------------- | -------------------- |
| Dize         | `Make`, `Make,Model` |

`vary-by-query`<xref:Microsoft.AspNetCore.Http.IQueryCollection.Keys*> <xref:Microsoft.AspNetCore.Http.HttpRequest.Query*> listelenen herhangi bir anahtarın değeri değiştiğinde önbellek yenilemeyi tetikleyen bir sorgu dizesinde (), virgülle ayrılmış bir listesini kabul eder.

Aşağıdaki örnek, ve değerlerini izler `Make` `Model` . Örnek, içeriği her farklı `Make` ve Web sunucusuna sunulan her bir için önbelleğe alır `Model` :

```cshtml
<cache vary-by-query="Make,Model">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-route"></a>Yönlendirme ölçütü

| Öznitelik türü | Örnekler             |
| -------------- | -------------------- |
| Dize         | `Make`, `Make,Model` |

`vary-by-route` yol verileri parametre değeri değiştiğinde önbellek yenilemesi tetikleyen bir virgülle ayrılmış yol parametresi listesini kabul eder.

Örnek:

*Startup.cs*:

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{Make?}/{Model?}");
```

*Index. cshtml*:

```cshtml
<cache vary-by-route="Make,Model">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-no-loccookie"></a>değişiklik ölçütü-cookie

| Öznitelik türü | Örnekler                                                                         |
| -------------- | -------------------------------------------------------------------------------- |
| Dize         | `.AspNetCore.Identity.Application`, `.AspNetCore.Identity.Application,HairColor` |

`vary-by-cookie`cookiedeğerler değiştiğinde önbellek yenilemeyi tetikleyen adların virgülle ayrılmış bir listesini kabul eder cookie .

Aşağıdaki örnek cookie ile ilişkili ' i izler ASP.NET Core Identity . Bir kullanıcının kimliği doğrulandığında, içindeki bir değişiklik Identity cookie bir önbellek yenilemesi tetikler:

```cshtml
<cache vary-by-cookie=".AspNetCore.Identity.Application">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-user"></a>kullanıcıya göre değişiklik

| Öznitelik türü  | Örnekler        | Varsayılan |
| --------------- | --------------- | ------- |
| Boole         | `true`, `false` | `true`  |

`vary-by-user` oturum açan kullanıcı (veya bağlam sorumlusu) değiştiğinde önbelleğin sıfırlanıp sıfırlanmayacağını belirtir. Geçerli Kullanıcı, Istek bağlamı sorumlusu olarak da bilinir ve Razor başvuru yaparak bir görünümde görüntülenebilir `@User.Identity.Name` .

Aşağıdaki örnek, bir önbellek yenilemeyi tetiklemek için geçerli oturum açan kullanıcıyı izler:

```cshtml
<cache vary-by-user="true">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

Bu özniteliğin kullanılması, oturum açma ve oturum kapatma döngüsüyle önbellekteki içerikleri saklar. Değer olarak ayarlandığında `true` , bir kimlik doğrulama çevrimi kimliği doğrulanmış kullanıcı için önbelleği geçersiz kılar. cookieBir kullanıcının kimliği doğrulandığında yeni bir benzersiz değer oluşturulduğundan önbellek geçersiz kılındı. No yoksa cookie veya süresi dolduğunda önbellek, anonim durum için korunur cookie . Kullanıcının kimliği **doğrulanmıyorsa** , önbellek korunur.

### <a name="vary-by"></a>değişiklik ölçütü-

| Öznitelik türü | Örnek  |
| -------------- | -------- |
| Dize         | `@Model` |

`vary-by` önbelleğe alınan verilerin özelleştirilmesine izin verir. Özniteliğin dize değeri tarafından başvurulan nesne değiştiğinde, önbellek etiketi Yardımcısı 'nın içeriği güncelleştirilir. Genellikle, model değerlerinin dize birleştirmesi bu özniteliğe atanır. Etkin olarak, bu, herhangi bir birleştirilmiş değerden bir güncelleştirmenin önbelleği geçersiz hale getirildiği bir senaryoya neden olur.

Aşağıdaki örnek, görünümü işleyen denetleyici yönteminin, iki yol parametrelerinin tamsayı değerini toplamasını ve ve `myParam1` `myParam2` toplamı tek model özelliği olarak döndürdüğünü varsayar. Bu toplam değiştiğinde, önbellek etiketi Yardımcısı 'nın içeriği işlenir ve yeniden önbelleğe alınır.  

Eylem:

```csharp
public IActionResult Index(string myParam1, string myParam2, string myParam3)
{
    int num1;
    int num2;
    int.TryParse(myParam1, out num1);
    int.TryParse(myParam2, out num2);
    return View(viewName, num1 + num2);
}
```

*Index. cshtml*:

```cshtml
<cache vary-by="@Model">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="priority"></a>Priority

| Öznitelik türü      | Örnekler                               | Varsayılan  |
| ------------------- | -------------------------------------- | -------- |
| `CacheItemPriority` | `High`, `Low`, `NeverRemove`, `Normal` | `Normal` |

`priority` yerleşik önbellek sağlayıcısına önbellek çıkarma kılavuzu sağlar. Web sunucusu, `Low` bellek baskısı altında olduğunda ön belleği önbelleğe alabilir.

Örnek:

```cshtml
<cache priority="High">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

`priority`Özniteliği belirli bir önbellek saklama düzeyini garanti etmez. `CacheItemPriority` yalnızca bir öneridir. Bu özniteliğin olarak ayarlanması `NeverRemove` , önbelleğe alınmış öğelerin her zaman korunmamasını garanti etmez. Daha fazla bilgi için [ek kaynaklar](#additional-resources) bölümündeki konulara bakın.

Önbellek etiketi Yardımcısı, [bellek önbelleği hizmetine](xref:performance/caching/memory)bağımlıdır. Önbellek etiketi Yardımcısı eklenmemişse hizmeti ekler.

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:performance/caching/memory>
* <xref:security/authentication/identity>
