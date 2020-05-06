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
# <a name="aspnet-core-blazor-forms-and-validation"></a><span data-ttu-id="c468f-103">ASP.NET Core Blazor formları ve doğrulaması</span><span class="sxs-lookup"><span data-stu-id="c468f-103">ASP.NET Core Blazor forms and validation</span></span>

<span data-ttu-id="c468f-104">[Daniel Roth](https://github.com/danroth27) ve [Luke Latham](https://github.com/guardrex) tarafından</span><span class="sxs-lookup"><span data-stu-id="c468f-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="c468f-105">Forms ve doğrulama, Blazor içinde [veri ek açıklamaları](xref:mvc/models/validation)kullanılarak desteklenir.</span><span class="sxs-lookup"><span data-stu-id="c468f-105">Forms and validation are supported in Blazor using [data annotations](xref:mvc/models/validation).</span></span>

<span data-ttu-id="c468f-106">Aşağıdaki `ExampleModel` tür, veri ek açıklamalarını kullanarak doğrulama mantığını tanımlar:</span><span class="sxs-lookup"><span data-stu-id="c468f-106">The following `ExampleModel` type defines validation logic using data annotations:</span></span>

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

<span data-ttu-id="c468f-107">Bir form, `EditForm` bileşeni kullanılarak tanımlanır.</span><span class="sxs-lookup"><span data-stu-id="c468f-107">A form is defined using the `EditForm` component.</span></span> <span data-ttu-id="c468f-108">Aşağıdaki formda tipik öğeler, bileşenler ve Razor kodu gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="c468f-108">The following form demonstrates typical elements, components, and Razor code:</span></span>

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

<span data-ttu-id="c468f-109">Yukarıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="c468f-109">In the preceding example:</span></span>

* <span data-ttu-id="c468f-110">Form, `name` `ExampleModel` türünde tanımlanan doğrulamayı kullanarak alanda Kullanıcı girişini doğrular.</span><span class="sxs-lookup"><span data-stu-id="c468f-110">The form validates user input in the `name` field using the validation defined in the `ExampleModel` type.</span></span> <span data-ttu-id="c468f-111">Model bileşen `@code` bloğunda oluşturulur ve özel bir alanda (`_exampleModel`) tutulur.</span><span class="sxs-lookup"><span data-stu-id="c468f-111">The model is created in the component's `@code` block and held in a private field (`_exampleModel`).</span></span> <span data-ttu-id="c468f-112">Alanı, `Model` `<EditForm>` öğesinin özniteliğine atanır.</span><span class="sxs-lookup"><span data-stu-id="c468f-112">The field is assigned to the `Model` attribute of the `<EditForm>` element.</span></span>
* <span data-ttu-id="c468f-113">`InputText` Bileşenin `@bind-Value` bağlamaları:</span><span class="sxs-lookup"><span data-stu-id="c468f-113">The `InputText` component's `@bind-Value` binds:</span></span>
  * <span data-ttu-id="c468f-114">Model özelliği (`_exampleModel.Name`) `InputText` bileşen `Value` özelliğine.</span><span class="sxs-lookup"><span data-stu-id="c468f-114">The model property (`_exampleModel.Name`) to the `InputText` component's `Value` property.</span></span>
  * <span data-ttu-id="c468f-115">`InputText` Bileşen `ValueChanged` özelliğine bir değişiklik olayı temsilcisi.</span><span class="sxs-lookup"><span data-stu-id="c468f-115">A change event delegate to the `InputText` component's `ValueChanged` property.</span></span>
* <span data-ttu-id="c468f-116">Bileşen `DataAnnotationsValidator` , veri ek açıklamalarını kullanarak doğrulama desteği ekler.</span><span class="sxs-lookup"><span data-stu-id="c468f-116">The `DataAnnotationsValidator` component attaches validation support using data annotations.</span></span>
* <span data-ttu-id="c468f-117">`ValidationSummary` Bileşen doğrulama iletilerini özetler.</span><span class="sxs-lookup"><span data-stu-id="c468f-117">The `ValidationSummary` component summarizes validation messages.</span></span>
* <span data-ttu-id="c468f-118">`HandleValidSubmit`Form başarıyla gönderdiğinde tetiklenir (doğrulamayı geçirir).</span><span class="sxs-lookup"><span data-stu-id="c468f-118">`HandleValidSubmit` is triggered when the form successfully submits (passes validation).</span></span>

<span data-ttu-id="c468f-119">Kullanıcı girişini almak ve doğrulamak için yerleşik bir giriş bileşenleri kümesi vardır.</span><span class="sxs-lookup"><span data-stu-id="c468f-119">A set of built-in input components are available to receive and validate user input.</span></span> <span data-ttu-id="c468f-120">Girişler değiştirildiklerinde ve bir form gönderildiğinde onaylanır.</span><span class="sxs-lookup"><span data-stu-id="c468f-120">Inputs are validated when they're changed and when a form is submitted.</span></span> <span data-ttu-id="c468f-121">Kullanılabilir giriş bileşenleri aşağıdaki tabloda gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="c468f-121">Available input components are shown in the following table.</span></span>

| <span data-ttu-id="c468f-122">Giriş bileşeni</span><span class="sxs-lookup"><span data-stu-id="c468f-122">Input component</span></span> | <span data-ttu-id="c468f-123">Olarak işlendi&hellip;</span><span class="sxs-lookup"><span data-stu-id="c468f-123">Rendered as&hellip;</span></span>       |
| --------------- | ------------------------- |
| `InputText`     | `<input>`                 |
| `InputTextArea` | `<textarea>`              |
| `InputSelect`   | `<select>`                |
| `InputNumber`   | `<input type="number">`   |
| `InputCheckbox` | `<input type="checkbox">` |
| `InputDate`     | `<input type="date">`     |

<span data-ttu-id="c468f-124">Dahil olmak üzere `EditForm`tüm giriş bileşenleri, rastgele öznitelikleri destekler.</span><span class="sxs-lookup"><span data-stu-id="c468f-124">All of the input components, including `EditForm`, support arbitrary attributes.</span></span> <span data-ttu-id="c468f-125">Bir bileşen parametresiyle eşleşmeyen herhangi bir öznitelik işlenmiş HTML öğesine eklenir.</span><span class="sxs-lookup"><span data-stu-id="c468f-125">Any attribute that doesn't match a component parameter is added to the rendered HTML element.</span></span>

<span data-ttu-id="c468f-126">Giriş bileşenleri, düzenleme sırasında doğrulamak ve CSS sınıfını alan durumunu yansıtacak şekilde değiştirmek için varsayılan davranışı sağlar.</span><span class="sxs-lookup"><span data-stu-id="c468f-126">Input components provide default behavior for validating on edit and changing their CSS class to reflect the field state.</span></span> <span data-ttu-id="c468f-127">Bazı bileşenler, yararlı ayrıştırma mantığını içerir.</span><span class="sxs-lookup"><span data-stu-id="c468f-127">Some components include useful parsing logic.</span></span> <span data-ttu-id="c468f-128">Örneğin, `InputDate` bunları doğrulama `InputNumber` hatası olarak kaydederek düzeltilemez değerleri düzgün şekilde işleyin.</span><span class="sxs-lookup"><span data-stu-id="c468f-128">For example, `InputDate` and `InputNumber` handle unparseable values gracefully by registering them as validation errors.</span></span> <span data-ttu-id="c468f-129">Null değerleri kabul edebilecek türler, hedef alanın null değer alabilme durumunu da destekler (örneğin, `int?`).</span><span class="sxs-lookup"><span data-stu-id="c468f-129">Types that can accept null values also support nullability of the target field (for example, `int?`).</span></span>

<span data-ttu-id="c468f-130">Aşağıdaki `Starship` tür, daha önce daha büyük bir özellik kümesi ve daha önceki `ExampleModel`veri açıklamalarını kullanarak doğrulama mantığını tanımlar:</span><span class="sxs-lookup"><span data-stu-id="c468f-130">The following `Starship` type defines validation logic using a larger set of properties and data annotations than the earlier `ExampleModel`:</span></span>

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

<span data-ttu-id="c468f-131">Önceki örnekte, `Description` hiçbir veri ek açıklaması mevcut olmadığından isteğe bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="c468f-131">In the preceding example, `Description` is optional because no data annotations are present.</span></span>

<span data-ttu-id="c468f-132">Aşağıdaki form, `Starship` modelde tanımlanan doğrulamayı kullanarak Kullanıcı girişini doğrular:</span><span class="sxs-lookup"><span data-stu-id="c468f-132">The following form validates user input using the validation defined in the `Starship` model:</span></span>

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

<span data-ttu-id="c468f-133">, `EditForm` Hangi alanların `EditContext` değiştirildiği ve geçerli doğrulama iletileri de dahil olmak üzere düzenleme işlemiyle ilgili meta verileri izleyen [basamaklı bir değer](xref:blazor/components#cascading-values-and-parameters) olarak oluşturur.</span><span class="sxs-lookup"><span data-stu-id="c468f-133">The `EditForm` creates an `EditContext` as a [cascading value](xref:blazor/components#cascading-values-and-parameters) that tracks metadata about the edit process, including which fields have been modified and the current validation messages.</span></span> <span data-ttu-id="c468f-134">`EditForm` Ayrıca geçerli ve geçersiz Gönderimlerle (`OnValidSubmit`, `OnInvalidSubmit`) uygun olaylar sağlar.</span><span class="sxs-lookup"><span data-stu-id="c468f-134">The `EditForm` also provides convenient events for valid and invalid submits (`OnValidSubmit`, `OnInvalidSubmit`).</span></span> <span data-ttu-id="c468f-135">Alternatif olarak, `OnSubmit` doğrulamayı tetiklemek ve alan değerlerini özel doğrulama kodu ile denetlemek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="c468f-135">Alternatively, use `OnSubmit` to trigger the validation and check field values with custom validation code.</span></span>

<span data-ttu-id="c468f-136">Aşağıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="c468f-136">In the following example:</span></span>

* <span data-ttu-id="c468f-137">`HandleSubmit` Yöntemi, **Gönder** düğmesi seçildiğinde çalışır.</span><span class="sxs-lookup"><span data-stu-id="c468f-137">The `HandleSubmit` method runs when the **Submit** button is selected.</span></span>
* <span data-ttu-id="c468f-138">Form, formun ' i kullanılarak onaylanır `EditContext`.</span><span class="sxs-lookup"><span data-stu-id="c468f-138">The form is validated using the form's `EditContext`.</span></span>
* <span data-ttu-id="c468f-139">Form, sunucusunda bir Web API uç noktası `EditContext` çağıran `ServerValidate` yönteme geçerek daha sonra onaylanır (*gösterilmez*).</span><span class="sxs-lookup"><span data-stu-id="c468f-139">The form is further validated by passing the `EditContext` to the `ServerValidate` method that calls a web API endpoint on the server (*not shown*).</span></span>
* <span data-ttu-id="c468f-140">Ek kod, istemci ve sunucu tarafı doğrulamasının sonucuna bağlı olarak çalıştırılır `isValid`.</span><span class="sxs-lookup"><span data-stu-id="c468f-140">Additional code is run depending on the result of the client- and server-side validation by checking `isValid`.</span></span>

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

## <a name="inputtext-based-on-the-input-event"></a><span data-ttu-id="c468f-141">Giriş olayına göre InputText</span><span class="sxs-lookup"><span data-stu-id="c468f-141">InputText based on the input event</span></span>

<span data-ttu-id="c468f-142">Olayı yerine `InputText` `input` olayını kullanan özel bir bileşen oluşturmak için bileşenini kullanın. `change`</span><span class="sxs-lookup"><span data-stu-id="c468f-142">Use the `InputText` component to create a custom component that uses the `input` event instead of the `change` event.</span></span>

<span data-ttu-id="c468f-143">Aşağıdaki biçimlendirmeye sahip bir bileşen oluşturun ve bileşeni tıpkı kullanıldığı gibi `InputText` kullanın:</span><span class="sxs-lookup"><span data-stu-id="c468f-143">Create a component with the following markup, and use the component just as `InputText` is used:</span></span>

```razor
@inherits InputText

<input 
    @attributes="AdditionalAttributes" 
    class="@CssClass" 
    value="@CurrentValue" 
    @oninput="EventCallback.Factory.CreateBinder<string>(
        this, __value => CurrentValueAsString = __value, CurrentValueAsString)" />
```

## <a name="work-with-radio-buttons"></a><span data-ttu-id="c468f-144">Radyo düğmeleriyle çalışma</span><span class="sxs-lookup"><span data-stu-id="c468f-144">Work with radio buttons</span></span>

<span data-ttu-id="c468f-145">Bir formda radyo düğmeleriyle çalışırken, radyo düğmeleri bir grup olarak değerlendirildiğinden veri bağlama diğer öğelerden farklı işlenir.</span><span class="sxs-lookup"><span data-stu-id="c468f-145">When working with radio buttons in a form, data binding is handled differently than other elements because radio buttons are evaluated as a group.</span></span> <span data-ttu-id="c468f-146">Her radyo düğmesinin değeri sabittir, ancak radyo düğmesi grubunun değeri seçili radyo düğmesinin değeridir.</span><span class="sxs-lookup"><span data-stu-id="c468f-146">The value of each radio button is fixed, but the value of the radio button group is the value of the selected radio button.</span></span> <span data-ttu-id="c468f-147">Aşağıdaki örnek, aşağıdakilerin nasıl yapılacağını göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="c468f-147">The following example shows how to:</span></span>

* <span data-ttu-id="c468f-148">Radyo düğmesi grubu için veri bağlamayı işleyin.</span><span class="sxs-lookup"><span data-stu-id="c468f-148">Handle data binding for a radio button group.</span></span>
* <span data-ttu-id="c468f-149">Özel `InputRadio` bir bileşen kullanarak doğrulamayı destekler.</span><span class="sxs-lookup"><span data-stu-id="c468f-149">Support validation using a custom `InputRadio` component.</span></span>

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

<span data-ttu-id="c468f-150">Aşağıdaki `EditForm` , kullanıcıdan bir derecelendirme `InputRadio` almak ve doğrulamak için önceki bileşeni kullanır:</span><span class="sxs-lookup"><span data-stu-id="c468f-150">The following `EditForm` uses the preceding `InputRadio` component to obtain and validate a rating from the user:</span></span>

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

## <a name="validation-support"></a><span data-ttu-id="c468f-151">Doğrulama desteği</span><span class="sxs-lookup"><span data-stu-id="c468f-151">Validation support</span></span>

<span data-ttu-id="c468f-152">`DataAnnotationsValidator` Bileşen, Basamaklandırılan `EditContext`veri açıklamalarını kullanarak doğrulama desteğini iliştirir.</span><span class="sxs-lookup"><span data-stu-id="c468f-152">The `DataAnnotationsValidator` component attaches validation support using data annotations to the cascaded `EditContext`.</span></span> <span data-ttu-id="c468f-153">Veri ek açıklamalarını kullanarak doğrulama desteğinin etkinleştirilmesi bu açık hareketi gerektirir.</span><span class="sxs-lookup"><span data-stu-id="c468f-153">Enabling support for validation using data annotations requires this explicit gesture.</span></span> <span data-ttu-id="c468f-154">Veri ek açıklamalarıyla farklı bir doğrulama sistemi kullanmak için, `DataAnnotationsValidator` öğesini özel bir uygulamayla değiştirin.</span><span class="sxs-lookup"><span data-stu-id="c468f-154">To use a different validation system than data annotations, replace the `DataAnnotationsValidator` with a custom implementation.</span></span> <span data-ttu-id="c468f-155">ASP.NET Core uygulama, başvuru kaynağında İnceleme için kullanılabilir: [dataannotationsvalidator](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[adddataannotationsvalidation](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="c468f-155">The ASP.NET Core implementation is available for inspection in the reference source: [DataAnnotationsValidator](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).</span></span>

Blazor<span data-ttu-id="c468f-156">iki tür doğrulama gerçekleştirir:</span><span class="sxs-lookup"><span data-stu-id="c468f-156"> performs two types of validation:</span></span>

* <span data-ttu-id="c468f-157">*Alan doğrulama* , Kullanıcı bir alanın dışına eklendiğinde gerçekleştirilir.</span><span class="sxs-lookup"><span data-stu-id="c468f-157">*Field validation* is performed when the user tabs out of a field.</span></span> <span data-ttu-id="c468f-158">Alan doğrulama sırasında, `DataAnnotationsValidator` bileşen bildirilen tüm doğrulama sonuçlarını alanla ilişkilendirir.</span><span class="sxs-lookup"><span data-stu-id="c468f-158">During field validation, the `DataAnnotationsValidator` component associates all reported validation results with the field.</span></span>
* <span data-ttu-id="c468f-159">Kullanıcı formu gönderdiğinde *model doğrulaması* gerçekleştirilir.</span><span class="sxs-lookup"><span data-stu-id="c468f-159">*Model validation* is performed when the user submits the form.</span></span> <span data-ttu-id="c468f-160">Model doğrulama sırasında, `DataAnnotationsValidator` bileşen, doğrulama sonucunun raporlandığı üye adına göre alanı belirlemeyi dener.</span><span class="sxs-lookup"><span data-stu-id="c468f-160">During model validation, the `DataAnnotationsValidator` component attempts to determine the field based on the member name that the validation result reports.</span></span> <span data-ttu-id="c468f-161">Tek bir üyeyle ilişkilendirilmeyen doğrulama sonuçları, bir alan yerine modeliyle ilişkilendirilir.</span><span class="sxs-lookup"><span data-stu-id="c468f-161">Validation results that aren't associated with an individual member are associated with the model rather than a field.</span></span>

### <a name="validation-summary-and-validation-message-components"></a><span data-ttu-id="c468f-162">Doğrulama özeti ve doğrulama Iletisi bileşenleri</span><span class="sxs-lookup"><span data-stu-id="c468f-162">Validation Summary and Validation Message components</span></span>

<span data-ttu-id="c468f-163">`ValidationSummary` Bileşen, [doğrulama özeti etiketi Yardımcısı](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper)'na benzer olan tüm doğrulama iletilerini özetler:</span><span class="sxs-lookup"><span data-stu-id="c468f-163">The `ValidationSummary` component summarizes all validation messages, which is similar to the [Validation Summary Tag Helper](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper):</span></span>

```razor
<ValidationSummary />
```

<span data-ttu-id="c468f-164">`Model` Parametresi ile belirli bir model için çıkış doğrulama iletileri:</span><span class="sxs-lookup"><span data-stu-id="c468f-164">Output validation messages for a specific model with the `Model` parameter:</span></span>
  
```razor
<ValidationSummary Model="@_starship" />
```

<span data-ttu-id="c468f-165">`ValidationMessage` Bileşeni, [doğrulama iletisi etiketi Yardımcısı](xref:mvc/views/working-with-forms#the-validation-message-tag-helper)'na benzer şekilde belirli bir alan için doğrulama iletileri görüntüler.</span><span class="sxs-lookup"><span data-stu-id="c468f-165">The `ValidationMessage` component displays validation messages for a specific field, which is similar to the [Validation Message Tag Helper](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span></span> <span data-ttu-id="c468f-166">`For` Özniteliği ile doğrulama için alanı ve model özelliğini adlandırırken bir lambda ifadesini belirtin:</span><span class="sxs-lookup"><span data-stu-id="c468f-166">Specify the field for validation with the `For` attribute and a lambda expression naming the model property:</span></span>

```razor
<ValidationMessage For="@(() => _starship.MaximumAccommodation)" />
```

<span data-ttu-id="c468f-167">Ve `ValidationMessage` `ValidationSummary` bileşenleri, rastgele öznitelikleri destekler.</span><span class="sxs-lookup"><span data-stu-id="c468f-167">The `ValidationMessage` and `ValidationSummary` components support arbitrary attributes.</span></span> <span data-ttu-id="c468f-168">Bir bileşen parametresiyle eşleşmeyen herhangi bir öznitelik oluşturulan `<div>` or `<ul>` öğesine eklenir.</span><span class="sxs-lookup"><span data-stu-id="c468f-168">Any attribute that doesn't match a component parameter is added to the generated `<div>` or `<ul>` element.</span></span>

### <a name="custom-validation-attributes"></a><span data-ttu-id="c468f-169">Özel doğrulama öznitelikleri</span><span class="sxs-lookup"><span data-stu-id="c468f-169">Custom validation attributes</span></span>

<span data-ttu-id="c468f-170">Bir doğrulama sonucunun [özel bir doğrulama özniteliği](xref:mvc/models/validation#custom-attributes)kullanılırken bir alanla doğru şekilde ilişkilendirildiğinden emin olmak için, şunu oluştururken doğrulama bağlamını <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> geçirin: <xref:System.ComponentModel.DataAnnotations.ValidationResult></span><span class="sxs-lookup"><span data-stu-id="c468f-170">To ensure that a validation result is correctly associated with a field when using a [custom validation attribute](xref:mvc/models/validation#custom-attributes), pass the validation context's <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> when creating the <xref:System.ComponentModel.DataAnnotations.ValidationResult>:</span></span>

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

### <a name="blazor-data-annotations-validation-package"></a>Blazor<span data-ttu-id="c468f-171">veri ek açıklamaları doğrulama paketi</span><span class="sxs-lookup"><span data-stu-id="c468f-171"> data annotations validation package</span></span>

<span data-ttu-id="c468f-172">[Microsoft. AspNetCore. components. Dataaçıklamalarda. Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) , `DataAnnotationsValidator` bileşeni kullanarak doğrulama deneyimini boşlukları dolduran bir pakettir.</span><span class="sxs-lookup"><span data-stu-id="c468f-172">The [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) is a package that fills validation experience gaps using the `DataAnnotationsValidator` component.</span></span> <span data-ttu-id="c468f-173">Paket şu anda *deneysel*.</span><span class="sxs-lookup"><span data-stu-id="c468f-173">The package is currently *experimental*.</span></span>

### <a name="compareproperty-attribute"></a><span data-ttu-id="c468f-174">[CompareProperty] özniteliği</span><span class="sxs-lookup"><span data-stu-id="c468f-174">[CompareProperty] attribute</span></span>

<span data-ttu-id="c468f-175">Doğrulama <xref:System.ComponentModel.DataAnnotations.CompareAttribute> sonucunu belirli bir üyeyle ilişkilendirmediği için `DataAnnotationsValidator` bileşen ile iyi çalışmaz.</span><span class="sxs-lookup"><span data-stu-id="c468f-175">The <xref:System.ComponentModel.DataAnnotations.CompareAttribute> doesn't work well with the `DataAnnotationsValidator` component because it doesn't associate the validation result with a specific member.</span></span> <span data-ttu-id="c468f-176">Bu, alan düzeyi doğrulama ve tüm modelin bir gönderme sırasında doğrulanması arasındaki tutarsız davranışa neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="c468f-176">This can result in inconsistent behavior between field-level validation and when the entire model is validated on a submit.</span></span> <span data-ttu-id="c468f-177">[Microsoft. AspNetCore. components. Dataaçıklamalarda. Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) *deneysel* Package, bu sınırlamalara geçici bir çözüm olan `ComparePropertyAttribute`ek bir doğrulama özniteliği sunar.</span><span class="sxs-lookup"><span data-stu-id="c468f-177">The [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) *experimental* package introduces an additional validation attribute, `ComparePropertyAttribute`, that works around these limitations.</span></span> <span data-ttu-id="c468f-178">Bir Blazor uygulamada, `[CompareProperty]` `[Compare]` özniteliği için doğrudan değiştirme olur.</span><span class="sxs-lookup"><span data-stu-id="c468f-178">In a Blazor app, `[CompareProperty]` is a direct replacement for the `[Compare]` attribute.</span></span>

### <a name="nested-models-collection-types-and-complex-types"></a><span data-ttu-id="c468f-179">İç içe modeller, koleksiyon türleri ve karmaşık türler</span><span class="sxs-lookup"><span data-stu-id="c468f-179">Nested models, collection types, and complex types</span></span>

Blazor<span data-ttu-id="c468f-180">yerleşik olan veri açıklamalarını kullanarak form girişini doğrulama desteği sağlar `DataAnnotationsValidator`.</span><span class="sxs-lookup"><span data-stu-id="c468f-180"> provides support for validating form input using data annotations with the built-in `DataAnnotationsValidator`.</span></span> <span data-ttu-id="c468f-181">Ancak, yalnızca `DataAnnotationsValidator` koleksiyonun üst düzey özelliklerini, koleksiyon veya karmaşık tür özellikleri olmayan forma doğrular.</span><span class="sxs-lookup"><span data-stu-id="c468f-181">However, the `DataAnnotationsValidator` only validates top-level properties of the model bound to the form that aren't collection- or complex-type properties.</span></span>

<span data-ttu-id="c468f-182">Koleksiyon ve karmaşık tür özellikleri dahil olmak üzere, bağlantılı modelin tüm nesne grafiğini doğrulamak için, `ObjectGraphDataAnnotationsValidator` *deneysel* [Microsoft. Aspnetcore. components. dataaçıklamalarda. Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) Package tarafından sunulan öğesini kullanın:</span><span class="sxs-lookup"><span data-stu-id="c468f-182">To validate the bound model's entire object graph, including collection- and complex-type properties, use the `ObjectGraphDataAnnotationsValidator` provided by the *experimental* [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) package:</span></span>

```razor
<EditForm Model="@_model" OnValidSubmit="HandleValidSubmit">
    <ObjectGraphDataAnnotationsValidator />
    ...
</EditForm>
```

<span data-ttu-id="c468f-183">İle `[ValidateComplexType]`model özelliklerine açıklama ekleyin.</span><span class="sxs-lookup"><span data-stu-id="c468f-183">Annotate model properties with `[ValidateComplexType]`.</span></span> <span data-ttu-id="c468f-184">Aşağıdaki model sınıflarında, `ShipDescription` sınıfı, model forma bağlandığında doğrulanacak ek veri açıklamalarını içerir:</span><span class="sxs-lookup"><span data-stu-id="c468f-184">In the following model classes, the `ShipDescription` class contains additional data annotations to validate when the model is bound to the form:</span></span>

<span data-ttu-id="c468f-185">*Starship.cs*:</span><span class="sxs-lookup"><span data-stu-id="c468f-185">*Starship.cs*:</span></span>

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

<span data-ttu-id="c468f-186">*ShipDescription.cs*:</span><span class="sxs-lookup"><span data-stu-id="c468f-186">*ShipDescription.cs*:</span></span>

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

### <a name="enable-the-submit-button-based-on-form-validation"></a><span data-ttu-id="c468f-187">Form doğrulamasına göre Gönder düğmesini etkinleştir</span><span class="sxs-lookup"><span data-stu-id="c468f-187">Enable the submit button based on form validation</span></span>

<span data-ttu-id="c468f-188">Form doğrulamasına göre Gönder düğmesini etkinleştirmek ve devre dışı bırakmak için:</span><span class="sxs-lookup"><span data-stu-id="c468f-188">To enable and disable the submit button based on form validation:</span></span>

* <span data-ttu-id="c468f-189">Bileşen başlatıldığında modeli atamak `EditContext` için formunu kullanın.</span><span class="sxs-lookup"><span data-stu-id="c468f-189">Use the form's `EditContext` to assign the model when the component is initialized.</span></span>
* <span data-ttu-id="c468f-190">Gönder düğmesini etkinleştirmek ve devre dışı bırakmak `OnFieldChanged` için bağlam geri aramasında formu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="c468f-190">Validate the form in the context's `OnFieldChanged` callback to enable and disable the submit button.</span></span>
* <span data-ttu-id="c468f-191">`Dispose` Yöntemi içindeki olay işleyicisinin üstünden geri dön.</span><span class="sxs-lookup"><span data-stu-id="c468f-191">Unhook the event handler in the `Dispose` method.</span></span> <span data-ttu-id="c468f-192">Daha fazla bilgi için bkz. <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span><span class="sxs-lookup"><span data-stu-id="c468f-192">For more information, see <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span></span>

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

<span data-ttu-id="c468f-193">Yukarıdaki örnekte, şu şekilde `_formInvalid` `false` ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="c468f-193">In the preceding example, set `_formInvalid` to `false` if:</span></span>

* <span data-ttu-id="c468f-194">Form geçerli varsayılan değerlerle önceden yüklenir.</span><span class="sxs-lookup"><span data-stu-id="c468f-194">The form is preloaded with valid default values.</span></span>
* <span data-ttu-id="c468f-195">Form yüklendiğinde Gönder düğmesinin etkinleştirilmesini istiyorsunuz.</span><span class="sxs-lookup"><span data-stu-id="c468f-195">You want the submit button enabled when the form loads.</span></span>

<span data-ttu-id="c468f-196">Önceki yaklaşımın yan etkisi, Kullanıcı herhangi bir alanla etkileşime `ValidationSummary` geçtiğinde bileşen geçersiz alanlarla doldurulmuştur.</span><span class="sxs-lookup"><span data-stu-id="c468f-196">A side effect of the preceding approach is that a `ValidationSummary` component is populated with invalid fields after the user interacts with any one field.</span></span> <span data-ttu-id="c468f-197">Bu senaryoya aşağıdaki yollarla değinilerek şunlar olabilir:</span><span class="sxs-lookup"><span data-stu-id="c468f-197">This scenario can be addressed in either of the following ways:</span></span>

* <span data-ttu-id="c468f-198">Form üzerinde bir `ValidationSummary` bileşen kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="c468f-198">Don't use a `ValidationSummary` component on the form.</span></span>
* <span data-ttu-id="c468f-199">Gönder düğmesi `ValidationSummary` seçildiğinde bileşeni görünür hale getirin (örneğin, bir `HandleValidSubmit` yöntemde).</span><span class="sxs-lookup"><span data-stu-id="c468f-199">Make the `ValidationSummary` component visible when the submit button is selected (for example, in a `HandleValidSubmit` method).</span></span>

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
