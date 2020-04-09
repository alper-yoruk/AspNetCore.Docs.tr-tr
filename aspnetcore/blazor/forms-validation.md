---
title: ASP.NET Blazor Temel formları ve doğrulama
author: guardrex
description: Formlar ve alan doğrulama senaryolarını Blazor'da nasıl kullanacağınızı öğrenin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/17/2020
no-loc:
- Blazor
- SignalR
uid: blazor/forms-validation
ms.openlocfilehash: 0359a9337860d9b8ce0b81d8833a034a898b05a5
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80218966"
---
# <a name="aspnet-core-blazor-forms-and-validation"></a>ASP.NET Core Blazor formları ve doğrulama

Yazar: [Daniel Roth](https://github.com/danroth27) ve [Luke Latham](https://github.com/guardrex)

Formlar ve doğrulama Blazor'da [veri ek açıklamaları](xref:mvc/models/validation)kullanılarak desteklenir.

Aşağıdaki `ExampleModel` tür, veri ek açıklamalarını kullanarak doğrulama mantığını tanımlar:

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

Form `EditForm` bileşeni kullanılarak tanımlanır. Aşağıdaki form tipik öğeleri, bileşenleri ve Razor kodunu gösterir:

```razor
<EditForm Model="@_exampleModel" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <InputText id="name" @bind-Value="_exampleModel.Name" />

    <button type="submit">Submit</button>
</EditForm>

@code {
    private ExampleModel _exampleModel = new ExampleModel();

    private void HandleValidSubmit()
    {
        Console.WriteLine("OnValidSubmit");
    }
}
```

Önceki örnekte:

* Form, türde tanımlanan doğrulamayı kullanarak `name` alandaki kullanıcı `ExampleModel` girişini doğrular. Model bileşenin `@code` bloğunda oluşturulur ve özel bir alanda`_exampleModel`tutulur ( ). Alan `Model` `<EditForm>` öğenin özniteliğine atanır.
* Bileşenin `InputText` `@bind-Value` bağlamaları:
  * Model özelliği`_exampleModel.Name`( ) `InputText` bileşenin `Value` özelliğine.
  * Bileşenin `InputText` `ValueChanged` özelliğine bir değişiklik olayı temsilcisi.
* Bileşen, `DataAnnotationsValidator` veri ek açıklamalarını kullanarak doğrulama desteği ne bağlar.
* Bileşen `ValidationSummary` doğrulama iletilerini özetler.
* `HandleValidSubmit`form başarılı bir şekilde gönderdiğinde (doğrulamayı geçtiğinde) tetiklenir.

Kullanıcı girdisini almak ve doğrulamak için bir dizi yerleşik giriş bileşeni kullanılabilir. Girişler değiştirildiğinde ve form gönderildiğinde doğrulanır. Kullanılabilir giriş bileşenleri aşağıdaki tabloda gösterilmiştir.

| Giriş bileşeni | Olarak işlenmiştir&hellip;       |
| --------------- | ------------------------- |
| `InputText`     | `<input>`                 |
| `InputTextArea` | `<textarea>`              |
| `InputSelect`   | `<select>`                |
| `InputNumber`   | `<input type="number">`   |
| `InputCheckbox` | `<input type="checkbox">` |
| `InputDate`     | `<input type="date">`     |

Rasgele öznitelikleri desteklemek de `EditForm`dahil olmak üzere tüm giriş bileşenleri. Bir bileşen parametresi ile eşleşmeyen herhangi bir öznitelik, işlenen HTML öğesine eklenir.

Giriş bileşenleri, edit'te doğrulama ve CSS sınıflarını alan durumunu yansıtacak şekilde değiştirmek için varsayılan davranış sağlar. Bazı bileşenler yararlı ayrışma mantığı içerir. Örneğin, `InputDate` ve `InputNumber` doğrulama hataları olarak kaydederek incelikle ayrılmaz değerleri işlemek. Null değerleri kabul edebilen türler de hedef alanın `int?`nullability destekler (örneğin,).

Aşağıdaki `Starship` tür, öncekinden `ExampleModel`daha büyük bir özellik kümesi ve veri ek açıklamaları kullanarak doğrulama mantığını tanımlar:

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class Starship
{
    [Required]
    [StringLength(16, ErrorMessage = "Identifier too long (16 character limit).")]
    public string Identifier { get; set; }

    public string Description { get; set; }

    [Required]
    public string Classification { get; set; }

    [Range(1, 100000, ErrorMessage = "Accommodation invalid (1-100000).")]
    public int MaximumAccommodation { get; set; }

    [Required]
    [Range(typeof(bool), "true", "true", 
        ErrorMessage = "This form disallows unapproved ships.")]
    public bool IsValidatedDesign { get; set; }

    [Required]
    public DateTime ProductionDate { get; set; }
}
```

Önceki örnekte, `Description` hiçbir veri ek açıklamaları olduğundan isteğe bağlıdır.

Aşağıdaki form, modelde tanımlanan doğrulamayı kullanarak `Starship` kullanıcı girdisini doğrular:

```razor
@page "/FormsValidation"

<h1>Starfleet Starship Database</h1>

<h2>New Ship Entry Form</h2>

<EditForm Model="@_starship" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <p>
        <label>
            Identifier:
            <InputText @bind-Value="_starship.Identifier" />
        </label>
    </p>
    <p>
        <label>
            Description (optional):
            <InputTextArea @bind-Value="_starship.Description" />
        </label>
    </p>
    <p>
        <label>
            Primary Classification:
            <InputSelect @bind-Value="_starship.Classification">
                <option value="">Select classification ...</option>
                <option value="Exploration">Exploration</option>
                <option value="Diplomacy">Diplomacy</option>
                <option value="Defense">Defense</option>
            </InputSelect>
        </label>
    </p>
    <p>
        <label>
            Maximum Accommodation:
            <InputNumber @bind-Value="_starship.MaximumAccommodation" />
        </label>
    </p>
    <p>
        <label>
            Engineering Approval:
            <InputCheckbox @bind-Value="_starship.IsValidatedDesign" />
        </label>
    </p>
    <p>
        <label>
            Production Date:
            <InputDate @bind-Value="_starship.ProductionDate" />
        </label>
    </p>

    <button type="submit">Submit</button>

    <p>
        <a href="http://www.startrek.com/">Star Trek</a>, 
        &copy;1966-2019 CBS Studios, Inc. and 
        <a href="https://www.paramount.com">Paramount Pictures</a>
    </p>
</EditForm>

@code {
    private Starship _starship = new Starship();

    private void HandleValidSubmit()
    {
        Console.WriteLine("OnValidSubmit");
    }
}
```

Bu, `EditForm` hangi `EditContext` alanların değiştirildiği ve geçerli doğrulama iletileri de dahil olmak üzere, edit işlemiyle ilgili meta verileri izleyen basamaklı bir [değer](xref:blazor/components#cascading-values-and-parameters) oluşturur. Ayrıca `EditForm` geçerli ve geçersiz gönderiler için`OnValidSubmit`uygun `OnInvalidSubmit`etkinlikler sağlar ( , ). Alternatif olarak, `OnSubmit` doğrulamayı tetiklemek ve alan değerlerini özel doğrulama koduyla denetlemek için kullanın.

Aşağıdaki örnekte:

* `HandleSubmit` **Gönder** düğmesi seçildiğinde yöntem çalışır.
* Form, `EditContext`formun.
* Form, sunucuda `ServerValidate` bir web `EditContext` API bitiş noktası çağıran yönteme geçerek daha da doğrulanır *(gösterilmez).*
* Ek kod, istemci ve sunucu tarafı doğrulamasının sonucuna bağlı `isValid`olarak denetleyerek çalıştırılır.

```razor
<EditForm EditContext="@_editContext" OnSubmit="@HandleSubmit">

    ...

    <button type="submit">Submit</button>
</EditForm>

@code {
    private Starship _starship = new Starship();
    private EditContext _editContext;

    protected override void OnInitialized()
    {
        _editContext = new EditContext(_starship);
    }

    private async Task HandleSubmit()
    {
        var isValid = _editContext.Validate() && 
            await ServerValidate(_editContext);

        if (isValid)
        {
            ...
        }
        else
        {
            ...
        }
    }

    private async Task<bool> ServerValidate(EditContext editContext)
    {
        var serverChecksValid = ...

        return serverChecksValid;
    }
}
```

## <a name="inputtext-based-on-the-input-event"></a>Giriş olayına göre Giriş Metni

Olay `InputText` yerine olayı kullanan özel bir bileşen oluşturmak için bileşeni kullanın. `input` `change`

Aşağıdaki biçimlendirmeye sahip bir bileşen oluşturun ve `InputText` bileşeni kullanıldığı gibi kullanın:

```razor
@inherits InputText

<input 
    @attributes="AdditionalAttributes" 
    class="@CssClass" 
    value="@CurrentValue" 
    @oninput="EventCallback.Factory.CreateBinder<string>(
        this, __value => CurrentValueAsString = __value, CurrentValueAsString)" />
