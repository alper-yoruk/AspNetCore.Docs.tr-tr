---
title: ASP.NET Core'da Bileşen Etiket Yardımcısı
author: guardrex
ms.author: riande
description: Razor bileşenlerini sayfalarda ve görünümlerde işlemek için ASP.NET Temel Bileşen Tag Helper'ı nasıl kullanacağınızı öğrenin.
ms.custom: mvc
ms.date: 04/01/2020
no-loc:
- Blazor
- SignalR
uid: mvc/views/tag-helpers/builtin-th/component-tag-helper
ms.openlocfilehash: 4a6b21229ce086099fcddfeb51c3a959ef639f24
ms.sourcegitcommit: e8dc30453af8bbefcb61857987090d79230a461d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/11/2020
ms.locfileid: "81123425"
---
# <a name="component-tag-helper-in-aspnet-core"></a>ASP.NET Core'da Bileşen Etiket Yardımcısı

Yazar: [Daniel Roth](https://github.com/danroth27) ve [Luke Latham](https://github.com/guardrex)

Bir bileşeni bir sayfa veya görünümden işlemek için [Bileşen Etiket Yardımcısı'nı](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper)kullanın.

## <a name="prerequisites"></a>Ön koşullar

<xref:blazor/integrate-components#prepare-the-app-to-use-components-in-pages-and-views> Makalenin sayfalar *ve görünümler bölümündeki bileşenleri kullanmak için uygulamayı Hazırla'daki* kılavuzu izleyin.

## <a name="component-tag-helper"></a>Bileşen Etiket Yardımcısı

Aşağıdaki Bileşen Tag Yardımcısı `Counter` bileşeni bir sayfada veya görünümde işler:

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Pages

...

<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

Önceki örnek, bileşenin `Counter` uygulamanın *Sayfalar* klasöründe olduğunu varsayar.

Bileşen Tag Yardımcısı da bileşenleri parametreleri geçirebilirsiniz. Onay kutusu `ColorfulCheckbox` etiketinin rengini ve boyutunu ayarlayan aşağıdaki bileşeni göz önünde bulundurun:

```razor
<label style="font-size:@(Size)px;color:@Color">
    <input @bind="Value"
           id="survey" 
           name="blazor" 
           type="checkbox" />
    Enjoying Blazor?
</label>

@code {
    [Parameter]
    public bool Value { get; set; }

    [Parameter]
    public int Size { get; set; } = 8;

    [Parameter]
    public string Color { get; set; }

    protected override void OnInitialized()
    {
        Size += 10;
    }
}
```

`Size` (`int`) `Color` ve`string`( ) [bileşen parametreleri](xref:blazor/components#component-parameters) Bileşen Tag Yardımcısı tarafından ayarlanabilir:

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Shared

...

<component type="typeof(ColorfulCheckbox)" render-mode="ServerPrerendered" 
    param-Size="14" param-Color="@("blue")" />
```

Önceki örnek, bileşenin `ColorfulCheckbox` uygulamanın *Paylaşılan* klasöründe olduğunu varsayar.

Aşağıdaki HTML sayfada veya görünümde işlenir:

```html
<label style="font-size:24px;color:blue">
    <input id="survey" name="blazor" type="checkbox">
    Enjoying Blazor?
</label>
```

Teklif edilen bir dize yi geçmek, `param-Color` önceki örnekte gösterildiği gibi açık bir [Razor ifadesi](xref:mvc/views/razor#explicit-razor-expressions)gerektirir. Tür değeri için `string` Razor ayrıştırma davranışı öznitelik `param-*` bir `object` tür olduğundan öznitelik için geçerli değildir.

Parametre türü JSON serileştirilebilir olmalıdır, bu da genellikle türün varsayılan bir oluşturucu ve ayarlanabilir özelliklere sahip olması gerektiği anlamına gelir. Örneğin, json serileştiricisi `Color` tarafından desteklenen ilkel türleri `Size` (ve) `Color` `int` `string`türleri olduğundan, önceki örnekte bir değer `Size` belirtebilirsiniz.

Aşağıdaki örnekte, bir sınıf nesnesi bileşene aktarılır:

*MyClass.cs*:

```csharp
public class MyClass
{
    public MyClass()
    {
    }

    public int MyInt { get; set; } = 999;
    public string MyString { get; set; } = "Initial value";
}
```

**Sınıfın ortak parametresiz bir oluşturucusu olmalıdır.**

*Paylaşılan/MyComponent.razor*:

```razor
<h2>MyComponent</h2>

<p>Int: @MyObject.MyInt</p>
<p>String: @MyObject.MyString</p>

@code
{
    [Parameter]
    public MyClass MyObject { get; set; }
}
```

*Sayfalar/MyPage.cshtml*:

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}
@using {APP ASSEMBLY}.Shared

...

@{
    var myObject = new MyClass();
    myObject.MyInt = 7;
    myObject.MyString = "Set by MyPage";
}

<component type="typeof(MyComponent)" render-mode="ServerPrerendered" 
    param-MyObject="@myObject" />
```

Önceki örnek, bileşenin `MyComponent` uygulamanın *Paylaşılan* klasöründe olduğunu varsayar. `MyClass`uygulamanın ad alanındadır (`{APP ASSEMBLY}`).

<xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>bileşenin:

* Sayfaya önceden işlenir.
* Sayfada statik HTML olarak işlenir veya kullanıcı aracısından bir Blazor uygulamasını önyükleme için gerekli bilgileri içeriyorsa.

| Render Modu | Açıklama |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Bileşeni statik HTML'ye dönüştürür ve sunucu Blazor uygulaması için bir işaretçi içerir. Kullanıcı aracısı başlatıldığında, bu işaretçi bir Blazor uygulamayı önyükleme için kullanılır. |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Blazor Sunucu uygulaması için işaretleyici işler. Bileşenden gelen çıktı dahil değildir. Kullanıcı aracısı başlatıldığında, bu işaretçi bir Blazor uygulamayı önyükleme için kullanılır. |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Bileşeni statik HTML'ye dönüştürür. |

Sayfalar ve görünümler bileşenleri kullanabilse de, tam tersi doğru değildir. Bileşenler, kısmi görünümler ve bölümler gibi görünüm ve sayfaya özgü özellikleri kullanamaz. Bileşendeki kısmi görünümden mantığı kullanmak için, kısmi görünüm mantığını bileşene dönüştürün.

Sunucu bileşenlerinin statik bir HTML sayfasından görüntülenmeleri desteklenmez.

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper>
* <xref:mvc/views/tag-helpers/intro>
* <xref:blazor/components>
