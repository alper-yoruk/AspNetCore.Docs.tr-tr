---
title: BlazorForms ve doğrulama ASP.NET Core
author: guardrex
description: İçindeki form ve alan doğrulama senaryolarını nasıl kullanacağınızı öğrenin Blazor .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/06/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/forms-validation
ms.openlocfilehash: 3e719261315ed3a17833da7751d801d79a11ee6c
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88014484"
---
# <a name="aspnet-core-no-locblazor-forms-and-validation"></a>BlazorForms ve doğrulama ASP.NET Core

[Daniel Roth](https://github.com/danroth27) ve [Luke Latham](https://github.com/guardrex) tarafından

Formlar ve doğrulama, Blazor [veri ek açıklamaları](xref:mvc/models/validation)kullanılarak desteklenir.

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

Bir form, bileşeni kullanılarak tanımlanır <xref:Microsoft.AspNetCore.Components.Forms.EditForm> . Aşağıdaki form tipik öğeleri, bileşenleri ve Razor kodu gösterir:

```razor
<EditForm Model="@exampleModel" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <InputText id="name" @bind-Value="exampleModel.Name" />

    <button type="submit">Submit</button>
</EditForm>

@code {
    private ExampleModel exampleModel = new ExampleModel();

    private void HandleValidSubmit()
    {
        Console.WriteLine("OnValidSubmit");
    }
}
```

Yukarıdaki örnekte:

* Form, `name` türünde tanımlanan doğrulamayı kullanarak alanda Kullanıcı girişini doğrular `ExampleModel` . Model bileşen `@code` bloğunda oluşturulur ve özel bir alanda ( `exampleModel` ) tutulur. Alanı, `Model` öğesinin özniteliğine atanır `<EditForm>` .
* <xref:Microsoft.AspNetCore.Components.Forms.InputText>Bileşenin `@bind-Value` bağlamaları:
  * Model özelliği ( `exampleModel.Name` ) <xref:Microsoft.AspNetCore.Components.Forms.InputText> bileşen `Value` özelliğine. Özellik bağlama hakkında daha fazla bilgi için bkz <xref:blazor/components/data-binding#parent-to-child-binding-with-component-parameters> ..
  * Bileşen özelliğine bir değişiklik olayı temsilcisi <xref:Microsoft.AspNetCore.Components.Forms.InputText> `ValueChanged` .
* <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>Bileşen, veri ek açıklamalarını kullanarak doğrulama desteği ekler.
* <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary>Bileşen doğrulama iletilerini özetler.
* `HandleValidSubmit`Form başarıyla gönderdiğinde tetiklenir (doğrulamayı geçirir).

## <a name="built-in-forms-components"></a>Yerleşik Forms bileşenleri

Kullanıcı girişini almak ve doğrulamak için yerleşik bir giriş bileşenleri kümesi vardır. Girişler değiştirildiklerinde ve bir form gönderildiğinde onaylanır. Kullanılabilir giriş bileşenleri aşağıdaki tabloda gösterilmiştir.

| Giriş bileşeni | Olarak işlendi&hellip; |
| --------------- | ------------------- |
| <xref:Microsoft.AspNetCore.Components.Forms.InputText> | `<input>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputTextArea> | `<textarea>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputSelect%601> | `<select>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> | `<input type="number">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputCheckbox> | `<input type="checkbox">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> | `<input type="date">` |

Dahil olmak üzere tüm giriş bileşenleri, <xref:Microsoft.AspNetCore.Components.Forms.EditForm> rastgele öznitelikleri destekler. Bir bileşen parametresiyle eşleşmeyen herhangi bir öznitelik işlenmiş HTML öğesine eklenir.

Giriş bileşenleri, düzenleme sırasında doğrulamak ve CSS sınıfını alan durumunu yansıtacak şekilde değiştirmek için varsayılan davranışı sağlar. Bazı bileşenler, yararlı ayrıştırma mantığını içerir. Örneğin, <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> bunları doğrulama hatası olarak kaydederek düzeltilemez değerleri düzgün şekilde işleyin. Null değerleri kabul edebilecek türler, hedef alanın null değer alabilme durumunu da destekler (örneğin, `int?` ).

Aşağıdaki tür, daha `Starship` önce daha büyük bir özellik kümesi ve daha önceki veri açıklamalarını kullanarak doğrulama mantığını tanımlar `ExampleModel` :

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

Aşağıdaki form, modelde tanımlanan doğrulamayı kullanarak Kullanıcı girişini doğrular `Starship` :

```razor
@page "/FormsValidation"

<h1>Starfleet Starship Database</h1>

<h2>New Ship Entry Form</h2>

<EditForm Model="@starship" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <p>
        <label>
            Identifier:
            <InputText @bind-Value="starship.Identifier" />
        </label>
    </p>
    <p>
        <label>
            Description (optional):
            <InputTextArea @bind-Value="starship.Description" />
        </label>
    </p>
    <p>
        <label>
            Primary Classification:
            <InputSelect @bind-Value="starship.Classification">
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
            <InputNumber @bind-Value="starship.MaximumAccommodation" />
        </label>
    </p>
    <p>
        <label>
            Engineering Approval:
            <InputCheckbox @bind-Value="starship.IsValidatedDesign" />
        </label>
    </p>
    <p>
        <label>
            Production Date:
            <InputDate @bind-Value="starship.ProductionDate" />
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
    private Starship starship = new Starship();

    private void HandleValidSubmit()
    {
        Console.WriteLine("OnValidSubmit");
    }
}
```

, <xref:Microsoft.AspNetCore.Components.Forms.EditForm> <xref:Microsoft.AspNetCore.Components.Forms.EditContext> Hangi alanların değiştirildiği ve geçerli doğrulama iletileri de dahil olmak üzere düzenleme işlemiyle ilgili meta verileri izleyen [basamaklı bir değer](xref:blazor/components/cascading-values-and-parameters) olarak oluşturur. <xref:Microsoft.AspNetCore.Components.Forms.EditForm>Ayrıca geçerli ve geçersiz Gönderimlerle (,) uygun olaylar <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnValidSubmit> sağlar <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnInvalidSubmit> . Alternatif olarak, <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit> doğrulamayı tetiklemek ve alan değerlerini özel doğrulama kodu ile denetlemek için kullanın.

Aşağıdaki örnekte:

* `HandleSubmit`Yöntemi **`Submit`** Düğme seçildiğinde çalışır.
* Form, formun ' i kullanılarak onaylanır <xref:Microsoft.AspNetCore.Components.Forms.EditContext> .
* Form, <xref:Microsoft.AspNetCore.Components.Forms.EditContext> `ServerValidate` sunucusunda BIR Web API uç noktası çağıran yönteme geçerek daha sonra onaylanır (*gösterilmez*).
* Ek kod, istemci ve sunucu tarafı doğrulamasının sonucuna bağlı olarak çalıştırılır `isValid` .

```razor
<EditForm EditContext="@editContext" OnSubmit="HandleSubmit">

    ...

    <button type="submit">Submit</button>