```

## <a name="work-with-radio-buttons"></a>Radyo düğmeleriyle çalışma

Bir formda radyo düğmeleri ile çalışırken, radyo düğmeleri bir grup olarak değerlendirildiğinden, veri bağlama diğer öğelerden farklı olarak işlenir. Her radyo düğmesinin değeri sabittir, ancak radyo düğme grubunun değeri seçilen radyo düğmesinin değeridir. Aşağıdaki örnek, nasıl yapılacağını gösterir:

* Bir radyo düğmesi grubu için veri bağlamayı işleme.
* Özel `InputRadio` bir bileşen kullanarak doğrulamayı destekleyin.

```razor
@using System.Globalization
@typeparam TValue
@inherits InputBase<TValue>

<input @attributes="AdditionalAttributes" type="radio" value="@SelectedValue" 
       checked="@(SelectedValue.Equals(Value))" @onchange="OnChange" />

@code {
    [Parameter]
    public TValue SelectedValue { get; set; }

    private void OnChange(ChangeEventArgs args)
    {
        CurrentValueAsString = args.Value.ToString();
    }

    protected override bool TryParseValueFromString(string value, 
        out TValue result, out string errorMessage)
    {
        var success = BindConverter.TryConvertTo<TValue>(
            value, CultureInfo.CurrentCulture, out var parsedValue);
        if (success)
        {
            result = parsedValue;
            errorMessage = null;

            return true;
        }
        else
        {
            result = default;
            errorMessage = $"{FieldIdentifier.FieldName} field isn't valid.";

            return false;
        }
    }
}
```

Aşağıdaki, `EditForm` kullanıcıdan `InputRadio` bir derecelendirme elde etmek ve doğrulamak için önceki bileşeni kullanır:

```razor
@page "/RadioButtonExample"
@using System.ComponentModel.DataAnnotations

