---
title: ASP.NET Core resim etiketi Yardımcısı
author: pkellner
description: Resim etiketi Yardımcısı ile çalışmayı gösterir.
ms.author: riande
ms.custom: mvc
ms.date: 04/06/2019
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
uid: mvc/views/tag-helpers/builtin-th/image-tag-helper
ms.openlocfilehash: c72dd4f6da796364756d001a6e424eb3a96710a4
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88633844"
---
# <a name="image-tag-helper-in-aspnet-core"></a>ASP.NET Core resim etiketi Yardımcısı

By [Peter Kellner](https://peterkellner.net)

Resim etiketi Yardımcısı, `<img>` statik görüntü dosyaları için önbellek-busting davranışı sağlamak üzere etiketini geliştirir.

Önbellek-busting dizesi, varlığın URL 'sine eklenen statik görüntü dosyasının karmasını temsil eden benzersiz bir değerdir. Benzersiz dize, istemcilerin (ve bazı proxy 'lerde), görüntüyü istemci önbelleğinden değil, ana bilgisayar Web sunucusundan yeniden yüklemesi için istemde bulunur.

Görüntü kaynağı (), `src` ana bilgisayar Web sunucusunda bir statik dosya ise:

* Benzersiz bir önbellek-busting dizesi, resim kaynağına bir sorgu parametresi olarak eklenir.
* Ana bilgisayar Web sunucusundaki dosya değişirse, güncelleştirilmiş istek parametresini içeren benzersiz bir istek URL 'SI oluşturulur.

Etiket Yardımcıları hakkında genel bilgi için bkz <xref:mvc/views/tag-helpers/intro> ..

## <a name="image-tag-helper-attributes"></a>Resim etiketi Yardımcısı öznitelikleri

### <a name="src"></a>src

Resim etiketi yardımcısını etkinleştirmek için, `src` öğesinde özniteliği gereklidir `<img>` .

Görüntü kaynağı ( `src` ), sunucudaki bir fiziksel statik dosyayı göstermelidir. , `src` Uzak bır URI ise, önbellek-busting sorgu dizesi parametresi oluşturulmaz.

### <a name="asp-append-version"></a>ASP-Append-sürüm

`asp-append-version` `true` Bir özniteliğiyle birlikte belirtildiğinde `src` , resim etiketi Yardımcısı çağırılır.

Aşağıdaki örnek bir resim etiketi yardımcısını kullanır:

```cshtml
<img src="~/images/asplogo.png" asp-append-version="true">
```

Statik dosya */Wwwroot/images/* dizininde varsa, oluşturulan HTML şuna benzerdir (karma farklı olur):

```html
<img src="/images/asplogo.png?v=Kl_dqr9NVtnMdsM2MUg4qthUnWZm5T1fCEimBPWDNgM">
```

Parametreye atanan değer, `v` diskteki *asplogo.png* dosyanın karma değeridir. Web sunucusu statik dosyaya okuma erişimi alamadığında, `v` `src` işlenen biçimlendirmede özniteliğe hiçbir parametre eklenmez.

## <a name="hash-caching-behavior"></a>Karma önbelleğe alma davranışı

Resim etiketi Yardımcısı, belirli bir dosyanın hesaplanmış karmasını depolamak için yerel Web sunucusundaki önbellek sağlayıcısını kullanır `Sha512` . Dosya birden çok kez isteniyorsa, karma yeniden hesaplanmadı. Önbellek, dosyanın karması hesaplanırken dosyaya iliştirilmiş bir dosya İzleyicisi tarafından geçersiz kılınır `Sha512` . Dosya diskte değiştiğinde yeni bir karma hesaplanır ve önbelleğe alınır.

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:performance/caching/memory>
