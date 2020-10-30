---
title: ASP.NET Core 'de ortam etiketi Yardımcısı
author: pkellner
description: Tüm özellikler dahil ASP.NET Core ortam etiketi Yardımcısı tanımlandı
ms.author: riande
ms.custom: mvc
ms.date: 10/10/2018
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
uid: mvc/views/tag-helpers/builtin-th/environment-tag-helper
ms.openlocfilehash: 4f8330521bb9114f6639c1889cc15ebd18adc0ed
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061138"
---
# <a name="environment-tag-helper-in-aspnet-core"></a>ASP.NET Core 'de ortam etiketi Yardımcısı

, [Peter Kellner](https://peterkellner.net) ve [Hisham bin ateya](https://twitter.com/hishambinateya) tarafından

Ortam etiketi Yardımcısı, Şirket içindeki içeriğini koşullu olarak geçerli [barındırma ortamına](xref:fundamentals/environments)göre oluşturur. Ortam etiketi Yardımcısı 'nın tek özniteliği, `names` ortam adlarının virgülle ayrılmış listesidir. Belirtilen ortam adlarından herhangi biri geçerli ortamla eşleşiyorsa, eklenen içerik işlenir.

Etiket Yardımcıları hakkında genel bilgi için bkz <xref:mvc/views/tag-helpers/intro> ..

## <a name="environment-tag-helper-attributes"></a>Ortam etiketi yardımcı öznitelikleri

### <a name="names"></a>adlar

`names` tek bir barındırma ortamı adını veya ekteki içeriğin işlenmesini tetikleyen barındırma ortamı adlarının virgülle ayrılmış bir listesini kabul eder.

Ortam değerleri [ıhostingenvironment. EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName*)tarafından döndürülen geçerli değerle karşılaştırılır. Karşılaştırma büyük/küçük harf durumunu yoksayar.

Aşağıdaki örnek bir ortam etiketi Yardımcısı kullanır. İçerik, barındırma ortamı hazırlama veya üretim ise işlenir:

```cshtml
<environment names="Staging,Production">
    <strong>HostingEnvironment.EnvironmentName is Staging or Production</strong>
</environment>
```

::: moniker range=">= aspnetcore-2.0"

## <a name="include-and-exclude-attributes"></a>öznitelikleri dahil etme ve hariç tutma

`include`& `exclude` dahil edilen veya dışlanan barındırma ortamı adlarına göre, eklenen içeriği işleyen öznitelik denetimi.

### <a name="include"></a>include

`include`Özelliği özniteliğe benzer bir davranış sergiler `names` . Öznitelik değerinde listelenen bir ortam, `include` etiketin içeriğini işlemek için uygulamanın barındırma ortamıyla ([ıhostingenvironment. EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName*)) eşleşmelidir `<environment>` .

```cshtml
<environment include="Staging,Production">
    <strong>HostingEnvironment.EnvironmentName is Staging or Production</strong>
</environment>
```

### <a name="exclude"></a>dışlama

`include`Özniteliğinin aksine,, `<environment>` barındırma ortamı öznitelik değerinde listelenen bir ortamla eşleşmediği zaman etiketin içeriği işlenir `exclude` .

```cshtml
<environment exclude="Development">
    <strong>HostingEnvironment.EnvironmentName is not Development</strong>
</environment>
```

::: moniker-end

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:fundamentals/environments>