<h1>Radio Button Group Test</h1>

<EditForm Model="_model" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    @for (int i = 1; i <= 5; i++)
    {
        <label>
            <InputRadio name="rate" SelectedValue="i" @bind-Value="_model.Rating" />
            @i
        </label>
    }

    <button type="submit">Submit</button>
</EditForm>

<p>You chose: @_model.Rating</p>

@code {
    private Model _model = new Model();

    private void HandleValidSubmit()
    {
        Console.WriteLine("valid");
    }

    public class Model
    {
        [Range(1, 5)]
        public int Rating { get; set; }
    }
}
```

## <a name="validation-support"></a>Doğrulama desteği

Bileşen, `DataAnnotationsValidator` basamaklı veri ek açıklamalarını kullanarak doğrulama `EditContext`desteği ne bağlar. Veri ek açıklamalarını kullanarak doğrulama desteği etkinleştirmek bu açık hareketi gerektirir. Veri ek açıklamalarından farklı bir doğrulama sistemi `DataAnnotationsValidator` kullanmak için, özel bir uygulama yla değiştirin. ASP.NET Core uygulaması referans kaynağında incelenmek için kullanılabilir: [DataAnnotationsValidator](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).

Blazoriki tür doğrulama gerçekleştirir:

* *Alan doğrulama,* kullanıcı bir alanın dışına çıktığında gerçekleştirilir. Alan doğrulama sırasında `DataAnnotationsValidator` bileşen, bildirilen tüm doğrulama sonuçlarını alanla ilişkilendirer.
* *Kullanıcı* formu gönderdiğinde model doğrulama gerçekleştirilir. Model doğrulama sırasında `DataAnnotationsValidator` bileşen, doğrulama sonucunun bildirdiği üye adı temel alınca alanı belirlemeye çalışır. Tek bir üyeyle ilişkilendirilmeen doğrulama sonuçları, bir alan yerine modelle ilişkilidir.

### <a name="validation-summary-and-validation-message-components"></a>Doğrulama Özeti ve Doğrulama İletisi bileşenleri

Bileşen, `ValidationSummary` [Doğrulama Özeti Etiket Yardımcısı'na](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper)benzer tüm doğrulama iletilerini özetler:

```razor
<ValidationSummary />
```

`Model` Parametreile belirli bir model için çıkış doğrulama iletileri:
  
```razor
<ValidationSummary Model="@_starship" />
```

Bileşen, `ValidationMessage` [Doğrulama İleti Stag Helper'a](xref:mvc/views/working-with-forms#the-validation-message-tag-helper)benzer belirli bir alan için doğrulama iletileri görüntüler. Öznitelik ve model özelliği `For` adlandırma bir lambda ifadesi ile doğrulama için alan belirtin:

```razor
<ValidationMessage For="@(() => _starship.MaximumAccommodation)" />
```

Ve `ValidationMessage` `ValidationSummary` bileşenleri rasgele öznitelikleri destekler. Bileşen parametresi ile eşleşmeyen öznitelik, oluşturulan `<div>` veya `<ul>` öğeye eklenir.

### <a name="custom-validation-attributes"></a>Özel doğrulama öznitelikleri

Bir doğrulama sonucunun, özel bir [doğrulama özniteliği](xref:mvc/models/validation#custom-attributes)kullanırken bir alanla doğru şekilde ilişkilendirildiğinden emin olmak için, aşağıdakileri <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> oluştururken doğrulama bağlamını <xref:System.ComponentModel.DataAnnotations.ValidationResult>geçirin:

```csharp
using System;
using System.ComponentModel.DataAnnotations;

