---
title: ASP.NET Core Blazor veri bağlama
author: guardrex
description: Uygulamalardaki bileşenler ve DOM öğeleri için veri bağlama özellikleri hakkında bilgi edinin Blazor .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/26/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/components/data-binding
ms.openlocfilehash: 35873e57171b4d86affcb475ad2d55aef443d3b5
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85399185"
---
# <a name="aspnet-core-blazor-data-binding"></a>ASP.NET Core Blazor veri bağlama

, [Luke Latham](https://github.com/guardrex) ve [Daniel Roth](https://github.com/danroth27) tarafından

Razorbileşenler, [`@bind`](xref:mvc/views/razor#bind) bir alan, özellik veya ifade değeri ile adlandırılmış BIR HTML öğesi özniteliği aracılığıyla veri bağlama özellikleri sağlar Razor .

Aşağıdaki örnek, `CurrentValue` özelliğini metin kutusu değerine bağlar:

```razor
<input @bind="CurrentValue" />

@code {
    private string CurrentValue { get; set; }
}
```

Metin kutusu odağı kaybettiğinde, özelliğin değeri güncellenir.

Metin kutusu kullanıcı arabiriminde, özelliğin değerini değiştirme yanıt olarak değil, yalnızca bileşen işlendiğinde güncelleştirilir. Bileşenler olay işleyicisi kodu yürütüldükten sonra kendilerini oluşturduğundan, özellik güncelleştirmeleri *genellikle* olay işleyicisi tetiklendikten hemen sonra Kullanıcı arabirimine yansıtılır.

[`@bind`](xref:mvc/views/razor#bind) `CurrentValue` Özelliği () ile kullanmak, `<input @bind="CurrentValue" />` temelde aşağıdakilere eşdeğerdir:

```razor
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = 
        __e.Value.ToString())" />
        
@code {
    private string CurrentValue { get; set; }
}
```

Bileşen işlendiğinde, `value` giriş öğesi `CurrentValue` özelliğinden gelir. Kullanıcı metin kutusuna yazdığında ve öğe odağını değiştirdiğinde, `onchange` olay tetiklenir ve `CurrentValue` özellik değiştirilen değere ayarlanır. [`@bind`](xref:mvc/views/razor#bind)Tür dönüştürmelerinde oluşan durumları işletiğinden, gerçekte kod oluşturma daha karmaşıktır. İlke ' de, [`@bind`](xref:mvc/views/razor#bind) bir ifadenin geçerli değerini bir `value` özniteliğiyle ilişkilendirir ve kayıtlı işleyiciyi kullanarak değişiklikleri işler.

Parametre içeren bir özniteliği de ekleyerek diğer olaylardaki bir özelliği veya alanı bağlayın `@bind:event` `event` . Aşağıdaki örnek, `CurrentValue` olayında özelliği bağlar `oninput` :

```razor
<input @bind="CurrentValue" @bind:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

`onchange`' In aksine, öğe odağı kaybettiğinde harekete geçirilir, `oninput` metin kutusunun değeri değiştiğinde harekete geçirilir.

`@bind-{ATTRIBUTE}` `@bind-{ATTRIBUTE}:event` Dışındaki öğe özniteliklerini bağlamak için sözdizimi ile kullanın `value` . Aşağıdaki örnekte, paragrafın stili `paragraphStyle` değer değiştiğinde güncelleştirilir:

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

Öznitelik bağlama büyük/küçük harfe duyarlıdır:

* `@bind`geçerli.
* `@Bind`ve `@BIND` geçersiz.

## <a name="unparsable-values"></a>Ayrıştırılamayan değerler

Bir Kullanıcı, bir veri sınırlama öğesine ayrıştırılamayan bir değer sağlıyorsa, bağlama olayı tetiklendiğinde, çözümlenemeyen değer otomatik olarak önceki değerine döndürülür.

Şu senaryoyu göz önünde bulundurun:

* Bir `<input>` öğesi, `int` Başlangıç değeri olan bir türe bağlanır `123` :

  ```razor
  <input @bind="MyProperty" />

  @code {
      [Parameter]
      public int MyProperty { get; set; } = 123;
  }
  ```
* Kullanıcı, öğesinin değerini sayfada olarak güncelleştirir `123.45` ve öğe odağını değiştirir.

Önceki senaryoda, öğenin değeri öğesine geri döndürülür `123` . Değeri `123.45` özgün değeri yararına reddedildiğinde `123` , Kullanıcı değerinin kabul edilmediğini anlamıştır.

Varsayılan olarak, bağlama öğenin `onchange` olayına ( `@bind="{PROPERTY OR FIELD}"` ) uygulanır. `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}`Farklı bir olayda bağlamayı tetiklemek için kullanın. `oninput`Event () için `@bind:event="oninput"` yeniden sürüm, ayrıştırılamayan bir değer sunan herhangi bir tuş vuruşu sonrasında oluşur. `oninput`Olayı, ilişkili bir tür ile hedeflerken `int` , bir kullanıcının bir karakter yazmasının engellenmiş olması engellenir `.` . Bir `.` karakter hemen kaldırılır, bu nedenle Kullanıcı yalnızca tam sayılara izin verilen anında geri bildirim alır. Olay üzerindeki değerin geri döndürülmesi `oninput` ideal değil (örneğin, kullanıcının ayrıştırılamayan bir değeri temizlemeye izin verilmesi gerektiği durumlarda) `<input>` . Alternatifler şunlardır:

* `oninput`Olayı kullanmayın. Varsayılan olayı kullanın `onchange` (yalnızca belirtin `@bind="{PROPERTY OR FIELD}"` ); burada, öğe odağı kaybetene kadar geçersiz bir değer geri döndürülemez.
* Veya gibi null yapılabilir bir türe bağlayın `int?` `string` ve geçersiz girdileri işlemek için özel mantık sağlayın.
* Veya gibi bir [form doğrulama bileşeni](xref:blazor/forms-validation)kullanın <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> . Form doğrulama bileşenlerinde geçersiz girişleri yönetmek için yerleşik destek vardır. Form doğrulama bileşenleri:
  * Kullanıcının geçersiz giriş sağlamasına ve ilişkili doğrulama hatalarını almasına izin verin <xref:Microsoft.AspNetCore.Components.Forms.EditContext> .
  * Kullanıcı ek WebForm verisi girmeye uğramadan doğrulama hatalarını Kullanıcı ARABIRIMINDE görüntüleyin.

## <a name="format-strings"></a>Biçim dizeleri

Veri bağlama, <xref:System.DateTime> kullanılarak biçim dizeleriyle birlikte kullanılabilir `@bind:format` . Para birimi veya sayı biçimleri gibi diğer biçim ifadeleri şu anda kullanılamaz.

```razor
<input @bind="StartDate" @bind:format="yyyy-MM-dd" />

@code {
    [Parameter]
    public DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

Yukarıdaki kodda, `<input>` öğesinin alan türü ( `type` ) varsayılan olarak olur `text` . `@bind:format`, aşağıdaki .NET türlerini bağlamak için desteklenir:

* <xref:System.DateTime?displayProperty=fullName>
* <xref:System.DateTime?displayProperty=fullName>?
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <xref:System.DateTimeOffset?displayProperty=fullName>?

`@bind:format`Özniteliği öğesi için uygulanacak tarih biçimini belirtir `value` `<input>` . Biçim Ayrıca bir olay gerçekleştiğinde değeri ayrıştırmak için de kullanılır `onchange` .

`date` Blazor Tarihleri biçimlendirmek için yerleşik destek içerdiğinden, alan türü için bir biçim belirtmenin kullanılması önerilmez. Önerinin artma içinde, `yyyy-MM-dd` alan türüyle bir biçim sağlanırsa, bağlama için yalnızca tarih biçimini kullanın `date` :

```razor
<input type="date" @bind="StartDate" @bind:format="yyyy-MM-dd">
```

## <a name="parent-to-child-binding-with-component-parameters"></a>Bileşen parametreleriyle üst-alt öğe bağlama

Bağlama, bir `@bind-{PROPERTY}` üst bileşenden bir özellik değerini alt bileşene doğru bir şekilde bağlayabileceği bileşen parametrelerini tanır. Bir alt öğeden üst öğeye bağlama, [zincirleme bağlama Ile alt-üst öğe bağlama](#child-to-parent-binding-with-chained-bind) bölümünde ele alınmıştır.

Aşağıdaki alt bileşende ( `ChildComponent` ) bir `Year` bileşen parametresi ve `YearChanged` geri çağırması vardır:

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

<xref:Microsoft.AspNetCore.Components.EventCallback%601>, bölümünde açıklanmıştır <xref:blazor/components/event-handling#eventcallback> .

Aşağıdaki üst bileşen şunları kullanır:

* `ChildComponent`ve `ParentYear` parametresini üst `Year` bileşenden alt bileşenin parametresine bağlar.
* `onclick`Olay, yöntemi tetiklemek için kullanılır `ChangeTheYear` . Daha fazla bilgi için bkz. <xref:blazor/components/event-handling>.

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

Yüklemesi `ParentComponent` aşağıdaki biçimlendirmeyi üretir:

```html
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

`ParentYear`Özelliğin değeri, içindeki düğme seçilerek değiştirilirse, `ParentComponent` `Year` öğesinin özelliği `ChildComponent` güncellenir. Yeni değeri, `Year` yeniden kullanıldığında kullanıcı arabiriminde işlenir `ParentComponent` :

```html
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

Parametrenin `Year` türüyle eşleşen bir yardımcı olayı olduğundan parametre bağlanabilir `YearChanged` `Year` .

Kurala göre, `<ChildComponent @bind-Year="ParentYear" />` temelde yazmaya eşdeğerdir:

```razor
<ChildComponent @bind-Year="ParentYear" @bind-Year:event="YearChanged" />
```

Genel olarak, bir özellik bir özniteliği eklenerek ilgili olay işleyicisine bağlanabilir `@bind-{PROPRETY}:event` . Örneğin, özelliği `MyProp` `MyEventHandler` aşağıdaki iki öznitelik kullanılarak bağlanabilir:

```razor
<MyComponent @bind-MyProp="MyValue" @bind-MyProp:event="MyEventHandler" />
```

## <a name="child-to-parent-binding-with-chained-bind"></a>Zincirli bağlama ile üstten üst öğe bağlama

Yaygın bir senaryo, bir veri bağlama parametresini bileşen çıkışında bir sayfa öğesine zincirlemesini sağlar. Birden çok bağlama düzeyi aynı anda gerçekleştiğinden, bu senaryoya *zincirleme bağlama* denir.

Bir zincir bağlama [`@bind`](xref:mvc/views/razor#bind) , sayfanın öğesinde sözdizimi ile uygulanamaz. Olay işleyicisi ve değeri ayrı olarak belirtilmelidir. Ancak, bir üst bileşen, bir [`@bind`](xref:mvc/views/razor#bind) sözdizimi bileşenin parametresiyle birlikte kullanabilir.

Aşağıdaki `PasswordField` bileşen ( `PasswordField.razor` ):

* Bir `<input>` öğenin değerini bir `Password` özelliğe ayarlar.
* `Password`Özelliği ile bir üst bileşen arasındaki değişiklikleri gösterir [`EventCallback`](xref:blazor/components/event-handling#eventcallback) .
* `onclick`Yöntemini tetiklemek için olayını kullanır `ToggleShowPassword` . Daha fazla bilgi için bkz. <xref:blazor/components/event-handling>.

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

`PasswordField`Bileşen başka bir bileşende kullanılır:

```razor
@page "/ParentComponent"

<h1>Parent Component</h1>

<PasswordField @bind-Password="password" />

@code {
    private string password;
}
```

Önceki örnekteki parolada denetim veya tuzak hataları gerçekleştirmek için:

* İçin bir yedekleme alanı oluşturun `Password` ( `password` Aşağıdaki örnek kodda).
* Ayarlayıcıdaki denetimleri veya yakalama hatalarını gerçekleştirin `Password` .

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

Bir form içindeki radyo düğmelerine bağlama hakkında bilgi için bkz <xref:blazor/forms-validation#work-with-radio-buttons> ..
