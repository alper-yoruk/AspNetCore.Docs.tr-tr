---
title: Forms Blazor ve doğrulama ASP.NET Core
author: guardrex
description: İçindeki Blazorform ve alan doğrulama senaryolarını nasıl kullanacağınızı öğrenin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/17/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/forms-validation
ms.openlocfilehash: 9ffcacc404aa868d533196e5c1bb52d9acdeb337
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82768987"
---
# <a name="aspnet-core-blazor-forms-and-validation"></a>ASP.NET Core Blazor formları ve doğrulaması

[Daniel Roth](https://github.com/danroth27) ve [Luke Latham](https://github.com/guardrex) tarafından

Forms ve doğrulama, Blazor içinde [veri ek açıklamaları](xref:mvc/models/validation)kullanılarak desteklenir.

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

Bir form, `EditForm` bileşeni kullanılarak tanımlanır. Aşağıdaki formda tipik öğeler, bileşenler ve Razor kodu gösterilmektedir:

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

Yukarıdaki örnekte:

* Form, `name` `ExampleModel` türünde tanımlanan doğrulamayı kullanarak alanda Kullanıcı girişini doğrular. Model bileşen `@code` bloğunda oluşturulur ve özel bir alanda (`_exampleModel`) tutulur. Alanı, `Model` `<EditForm>` öğesinin özniteliğine atanır.
* `InputText` Bileşenin `@bind-Value` bağlamaları:
  * Model özelliği (`_exampleModel.Name`) `InputText` bileşen `Value` özelliğine.
  * `InputText` Bileşen `ValueChanged` özelliğine bir değişiklik olayı temsilcisi.
* Bileşen `DataAnnotationsValidator` , veri ek açıklamalarını kullanarak doğrulama desteği ekler.
* `ValidationSummary` Bileşen doğrulama iletilerini özetler.
* `HandleValidSubmit`Form başarıyla gönderdiğinde tetiklenir (doğrulamayı geçirir).

Kullanıcı girişini almak ve doğrulamak için yerleşik bir giriş bileşenleri kümesi vardır. Girişler değiştirildiklerinde ve bir form gönderildiğinde onaylanır. Kullanılabilir giriş bileşenleri aşağıdaki tabloda gösterilmiştir.

| Giriş bileşeni | Olarak işlendi&hellip;       |
| --------------- | ------------------------- |
| `InputText`     | `<input>`                 |
| `InputTextArea` | `<textarea>`              |
| `InputSelect`   | `<select>`                |
| `InputNumber`   | `<input type="number">`   |
| `InputCheckbox` | `<input type="checkbox">` |
| `InputDate`     | `<input type="date">`     |

Dahil olmak üzere `EditForm`tüm giriş bileşenleri, rastgele öznitelikleri destekler. Bir bileşen parametresiyle eşleşmeyen herhangi bir öznitelik işlenmiş HTML öğesine eklenir.

Giriş bileşenleri, düzenleme sırasında doğrulamak ve CSS sınıfını alan durumunu yansıtacak şekilde değiştirmek için varsayılan davranışı sağlar. Bazı bileşenler, yararlı ayrıştırma mantığını içerir. Örneğin, `InputDate` bunları doğrulama `InputNumber` hatası olarak kaydederek düzeltilemez değerleri düzgün şekilde işleyin. Null değerleri kabul edebilecek türler, hedef alanın null değer alabilme durumunu da destekler (örneğin, `int?`).

Aşağıdaki `Starship` tür, daha önce daha büyük bir özellik kümesi ve daha önceki `ExampleModel`veri açıklamalarını kullanarak doğrulama mantığını tanımlar:

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

Önceki örnekte, `Description` hiçbir veri ek açıklaması mevcut olmadığından isteğe bağlıdır.

Aşağıdaki form, `Starship` modelde tanımlanan doğrulamayı kullanarak Kullanıcı girişini doğrular:

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

, `EditForm` Hangi alanların `EditContext` değiştirildiği ve geçerli doğrulama iletileri de dahil olmak üzere düzenleme işlemiyle ilgili meta verileri izleyen [basamaklı bir değer](xref:blazor/components#cascading-values-and-parameters) olarak oluşturur. `EditForm` Ayrıca geçerli ve geçersiz Gönderimlerle (`OnValidSubmit`, `OnInvalidSubmit`) uygun olaylar sağlar. Alternatif olarak, `OnSubmit` doğrulamayı tetiklemek ve alan değerlerini özel doğrulama kodu ile denetlemek için kullanın.

Aşağıdaki örnekte:

* `HandleSubmit` Yöntemi, **Gönder** düğmesi seçildiğinde çalışır.
* Form, formun ' i kullanılarak onaylanır `EditContext`.
* Form, sunucusunda bir Web API uç noktası `EditContext` çağıran `ServerValidate` yönteme geçerek daha sonra onaylanır (*gösterilmez*).
* Ek kod, istemci ve sunucu tarafı doğrulamasının sonucuna bağlı olarak çalıştırılır `isValid`.

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

## <a name="inputtext-based-on-the-input-event"></a>Giriş olayına göre InputText

Olayı yerine `InputText` `input` olayını kullanan özel bir bileşen oluşturmak için bileşenini kullanın. `change`

Aşağıdaki biçimlendirmeye sahip bir bileşen oluşturun ve bileşeni tıpkı kullanıldığı gibi `InputText` kullanın:

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

Bir formda radyo düğmeleriyle çalışırken, radyo düğmeleri bir grup olarak değerlendirildiğinden veri bağlama diğer öğelerden farklı işlenir. Her radyo düğmesinin değeri sabittir, ancak radyo düğmesi grubunun değeri seçili radyo düğmesinin değeridir. Aşağıdaki örnek, aşağıdakilerin nasıl yapılacağını göstermektedir:

* Radyo düğmesi grubu için veri bağlamayı işleyin.
* Özel `InputRadio` bir bileşen kullanarak doğrulamayı destekler.

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

Aşağıdaki `EditForm` , kullanıcıdan bir derecelendirme `InputRadio` almak ve doğrulamak için önceki bileşeni kullanır:

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

`DataAnnotationsValidator` Bileşen, Basamaklandırılan `EditContext`veri açıklamalarını kullanarak doğrulama desteğini iliştirir. Veri ek açıklamalarını kullanarak doğrulama desteğinin etkinleştirilmesi bu açık hareketi gerektirir. Veri ek açıklamalarıyla farklı bir doğrulama sistemi kullanmak için, `DataAnnotationsValidator` öğesini özel bir uygulamayla değiştirin. ASP.NET Core uygulama, başvuru kaynağında İnceleme için kullanılabilir: [dataannotationsvalidator](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[adddataannotationsvalidation](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).

Blazoriki tür doğrulama gerçekleştirir:

* *Alan doğrulama* , Kullanıcı bir alanın dışına eklendiğinde gerçekleştirilir. Alan doğrulama sırasında, `DataAnnotationsValidator` bileşen bildirilen tüm doğrulama sonuçlarını alanla ilişkilendirir.
* Kullanıcı formu gönderdiğinde *model doğrulaması* gerçekleştirilir. Model doğrulama sırasında, `DataAnnotationsValidator` bileşen, doğrulama sonucunun raporlandığı üye adına göre alanı belirlemeyi dener. Tek bir üyeyle ilişkilendirilmeyen doğrulama sonuçları, bir alan yerine modeliyle ilişkilendirilir.

### <a name="validation-summary-and-validation-message-components"></a>Doğrulama özeti ve doğrulama Iletisi bileşenleri

`ValidationSummary` Bileşen, [doğrulama özeti etiketi Yardımcısı](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper)'na benzer olan tüm doğrulama iletilerini özetler:

```razor
<ValidationSummary />
```

`Model` Parametresi ile belirli bir model için çıkış doğrulama iletileri:
  
```razor
<ValidationSummary Model="@_starship" />
```

`ValidationMessage` Bileşeni, [doğrulama iletisi etiketi Yardımcısı](xref:mvc/views/working-with-forms#the-validation-message-tag-helper)'na benzer şekilde belirli bir alan için doğrulama iletileri görüntüler. `For` Özniteliği ile doğrulama için alanı ve model özelliğini adlandırırken bir lambda ifadesini belirtin:

```razor
<ValidationMessage For="@(() => _starship.MaximumAccommodation)" />
```

Ve `ValidationMessage` `ValidationSummary` bileşenleri, rastgele öznitelikleri destekler. Bir bileşen parametresiyle eşleşmeyen herhangi bir öznitelik oluşturulan `<div>` or `<ul>` öğesine eklenir.

### <a name="custom-validation-attributes"></a>Özel doğrulama öznitelikleri

Bir doğrulama sonucunun [özel bir doğrulama özniteliği](xref:mvc/models/validation#custom-attributes)kullanılırken bir alanla doğru şekilde ilişkilendirildiğinden emin olmak için, şunu oluştururken doğrulama bağlamını <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> geçirin: <xref:System.ComponentModel.DataAnnotations.ValidationResult>

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

### <a name="blazor-data-annotations-validation-package"></a>Blazorveri ek açıklamaları doğrulama paketi

[Microsoft. AspNetCore. components. Dataaçıklamalarda. Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) , `DataAnnotationsValidator` bileşeni kullanarak doğrulama deneyimini boşlukları dolduran bir pakettir. Paket şu anda *deneysel*.

### <a name="compareproperty-attribute"></a>[CompareProperty] özniteliği

Doğrulama <xref:System.ComponentModel.DataAnnotations.CompareAttribute> sonucunu belirli bir üyeyle ilişkilendirmediği için `DataAnnotationsValidator` bileşen ile iyi çalışmaz. Bu, alan düzeyi doğrulama ve tüm modelin bir gönderme sırasında doğrulanması arasındaki tutarsız davranışa neden olabilir. [Microsoft. AspNetCore. components. Dataaçıklamalarda. Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) *deneysel* Package, bu sınırlamalara geçici bir çözüm olan `ComparePropertyAttribute`ek bir doğrulama özniteliği sunar. Bir Blazor uygulamada, `[CompareProperty]` `[Compare]` özniteliği için doğrudan değiştirme olur.

### <a name="nested-models-collection-types-and-complex-types"></a>İç içe modeller, koleksiyon türleri ve karmaşık türler

Blazoryerleşik olan veri açıklamalarını kullanarak form girişini doğrulama desteği sağlar `DataAnnotationsValidator`. Ancak, yalnızca `DataAnnotationsValidator` koleksiyonun üst düzey özelliklerini, koleksiyon veya karmaşık tür özellikleri olmayan forma doğrular.

Koleksiyon ve karmaşık tür özellikleri dahil olmak üzere, bağlantılı modelin tüm nesne grafiğini doğrulamak için, `ObjectGraphDataAnnotationsValidator` *deneysel* [Microsoft. Aspnetcore. components. dataaçıklamalarda. Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) Package tarafından sunulan öğesini kullanın:

```razor
<EditForm Model="@_model" OnValidSubmit="HandleValidSubmit">
    <ObjectGraphDataAnnotationsValidator />
    ...
</EditForm>
```

İle `[ValidateComplexType]`model özelliklerine açıklama ekleyin. Aşağıdaki model sınıflarında, `ShipDescription` sınıfı, model forma bağlandığında doğrulanacak ek veri açıklamalarını içerir:

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

### <a name="enable-the-submit-button-based-on-form-validation"></a>Form doğrulamasına göre Gönder düğmesini etkinleştir

Form doğrulamasına göre Gönder düğmesini etkinleştirmek ve devre dışı bırakmak için:

* Bileşen başlatıldığında modeli atamak `EditContext` için formunu kullanın.
* Gönder düğmesini etkinleştirmek ve devre dışı bırakmak `OnFieldChanged` için bağlam geri aramasında formu doğrulayın.
* `Dispose` Yöntemi içindeki olay işleyicisinin üstünden geri dön. Daha fazla bilgi için bkz. <xref:blazor/lifecycle#component-disposal-with-idisposable>.

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

Yukarıdaki örnekte, şu şekilde `_formInvalid` `false` ayarlayın:

* Form geçerli varsayılan değerlerle önceden yüklenir.
* Form yüklendiğinde Gönder düğmesinin etkinleştirilmesini istiyorsunuz.

Önceki yaklaşımın yan etkisi, Kullanıcı herhangi bir alanla etkileşime `ValidationSummary` geçtiğinde bileşen geçersiz alanlarla doldurulmuştur. Bu senaryoya aşağıdaki yollarla değinilerek şunlar olabilir:

* Form üzerinde bir `ValidationSummary` bileşen kullanmayın.
* Gönder düğmesi `ValidationSummary` seçildiğinde bileşeni görünür hale getirin (örneğin, bir `HandleValidSubmit` yöntemde).

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