private class MyCustomValidator : ValidationAttribute
{
    protected override ValidationResult IsValid(object value, 
        ValidationContext validationContext)
    {
        ...

        return new ValidationResult("Validation message to user.",
            new[] { validationContext.MemberName });
    }
}
```

### <a name="opno-locblazor-data-annotations-validation-package"></a>Blazorveri ek açıklamaları doğrulama paketi

[Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) `DataAnnotationsValidator` bileşeni kullanarak doğrulama deneyimi boşlukları dolduran bir pakettir. Paket şu anda *deneysel.*

### <a name="compareproperty-attribute"></a>[CompareProperty] özniteliği

Doğrulama <xref:System.ComponentModel.DataAnnotations.CompareAttribute> sonucunu belirli bir `DataAnnotationsValidator` üyeyle ilişkilendirmediği için bileşenle iyi çalışmaz. Bu, alan düzeyinde doğrulama ile tüm model in bir gönderide doğrulandığında tutarsız davranışlara neden olabilir. [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) *deneysel* paketi ek bir doğrulama özniteliği tanıttı, `ComparePropertyAttribute`bu sınırlamalar etrafında çalışır. Bir Blazor uygulamada, `[CompareProperty]` öznitelik için `[Compare]` doğrudan bir yedektir.

### <a name="nested-models-collection-types-and-complex-types"></a>İç içe modeller, koleksiyon türleri ve karmaşık türleri

Blazoryerleşik veri ek açıklamaları kullanarak form girişi doğrulama desteği `DataAnnotationsValidator`sağlar. Ancak, `DataAnnotationsValidator` yalnızca koleksiyon veya karmaşık tür özellikleri olmayan forma bağlı modelin üst düzey özelliklerini doğrular.

Ciltli modelin toplama ve karmaşık tür özellikleri de dahil olmak üzere `ObjectGraphDataAnnotationsValidator` tüm nesne grafiğini doğrulamak *için, deneysel* [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) paketi tarafından sağlanan kullanın:

```razor
<EditForm Model="@_model" OnValidSubmit="HandleValidSubmit">
    <ObjectGraphDataAnnotationsValidator />
    ...
