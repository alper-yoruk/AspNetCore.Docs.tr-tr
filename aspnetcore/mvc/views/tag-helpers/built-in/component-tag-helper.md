---
title: ASP.NET Core bileşen etiketi Yardımcısı
author: guardrex
ms.author: riande
description: Sayfalardaki ve görünümlerde bileşenleri işlemek için ASP.NET Core bileşen etiketi Yardımcısı 'nı kullanmayı öğrenin Razor .
ms.custom: mvc
ms.date: 10/29/2020
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
uid: mvc/views/tag-helpers/builtin-th/component-tag-helper
ms.openlocfilehash: 8e780de2367f66ad1f5197077d5243e0b85a41dd
ms.sourcegitcommit: fe5a287fa6b9477b130aa39728f82cdad57611ee
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431049"
---
# <a name="component-tag-helper-in-aspnet-core"></a>ASP.NET Core bileşen etiketi Yardımcısı

[Daniel Roth](https://github.com/danroth27) ve [Luke Latham](https://github.com/guardrex) tarafından

## <a name="prerequisites"></a>Önkoşullar

*Yapılandırma* bölümündeki yönergeleri izleyerek şunlardan birini yapın:

* [Blazor WebAssembly](xref:blazor/components/prerendering-and-integration?pivots=webassembly)
* [Blazor Server](xref:blazor/components/prerendering-and-integration?pivots=server)

## <a name="component-tag-helper"></a>Bileşen etiketi Yardımcısı

Bir sayfadan veya görünümden bir bileşeni işlemek için [bileşen etiketi yardımcısını](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper) ( `<component>` tag) kullanın.

> [!NOTE]
> Razor Razor *Barındırılan bir Blazor WebAssembly UYGULAMADAKI* bileşenleri sayfalar ve MVC uygulamalarıyla tümleştirmek, .net 5,0 veya sonraki sürümlerde ASP.NET Core desteklenir.

<xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> bileşenin şunları yapıp kullanmadığını yapılandırır:

* , Sayfaya ön gönderilir.
* , Sayfada statik HTML olarak veya Kullanıcı aracısından bir uygulamayı önyüklemek için gerekli bilgileri içeriyorsa Blazor .

::: moniker range=">= aspnetcore-5.0"

Blazor WebAssembly uygulama işleme modları aşağıdaki tabloda gösterilmiştir.

| Oluşturma modu | Description |
| ----------- | ----------- |
| `WebAssembly` | Bir Blazor WebAssembly uygulamanın tarayıcıya yüklendiğinde etkileşimli bir bileşeni içermesi için kullanılacak bir işaret oluşturur. Bileşen ön işlenmiş değildir. Bu seçenek farklı sayfalarda farklı bileşenlerin işlenmesine daha kolay hale gelir Blazor WebAssembly . |
| `WebAssemblyPrerendered` | Bileşeni statik HTML 'ye ön ekler ve Blazor WebAssembly daha sonra, tarayıcıya yüklendiğinde bileşeni etkileşimli hale getirmek için bir uygulamanın işaretçisini içerir. |

Blazor Server uygulama işleme modları aşağıdaki tabloda gösterilmiştir.

| Oluşturma modu | Description |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Bileşeni statik HTML olarak işler ve uygulama için bir işaret içerir Blazor Server . Kullanıcı Aracısı başladığında, bu işaretleyici bir uygulamayı önyüklemek için kullanılır Blazor . |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Bir uygulama için işaretleyici işler Blazor Server . Bileşen çıkışı dahil değildir. Kullanıcı Aracısı başladığında, bu işaretleyici bir uygulamayı önyüklemek için kullanılır Blazor . |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Bileşeni statik HTML olarak işler. |

::: moniker-end

::: moniker range="< aspnetcore-5.0"

Blazor Server uygulama işleme modları aşağıdaki tabloda gösterilmiştir.

| Oluşturma modu | Description |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Bileşeni statik HTML olarak işler ve uygulama için bir işaret içerir Blazor Server . Kullanıcı Aracısı başladığında, bu işaretleyici bir uygulamayı önyüklemek için kullanılır Blazor . |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Bir uygulama için işaretleyici işler Blazor Server . Bileşen çıkışı dahil değildir. Kullanıcı Aracısı başladığında, bu işaretleyici bir uygulamayı önyüklemek için kullanılır Blazor . |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Bileşeni statik HTML olarak işler. |

::: moniker-end

Ek özellikler şunlardır:

* Birden çok bileşeni işlemeye yönelik birden çok bileşen etiketi yardımcıya Razor izin veriliyor.
* Uygulama başladıktan sonra bileşenler dinamik olarak işlenemiyor.
* Sayfalar ve görünümler bileşenleri kullanırken, listesiyse doğru değildir. Bileşenler, kısmi görünümler ve bölümler gibi görünüm ve sayfaya özgü özellikleri kullanamaz. Bileşen içindeki kısmi görünümden mantığı kullanmak için kısmi görünüm mantığını bir bileşene ayırın.
* Statik HTML sayfasından sunucu bileşenleri işleme desteklenmiyor.

Aşağıdaki bileşen etiketi Yardımcısı, `Counter` bileşeni bir uygulamada bir sayfada veya görünümünde işler Blazor Server `ServerPrerendered` :

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Pages

...

<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

Yukarıdaki örnekte, `Counter` bileşenin uygulamanın *Sayfalar* klasöründe olduğu varsayılır. Yer tutucu, `{APP ASSEMBLY}` uygulamanın derleme adıdır (örneğin, `@using BlazorSample.Pages` veya `@using BlazorSample.Client.Pages` barındırılan bir Blazor çözümde).

Bileşen etiketi Yardımcısı, parametreleri bileşenlere de geçirebilir. `ColorfulCheckbox`Onay kutusu etiketinin rengini ve boyutunu ayarlayan aşağıdaki bileşeni göz önünde bulundurun:

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

`Size`( `int` ) Ve `Color` ( `string` ) [bileşen parametreleri](xref:blazor/components/index#component-parameters) bileşen etiketi Yardımcısı tarafından ayarlanabilir:

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Shared

...

<component type="typeof(ColorfulCheckbox)" render-mode="ServerPrerendered" 
    param-Size="14" param-Color="@("blue")" />
```

Yukarıdaki örnekte, `ColorfulCheckbox` bileşenin uygulamanın *paylaşılan* klasöründe olduğu varsayılır. Yer tutucu, `{APP ASSEMBLY}` uygulamanın derleme adıdır (örneğin, `@using BlazorSample.Shared` ).

Aşağıdaki HTML sayfada veya görünümde işlenir:

```html
<label style="font-size:24px;color:blue">
    <input id="survey" name="blazor" type="checkbox">
    Enjoying Blazor?
</label>
```

Tırnak içine alınan bir dizeyi geçirmek, yukarıdaki örnekte gösterildiği gibi [açık bir Razor ifade](xref:mvc/views/razor#explicit-razor-expressions)gerektirir `param-Color` . Razor `string` `param-*` Öznitelik bir tür olduğundan, bir tür değeri için ayrıştırma davranışı bir özniteliğe uygulanmaz `object` .

Aşağıdakiler dışında tüm parametre türleri desteklenir:

* Genel parametreler.
* Seri hale getirilebilir olmayan parametreler.
* Koleksiyon parametrelerinde devralma.
* Türü Blazor WebAssembly uygulamanın dışında veya geç tarafından yüklenen bir derlemede tanımlanan parametreler.

Parametre türünün JSON serileştirilebilir olması gerekir, bu, genellikle türün bir varsayılan oluşturucuya ve ayarlanabilir özelliklere sahip olması anlamına gelir. Örneğin, `Size` `Color` ve TÜRLERI, `Size` `Color` `int` `string` JSON seri hale getiricisi tarafından desteklenen temel türler (ve) olduğundan, önceki örnekte ve için bir değer belirtebilirsiniz.

Aşağıdaki örnekte, bileşene bir sınıf nesnesi geçirilir:

*MyClass.cs* :

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

**Sınıf ortak parametresiz bir oluşturucuya sahip olmalıdır.**

*Shared/MyComponent. Razor* :

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

*Pages/MyPage. cshtml* :

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

Yukarıdaki örnekte, `MyComponent` bileşenin uygulamanın *paylaşılan* klasöründe olduğu varsayılır. Yer tutucu, `{APP ASSEMBLY}` uygulamanın derleme adıdır (örneğin, `@using BlazorSample` ve `@using BlazorSample.Shared` ). `MyClass` , uygulamanın ad alanıdır.

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper>
* <xref:mvc/views/tag-helpers/intro>
* <xref:blazor/components/index>
