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
# <a name="aspnet-core-blazor-forms-and-validation"></a><span data-ttu-id="4f898-103">ASP.NET Core Blazor formları ve doğrulama</span><span class="sxs-lookup"><span data-stu-id="4f898-103">ASP.NET Core Blazor forms and validation</span></span>

<span data-ttu-id="4f898-104">Yazar: [Daniel Roth](https://github.com/danroth27) ve [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="4f898-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="4f898-105">Formlar ve doğrulama Blazor'da [veri ek açıklamaları](xref:mvc/models/validation)kullanılarak desteklenir.</span><span class="sxs-lookup"><span data-stu-id="4f898-105">Forms and validation are supported in Blazor using [data annotations](xref:mvc/models/validation).</span></span>

<span data-ttu-id="4f898-106">Aşağıdaki `ExampleModel` tür, veri ek açıklamalarını kullanarak doğrulama mantığını tanımlar:</span><span class="sxs-lookup"><span data-stu-id="4f898-106">The following `ExampleModel` type defines validation logic using data annotations:</span></span>

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

<span data-ttu-id="4f898-107">Form `EditForm` bileşeni kullanılarak tanımlanır.</span><span class="sxs-lookup"><span data-stu-id="4f898-107">A form is defined using the `EditForm` component.</span></span> <span data-ttu-id="4f898-108">Aşağıdaki form tipik öğeleri, bileşenleri ve Razor kodunu gösterir:</span><span class="sxs-lookup"><span data-stu-id="4f898-108">The following form demonstrates typical elements, components, and Razor code:</span></span>

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

<span data-ttu-id="4f898-109">Önceki örnekte:</span><span class="sxs-lookup"><span data-stu-id="4f898-109">In the preceding example:</span></span>

* <span data-ttu-id="4f898-110">Form, türde tanımlanan doğrulamayı kullanarak `name` alandaki kullanıcı `ExampleModel` girişini doğrular.</span><span class="sxs-lookup"><span data-stu-id="4f898-110">The form validates user input in the `name` field using the validation defined in the `ExampleModel` type.</span></span> <span data-ttu-id="4f898-111">Model bileşenin `@code` bloğunda oluşturulur ve özel bir alanda`_exampleModel`tutulur ( ).</span><span class="sxs-lookup"><span data-stu-id="4f898-111">The model is created in the component's `@code` block and held in a private field (`_exampleModel`).</span></span> <span data-ttu-id="4f898-112">Alan `Model` `<EditForm>` öğenin özniteliğine atanır.</span><span class="sxs-lookup"><span data-stu-id="4f898-112">The field is assigned to the `Model` attribute of the `<EditForm>` element.</span></span>
* <span data-ttu-id="4f898-113">Bileşenin `InputText` `@bind-Value` bağlamaları:</span><span class="sxs-lookup"><span data-stu-id="4f898-113">The `InputText` component's `@bind-Value` binds:</span></span>
  * <span data-ttu-id="4f898-114">Model özelliği`_exampleModel.Name`( ) `InputText` bileşenin `Value` özelliğine.</span><span class="sxs-lookup"><span data-stu-id="4f898-114">The model property (`_exampleModel.Name`) to the `InputText` component's `Value` property.</span></span>
  * <span data-ttu-id="4f898-115">Bileşenin `InputText` `ValueChanged` özelliğine bir değişiklik olayı temsilcisi.</span><span class="sxs-lookup"><span data-stu-id="4f898-115">A change event delegate to the `InputText` component's `ValueChanged` property.</span></span>
* <span data-ttu-id="4f898-116">Bileşen, `DataAnnotationsValidator` veri ek açıklamalarını kullanarak doğrulama desteği ne bağlar.</span><span class="sxs-lookup"><span data-stu-id="4f898-116">The `DataAnnotationsValidator` component attaches validation support using data annotations.</span></span>
* <span data-ttu-id="4f898-117">Bileşen `ValidationSummary` doğrulama iletilerini özetler.</span><span class="sxs-lookup"><span data-stu-id="4f898-117">The `ValidationSummary` component summarizes validation messages.</span></span>
* <span data-ttu-id="4f898-118">`HandleValidSubmit`form başarılı bir şekilde gönderdiğinde (doğrulamayı geçtiğinde) tetiklenir.</span><span class="sxs-lookup"><span data-stu-id="4f898-118">`HandleValidSubmit` is triggered when the form successfully submits (passes validation).</span></span>

<span data-ttu-id="4f898-119">Kullanıcı girdisini almak ve doğrulamak için bir dizi yerleşik giriş bileşeni kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="4f898-119">A set of built-in input components are available to receive and validate user input.</span></span> <span data-ttu-id="4f898-120">Girişler değiştirildiğinde ve form gönderildiğinde doğrulanır.</span><span class="sxs-lookup"><span data-stu-id="4f898-120">Inputs are validated when they're changed and when a form is submitted.</span></span> <span data-ttu-id="4f898-121">Kullanılabilir giriş bileşenleri aşağıdaki tabloda gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="4f898-121">Available input components are shown in the following table.</span></span>

| <span data-ttu-id="4f898-122">Giriş bileşeni</span><span class="sxs-lookup"><span data-stu-id="4f898-122">Input component</span></span> | <span data-ttu-id="4f898-123">Olarak işlenmiştir&hellip;</span><span class="sxs-lookup"><span data-stu-id="4f898-123">Rendered as&hellip;</span></span>       |
| --------------- | ------------------------- |
| `InputText`     | `<input>`                 |
| `InputTextArea` | `<textarea>`              |
| `InputSelect`   | `<select>`                |
| `InputNumber`   | `<input type="number">`   |
| `InputCheckbox` | `<input type="checkbox">` |
| `InputDate`     | `<input type="date">`     |

<span data-ttu-id="4f898-124">Rasgele öznitelikleri desteklemek de `EditForm`dahil olmak üzere tüm giriş bileşenleri.</span><span class="sxs-lookup"><span data-stu-id="4f898-124">All of the input components, including `EditForm`, support arbitrary attributes.</span></span> <span data-ttu-id="4f898-125">Bir bileşen parametresi ile eşleşmeyen herhangi bir öznitelik, işlenen HTML öğesine eklenir.</span><span class="sxs-lookup"><span data-stu-id="4f898-125">Any attribute that doesn't match a component parameter is added to the rendered HTML element.</span></span>

<span data-ttu-id="4f898-126">Giriş bileşenleri, edit'te doğrulama ve CSS sınıflarını alan durumunu yansıtacak şekilde değiştirmek için varsayılan davranış sağlar.</span><span class="sxs-lookup"><span data-stu-id="4f898-126">Input components provide default behavior for validating on edit and changing their CSS class to reflect the field state.</span></span> <span data-ttu-id="4f898-127">Bazı bileşenler yararlı ayrışma mantığı içerir.</span><span class="sxs-lookup"><span data-stu-id="4f898-127">Some components include useful parsing logic.</span></span> <span data-ttu-id="4f898-128">Örneğin, `InputDate` ve `InputNumber` doğrulama hataları olarak kaydederek incelikle ayrılmaz değerleri işlemek.</span><span class="sxs-lookup"><span data-stu-id="4f898-128">For example, `InputDate` and `InputNumber` handle unparseable values gracefully by registering them as validation errors.</span></span> <span data-ttu-id="4f898-129">Null değerleri kabul edebilen türler de hedef alanın `int?`nullability destekler (örneğin,).</span><span class="sxs-lookup"><span data-stu-id="4f898-129">Types that can accept null values also support nullability of the target field (for example, `int?`).</span></span>

<span data-ttu-id="4f898-130">Aşağıdaki `Starship` tür, öncekinden `ExampleModel`daha büyük bir özellik kümesi ve veri ek açıklamaları kullanarak doğrulama mantığını tanımlar:</span><span class="sxs-lookup"><span data-stu-id="4f898-130">The following `Starship` type defines validation logic using a larger set of properties and data annotations than the earlier `ExampleModel`:</span></span>

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

<span data-ttu-id="4f898-131">Önceki örnekte, `Description` hiçbir veri ek açıklamaları olduğundan isteğe bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="4f898-131">In the preceding example, `Description` is optional because no data annotations are present.</span></span>

<span data-ttu-id="4f898-132">Aşağıdaki form, modelde tanımlanan doğrulamayı kullanarak `Starship` kullanıcı girdisini doğrular:</span><span class="sxs-lookup"><span data-stu-id="4f898-132">The following form validates user input using the validation defined in the `Starship` model:</span></span>

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

<span data-ttu-id="4f898-133">Bu, `EditForm` hangi `EditContext` alanların değiştirildiği ve geçerli doğrulama iletileri de dahil olmak üzere, edit işlemiyle ilgili meta verileri izleyen basamaklı bir [değer](xref:blazor/components#cascading-values-and-parameters) oluşturur.</span><span class="sxs-lookup"><span data-stu-id="4f898-133">The `EditForm` creates an `EditContext` as a [cascading value](xref:blazor/components#cascading-values-and-parameters) that tracks metadata about the edit process, including which fields have been modified and the current validation messages.</span></span> <span data-ttu-id="4f898-134">Ayrıca `EditForm` geçerli ve geçersiz gönderiler için`OnValidSubmit`uygun `OnInvalidSubmit`etkinlikler sağlar ( , ).</span><span class="sxs-lookup"><span data-stu-id="4f898-134">The `EditForm` also provides convenient events for valid and invalid submits (`OnValidSubmit`, `OnInvalidSubmit`).</span></span> <span data-ttu-id="4f898-135">Alternatif olarak, `OnSubmit` doğrulamayı tetiklemek ve alan değerlerini özel doğrulama koduyla denetlemek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="4f898-135">Alternatively, use `OnSubmit` to trigger the validation and check field values with custom validation code.</span></span>

<span data-ttu-id="4f898-136">Aşağıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="4f898-136">In the following example:</span></span>

* <span data-ttu-id="4f898-137">`HandleSubmit` **Gönder** düğmesi seçildiğinde yöntem çalışır.</span><span class="sxs-lookup"><span data-stu-id="4f898-137">The `HandleSubmit` method runs when the **Submit** button is selected.</span></span>
* <span data-ttu-id="4f898-138">Form, `EditContext`formun.</span><span class="sxs-lookup"><span data-stu-id="4f898-138">The form is validated using the form's `EditContext`.</span></span>
* <span data-ttu-id="4f898-139">Form, sunucuda `ServerValidate` bir web `EditContext` API bitiş noktası çağıran yönteme geçerek daha da doğrulanır *(gösterilmez).*</span><span class="sxs-lookup"><span data-stu-id="4f898-139">The form is further validated by passing the `EditContext` to the `ServerValidate` method that calls a web API endpoint on the server (*not shown*).</span></span>
* <span data-ttu-id="4f898-140">Ek kod, istemci ve sunucu tarafı doğrulamasının sonucuna bağlı `isValid`olarak denetleyerek çalıştırılır.</span><span class="sxs-lookup"><span data-stu-id="4f898-140">Additional code is run depending on the result of the client- and server-side validation by checking `isValid`.</span></span>

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

## <a name="inputtext-based-on-the-input-event"></a><span data-ttu-id="4f898-141">Giriş olayına göre Giriş Metni</span><span class="sxs-lookup"><span data-stu-id="4f898-141">InputText based on the input event</span></span>

<span data-ttu-id="4f898-142">Olay `InputText` yerine olayı kullanan özel bir bileşen oluşturmak için bileşeni kullanın. `input` `change`</span><span class="sxs-lookup"><span data-stu-id="4f898-142">Use the `InputText` component to create a custom component that uses the `input` event instead of the `change` event.</span></span>

<span data-ttu-id="4f898-143">Aşağıdaki biçimlendirmeye sahip bir bileşen oluşturun ve `InputText` bileşeni kullanıldığı gibi kullanın:</span><span class="sxs-lookup"><span data-stu-id="4f898-143">Create a component with the following markup, and use the component just as `InputText` is used:</span></span>

```razor
@inherits InputText

<input 
    @attributes="AdditionalAttributes" 
    class="@CssClass" 
    value="@CurrentValue" 
    @oninput="EventCallback.Factory.CreateBinder<string>(
        this, __value => CurrentValueAsString = __value, CurrentValueAsString)" />
```

## <a name="work-with-radio-buttons"></a><span data-ttu-id="4f898-144">Radyo düğmeleriyle çalışma</span><span class="sxs-lookup"><span data-stu-id="4f898-144">Work with radio buttons</span></span>

<span data-ttu-id="4f898-145">Bir formda radyo düğmeleri ile çalışırken, radyo düğmeleri bir grup olarak değerlendirildiğinden, veri bağlama diğer öğelerden farklı olarak işlenir.</span><span class="sxs-lookup"><span data-stu-id="4f898-145">When working with radio buttons in a form, data binding is handled differently than other elements because radio buttons are evaluated as a group.</span></span> <span data-ttu-id="4f898-146">Her radyo düğmesinin değeri sabittir, ancak radyo düğme grubunun değeri seçilen radyo düğmesinin değeridir.</span><span class="sxs-lookup"><span data-stu-id="4f898-146">The value of each radio button is fixed, but the value of the radio button group is the value of the selected radio button.</span></span> <span data-ttu-id="4f898-147">Aşağıdaki örnek, nasıl yapılacağını gösterir:</span><span class="sxs-lookup"><span data-stu-id="4f898-147">The following example shows how to:</span></span>

* <span data-ttu-id="4f898-148">Bir radyo düğmesi grubu için veri bağlamayı işleme.</span><span class="sxs-lookup"><span data-stu-id="4f898-148">Handle data binding for a radio button group.</span></span>
* <span data-ttu-id="4f898-149">Özel `InputRadio` bir bileşen kullanarak doğrulamayı destekleyin.</span><span class="sxs-lookup"><span data-stu-id="4f898-149">Support validation using a custom `InputRadio` component.</span></span>

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

<span data-ttu-id="4f898-150">Aşağıdaki, `EditForm` kullanıcıdan `InputRadio` bir derecelendirme elde etmek ve doğrulamak için önceki bileşeni kullanır:</span><span class="sxs-lookup"><span data-stu-id="4f898-150">The following `EditForm` uses the preceding `InputRadio` component to obtain and validate a rating from the user:</span></span>

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

## <a name="validation-support"></a><span data-ttu-id="4f898-151">Doğrulama desteği</span><span class="sxs-lookup"><span data-stu-id="4f898-151">Validation support</span></span>

<span data-ttu-id="4f898-152">Bileşen, `DataAnnotationsValidator` basamaklı veri ek açıklamalarını kullanarak doğrulama `EditContext`desteği ne bağlar.</span><span class="sxs-lookup"><span data-stu-id="4f898-152">The `DataAnnotationsValidator` component attaches validation support using data annotations to the cascaded `EditContext`.</span></span> <span data-ttu-id="4f898-153">Veri ek açıklamalarını kullanarak doğrulama desteği etkinleştirmek bu açık hareketi gerektirir.</span><span class="sxs-lookup"><span data-stu-id="4f898-153">Enabling support for validation using data annotations requires this explicit gesture.</span></span> <span data-ttu-id="4f898-154">Veri ek açıklamalarından farklı bir doğrulama sistemi `DataAnnotationsValidator` kullanmak için, özel bir uygulama yla değiştirin.</span><span class="sxs-lookup"><span data-stu-id="4f898-154">To use a different validation system than data annotations, replace the `DataAnnotationsValidator` with a custom implementation.</span></span> <span data-ttu-id="4f898-155">ASP.NET Core uygulaması referans kaynağında incelenmek için kullanılabilir: [DataAnnotationsValidator](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="4f898-155">The ASP.NET Core implementation is available for inspection in the reference source: [DataAnnotationsValidator](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).</span></span>

Blazor<span data-ttu-id="4f898-156">iki tür doğrulama gerçekleştirir:</span><span class="sxs-lookup"><span data-stu-id="4f898-156"> performs two types of validation:</span></span>

* <span data-ttu-id="4f898-157">*Alan doğrulama,* kullanıcı bir alanın dışına çıktığında gerçekleştirilir.</span><span class="sxs-lookup"><span data-stu-id="4f898-157">*Field validation* is performed when the user tabs out of a field.</span></span> <span data-ttu-id="4f898-158">Alan doğrulama sırasında `DataAnnotationsValidator` bileşen, bildirilen tüm doğrulama sonuçlarını alanla ilişkilendirer.</span><span class="sxs-lookup"><span data-stu-id="4f898-158">During field validation, the `DataAnnotationsValidator` component associates all reported validation results with the field.</span></span>
* <span data-ttu-id="4f898-159">*Kullanıcı* formu gönderdiğinde model doğrulama gerçekleştirilir.</span><span class="sxs-lookup"><span data-stu-id="4f898-159">*Model validation* is performed when the user submits the form.</span></span> <span data-ttu-id="4f898-160">Model doğrulama sırasında `DataAnnotationsValidator` bileşen, doğrulama sonucunun bildirdiği üye adı temel alınca alanı belirlemeye çalışır.</span><span class="sxs-lookup"><span data-stu-id="4f898-160">During model validation, the `DataAnnotationsValidator` component attempts to determine the field based on the member name that the validation result reports.</span></span> <span data-ttu-id="4f898-161">Tek bir üyeyle ilişkilendirilmeen doğrulama sonuçları, bir alan yerine modelle ilişkilidir.</span><span class="sxs-lookup"><span data-stu-id="4f898-161">Validation results that aren't associated with an individual member are associated with the model rather than a field.</span></span>

### <a name="validation-summary-and-validation-message-components"></a><span data-ttu-id="4f898-162">Doğrulama Özeti ve Doğrulama İletisi bileşenleri</span><span class="sxs-lookup"><span data-stu-id="4f898-162">Validation Summary and Validation Message components</span></span>

<span data-ttu-id="4f898-163">Bileşen, `ValidationSummary` [Doğrulama Özeti Etiket Yardımcısı'na](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper)benzer tüm doğrulama iletilerini özetler:</span><span class="sxs-lookup"><span data-stu-id="4f898-163">The `ValidationSummary` component summarizes all validation messages, which is similar to the [Validation Summary Tag Helper](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper):</span></span>

```razor
<ValidationSummary />
```

<span data-ttu-id="4f898-164">`Model` Parametreile belirli bir model için çıkış doğrulama iletileri:</span><span class="sxs-lookup"><span data-stu-id="4f898-164">Output validation messages for a specific model with the `Model` parameter:</span></span>
  
```razor
<ValidationSummary Model="@_starship" />
```

<span data-ttu-id="4f898-165">Bileşen, `ValidationMessage` [Doğrulama İleti Stag Helper'a](xref:mvc/views/working-with-forms#the-validation-message-tag-helper)benzer belirli bir alan için doğrulama iletileri görüntüler.</span><span class="sxs-lookup"><span data-stu-id="4f898-165">The `ValidationMessage` component displays validation messages for a specific field, which is similar to the [Validation Message Tag Helper](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span></span> <span data-ttu-id="4f898-166">Öznitelik ve model özelliği `For` adlandırma bir lambda ifadesi ile doğrulama için alan belirtin:</span><span class="sxs-lookup"><span data-stu-id="4f898-166">Specify the field for validation with the `For` attribute and a lambda expression naming the model property:</span></span>

```razor
<ValidationMessage For="@(() => _starship.MaximumAccommodation)" />
```

<span data-ttu-id="4f898-167">Ve `ValidationMessage` `ValidationSummary` bileşenleri rasgele öznitelikleri destekler.</span><span class="sxs-lookup"><span data-stu-id="4f898-167">The `ValidationMessage` and `ValidationSummary` components support arbitrary attributes.</span></span> <span data-ttu-id="4f898-168">Bileşen parametresi ile eşleşmeyen öznitelik, oluşturulan `<div>` veya `<ul>` öğeye eklenir.</span><span class="sxs-lookup"><span data-stu-id="4f898-168">Any attribute that doesn't match a component parameter is added to the generated `<div>` or `<ul>` element.</span></span>

### <a name="custom-validation-attributes"></a><span data-ttu-id="4f898-169">Özel doğrulama öznitelikleri</span><span class="sxs-lookup"><span data-stu-id="4f898-169">Custom validation attributes</span></span>

<span data-ttu-id="4f898-170">Bir doğrulama sonucunun, özel bir [doğrulama özniteliği](xref:mvc/models/validation#custom-attributes)kullanırken bir alanla doğru şekilde ilişkilendirildiğinden emin olmak için, aşağıdakileri <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> oluştururken doğrulama bağlamını <xref:System.ComponentModel.DataAnnotations.ValidationResult>geçirin:</span><span class="sxs-lookup"><span data-stu-id="4f898-170">To ensure that a validation result is correctly associated with a field when using a [custom validation attribute](xref:mvc/models/validation#custom-attributes), pass the validation context's <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> when creating the <xref:System.ComponentModel.DataAnnotations.ValidationResult>:</span></span>

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

### <a name="opno-locblazor-data-annotations-validation-package"></a>Blazor<span data-ttu-id="4f898-171">veri ek açıklamaları doğrulama paketi</span><span class="sxs-lookup"><span data-stu-id="4f898-171"> data annotations validation package</span></span>

<span data-ttu-id="4f898-172">[Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) `DataAnnotationsValidator` bileşeni kullanarak doğrulama deneyimi boşlukları dolduran bir pakettir.</span><span class="sxs-lookup"><span data-stu-id="4f898-172">The [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) is a package that fills validation experience gaps using the `DataAnnotationsValidator` component.</span></span> <span data-ttu-id="4f898-173">Paket şu anda *deneysel.*</span><span class="sxs-lookup"><span data-stu-id="4f898-173">The package is currently *experimental*.</span></span>

### <a name="compareproperty-attribute"></a><span data-ttu-id="4f898-174">[CompareProperty] özniteliği</span><span class="sxs-lookup"><span data-stu-id="4f898-174">[CompareProperty] attribute</span></span>

<span data-ttu-id="4f898-175">Doğrulama <xref:System.ComponentModel.DataAnnotations.CompareAttribute> sonucunu belirli bir `DataAnnotationsValidator` üyeyle ilişkilendirmediği için bileşenle iyi çalışmaz.</span><span class="sxs-lookup"><span data-stu-id="4f898-175">The <xref:System.ComponentModel.DataAnnotations.CompareAttribute> doesn't work well with the `DataAnnotationsValidator` component because it doesn't associate the validation result with a specific member.</span></span> <span data-ttu-id="4f898-176">Bu, alan düzeyinde doğrulama ile tüm model in bir gönderide doğrulandığında tutarsız davranışlara neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="4f898-176">This can result in inconsistent behavior between field-level validation and when the entire model is validated on a submit.</span></span> <span data-ttu-id="4f898-177">[Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) *deneysel* paketi ek bir doğrulama özniteliği tanıttı, `ComparePropertyAttribute`bu sınırlamalar etrafında çalışır.</span><span class="sxs-lookup"><span data-stu-id="4f898-177">The [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) *experimental* package introduces an additional validation attribute, `ComparePropertyAttribute`, that works around these limitations.</span></span> <span data-ttu-id="4f898-178">Bir Blazor uygulamada, `[CompareProperty]` öznitelik için `[Compare]` doğrudan bir yedektir.</span><span class="sxs-lookup"><span data-stu-id="4f898-178">In a Blazor app, `[CompareProperty]` is a direct replacement for the `[Compare]` attribute.</span></span>

### <a name="nested-models-collection-types-and-complex-types"></a><span data-ttu-id="4f898-179">İç içe modeller, koleksiyon türleri ve karmaşık türleri</span><span class="sxs-lookup"><span data-stu-id="4f898-179">Nested models, collection types, and complex types</span></span>

Blazor<span data-ttu-id="4f898-180">yerleşik veri ek açıklamaları kullanarak form girişi doğrulama desteği `DataAnnotationsValidator`sağlar.</span><span class="sxs-lookup"><span data-stu-id="4f898-180"> provides support for validating form input using data annotations with the built-in `DataAnnotationsValidator`.</span></span> <span data-ttu-id="4f898-181">Ancak, `DataAnnotationsValidator` yalnızca koleksiyon veya karmaşık tür özellikleri olmayan forma bağlı modelin üst düzey özelliklerini doğrular.</span><span class="sxs-lookup"><span data-stu-id="4f898-181">However, the `DataAnnotationsValidator` only validates top-level properties of the model bound to the form that aren't collection- or complex-type properties.</span></span>

<span data-ttu-id="4f898-182">Ciltli modelin toplama ve karmaşık tür özellikleri de dahil olmak üzere `ObjectGraphDataAnnotationsValidator` tüm nesne grafiğini doğrulamak *için, deneysel* [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) paketi tarafından sağlanan kullanın:</span><span class="sxs-lookup"><span data-stu-id="4f898-182">To validate the bound model's entire object graph, including collection- and complex-type properties, use the `ObjectGraphDataAnnotationsValidator` provided by the *experimental* [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) package:</span></span>

```razor
<EditForm Model="@_model" OnValidSubmit="HandleValidSubmit">
    <ObjectGraphDataAnnotationsValidator />
    ...
</EditForm>
```

<span data-ttu-id="4f898-183">Model özelliklerini `[ValidateComplexType]`' le açıklama</span><span class="sxs-lookup"><span data-stu-id="4f898-183">Annotate model properties with `[ValidateComplexType]`.</span></span> <span data-ttu-id="4f898-184">Aşağıdaki model sınıflarında, `ShipDescription` model in forma ne zaman bağlı olduğunu doğrulamak için ek veri ek açıklamaları içerir:</span><span class="sxs-lookup"><span data-stu-id="4f898-184">In the following model classes, the `ShipDescription` class contains additional data annotations to validate when the model is bound to the form:</span></span>

<span data-ttu-id="4f898-185">*Starship.cs*:</span><span class="sxs-lookup"><span data-stu-id="4f898-185">*Starship.cs*:</span></span>

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

<span data-ttu-id="4f898-186">*ShipDescription.cs*:</span><span class="sxs-lookup"><span data-stu-id="4f898-186">*ShipDescription.cs*:</span></span>

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

### <a name="enable-the-submit-button-based-on-form-validation"></a><span data-ttu-id="4f898-187">Form doğrulamaya göre gönder düğmesini etkinleştirme</span><span class="sxs-lookup"><span data-stu-id="4f898-187">Enable the submit button based on form validation</span></span>

<span data-ttu-id="4f898-188">Form doğrulamasına dayalı gönder düğmesini etkinleştirmek ve devre dışı kalımsağlamak için:</span><span class="sxs-lookup"><span data-stu-id="4f898-188">To enable and disable the submit button based on form validation:</span></span>

* <span data-ttu-id="4f898-189">Bileşen başharfe `EditContext` geçtiğinde modeli atamak için formun kini kullanın.</span><span class="sxs-lookup"><span data-stu-id="4f898-189">Use the form's `EditContext` to assign the model when the component is initialized.</span></span>
* <span data-ttu-id="4f898-190">Gönder düğmesini etkinleştirmek `OnFieldChanged` ve devre dışı kılarak bağlamın geri aramasında formu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="4f898-190">Validate the form in the context's `OnFieldChanged` callback to enable and disable the submit button.</span></span>
* <span data-ttu-id="4f898-191">Yöntemdeki olay işleyicisini `Dispose` boşaltın.</span><span class="sxs-lookup"><span data-stu-id="4f898-191">Unhook the event handler in the `Dispose` method.</span></span> <span data-ttu-id="4f898-192">Daha fazla bilgi için bkz. <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span><span class="sxs-lookup"><span data-stu-id="4f898-192">For more information, see <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span></span>

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

<span data-ttu-id="4f898-193">Yukarıdaki örnekte, aşağıdakileri `false` yapacak şekilde ayarlanır: `_formInvalid`</span><span class="sxs-lookup"><span data-stu-id="4f898-193">In the preceding example, set `_formInvalid` to `false` if:</span></span>

* <span data-ttu-id="4f898-194">Form geçerli varsayılan değerlerle önceden yüklenir.</span><span class="sxs-lookup"><span data-stu-id="4f898-194">The form is preloaded with valid default values.</span></span>
* <span data-ttu-id="4f898-195">Form yüklendiğinde gönder düğmesinin etkin olmasını istiyorsunuz.</span><span class="sxs-lookup"><span data-stu-id="4f898-195">You want the submit button enabled when the form loads.</span></span>

<span data-ttu-id="4f898-196">Önceki yaklaşımın bir yan etkisi, `ValidationSummary` kullanıcı herhangi bir alanla etkileşime geçtikten sonra bir bileşenin geçersiz alanlarla doldurulan olmasıdır.</span><span class="sxs-lookup"><span data-stu-id="4f898-196">A side effect of the preceding approach is that a `ValidationSummary` component is populated with invalid fields after the user interacts with any one field.</span></span> <span data-ttu-id="4f898-197">Bu senaryo aşağıdaki yollardan biri olarak ele alınabilir:</span><span class="sxs-lookup"><span data-stu-id="4f898-197">This scenario can be addressed in either of the following ways:</span></span>

* <span data-ttu-id="4f898-198">Formda bir `ValidationSummary` bileşen kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="4f898-198">Don't use a `ValidationSummary` component on the form.</span></span>
* <span data-ttu-id="4f898-199">Gönder `ValidationSummary` düğmesi seçildiğinde bileşeni görünür hale getirin `HandleValidSubmit` (örneğin, bir yöntemde).</span><span class="sxs-lookup"><span data-stu-id="4f898-199">Make the `ValidationSummary` component visible when the submit button is selected (for example, in a `HandleValidSubmit` method).</span></span>

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