</EditForm>
```

Model özelliklerini `[ValidateComplexType]`' le açıklama Aşağıdaki model sınıflarında, `ShipDescription` model in forma ne zaman bağlı olduğunu doğrulamak için ek veri ek açıklamaları içerir:

*Starship.cs*:

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class Starship
{
    ...

    [ValidateComplexType]
    public ShipDescription ShipDescription { get; set; }

    ...
}
```

*ShipDescription.cs*:

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class ShipDescription
{
    [Required]
    [StringLength(40, ErrorMessage = "Description too long (40 char).")]
    public string ShortDescription { get; set; }
    
    [Required]
    [StringLength(240, ErrorMessage = "Description too long (240 char).")]
    public string LongDescription { get; set; }
}
```

### <a name="enable-the-submit-button-based-on-form-validation"></a>Form doğrulamaya göre gönder düğmesini etkinleştirme

Form doğrulamasına dayalı gönder düğmesini etkinleştirmek ve devre dışı kalımsağlamak için:

* Bileşen başharfe `EditContext` geçtiğinde modeli atamak için formun kini kullanın.
* Gönder düğmesini etkinleştirmek `OnFieldChanged` ve devre dışı kılarak bağlamın geri aramasında formu doğrulayın.
* Yöntemdeki olay işleyicisini `Dispose` boşaltın. Daha fazla bilgi için bkz. <xref:blazor/lifecycle#component-disposal-with-idisposable>.

```razor
@implements IDisposable

<EditForm EditContext="@_editContext">
    <DataAnnotationsValidator />
    <ValidationSummary />

    ...

    <button type="submit" disabled="@_formInvalid">Submit</button>
</EditForm>

@code {
    private Starship _starship = new Starship();
    private bool _formInvalid = true;
    private EditContext _editContext;

    protected override void OnInitialized()
    {
        _editContext = new EditContext(_starship);
        _editContext.OnFieldChanged += HandleFieldChanged;
    }

    private void HandleFieldChanged(object sender, FieldChangedEventArgs e)
    {
        _formInvalid = !_editContext.Validate();
        StateHasChanged();
    }

    public void Dispose()
    {
        _editContext.OnFieldChanged -= HandleFieldChanged;
    }
}
```

Yukarıdaki örnekte, aşağıdakileri `false` yapacak şekilde ayarlanır: `_formInvalid`

* Form geçerli varsayılan değerlerle önceden yüklenir.
* Form yüklendiğinde gönder düğmesinin etkin olmasını istiyorsunuz.

Önceki yaklaşımın bir yan etkisi, `ValidationSummary` kullanıcı herhangi bir alanla etkileşime geçtikten sonra bir bileşenin geçersiz alanlarla doldurulan olmasıdır. Bu senaryo aşağıdaki yollardan biri olarak ele alınabilir:

* Formda bir `ValidationSummary` bileşen kullanmayın.
* Gönder `ValidationSummary` düğmesi seçildiğinde bileşeni görünür hale getirin `HandleValidSubmit` (örneğin, bir yöntemde).

```razor
<EditForm EditContext="@_editContext" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary style="@_displaySummary" />

    ...

    <button type="submit" disabled="@_formInvalid">Submit</button>
</EditForm>

@code {
    private string _displaySummary = "display:none";

    ...

    private void HandleValidSubmit()
    {
        _displaySummary = "display:block";
    }
}
```