</EditForm>

@code {
    private Starship starship = new Starship();
    private EditContext editContext;

    protected override void OnInitialized()
    {
        editContext = new EditContext(starship);
    }

    private async Task HandleSubmit()
    {
        var isValid = editContext.Validate() && 
            await ServerValidate(editContext);

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

<xref:Microsoft.AspNetCore.Components.Forms.InputText>Olayı yerine olayını kullanan özel bir bileşen oluşturmak için bileşenini kullanın `input` `change` .

Aşağıdaki örnekte, `CustomInputText` bileşen Framework `InputText` bileşenini devralır ve olay bağlamayı ( <xref:Microsoft.AspNetCore.Components.EventCallbackFactoryBinderExtensions.CreateBinder%2A> ) `oninput` olaya ayarlar.

`Shared/CustomInputText.razor`:

```razor
@inherits InputText

<input 
    @attributes="AdditionalAttributes" 
    class="@CssClass" 
    value="@CurrentValue"
    @oninput="EventCallback.Factory.CreateBinder<string>(
         this, __value => CurrentValueAsString = __value, 
         CurrentValueAsString)" />
```

`CustomInputText`Bileşen her yerde kullanılabilir <xref:Microsoft.AspNetCore.Components.Forms.InputText> :

`Pages/TestForm.razor`:

```razor
@page  "/testform"
@using System.ComponentModel.DataAnnotations;

<EditForm Model="@exampleModel" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <CustomInputText @bind-Value="exampleModel.Name" />

    <button type="submit">Submit</button>
</EditForm>

<p>
    CurrentValue: @exampleModel.Name
</p>

@code {
    private ExampleModel exampleModel = new ExampleModel();

    private void HandleValidSubmit()
    {
        Console.WriteLine("OnValidSubmit");
    }

    public class ExampleModel
    {
        [Required]
        [StringLength(10, ErrorMessage = "Name is too long.")]
        public string Name { get; set; }
    }
}
```

## <a name="radio-buttons"></a>Radyo düğmeleri

Bir formda radyo düğmeleriyle çalışırken, radyo düğmeleri bir grup olarak değerlendirildiğinden veri bağlama diğer öğelerden farklı işlenir. Her radyo düğmesinin değeri sabittir, ancak radyo düğmesi grubunun değeri seçili radyo düğmesinin değeridir. Aşağıdaki örnek, aşağıdakilerin nasıl yapılacağını göstermektedir:

* Radyo düğmesi grubu için veri bağlamayı işleyin.
* Özel bir bileşen kullanarak doğrulamayı destekler `InputRadio` .

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

Aşağıdaki, <xref:Microsoft.AspNetCore.Components.Forms.EditForm> `InputRadio` kullanıcıdan bir derecelendirme almak ve doğrulamak için önceki bileşeni kullanır:

```razor
@page "/RadioButtonExample"
@using System.ComponentModel.DataAnnotations

<h1>Radio Button Group Test</h1>

<EditForm Model="model" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    @for (int i = 1; i <= 5; i++)
    {
        <label>
            <InputRadio name="rate" SelectedValue="i" @bind-Value="model.Rating" />
            @i
        </label>
    }

    <button type="submit">Submit</button>
</EditForm>

<p>You chose: @model.Rating</p>

@code {
    private Model model = new Model();

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

## <a name="binding-select-element-options-to-c-object-null-values"></a>`<select>`Öğe seçeneklerini C# nesne değerlerine bağlama `null`

Bir `<select>` öğe seçeneği değerini C# nesne değeri olarak göstermek için herhangi bir mümkün değildir `null` , çünkü:

* HTML özniteliklerinin değerleri olamaz `null` . HTML olarak en yakın eşdeğeri, `null` ÖĞESINDEN html özniteliğinin yokluğunda olur `value` `<option>` .
* `<option>`Özniteliği olmayan bir seçerken `value` , tarayıcı değeri bu öğenin *metin içeriği* olarak değerlendirir `<option>` .

BlazorFramework, aşağıdakileri içereceği varsayılan davranışı bastırmak için denenmez:

* Çerçevede özel durum geçici çözümleri zinciri oluşturma.
* Geçerli çerçeve davranışında son değişiklikler.

::: moniker range=">= aspnetcore-5.0"

HTML 'deki en büyük plausible `null` eşdeğeri boş bir *dizedir* `value` . BlazorÇerçeve, `null` bir değerine iki yönlü bağlama yönelik boş dize dönüştürmelerini işler `<select>` .

::: moniker-end

::: moniker range="< aspnetcore-5.0"

BlazorÇerçeve, `null` iki yönlü bir değere bağlamayı denerken boş dize dönüştürmeleri için otomatik olarak işleme yapmaz `<select>` . Daha fazla bilgi için bkz. [ `<select>` null değere bağlamayı çözme (DotNet/aspnetcore #23221)](https://github.com/dotnet/aspnetcore/pull/23221).

::: moniker-end

## <a name="validation-support"></a>Doğrulama desteği

<xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>Bileşen, Basamaklandırılan veri açıklamalarını kullanarak doğrulama desteğini iliştirir <xref:Microsoft.AspNetCore.Components.Forms.EditContext> . Veri ek açıklamalarını kullanarak doğrulama desteğinin etkinleştirilmesi bu açık hareketi gerektirir. Veri ek açıklamalarıyla farklı bir doğrulama sistemi kullanmak için, öğesini <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> özel bir uygulamayla değiştirin. ASP.NET Core uygulama, başvuru kaynağında İnceleme için kullanılabilir: [`DataAnnotationsValidator`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs) / [`AddDataAnnotationsValidation`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs) . Başvuru kaynağına yapılan önceki bağlantılar, deponun `master` dalından kod sağlar ve bu, ürün biriminin ASP.NET Core sonraki sürümü için geçerli geliştirmeyi temsil eder. Farklı bir sürümün dalını seçmek için GitHub dal seçicisini (örneğin `release/3.1` ) kullanın.

Blazoriki tür doğrulama gerçekleştirir:

* *Alan doğrulama* , Kullanıcı bir alanın dışına eklendiğinde gerçekleştirilir. Alan doğrulama sırasında, <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> bileşen bildirilen tüm doğrulama sonuçlarını alanla ilişkilendirir.
* Kullanıcı formu gönderdiğinde *model doğrulaması* gerçekleştirilir. Model doğrulama sırasında, <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> Bileşen, doğrulama sonucunun raporlandığı üye adına göre alanı belirlemeyi dener. Tek bir üyeyle ilişkilendirilmeyen doğrulama sonuçları, bir alan yerine modeliyle ilişkilendirilir.

### <a name="validation-summary-and-validation-message-components"></a>Doğrulama özeti ve doğrulama Iletisi bileşenleri

<xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary>Bileşen, [doğrulama özeti etiketi Yardımcısı](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper)'na benzer olan tüm doğrulama iletilerini özetler:

```razor
<ValidationSummary />
```

Parametresi ile belirli bir model için çıkış doğrulama iletileri `Model` :
  
```razor
<ValidationSummary Model="@starship" />
```

<xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601>Bileşeni, [doğrulama Iletisi etiketi Yardımcısı](xref:mvc/views/working-with-forms#the-validation-message-tag-helper)'na benzer şekilde belirli bir alan için doğrulama iletileri görüntüler. Özniteliği ile doğrulama için alanı `For` ve model özelliğini adlandırırken bir lambda ifadesini belirtin:

```razor
<ValidationMessage For="@(() => starship.MaximumAccommodation)" />
```

<xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601>Ve <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> bileşenleri, rastgele öznitelikleri destekler. Bir bileşen parametresiyle eşleşmeyen herhangi bir öznitelik oluşturulan `<div>` or `<ul>` öğesine eklenir.

Uygulamanın stil sayfasındaki (veya) doğrulama iletilerinin stilini denetleyin `wwwroot/css/app.css` `wwwroot/css/site.css` . Varsayılan `validation-message` sınıf, doğrulama iletilerinin metin rengini kırmızı olarak ayarlar:

```css
.validation-message {
    color: red;
}
```

### <a name="custom-validation-attributes"></a>Özel doğrulama öznitelikleri

Bir doğrulama sonucunun [özel bir doğrulama özniteliği](xref:mvc/models/validation#custom-attributes)kullanılırken bir alanla doğru şekilde ilişkilendirildiğinden emin olmak için, şunu oluştururken doğrulama bağlamını geçirin <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> <xref:System.ComponentModel.DataAnnotations.ValidationResult> :

```csharp
using System;
using System.ComponentModel.DataAnnotations;

private class CustomValidator : ValidationAttribute
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

> [!NOTE]
> <xref:System.ComponentModel.DataAnnotations.ValidationContext.GetService%2A?displayProperty=nameWithType>, `null` değeridir. Yönteminde doğrulama için ekleme Hizmetleri `IsValid` desteklenmiyor.

### <a name="no-locblazor-data-annotations-validation-package"></a>Blazorveri ek açıklamaları doğrulama paketi

, [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) Bileşeni kullanarak doğrulama deneyimini boşlukları dolduran bir pakettir <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> . Paket şu anda *deneysel*.

### <a name="compareproperty-attribute"></a>[CompareProperty] özniteliği

<xref:System.ComponentModel.DataAnnotations.CompareAttribute> <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> Doğrulama sonucunu belirli bir üyeyle ilişkilendirmediği için bileşen ile iyi çalışmaz. Bu, alan düzeyi doğrulama ve tüm modelin bir gönderme sırasında doğrulanması arasındaki tutarsız davranışa neden olabilir. [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) *Deneysel* paket, `ComparePropertyAttribute` Bu sınırlamalar etrafında çalışabilen ek bir doğrulama özniteliği sunar. Bir Blazor uygulamada, `[CompareProperty]` özniteliği için doğrudan değiştirme olur [`[Compare]`](xref:System.ComponentModel.DataAnnotations.CompareAttribute) .

### <a name="nested-models-collection-types-and-complex-types"></a>İç içe modeller, koleksiyon türleri ve karmaşık türler

Blazoryerleşik olan veri açıklamalarını kullanarak form girişini doğrulama desteği sağlar <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> . Ancak, <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> yalnızca koleksiyonun üst düzey özelliklerini, koleksiyon veya karmaşık tür özellikleri olmayan forma doğrular.

Koleksiyon ve karmaşık tür özellikleri dahil olmak üzere, bağlantılı modelin tüm nesne grafiğini doğrulamak için `ObjectGraphDataAnnotationsValidator` *deneysel* paket tarafından sunulan öğesini kullanın [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) :

```razor
<EditForm Model="@model" OnValidSubmit="HandleValidSubmit">
    <ObjectGraphDataAnnotationsValidator />
    ...
</EditForm>
```

İle model özelliklerine açıklama ekleyin `[ValidateComplexType]` . Aşağıdaki model sınıflarında, `ShipDescription` sınıfı, model forma bağlandığında doğrulanacak ek veri açıklamalarını içerir:

`Starship.cs`:

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

`ShipDescription.cs`:

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

* <xref:Microsoft.AspNetCore.Components.Forms.EditContext>Bileşen başlatıldığında modeli atamak için formunu kullanın.
* <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged>Gönder düğmesini etkinleştirmek ve devre dışı bırakmak için bağlam geri aramasında formu doğrulayın.
* Yöntemi içindeki olay işleyicisinin üstünden geri dön `Dispose` . Daha fazla bilgi için bkz. <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.

```razor
@implements IDisposable

<EditForm EditContext="@editContext">
    <DataAnnotationsValidator />
    <ValidationSummary />

    ...

    <button type="submit" disabled="@formInvalid">Submit</button>
</EditForm>

@code {
    private Starship starship = new Starship();
    private bool formInvalid = true;
    private EditContext editContext;

    protected override void OnInitialized()
    {
        editContext = new EditContext(starship);
        editContext.OnFieldChanged += HandleFieldChanged;
    }

    private void HandleFieldChanged(object sender, FieldChangedEventArgs e)
    {
        formInvalid = !editContext.Validate();
        StateHasChanged();
    }

    public void Dispose()
    {
        editContext.OnFieldChanged -= HandleFieldChanged;
    }
}
```

Yukarıdaki örnekte, şu şekilde ayarlayın `formInvalid` `false` :

* Form geçerli varsayılan değerlerle önceden yüklenir.
* Form yüklendiğinde Gönder düğmesinin etkinleştirilmesini istiyorsunuz.

Önceki yaklaşımın yan etkisi, <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> Kullanıcı herhangi bir alanla etkileşime geçtiğinde bileşen geçersiz alanlarla doldurulmuştur. Bu senaryoya aşağıdaki yollarla değinilerek şunlar olabilir:

* <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary>Form üzerinde bir bileşen kullanmayın.
* <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary>Gönder düğmesi seçildiğinde bileşeni görünür hale getirin (örneğin, bir `HandleValidSubmit` yöntemde).

```razor
<EditForm EditContext="@editContext" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary style="@displaySummary" />

    ...

    <button type="submit" disabled="@formInvalid">Submit</button>
</EditForm>

@code {
    private string displaySummary = "display:none";

    ...

    private void HandleValidSubmit()
    {
        displaySummary = "display:block";
    }
}
```

## <a name="troubleshoot"></a>Sorun giderme

> InvalidOperationException: EditForm bir model parametresi veya bir EditContext parametresi gerektiriyor, ancak her ikisini de içermemelidir.

Veya olduğunu doğrulayın <xref:Microsoft.AspNetCore.Components.Forms.EditForm> <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> <xref:Microsoft.AspNetCore.Components.Forms.EditContext> .

Forma atama yaparken <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> , aşağıdaki örnekte gösterildiği gibi model türünün örneği oluşturulmuş olduğunu doğrulayın:

```csharp
private ExampleModel exampleModel = new ExampleModel();
```
