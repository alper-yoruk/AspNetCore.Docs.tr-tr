---
title: ASP.NET Core Blazor veri bağlama
author: guardrex
description: Uygulamalardaki bileşenler ve DOM öğeleri için veri bağlama özellikleri hakkında bilgi edinin Blazor .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/22/2020
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
uid: blazor/components/data-binding
ms.openlocfilehash: 67a63f1b4f705a4857dea2e6d1a942d4f21469f5
ms.sourcegitcommit: 83524f739dd25fbfa95ee34e95342afb383b49fe
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99057102"
---
# <a name="aspnet-core-no-locblazor-data-binding"></a>ASP.NET Core Blazor veri bağlama

[Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27)ve [Steve Sanderson](https://github.com/SteveSandersonMS) tarafından

Razor bileşenler, [`@bind`](xref:mvc/views/razor#bind) bir alan, özellik veya ifade değeri ile adlandırılmış BIR HTML öğesi özniteliği aracılığıyla veri bağlama özellikleri sağlar Razor .

Aşağıdaki örnek, bir `<input>` öğesini `currentValue` alana ve `<input>` öğesi `CurrentValue` özelliğine bağlar:

```razor
<p>
    <input @bind="currentValue" /> Current value: @currentValue
</p>

<p>
    <input @bind="CurrentValue" /> Current value: @CurrentValue
</p>

@code {
    private string currentValue;

    private string CurrentValue { get; set; }
}
```

Öğelerden biri odağı kaybettiğinde, ilişkili alanı veya özelliği güncellenir.

Metin kutusu kullanıcı arabiriminde, alanın veya özelliğin değerini değiştirme yanıt olarak değil, yalnızca bileşen işlendiğinde güncellenir. Bileşenler olay işleyicisi kodu yürütüldükten sonra kendilerini oluşturduğundan, alan ve özellik güncelleştirmeleri *genellikle* bir olay işleyicisi tetiklendikten hemen sonra Kullanıcı arabirimine yansıtılır.

[`@bind`](xref:mvc/views/razor#bind) `CurrentValue` Özelliği () ile kullanmak, `<input @bind="CurrentValue" />` temelde aşağıdakilere eşdeğerdir:

```razor
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = 
        __e.Value.ToString())" />

@code {
    private string CurrentValue { get; set; }
}
```

Bileşen işlendiğinde, `value` giriş öğesi `CurrentValue` özelliğinden gelir. Kullanıcı metin kutusuna yazdığında ve öğe odağını değiştirdiğinde, `onchange` olay tetiklenir ve `CurrentValue` özellik değiştirilen değere ayarlanır. Gerçekte, kod oluşturma bu değerden daha karmaşıktır çünkü [`@bind`](xref:mvc/views/razor#bind) tür dönüştürmelerinden oluşan durumları işler. İlke ' de, [`@bind`](xref:mvc/views/razor#bind) bir ifadenin geçerli değerini bir `value` özniteliğiyle ilişkilendirir ve kayıtlı işleyiciyi kullanarak değişiklikleri işler.

Parametre içeren bir özniteliği de ekleyerek diğer olaylardaki bir özelliği veya alanı bağlayın `@bind:event` `event` . Aşağıdaki örnek, `CurrentValue` olayında özelliği bağlar `oninput` :

```razor
<input @bind="CurrentValue" @bind:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

`onchange`' In aksine, öğe odağı kaybettiğinde harekete geçirilir, `oninput` metin kutusunun değeri değiştiğinde harekete geçirilir.

<!-- Hold location for resolution of https://github.com/dotnet/AspNetCore.Docs/issues/19721 -->

Öznitelik bağlama büyük/küçük harfe duyarlıdır:

* `@bind` geçerli.
* `@Bind` ve `@BIND` geçersiz.

## <a name="unparsable-values"></a>Ayrıştırılamayan değerler

Bir Kullanıcı, bir veri sınırlama öğesine ayrıştırılamayan bir değer sağlıyorsa, bağlama olayı tetiklendiğinde, çözümlenemeyen değer otomatik olarak önceki değerine döndürülür.

Şu senaryoyu göz önünde bulundurun:

* Bir `<input>` öğesi, `int` Başlangıç değeri olan bir türe bağlanır `123` :

  ```razor
  <input @bind="inputValue" />

  @code {
      private int inputValue = 123;
  }
  ```

* Kullanıcı, öğesinin değerini sayfada olarak güncelleştirir `123.45` ve öğe odağını değiştirir.

Önceki senaryoda, öğenin değeri öğesine geri döndürülür `123` . Değeri `123.45` özgün değeri yararına reddedildiğinde `123` , Kullanıcı değerinin kabul edilmediğini anlamıştır.

Varsayılan olarak, bağlama öğenin `onchange` olayına ( `@bind="{PROPERTY OR FIELD}"` ) uygulanır. `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}`Farklı bir olayda bağlamayı tetiklemek için kullanın. `oninput`Event () için `@bind:event="oninput"` yeniden sürüm, ayrıştırılamayan bir değer sunan herhangi bir tuş vuruşu sonrasında oluşur. `oninput`Olayı, ilişkili bir tür ile hedeflerken `int` , bir kullanıcının bir karakter yazmasının engellenmiş olması engellenir `.` . Bir `.` karakter hemen kaldırılır, bu nedenle Kullanıcı yalnızca tam sayılara izin verilen anında geri bildirim alır. Olay üzerindeki değerin geri döndürülmesi `oninput` ideal değil (örneğin, kullanıcının ayrıştırılamayan bir değeri temizlemeye izin verilmesi gerektiği durumlarda) `<input>` . Alternatifler şunlardır:

* `oninput`Olayı kullanmayın. Varsayılan olayı kullanın `onchange` (yalnızca belirtin `@bind="{PROPERTY OR FIELD}"` ); burada, öğe odağı kaybetene kadar geçersiz bir değer geri döndürülemez.
* Veya gibi null yapılabilir bir türe bağlayın `int?` `string` ve geçersiz girdileri işlemek için özel mantık sağlayın.
* Veya gibi bir [form doğrulama bileşeni](xref:blazor/forms-validation)kullanın <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> . Form doğrulama bileşenlerinde geçersiz girişleri yönetmek için yerleşik destek vardır. Daha fazla bilgi için bkz. <xref:blazor/forms-validation>. Form doğrulama bileşenleri:
  * Kullanıcının geçersiz giriş sağlamasına ve ilişkili doğrulama hatalarını almasına izin verin <xref:Microsoft.AspNetCore.Components.Forms.EditContext> .
  * Kullanıcı ek WebForm verisi girmeye uğramadan doğrulama hatalarını Kullanıcı ARABIRIMINDE görüntüleyin.

## <a name="format-strings"></a>Biçim dizeleri

Veri bağlama, <xref:System.DateTime> kullanılarak biçim dizeleriyle birlikte kullanılabilir `@bind:format` . Para birimi veya sayı biçimleri gibi diğer biçim ifadeleri şu anda kullanılamaz.

```razor
<input @bind="startDate" @bind:format="yyyy-MM-dd" />

@code {
    private DateTime startDate = new DateTime(2020, 1, 1);
}
```

Yukarıdaki kodda, `<input>` öğesinin alan türü ( `type` ) varsayılan olarak olur `text` . `@bind:format` , aşağıdaki .NET türlerini bağlamak için desteklenir:

* <xref:System.DateTime?displayProperty=fullName>
* <xref:System.DateTime?displayProperty=fullName>?
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <xref:System.DateTimeOffset?displayProperty=fullName>?

`@bind:format`Özniteliği öğesi için uygulanacak tarih biçimini belirtir `value` `<input>` . Biçim Ayrıca bir olay gerçekleştiğinde değeri ayrıştırmak için de kullanılır `onchange` .

`date` Blazor Tarihleri biçimlendirmek için yerleşik destek içerdiğinden, alan türü için bir biçim belirtmenin kullanılması önerilmez. Önerinin artma içinde, `yyyy-MM-dd` alan türüyle bir biçim sağlanırsa yalnızca bağlama için tarih biçimini kullanın `date` :

```razor
<input type="date" @bind="startDate" @bind:format="yyyy-MM-dd">
```

## <a name="binding-with-component-parameters"></a>Bileşen parametreleriyle bağlama

Yaygın bir senaryo, alt bileşendeki bir özelliği üst öğesinde bir özelliğe bağlamadır. Birden çok bağlama düzeyi aynı anda gerçekleştiğinden, bu senaryoya *zincirleme bağlama* denir.

[Bileşen parametreleri](xref:blazor/components/index#component-parameters) bir üst bileşenin sözdizimi ile bağlama özelliklerine izin verir `@bind-{PROPERTY}` .

Zincirli bağlamalar [`@bind`](xref:mvc/views/razor#bind) alt bileşendeki sözdizimiyle uygulanamaz. Alt bileşenden üst öğe içindeki özelliği güncelleştirmeyi desteklemek için bir olay işleyicisi ve değeri ayrı olarak belirtilmelidir.

Üst bileşen, [`@bind`](xref:mvc/views/razor#bind) alt bileşenle veri bağlamayı ayarlamak için söz dizimini kullanır.

Aşağıdaki `Child` bileşende ( `Shared/Child.razor` ) bir `Year` bileşen parametresi ve `YearChanged` geri çağırması vardır:

```razor
<div class="card bg-light mt-3" style="width:18rem ">
    <div class="card-body">
        <h3 class="card-title">Child Component</h3>
        <p class="card-text">Child <code>Year</code>: @Year</p>
    </div>
</div>

<button @onclick="UpdateYearFromChild">Update Year from Child</button>

@code {
    private Random r = new Random();

    [Parameter]
    public int Year { get; set; }

    [Parameter]
    public EventCallback<int> YearChanged { get; set; }

    private async Task UpdateYearFromChild()
    {
        await YearChanged.InvokeAsync(r.Next(1950, 2021));
    }
}
```

Geri çağırma ( <xref:Microsoft.AspNetCore.Components.EventCallback%601> ), bileşen parametre adı olarak " `Changed` " soneki () ile adlandırılmalıdır `{PARAMETER NAME}Changed` . Önceki örnekte, geri çağırma adlandırılır `YearChanged` . <xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A?displayProperty=nameWithType> Belirtilen bağımsız değişkenle bağlama ile ilişkili temsilciyi çağırır ve değiştirilen özellik için bir olay bildirimi gönderir.

Aşağıdaki `Parent` bileşende ( `Parent.razor` ), `year` alanı `Year` alt bileşenin parametresine bağlanır:

```razor
@page "/Parent"

<h1>Parent Component</h1>

<p>Parent <code>year</code>: @year</p>

<button @onclick="UpdateYear">Update Parent <code>year</code></button>

<Child @bind-Year="year" />

@code {
    private Random r = new Random();
    private int year = 1979;

    private void UpdateYear()
    {
        year = r.Next(1950, 2021);
    }
}
```

Parametrenin `Year` türüyle eşleşen bir yardımcı olayı olduğundan parametre bağlanabilir `YearChanged` `Year` .

Kurala göre, bir özellik işleyiciye atanmış bir özniteliği ekleyerek karşılık gelen bir olay işleyicisine bağlanabilir `@bind-{PROPERTY}:event` . `<Child @bind-Year="year" />` yazmaya eşittir:

```razor
<Child @bind-Year="year" @bind-Year:event="YearChanged" />
```

Daha karmaşık ve gerçek dünyada bir örnekte, aşağıdaki `PasswordField` bileşen ( `PasswordField.razor` ):

* Bir `<input>` öğenin değerini bir `password` alana ayarlar.
* Bir özelliğin değişikliklerini, `Password` [`EventCallback`](xref:blazor/components/event-handling#eventcallback) alt öğenin geçerli değerinde bağımsız değişkeni olarak geçen bir üst bileşene gösterir `password` .
* `onclick`Yöntemini tetiklemek için olayını kullanır `ToggleShowPassword` . Daha fazla bilgi için bkz. <xref:blazor/components/event-handling>.

```razor
<h1>Provide your password</h1>

Password:

<input @oninput="OnPasswordChanged" 
       required 
       type="@(showPassword ? "text" : "password")" 
       value="@password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

@code {
    private bool showPassword;
    private string password;

    [Parameter]
    public string Password { get; set; }

    [Parameter]
    public EventCallback<string> PasswordChanged { get; set; }

    private async Task OnPasswordChanged(ChangeEventArgs e)
    {
        password = e.Value.ToString();

        await PasswordChanged.InvokeAsync(password);
    }

    private void ToggleShowPassword()
    {
        showPassword = !showPassword;
    }
}
```

`PasswordField`Bileşen başka bir bileşende kullanılır:

```razor
@page "/Parent"

<h1>Parent Component</h1>

<PasswordField @bind-Password="password" />

@code {
    private string password;
}
```

Bağlamanın temsilcisini çağıran yöntemde denetimler veya tuzak hataları gerçekleştirin. Aşağıdaki örnek, parolanın değerinde bir boşluk kullanılmışsa kullanıcıya anında geri bildirim sağlar:

```razor
<h1>Child Component</h1>

Password: 

<input @oninput="OnPasswordChanged" 
       required 
       type="@(showPassword ? "text" : "password")" 
       value="@password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

<span class="text-danger">@validationMessage</span>

@code {
    private bool showPassword;
    private string password;
    private string validationMessage;

    [Parameter]
    public string Password { get; set; }

    [Parameter]
    public EventCallback<string> PasswordChanged { get; set; }

    private Task OnPasswordChanged(ChangeEventArgs e)
    {
        password = e.Value.ToString();

        if (password.Contains(' '))
        {
            validationMessage = "Spaces not allowed!";

            return Task.CompletedTask;
        }
        else
        {
            validationMessage = string.Empty;

            return PasswordChanged.InvokeAsync(password);
        }
    }

    private void ToggleShowPassword()
    {
        showPassword = !showPassword;
    }
}
```

Hakkında daha fazla bilgi için <xref:Microsoft.AspNetCore.Components.EventCallback%601> bkz <xref:blazor/components/event-handling#eventcallback> ..

## <a name="bind-across-more-than-two-components"></a>İkiden fazla bileşen arasında bağlama

Herhangi bir sayıda iç içe bileşeni bağlayabilirsiniz, ancak tek yönlü verilerin akışını dikkate almanız gerekir:

* Değişiklik bildirimleri *hiyerarşinin akışını*.
* Yeni parametre değerleri *hiyerarşinin altına akar*.

Ortak ve önerilen bir yaklaşım, hangi durumun güncellenmesi gerektiğini öğrenmek için temel alınan verileri yalnızca üst bileşende depomaktır.

Aşağıdaki bileşenler önceki kavramları göstermektedir:

`ParentComponent.razor`:

```razor
<h1>Parent Component</h1>

<p>Parent Message: <b>@parentMessage</b></p>

<p>
    <button @onclick="ChangeValue">Change from Parent</button>
</p>

<ChildComponent @bind-ChildMessage="parentMessage" />

@code {
    private string parentMessage = "Initial value set in Parent";

    private void ChangeValue()
    {
        parentMessage = $"Set in Parent {DateTime.Now}";
    }
}
```

`ChildComponent.razor`:

```razor
<div class="border rounded m-1 p-1">
    <h2>Child Component</h2>

    <p>Child Message: <b>@ChildMessage</b></p>

    <p>
        <button @onclick="ChangeValue">Change from Child</button>
    </p>

    <GrandchildComponent @bind-GrandchildMessage="BoundValue" />
</div>

@code {
    [Parameter]
    public string ChildMessage { get; set; }

    [Parameter]
    public EventCallback<string> ChildMessageChanged { get; set; }

    private string BoundValue
    {
        get => ChildMessage;
        set => ChildMessageChanged.InvokeAsync(value);
    }

    private async Task ChangeValue()
    {
        await ChildMessageChanged.InvokeAsync(
            $"Set in Child {DateTime.Now}");
    }
}
```

`GrandchildComponent.razor`:

```razor
<div class="border rounded m-1 p-1">
    <h3>Grandchild Component</h3>

    <p>Grandchild Message: <b>@GrandchildMessage</b></p>

    <p>
        <button @onclick="ChangeValue">Change from Grandchild</button>
    </p>
</div>

@code {
    [Parameter]
    public string GrandchildMessage { get; set; }

    [Parameter]
    public EventCallback<string> GrandchildMessageChanged { get; set; }

    private async Task ChangeValue()
    {
        await GrandchildMessageChanged.InvokeAsync(
            $"Set in Grandchild {DateTime.Now}");
    }
}
```

İç içe geçmiş olmayan bileşenler arasında bellek içi veri paylaşımına uygun alternatif bir yaklaşım için, makalenin *bellek içi durum kapsayıcısı hizmeti* bölümüne bakın <xref:blazor/state-management> .

## <a name="additional-resources"></a>Ek kaynaklar

* [Bir formda radyo düğmelerine bağlama](xref:blazor/forms-validation#radio-buttons)
* [`<select>`Form Içindeki C# nesne değerlerine öğe seçenekleri bağlama `null`](xref:blazor/forms-validation#binding-select-element-options-to-c-object-null-values)
