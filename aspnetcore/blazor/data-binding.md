---
title: ASP.NET Blazor Çekirdek veri bağlama
author: guardrex
description: Uygulamalardaki Blazor bileşenler ve DOM öğeleri için veri bağlama özellikleri hakkında bilgi edinin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/26/2020
no-loc:
- Blazor
- SignalR
uid: blazor/data-binding
ms.openlocfilehash: a7b3730dad48b5bbb6134dab181051da4e3651b4
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80320958"
---
# <a name="aspnet-core-opno-locblazor-data-binding"></a>ASP.NET Blazor Çekirdek veri bağlama

Yazar: [Luke Latham](https://github.com/guardrex) ve [Daniel Roth](https://github.com/danroth27)

Razor bileşenleri, alan, özellik veya Jilet ifade değeriyle birlikte adlı [`@bind`](xref:mvc/views/razor#bind) bir HTML öğesi özniteliği aracılığıyla veri bağlama özellikleri sağlar.

Aşağıdaki örnek özelliği metin `CurrentValue` kutusunun değerine bağlar:

```razor
<input @bind="CurrentValue" />

@code {
    private string CurrentValue { get; set; }
}
```

Metin kutusu odağı kaybettiğinde, özelliğin değeri güncelleştirilir.

Metin kutusu, yalnızca bileşen işlendiğinde, özelliğin değerini değiştirmeye yanıt olarak değil, Kullanıcı Arası Bilgi Birimi'nde güncelleştirilir. Bileşenler olay işleyicisi kodu yürütülmeden sonra kendilerini işlediğinden, özellik güncelleştirmeleri *genellikle* bir olay işleyicisi tetiklendikten hemen sonra UI'ye yansıtılır.

Özellik ile birlikte kullanmak `@bind` aslında aşağıdakilere eşdeğerdir:`<input @bind="CurrentValue" />` `CurrentValue`

```razor
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = 
        __e.Value.ToString())" />
        
@code {
    private string CurrentValue { get; set; }
}
```

Bileşen işlendiğinde, giriş `value` öğesinin `CurrentValue` özelliği nden gelir. Kullanıcı metin kutusunda ki öğe odağında yazıp `onchange` değiştirdiğinde, `CurrentValue` olay ateşlenir ve özellik değiştirilen değere ayarlanır. Gerçekte, tür dönüşümlerinin gerçekleştirildiği `@bind` durumları işlediği için kod oluşturma daha karmaşıktır. Prensip `@bind` olarak, bir ifadenin geçerli `value` değerini bir öznitelikle ilişkilendirer ve değişiklikleri kayıtlı işleyiciyi kullanarak işler.

`event` Parametreli bir `@bind:event` öznitelik de ekleyerek bir özelliği veya alanı diğer olaylara bağla. Aşağıdaki örnek `CurrentValue` `oninput` olay özelliği bağlar:

```razor
<input @bind="CurrentValue" @bind:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

Öğe `onchange`odağı kaybettiğinde ateşlenen, `oninput` metin kutusunun değeri değiştiğinde çıkanın aksine.

Öğe `@bind-{ATTRIBUTE}` `@bind-{ATTRIBUTE}:event` özniteliklerini başka bir öğe `value`öznitelikleribağlamak için sözdizimi ile kullanın. Aşağıdaki örnekte, `_paragraphStyle` değer değiştiğinde paragrafın stili güncelleştirilir:

```razor
@page "/binding-example"

<p>
    <input type="text" @bind="_paragraphStyle" />
</p>

<p @bind-style="_paragraphStyle" @bind-style:event="onchange">
    Blazorify the app!
</p>

@code {
    private string _paragraphStyle = "color:red";
}
```

Öznitelik bağlama büyük/küçük harf duyarlıdır. Örneğin, `@bind` geçerli dir `@Bind` ve geçersizdir.

## <a name="unparsable-values"></a>Parlanamaz değerler

Bir kullanıcı veri yle dolu bir öğeye ayrı ayrı değer verdiğinde, bağlama olayı tetiklendiğinde, ayrıştırılamaz değer otomatik olarak önceki değerine geri döndürülür.

Şu senaryoyu göz önünde bulundurun:

* Bir `<input>` öğe, başlangıç `int` değeri olan bir `123`türe bağlıdır:

  ```razor
  <input @bind="MyProperty" />

  @code {
      [Parameter]
      public int MyProperty { get; set; } = 123;
  }
  ```
* Kullanıcı öğenin değerini `123.45` sayfaya güncelleştirir ve öğe odağı değişir.

Önceki senaryoda, öğenin değeri `123`. Değer `123.45` orijinal değeri lehine `123`reddedildiğinde, kullanıcı değerinin kabul olmadığını anlar.

Varsayılan olarak, bağlama öğenin `onchange` olay (`@bind="{PROPERTY OR FIELD}"`için geçerlidir. Farklı `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` bir olay üzerinde bağlamayı tetiklemek için kullanın. `oninput` Olay için`@bind:event="oninput"`( ), reversion ayrılmaz bir değer tanıtan herhangi bir tuş vuruşundan sonra oluşur. Olayı bağlı `oninput` bir `int`türle hedeflenirken, kullanıcının karakter `.` yazması engellenir. Bir `.` karakter hemen kaldırılır, böylece kullanıcı yalnızca tam sayılara izin verilen anında geri bildirim alır. `oninput` Kullanıcının ayrı ayrı bir `<input>` değeri temizlemesine izin verilmesi gerektiği gibi, olaydaki değeri geri almanın ideal olmadığı senaryolar vardır. Alternatifler şunlardır:

* `oninput` Olayı kullanma. Öğe odağı `onchange` kaybedene `@bind="{PROPERTY OR FIELD}"`kadar geçersiz bir değerin geri döndürülmediği varsayılan olayı (yalnızca belirtin) kullanın.
* Geçersiz girişleri işlemek için özel `int?` mantık `string`sağlayın, örneğin, geçersiz bir türe bağlayın.
* Bir [form doğrulama bileşeni](xref:blazor/forms-validation)kullanın `InputNumber` `InputDate`, gibi veya . Form doğrulama bileşenleri, geçersiz girişleri yönetmek için yerleşik desteğe sahiptir. Form doğrulama bileşenleri:
  * Kullanıcının geçersiz giriş sağlamasına ve ilişkili `EditContext`üzerinde doğrulama hataları almasına izin ver.
  * Kullanıcının ek web form verileri girmesini engellemeden Kullanıcı Arama Bilgisi'ndeki doğrulama hatalarını görüntüleyin.

## <a name="format-strings"></a>Dizeleri biçimlendirme

Veri bağlama <xref:System.DateTime> kullanarak [`@bind:format`](xref:mvc/views/razor#bind)biçim dizeleri ile çalışır. Para birimi veya sayı biçimleri gibi diğer biçim ifadeleri şu anda kullanılamaz.

```razor
<input @bind="StartDate" @bind:format="yyyy-MM-dd" />

@code {
    [Parameter]
    public DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

Önceki kodda, öğenin `<input>` alan türü`type`( ) `text`varsayılan olarak . `@bind:format`aşağıdaki .NET türlerinin bağlanması için desteklenir:

* <xref:System.DateTime?displayProperty=fullName>
* <xref:System.DateTime?displayProperty=fullName>?
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <xref:System.DateTimeOffset?displayProperty=fullName>?

Öznitelik, `@bind:format` `value` `<input>` öğenin uygulanması için tarih biçimini belirtir. Biçim, bir `onchange` olay oluştuğunda değeri ayrıştırmak için de kullanılır.

Tarihleri biçimlendirmek `date` için yerleşik desteği Blazor olduğundan alan türü için bir biçim belirtmek önerilmez. Öneriye rağmen, `yyyy-MM-dd` `date` alan türüyle birlikte bir biçim sağlanıyorsa, yalnızca doğru çalışması için tarih biçimini kullanın:

```razor
<input type="date" @bind="StartDate" @bind:format="yyyy-MM-dd">
```

## <a name="parent-to-child-binding-with-component-parameters"></a>Bileşen parametreleri ile üst-alt bağlama

Bağlama, bir üst `@bind-{PROPERTY}` bileşenden alt bileşene bir özellik değeri bağlayabilirsiniz bileşen parametrelerini tanır. Bir çocuktan bir ebeveyne bağlanma, [zincirli bağlama bölümüyle çocuktan ebeveyne bağlama](#child-to-parent-binding-with-chained-bind) kapsamındadır.

Aşağıdaki alt bileşen`ChildComponent`( `Year` ) bir `YearChanged` bileşen parametresi ve geri arama vardır:

```razor
<h2>Child Component</h2>

<p>Year: @Year</p>

@code {
    [Parameter]
    public int Year { get; set; }

    [Parameter]
    public EventCallback<int> YearChanged { get; set; }
}
```

`EventCallback<T>`olarak <xref:blazor/event-handling#eventcallback>açıklanmıştır.

Aşağıdaki üst bileşen kullanır:

* `ChildComponent`ve parametreyi `ParentYear` alt bileşendeki `Year` üst öğeden parametreye bağlar.
* Olay `onclick` `ChangeTheYear` yöntemi tetiklemek için kullanılır. Daha fazla bilgi için bkz. <xref:blazor/event-handling>.

```razor
@page "/ParentComponent"

<h1>Parent Component</h1>

<p>ParentYear: @ParentYear</p>

<ChildComponent @bind-Year="ParentYear" />

<button class="btn btn-primary" @onclick="ChangeTheYear">
    Change Year to 1986
</button>

@code {
    [Parameter]
    public int ParentYear { get; set; } = 1978;

    private void ChangeTheYear()
    {
        ParentYear = 1986;
    }
}
```

Yükleme `ParentComponent` aşağıdaki biçimlendirme üretir:

```html
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

Özelliğin `ParentYear` değeri `ParentComponent`değiştirilirse, düğme seçilerek , `Year` özelliği `ChildComponent` güncelleştirilir. Yeni değer, `Year` yeniden işlendiğinde UI'de `ParentComponent` işlenir:

```html
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

`Year` Parametre, `Year` parametrenin türüyle eşleşen bir eşlik `YearChanged` eden olayı olduğundan bağlanabilir.

Sözleşmeye `<ChildComponent @bind-Year="ParentYear" />` göre, temelde yazmaya eşdeğerdir:

```razor
<ChildComponent @bind-Year="ParentYear" @bind-Year:event="YearChanged" />
```

Genel olarak, bir özellik bir `@bind-{PROPRETY}:event` öznitelik ekleyerek ilgili olay işleyicisi bağlanabilir. Örneğin, özellik `MyProp` aşağıdaki iki `MyEventHandler` öznitelikleri kullanarak bağlı olabilir:

```razor
<MyComponent @bind-MyProp="MyValue" @bind-MyProp:event="MyEventHandler" />
```

## <a name="child-to-parent-binding-with-chained-bind"></a>Zincirli bağlama ile alt-üst teneye bağlama

Yaygın bir senaryo, bileşenin çıktısındaki bir sayfa öğesine veriye bağlı bir parametreyi zincirlemektir. Birden çok bağlama düzeyi aynı anda oluştuğundan, bu senaryoya *zincirleme bağlama* denir.

Zincirleme bağlama, sayfanın öğesindeki `@bind` sözdizimiyle uygulaılamaz. Olay işleyicisi ve değeri ayrı olarak belirtilmelidir. Ancak bir üst bileşen, `@bind` bileşenin parametresi ile sözdizimini kullanabilir.

Aşağıdaki `PasswordField` bileşen (*PasswordField.razor):*

* Bir `<input>` öğeiçin `Password` öğenin değerini ayarlar.
* Özellik değişikliklerini `Password` [EventCallback](xref:blazor/event-handling#eventcallback)ile bir üst bileşene maruz bırakır.
* Yöntemi `onclick` tetiklemek için kullanılan olayı kullanır. `ToggleShowPassword` Daha fazla bilgi için bkz. <xref:blazor/event-handling>.

```razor
<h1>Child Component</h1>

Password: 

<input @oninput="OnPasswordChanged" 
       required 
       type="@(_showPassword ? "text" : "password")" 
       value="@Password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

@code {
    private bool _showPassword;

    [Parameter]
    public string Password { get; set; }

    [Parameter]
    public EventCallback<string> PasswordChanged { get; set; }

    private Task OnPasswordChanged(ChangeEventArgs e)
    {
        Password = e.Value.ToString();

        return PasswordChanged.InvokeAsync(Password);
    }

    private void ToggleShowPassword()
    {
        _showPassword = !_showPassword;
    }
}
```

Bileşen `PasswordField` başka bir bileşende kullanılır:

```razor
@page "/ParentComponent"

<h1>Parent Component</h1>

<PasswordField @bind-Password="_password" />

@code {
    private string _password;
}
```

Önceki örnekte parola üzerinde denetimler veya bindirme hataları gerçekleştirmek için:

* (Aşağıdaki`_password` örnek `Password` kodda) için bir destek alanı oluşturun.
* Ayarlayıcıdaki denetimleri veya `Password` bindirme hatalarını gerçekleştirin.

Aşağıdaki örnek, parolanın değerinde bir alan kullanılırsa kullanıcıya anında geri bildirim sağlar:

```razor
<h1>Child Component</h1>

Password: 

<input @oninput="OnPasswordChanged" 
       required 
       type="@(_showPassword ? "text" : "password")" 
       value="@Password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

<span class="text-danger">@_validationMessage</span>

@code {
    private bool _showPassword;
    private string _password;
    private string _validationMessage;

    [Parameter]
    public string Password
    {
        get { return _password ?? string.Empty; }
        set
        {
            if (_password != value)
            {
                if (value.Contains(' '))
                {
                    _validationMessage = "Spaces not allowed!";
                }
                else
                {
                    _password = value;
                    _validationMessage = string.Empty;
                }
            }
        }
    }

    [Parameter]
    public EventCallback<string> PasswordChanged { get; set; }

    private Task OnPasswordChanged(ChangeEventArgs e)
    {
        Password = e.Value.ToString();

        return PasswordChanged.InvokeAsync(Password);
    }

    private void ToggleShowPassword()
    {
        _showPassword = !_showPassword;
    }
}
```

## <a name="radio-buttons"></a>Radyo düğmeleri

Bir formdaki radyo düğmelerine bağlama hakkında <xref:blazor/forms-validation#work-with-radio-buttons>bilgi için bkz.
