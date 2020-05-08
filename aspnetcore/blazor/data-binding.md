---
title: ASP.NET Core Blazor veri bağlama
author: guardrex
description: Blazor Uygulamalardaki BILEŞENLER ve DOM öğeleri için veri bağlama özellikleri hakkında bilgi edinin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/26/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/data-binding
ms.openlocfilehash: b4951c5eb712b15db3a7c1ccd57ae01c530a23ef
ms.sourcegitcommit: 84b46594f57608f6ac4f0570172c7051df507520
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82967174"
---
# <a name="aspnet-core-blazor-data-binding"></a>ASP.NET Core Blazor veri bağlama

, [Luke Latham](https://github.com/guardrex) ve [Daniel Roth](https://github.com/danroth27) tarafından

Razorbileşenler, bir alan, özellik veya [`@bind`](xref:mvc/views/razor#bind) Razor ifade DEĞERI ile adlandırılmış bir HTML öğesi özniteliği aracılığıyla veri bağlama özellikleri sağlar.

Aşağıdaki örnek, `CurrentValue` özelliğini metin kutusu değerine bağlar:

```razor
<input @bind="CurrentValue" />

@code {
    private string CurrentValue { get; set; }
}
```

Metin kutusu odağı kaybettiğinde, özelliğin değeri güncellenir.

Metin kutusu kullanıcı arabiriminde, özelliğin değerini değiştirme yanıt olarak değil, yalnızca bileşen işlendiğinde güncelleştirilir. Bileşenler olay işleyicisi kodu yürütüldükten sonra kendilerini oluşturduğundan, özellik güncelleştirmeleri *genellikle* olay işleyicisi tetiklendikten hemen sonra Kullanıcı arabirimine yansıtılır.

`CurrentValue` Özelliği (`<input @bind="CurrentValue" />`) ile kullanmak `@bind` , temelde aşağıdakilere eşdeğerdir:

```razor
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = 
        __e.Value.ToString())" />
        
@code {
    private string CurrentValue { get; set; }
}
```

Bileşen işlendiğinde, `value` giriş öğesi `CurrentValue` özelliğinden gelir. Kullanıcı metin kutusuna yazdığında ve öğe odağını değiştirdiğinde, `onchange` olay tetiklenir ve `CurrentValue` Özellik değiştirilen değere ayarlanır. Tür dönüştürmelerinde oluşan durumları işletiğinden `@bind` , gerçekte kod oluşturma daha karmaşıktır. İlke ' de `@bind` , bir ifadenin geçerli değerini bir `value` özniteliğiyle ilişkilendirir ve kayıtlı işleyiciyi kullanarak değişiklikleri işler.

`event` Parametre içeren bir `@bind:event` özniteliği de ekleyerek diğer olaylardaki bir özelliği veya alanı bağlayın. Aşağıdaki örnek, `CurrentValue` `oninput` olayında özelliği bağlar:

```razor
<input @bind="CurrentValue" @bind:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

' `onchange`In aksine, öğe odağı kaybettiğinde harekete geçirilir, `oninput` metin kutusunun değeri değiştiğinde harekete geçirilir.

Dışındaki `@bind-{ATTRIBUTE}` `value`öğe `@bind-{ATTRIBUTE}:event` özniteliklerini bağlamak için sözdizimi ile kullanın. Aşağıdaki örnekte, paragrafın stili `paragraphStyle` değer değiştiğinde güncelleştirilir:

```razor
@page "/binding-example"

<p>
    <input type="text" @bind="paragraphStyle" />
</p>

<p @bind-style="paragraphStyle" @bind-style:event="onchange">
    Blazorify the app!
</p>

@code {
    private string paragraphStyle = "color:red";
}
```

Öznitelik bağlama büyük/küçük harfe duyarlıdır. Örneğin, `@bind` geçerlidir ve `@Bind` geçersizdir.

## <a name="unparsable-values"></a>Ayrıştırılamayan değerler

Bir Kullanıcı, bir veri sınırlama öğesine ayrıştırılamayan bir değer sağlıyorsa, bağlama olayı tetiklendiğinde, çözümlenemeyen değer otomatik olarak önceki değerine döndürülür.

Şu senaryoyu göz önünde bulundurun:

* Bir `<input>` öğesi, başlangıç değeri olan `int` bir türe bağlanır `123`:

  ```razor
  <input @bind="MyProperty" />

  @code {
      [Parameter]
      public int MyProperty { get; set; } = 123;
  }
  ```
* Kullanıcı, öğesinin değerini sayfada olarak `123.45` güncelleştirir ve öğe odağını değiştirir.

Önceki senaryoda, öğenin değeri öğesine `123`geri döndürülür. Değeri `123.45` özgün değeri yararına reddedildiğinde `123`, Kullanıcı değerinin kabul edilmediğini anlamıştır.

Varsayılan olarak, bağlama öğenin `onchange` olayına (`@bind="{PROPERTY OR FIELD}"`) uygulanır. Farklı `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` bir olayda bağlamayı tetiklemek için kullanın. `oninput` Event (`@bind:event="oninput"`) için yeniden sürüm, ayrıştırılamayan bir değer sunan herhangi bir tuş vuruşu sonrasında oluşur. `oninput` Olayı, ilişkili bir `int`tür ile hedeflerken, bir kullanıcının bir `.` karakter yazmasının engellenmiş olması engellenir. Bir `.` karakter hemen kaldırılır, bu nedenle Kullanıcı yalnızca tam sayılara izin verilen anında geri bildirim alır. `oninput` Olay üzerindeki değerin geri döndürülmesi ideal değil (örneğin, kullanıcının ayrıştırılamayan `<input>` bir değeri temizlemeye izin verilmesi gerektiği durumlarda). Alternatifler şunlardır:

* `oninput` Olayı kullanmayın. Varsayılan `onchange` olayı kullanın (yalnızca belirtin `@bind="{PROPERTY OR FIELD}"`); burada, öğe odağı kaybetene kadar geçersiz bir değer geri döndürülemez.
* `int?` Veya `string`gibi null yapılabilir bir türe bağlayın ve geçersiz girdileri işlemek için özel mantık sağlayın.
* Veya gibi bir [form doğrulama bileşeni](xref:blazor/forms-validation) `InputDate` `InputNumber` kullanın. Form doğrulama bileşenlerinde geçersiz girişleri yönetmek için yerleşik destek vardır. Form doğrulama bileşenleri:
  * Kullanıcının geçersiz giriş sağlamasına ve ilişkili `EditContext`doğrulama hatalarını almasına izin verin.
  * Kullanıcı ek WebForm verisi girmeye uğramadan doğrulama hatalarını Kullanıcı ARABIRIMINDE görüntüleyin.

## <a name="format-strings"></a>Biçim dizeleri

Veri bağlama, kullanılarak <xref:System.DateTime> [`@bind:format`](xref:mvc/views/razor#bind)biçim dizeleriyle birlikte kullanılabilir. Para birimi veya sayı biçimleri gibi diğer biçim ifadeleri şu anda kullanılamaz.

```razor
<input @bind="StartDate" @bind:format="yyyy-MM-dd" />

@code {
    [Parameter]
    public DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

Yukarıdaki kodda, `<input>` öğesinin alan türü (`type`) varsayılan olarak `text`olur. `@bind:format`, aşağıdaki .NET türlerini bağlamak için desteklenir:

* <xref:System.DateTime?displayProperty=fullName>
* <xref:System.DateTime?displayProperty=fullName>?
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <xref:System.DateTimeOffset?displayProperty=fullName>?

`@bind:format` `value` Özniteliği `<input>` öğesi için uygulanacak tarih biçimini belirtir. Biçim Ayrıca bir `onchange` olay gerçekleştiğinde değeri ayrıştırmak için de kullanılır.

Tarihleri biçimlendirmek için yerleşik destek `date` içerdiğinden Blazor , alan türü için bir biçim belirtmenin kullanılması önerilmez. Önerinin artma içinde, `yyyy-MM-dd` `date` alan türüyle bir biçim sağlanırsa, bağlama için yalnızca tarih biçimini kullanın:

```razor
<input type="date" @bind="StartDate" @bind:format="yyyy-MM-dd">
```

## <a name="parent-to-child-binding-with-component-parameters"></a>Bileşen parametreleriyle üst-alt öğe bağlama

Bağlama, bir üst bileşenden bir `@bind-{PROPERTY}` özellik değerini alt bileşene doğru bir şekilde bağlayabileceği bileşen parametrelerini tanır. Bir alt öğeden üst öğeye bağlama, [zincirleme bağlama Ile alt-üst öğe bağlama](#child-to-parent-binding-with-chained-bind) bölümünde ele alınmıştır.

Aşağıdaki alt bileşende (`ChildComponent`) bir `Year` bileşen parametresi ve `YearChanged` geri çağırması vardır:

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

`EventCallback<T>`, bölümünde <xref:blazor/event-handling#eventcallback>açıklanmıştır.

Aşağıdaki üst bileşen şunları kullanır:

* `ChildComponent`ve `ParentYear` parametresini üst bileşenden alt bileşenin `Year` parametresine bağlar.
* `onclick` Olay, `ChangeTheYear` yöntemi tetiklemek için kullanılır. Daha fazla bilgi için bkz. <xref:blazor/event-handling>.

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

Yüklemesi aşağıdaki `ParentComponent` biçimlendirmeyi üretir:

```html
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

`ParentYear` Özelliğin değeri, `ParentComponent`içindeki düğme seçilerek değiştirilirse, öğesinin `Year` `ChildComponent` özelliği güncellenir. Yeni değeri `Year` , `ParentComponent` yeniden kullanıldığında kullanıcı arabiriminde işlenir:

```html
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

Parametrenin `Year` türüyle `YearChanged` `Year` eşleşen bir yardımcı olayı olduğundan parametre bağlanabilir.

Kurala göre, `<ChildComponent @bind-Year="ParentYear" />` temelde yazmaya eşdeğerdir:

```razor
<ChildComponent @bind-Year="ParentYear" @bind-Year:event="YearChanged" />
```

Genel olarak, bir özellik bir `@bind-{PROPRETY}:event` özniteliği eklenerek ilgili olay işleyicisine bağlanabilir. Örneğin, özelliği `MyProp` aşağıdaki iki öznitelik `MyEventHandler` kullanılarak bağlanabilir:

```razor
<MyComponent @bind-MyProp="MyValue" @bind-MyProp:event="MyEventHandler" />
```

## <a name="child-to-parent-binding-with-chained-bind"></a>Zincirli bağlama ile üstten üst öğe bağlama

Yaygın bir senaryo, bir veri bağlama parametresini bileşen çıkışında bir sayfa öğesine zincirlemesini sağlar. Birden çok bağlama düzeyi aynı anda gerçekleştiğinden, bu senaryoya *zincirleme bağlama* denir.

Bir zincir bağlama, sayfanın öğesinde sözdizimi `@bind` ile uygulanamaz. Olay işleyicisi ve değeri ayrı olarak belirtilmelidir. Ancak, bir üst bileşen, bir sözdizimi `@bind` bileşenin parametresiyle birlikte kullanabilir.

Aşağıdaki `PasswordField` bileşen (*passwordfield. Razor*):

* Bir `<input>` öğenin değerini bir `Password` özelliğe ayarlar.
* `Password` Özellik değişikliklerini bir [eventcallback](xref:blazor/event-handling#eventcallback)ile üst bileşene gösterir.
* , `ToggleShowPassword` Yöntemini `onclick` tetiklemek için kullanılan olayını kullanır. Daha fazla bilgi için bkz. <xref:blazor/event-handling>.

```razor
<h1>Child Component</h1>

Password: 

<input @oninput="OnPasswordChanged" 
       required 
       type="@(showPassword ? "text" : "password")" 
       value="@Password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

@code {
    private bool showPassword;

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
        showPassword = !showPassword;
    }
}
```

`PasswordField` Bileşen başka bir bileşende kullanılır:

```razor
@page "/ParentComponent"

<h1>Parent Component</h1>

<PasswordField @bind-Password="password" />

@code {
    private string password;
}
```

Önceki örnekteki parolada denetim veya tuzak hataları gerçekleştirmek için:

* İçin `Password` bir yedekleme alanı oluşturun (`password` aşağıdaki örnek kodda).
* `Password` Ayarlayıcıdaki denetimleri veya yakalama hatalarını gerçekleştirin.

Aşağıdaki örnek, parolanın değerinde bir boşluk kullanılmışsa kullanıcıya anında geri bildirim sağlar:

```razor
<h1>Child Component</h1>

Password: 

<input @oninput="OnPasswordChanged" 
       required 
       type="@(showPassword ? "text" : "password")" 
       value="@Password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

<span class="text-danger">@validationMessage</span>

@code {
    private bool showPassword;
    private string password;
    private string validationMessage;

    [Parameter]
    public string Password
    {
        get { return password ?? string.Empty; }
        set
        {
            if (password != value)
            {
                if (value.Contains(' '))
                {
                    validationMessage = "Spaces not allowed!";
                }
                else
                {
                    password = value;
                    validationMessage = string.Empty;
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
        showPassword = !showPassword;
    }
}
```

## <a name="radio-buttons"></a>Radyo düğmeleri

Bir form içindeki radyo düğmelerine bağlama hakkında bilgi için bkz <xref:blazor/forms-validation#work-with-radio-buttons>..
