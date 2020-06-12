---
title: BlazorForms ve doğrulama ASP.NET Core
author: guardrex
description: İçindeki form ve alan doğrulama senaryolarını nasıl kullanacağınızı öğrenin Blazor .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/04/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/forms-validation
ms.openlocfilehash: 707686cdc4ceb6605d6214eeeee0f6dd2e121c90
ms.sourcegitcommit: 6371114344a5f4fbc5d4a119b0be1ad3762e0216
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/10/2020
ms.locfileid: "84679533"
---
# <a name="aspnet-core-blazor-forms-and-validation"></a><span data-ttu-id="7d6dc-103">BlazorForms ve doğrulama ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7d6dc-103">ASP.NET Core Blazor forms and validation</span></span>

<span data-ttu-id="7d6dc-104">[Daniel Roth](https://github.com/danroth27) ve [Luke Latham](https://github.com/guardrex) tarafından</span><span class="sxs-lookup"><span data-stu-id="7d6dc-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="7d6dc-105">Formlar ve doğrulama, Blazor [veri ek açıklamaları](xref:mvc/models/validation)kullanılarak desteklenir.</span><span class="sxs-lookup"><span data-stu-id="7d6dc-105">Forms and validation are supported in Blazor using [data annotations](xref:mvc/models/validation).</span></span>

<span data-ttu-id="7d6dc-106">Aşağıdaki `ExampleModel` tür, veri ek açıklamalarını kullanarak doğrulama mantığını tanımlar:</span><span class="sxs-lookup"><span data-stu-id="7d6dc-106">The following `ExampleModel` type defines validation logic using data annotations:</span></span>

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

<span data-ttu-id="7d6dc-107">Bir form, bileşeni kullanılarak tanımlanır <xref:Microsoft.AspNetCore.Components.Forms.EditForm> .</span><span class="sxs-lookup"><span data-stu-id="7d6dc-107">A form is defined using the <xref:Microsoft.AspNetCore.Components.Forms.EditForm> component.</span></span> <span data-ttu-id="7d6dc-108">Aşağıdaki form tipik öğeleri, bileşenleri ve Razor kodu gösterir:</span><span class="sxs-lookup"><span data-stu-id="7d6dc-108">The following form demonstrates typical elements, components, and Razor code:</span></span>

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

<span data-ttu-id="7d6dc-109">Yukarıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="7d6dc-109">In the preceding example:</span></span>

* <span data-ttu-id="7d6dc-110">Form, `name` türünde tanımlanan doğrulamayı kullanarak alanda Kullanıcı girişini doğrular `ExampleModel` .</span><span class="sxs-lookup"><span data-stu-id="7d6dc-110">The form validates user input in the `name` field using the validation defined in the `ExampleModel` type.</span></span> <span data-ttu-id="7d6dc-111">Model bileşen `@code` bloğunda oluşturulur ve özel bir alanda ( `exampleModel` ) tutulur.</span><span class="sxs-lookup"><span data-stu-id="7d6dc-111">The model is created in the component's `@code` block and held in a private field (`exampleModel`).</span></span> <span data-ttu-id="7d6dc-112">Alanı, `Model` öğesinin özniteliğine atanır `<EditForm>` .</span><span class="sxs-lookup"><span data-stu-id="7d6dc-112">The field is assigned to the `Model` attribute of the `<EditForm>` element.</span></span>
* <span data-ttu-id="7d6dc-113"><xref:Microsoft.AspNetCore.Components.Forms.InputText>Bileşenin `@bind-Value` bağlamaları:</span><span class="sxs-lookup"><span data-stu-id="7d6dc-113">The <xref:Microsoft.AspNetCore.Components.Forms.InputText> component's `@bind-Value` binds:</span></span>
  * <span data-ttu-id="7d6dc-114">Model özelliği ( `exampleModel.Name` ) <xref:Microsoft.AspNetCore.Components.Forms.InputText> bileşen `Value` özelliğine.</span><span class="sxs-lookup"><span data-stu-id="7d6dc-114">The model property (`exampleModel.Name`) to the <xref:Microsoft.AspNetCore.Components.Forms.InputText> component's `Value` property.</span></span> <span data-ttu-id="7d6dc-115">Özellik bağlama hakkında daha fazla bilgi için bkz <xref:blazor/data-binding#parent-to-child-binding-with-component-parameters> ..</span><span class="sxs-lookup"><span data-stu-id="7d6dc-115">For more information on property binding, see <xref:blazor/data-binding#parent-to-child-binding-with-component-parameters>.</span></span>
  * <span data-ttu-id="7d6dc-116">Bileşen özelliğine bir değişiklik olayı temsilcisi <xref:Microsoft.AspNetCore.Components.Forms.InputText> `ValueChanged` .</span><span class="sxs-lookup"><span data-stu-id="7d6dc-116">A change event delegate to the <xref:Microsoft.AspNetCore.Components.Forms.InputText> component's `ValueChanged` property.</span></span>
* <span data-ttu-id="7d6dc-117"><xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>Bileşen, veri ek açıklamalarını kullanarak doğrulama desteği ekler.</span><span class="sxs-lookup"><span data-stu-id="7d6dc-117">The <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component attaches validation support using data annotations.</span></span>
* <span data-ttu-id="7d6dc-118"><xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary>Bileşen doğrulama iletilerini özetler.</span><span class="sxs-lookup"><span data-stu-id="7d6dc-118">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component summarizes validation messages.</span></span>
* <span data-ttu-id="7d6dc-119">`HandleValidSubmit`Form başarıyla gönderdiğinde tetiklenir (doğrulamayı geçirir).</span><span class="sxs-lookup"><span data-stu-id="7d6dc-119">`HandleValidSubmit` is triggered when the form successfully submits (passes validation).</span></span>

<span data-ttu-id="7d6dc-120">Kullanıcı girişini almak ve doğrulamak için yerleşik bir giriş bileşenleri kümesi vardır.</span><span class="sxs-lookup"><span data-stu-id="7d6dc-120">A set of built-in input components are available to receive and validate user input.</span></span> <span data-ttu-id="7d6dc-121">Girişler değiştirildiklerinde ve bir form gönderildiğinde onaylanır.</span><span class="sxs-lookup"><span data-stu-id="7d6dc-121">Inputs are validated when they're changed and when a form is submitted.</span></span> <span data-ttu-id="7d6dc-122">Kullanılabilir giriş bileşenleri aşağıdaki tabloda gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="7d6dc-122">Available input components are shown in the following table.</span></span>

| <span data-ttu-id="7d6dc-123">Giriş bileşeni</span><span class="sxs-lookup"><span data-stu-id="7d6dc-123">Input component</span></span> | <span data-ttu-id="7d6dc-124">Olarak işlendi&hellip;</span><span class="sxs-lookup"><span data-stu-id="7d6dc-124">Rendered as&hellip;</span></span> |
| --------------- | ------------------- |
| <xref:Microsoft.AspNetCore.Components.Forms.InputText> | `<input>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputTextArea> | `<textarea>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputSelect%601> | `<select>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> | `<input type="number">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputCheckbox> | `<input type="checkbox">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> | `<input type="date">` |

<span data-ttu-id="7d6dc-125">Dahil olmak üzere tüm giriş bileşenleri, <xref:Microsoft.AspNetCore.Components.Forms.EditForm> rastgele öznitelikleri destekler.</span><span class="sxs-lookup"><span data-stu-id="7d6dc-125">All of the input components, including <xref:Microsoft.AspNetCore.Components.Forms.EditForm>, support arbitrary attributes.</span></span> <span data-ttu-id="7d6dc-126">Bir bileşen parametresiyle eşleşmeyen herhangi bir öznitelik işlenmiş HTML öğesine eklenir.</span><span class="sxs-lookup"><span data-stu-id="7d6dc-126">Any attribute that doesn't match a component parameter is added to the rendered HTML element.</span></span>

<span data-ttu-id="7d6dc-127">Giriş bileşenleri, düzenleme sırasında doğrulamak ve CSS sınıfını alan durumunu yansıtacak şekilde değiştirmek için varsayılan davranışı sağlar.</span><span class="sxs-lookup"><span data-stu-id="7d6dc-127">Input components provide default behavior for validating on edit and changing their CSS class to reflect the field state.</span></span> <span data-ttu-id="7d6dc-128">Bazı bileşenler, yararlı ayrıştırma mantığını içerir.</span><span class="sxs-lookup"><span data-stu-id="7d6dc-128">Some components include useful parsing logic.</span></span> <span data-ttu-id="7d6dc-129">Örneğin, <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> bunları doğrulama hatası olarak kaydederek düzeltilemez değerleri düzgün şekilde işleyin.</span><span class="sxs-lookup"><span data-stu-id="7d6dc-129">For example, <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> and <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> handle unparseable values gracefully by registering them as validation errors.</span></span> <span data-ttu-id="7d6dc-130">Null değerleri kabul edebilecek türler, hedef alanın null değer alabilme durumunu da destekler (örneğin, `int?` ).</span><span class="sxs-lookup"><span data-stu-id="7d6dc-130">Types that can accept null values also support nullability of the target field (for example, `int?`).</span></span>

<span data-ttu-id="7d6dc-131">Aşağıdaki tür, daha `Starship` önce daha büyük bir özellik kümesi ve daha önceki veri açıklamalarını kullanarak doğrulama mantığını tanımlar `ExampleModel` :</span><span class="sxs-lookup"><span data-stu-id="7d6dc-131">The following `Starship` type defines validation logic using a larger set of properties and data annotations than the earlier `ExampleModel`:</span></span>

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

<span data-ttu-id="7d6dc-132">Önceki örnekte, `Description` hiçbir veri ek açıklaması mevcut olmadığından isteğe bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="7d6dc-132">In the preceding example, `Description` is optional because no data annotations are present.</span></span>

<span data-ttu-id="7d6dc-133">Aşağıdaki form, modelde tanımlanan doğrulamayı kullanarak Kullanıcı girişini doğrular `Starship` :</span><span class="sxs-lookup"><span data-stu-id="7d6dc-133">The following form validates user input using the validation defined in the `Starship` model:</span></span>

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

<span data-ttu-id="7d6dc-134">, <xref:Microsoft.AspNetCore.Components.Forms.EditForm> <xref:Microsoft.AspNetCore.Components.Forms.EditContext> Hangi alanların değiştirildiği ve geçerli doğrulama iletileri de dahil olmak üzere düzenleme işlemiyle ilgili meta verileri izleyen [basamaklı bir değer](xref:blazor/components#cascading-values-and-parameters) olarak oluşturur.</span><span class="sxs-lookup"><span data-stu-id="7d6dc-134">The <xref:Microsoft.AspNetCore.Components.Forms.EditForm> creates an <xref:Microsoft.AspNetCore.Components.Forms.EditContext> as a [cascading value](xref:blazor/components#cascading-values-and-parameters) that tracks metadata about the edit process, including which fields have been modified and the current validation messages.</span></span> <span data-ttu-id="7d6dc-135"><xref:Microsoft.AspNetCore.Components.Forms.EditForm>Ayrıca geçerli ve geçersiz Gönderimlerle (,) uygun olaylar <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnValidSubmit> sağlar <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnInvalidSubmit> .</span><span class="sxs-lookup"><span data-stu-id="7d6dc-135">The <xref:Microsoft.AspNetCore.Components.Forms.EditForm> also provides convenient events for valid and invalid submits (<xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnValidSubmit>, <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnInvalidSubmit>).</span></span> <span data-ttu-id="7d6dc-136">Alternatif olarak, <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit> doğrulamayı tetiklemek ve alan değerlerini özel doğrulama kodu ile denetlemek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="7d6dc-136">Alternatively, use <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit> to trigger the validation and check field values with custom validation code.</span></span>

<span data-ttu-id="7d6dc-137">Aşağıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="7d6dc-137">In the following example:</span></span>

* <span data-ttu-id="7d6dc-138">`HandleSubmit`Yöntemi, **Gönder** düğmesi seçildiğinde çalışır.</span><span class="sxs-lookup"><span data-stu-id="7d6dc-138">The `HandleSubmit` method runs when the **Submit** button is selected.</span></span>
* <span data-ttu-id="7d6dc-139">Form, formun ' i kullanılarak onaylanır <xref:Microsoft.AspNetCore.Components.Forms.EditContext> .</span><span class="sxs-lookup"><span data-stu-id="7d6dc-139">The form is validated using the form's <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span>
* <span data-ttu-id="7d6dc-140">Form, <xref:Microsoft.AspNetCore.Components.Forms.EditContext> `ServerValidate` sunucusunda BIR Web API uç noktası çağıran yönteme geçerek daha sonra onaylanır (*gösterilmez*).</span><span class="sxs-lookup"><span data-stu-id="7d6dc-140">The form is further validated by passing the <xref:Microsoft.AspNetCore.Components.Forms.EditContext> to the `ServerValidate` method that calls a web API endpoint on the server (*not shown*).</span></span>
* <span data-ttu-id="7d6dc-141">Ek kod, istemci ve sunucu tarafı doğrulamasının sonucuna bağlı olarak çalıştırılır `isValid` .</span><span class="sxs-lookup"><span data-stu-id="7d6dc-141">Additional code is run depending on the result of the client- and server-side validation by checking `isValid`.</span></span>

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

## <a name="inputtext-based-on-the-input-event"></a><span data-ttu-id="7d6dc-142">Giriş olayına göre InputText</span><span class="sxs-lookup"><span data-stu-id="7d6dc-142">InputText based on the input event</span></span>

<span data-ttu-id="7d6dc-143"><xref:Microsoft.AspNetCore.Components.Forms.InputText>Olayı yerine olayını kullanan özel bir bileşen oluşturmak için bileşenini kullanın `input` `change` .</span><span class="sxs-lookup"><span data-stu-id="7d6dc-143">Use the <xref:Microsoft.AspNetCore.Components.Forms.InputText> component to create a custom component that uses the `input` event instead of the `change` event.</span></span>

<span data-ttu-id="7d6dc-144">Aşağıdaki örnekte, `CustomInputText` bileşen Framework `InputText` bileşenini devralır ve olay bağlamayı ( <xref:Microsoft.AspNetCore.Components.EventCallbackFactoryBinderExtensions.CreateBinder%2A> ) `oninput` olaya ayarlar.</span><span class="sxs-lookup"><span data-stu-id="7d6dc-144">In the following example, the `CustomInputText` component inherits the framework's `InputText` component and sets the event binding (<xref:Microsoft.AspNetCore.Components.EventCallbackFactoryBinderExtensions.CreateBinder%2A>) to the `oninput` event.</span></span>

<span data-ttu-id="7d6dc-145">*Shared/CustomInputText. Razor*:</span><span class="sxs-lookup"><span data-stu-id="7d6dc-145">*Shared/CustomInputText.razor*:</span></span>

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

<span data-ttu-id="7d6dc-146">`CustomInputText`Bileşen her yerde kullanılabilir <xref:Microsoft.AspNetCore.Components.Forms.InputText> :</span><span class="sxs-lookup"><span data-stu-id="7d6dc-146">The `CustomInputText` component can be used anywhere <xref:Microsoft.AspNetCore.Components.Forms.InputText> is used:</span></span>

<span data-ttu-id="7d6dc-147">*Pages/TestForm. Razor*:</span><span class="sxs-lookup"><span data-stu-id="7d6dc-147">*Pages/TestForm.razor*:</span></span>

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

## <a name="work-with-radio-buttons"></a><span data-ttu-id="7d6dc-148">Radyo düğmeleriyle çalışma</span><span class="sxs-lookup"><span data-stu-id="7d6dc-148">Work with radio buttons</span></span>

<span data-ttu-id="7d6dc-149">Bir formda radyo düğmeleriyle çalışırken, radyo düğmeleri bir grup olarak değerlendirildiğinden veri bağlama diğer öğelerden farklı işlenir.</span><span class="sxs-lookup"><span data-stu-id="7d6dc-149">When working with radio buttons in a form, data binding is handled differently than other elements because radio buttons are evaluated as a group.</span></span> <span data-ttu-id="7d6dc-150">Her radyo düğmesinin değeri sabittir, ancak radyo düğmesi grubunun değeri seçili radyo düğmesinin değeridir.</span><span class="sxs-lookup"><span data-stu-id="7d6dc-150">The value of each radio button is fixed, but the value of the radio button group is the value of the selected radio button.</span></span> <span data-ttu-id="7d6dc-151">Aşağıdaki örnek, aşağıdakilerin nasıl yapılacağını göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="7d6dc-151">The following example shows how to:</span></span>

* <span data-ttu-id="7d6dc-152">Radyo düğmesi grubu için veri bağlamayı işleyin.</span><span class="sxs-lookup"><span data-stu-id="7d6dc-152">Handle data binding for a radio button group.</span></span>
* <span data-ttu-id="7d6dc-153">Özel bir bileşen kullanarak doğrulamayı destekler `InputRadio` .</span><span class="sxs-lookup"><span data-stu-id="7d6dc-153">Support validation using a custom `InputRadio` component.</span></span>

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

<span data-ttu-id="7d6dc-154">Aşağıdaki, <xref:Microsoft.AspNetCore.Components.Forms.EditForm> `InputRadio` kullanıcıdan bir derecelendirme almak ve doğrulamak için önceki bileşeni kullanır:</span><span class="sxs-lookup"><span data-stu-id="7d6dc-154">The following <xref:Microsoft.AspNetCore.Components.Forms.EditForm> uses the preceding `InputRadio` component to obtain and validate a rating from the user:</span></span>

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

## <a name="validation-support"></a><span data-ttu-id="7d6dc-155">Doğrulama desteği</span><span class="sxs-lookup"><span data-stu-id="7d6dc-155">Validation support</span></span>

<span data-ttu-id="7d6dc-156"><xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>Bileşen, Basamaklandırılan veri açıklamalarını kullanarak doğrulama desteğini iliştirir <xref:Microsoft.AspNetCore.Components.Forms.EditContext> .</span><span class="sxs-lookup"><span data-stu-id="7d6dc-156">The <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component attaches validation support using data annotations to the cascaded <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span> <span data-ttu-id="7d6dc-157">Veri ek açıklamalarını kullanarak doğrulama desteğinin etkinleştirilmesi bu açık hareketi gerektirir.</span><span class="sxs-lookup"><span data-stu-id="7d6dc-157">Enabling support for validation using data annotations requires this explicit gesture.</span></span> <span data-ttu-id="7d6dc-158">Veri ek açıklamalarıyla farklı bir doğrulama sistemi kullanmak için, öğesini <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> özel bir uygulamayla değiştirin.</span><span class="sxs-lookup"><span data-stu-id="7d6dc-158">To use a different validation system than data annotations, replace the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> with a custom implementation.</span></span> <span data-ttu-id="7d6dc-159">ASP.NET Core uygulama, başvuru kaynağında İnceleme için kullanılabilir: [dataannotationsvalidator](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs) / [adddataannotationsvalidation](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="7d6dc-159">The ASP.NET Core implementation is available for inspection in the reference source: [DataAnnotationsValidator](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).</span></span>

Blazor<span data-ttu-id="7d6dc-160">iki tür doğrulama gerçekleştirir:</span><span class="sxs-lookup"><span data-stu-id="7d6dc-160"> performs two types of validation:</span></span>

* <span data-ttu-id="7d6dc-161">*Alan doğrulama* , Kullanıcı bir alanın dışına eklendiğinde gerçekleştirilir.</span><span class="sxs-lookup"><span data-stu-id="7d6dc-161">*Field validation* is performed when the user tabs out of a field.</span></span> <span data-ttu-id="7d6dc-162">Alan doğrulama sırasında, <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> bileşen bildirilen tüm doğrulama sonuçlarını alanla ilişkilendirir.</span><span class="sxs-lookup"><span data-stu-id="7d6dc-162">During field validation, the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component associates all reported validation results with the field.</span></span>
* <span data-ttu-id="7d6dc-163">Kullanıcı formu gönderdiğinde *model doğrulaması* gerçekleştirilir.</span><span class="sxs-lookup"><span data-stu-id="7d6dc-163">*Model validation* is performed when the user submits the form.</span></span> <span data-ttu-id="7d6dc-164">Model doğrulama sırasında, <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> Bileşen, doğrulama sonucunun raporlandığı üye adına göre alanı belirlemeyi dener.</span><span class="sxs-lookup"><span data-stu-id="7d6dc-164">During model validation, the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component attempts to determine the field based on the member name that the validation result reports.</span></span> <span data-ttu-id="7d6dc-165">Tek bir üyeyle ilişkilendirilmeyen doğrulama sonuçları, bir alan yerine modeliyle ilişkilendirilir.</span><span class="sxs-lookup"><span data-stu-id="7d6dc-165">Validation results that aren't associated with an individual member are associated with the model rather than a field.</span></span>

### <a name="validation-summary-and-validation-message-components"></a><span data-ttu-id="7d6dc-166">Doğrulama özeti ve doğrulama Iletisi bileşenleri</span><span class="sxs-lookup"><span data-stu-id="7d6dc-166">Validation Summary and Validation Message components</span></span>

<span data-ttu-id="7d6dc-167"><xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary>Bileşen, [doğrulama özeti etiketi Yardımcısı](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper)'na benzer olan tüm doğrulama iletilerini özetler:</span><span class="sxs-lookup"><span data-stu-id="7d6dc-167">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component summarizes all validation messages, which is similar to the [Validation Summary Tag Helper](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper):</span></span>

```razor
<ValidationSummary />
```

<span data-ttu-id="7d6dc-168">Parametresi ile belirli bir model için çıkış doğrulama iletileri `Model` :</span><span class="sxs-lookup"><span data-stu-id="7d6dc-168">Output validation messages for a specific model with the `Model` parameter:</span></span>
  
```razor
<ValidationSummary Model="@starship" />
```

<span data-ttu-id="7d6dc-169"><xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601>Bileşeni, [doğrulama Iletisi etiketi Yardımcısı](xref:mvc/views/working-with-forms#the-validation-message-tag-helper)'na benzer şekilde belirli bir alan için doğrulama iletileri görüntüler.</span><span class="sxs-lookup"><span data-stu-id="7d6dc-169">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> component displays validation messages for a specific field, which is similar to the [Validation Message Tag Helper](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span></span> <span data-ttu-id="7d6dc-170">Özniteliği ile doğrulama için alanı `For` ve model özelliğini adlandırırken bir lambda ifadesini belirtin:</span><span class="sxs-lookup"><span data-stu-id="7d6dc-170">Specify the field for validation with the `For` attribute and a lambda expression naming the model property:</span></span>

```razor
<ValidationMessage For="@(() => starship.MaximumAccommodation)" />
```

<span data-ttu-id="7d6dc-171"><xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601>Ve <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> bileşenleri, rastgele öznitelikleri destekler.</span><span class="sxs-lookup"><span data-stu-id="7d6dc-171">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> and <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> components support arbitrary attributes.</span></span> <span data-ttu-id="7d6dc-172">Bir bileşen parametresiyle eşleşmeyen herhangi bir öznitelik oluşturulan `<div>` or `<ul>` öğesine eklenir.</span><span class="sxs-lookup"><span data-stu-id="7d6dc-172">Any attribute that doesn't match a component parameter is added to the generated `<div>` or `<ul>` element.</span></span>

### <a name="custom-validation-attributes"></a><span data-ttu-id="7d6dc-173">Özel doğrulama öznitelikleri</span><span class="sxs-lookup"><span data-stu-id="7d6dc-173">Custom validation attributes</span></span>

<span data-ttu-id="7d6dc-174">Bir doğrulama sonucunun [özel bir doğrulama özniteliği](xref:mvc/models/validation#custom-attributes)kullanılırken bir alanla doğru şekilde ilişkilendirildiğinden emin olmak için, şunu oluştururken doğrulama bağlamını geçirin <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> <xref:System.ComponentModel.DataAnnotations.ValidationResult> :</span><span class="sxs-lookup"><span data-stu-id="7d6dc-174">To ensure that a validation result is correctly associated with a field when using a [custom validation attribute](xref:mvc/models/validation#custom-attributes), pass the validation context's <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> when creating the <xref:System.ComponentModel.DataAnnotations.ValidationResult>:</span></span>

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

### <a name="blazor-data-annotations-validation-package"></a>Blazor<span data-ttu-id="7d6dc-175">veri ek açıklamaları doğrulama paketi</span><span class="sxs-lookup"><span data-stu-id="7d6dc-175"> data annotations validation package</span></span>

<span data-ttu-id="7d6dc-176">[Microsoft. AspNetCore. components. Dataaçıklamalarda. Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) , bileşeni kullanarak doğrulama deneyimini boşlukları dolduran bir pakettir <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> .</span><span class="sxs-lookup"><span data-stu-id="7d6dc-176">The [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) is a package that fills validation experience gaps using the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component.</span></span> <span data-ttu-id="7d6dc-177">Paket şu anda *deneysel*.</span><span class="sxs-lookup"><span data-stu-id="7d6dc-177">The package is currently *experimental*.</span></span>

### <a name="compareproperty-attribute"></a><span data-ttu-id="7d6dc-178">[CompareProperty] özniteliği</span><span class="sxs-lookup"><span data-stu-id="7d6dc-178">[CompareProperty] attribute</span></span>

<span data-ttu-id="7d6dc-179"><xref:System.ComponentModel.DataAnnotations.CompareAttribute> <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> Doğrulama sonucunu belirli bir üyeyle ilişkilendirmediği için bileşen ile iyi çalışmaz.</span><span class="sxs-lookup"><span data-stu-id="7d6dc-179">The <xref:System.ComponentModel.DataAnnotations.CompareAttribute> doesn't work well with the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component because it doesn't associate the validation result with a specific member.</span></span> <span data-ttu-id="7d6dc-180">Bu, alan düzeyi doğrulama ve tüm modelin bir gönderme sırasında doğrulanması arasındaki tutarsız davranışa neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="7d6dc-180">This can result in inconsistent behavior between field-level validation and when the entire model is validated on a submit.</span></span> <span data-ttu-id="7d6dc-181">[Microsoft. AspNetCore. components. Dataaçıklamalarda. Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) *deneysel* Package, `ComparePropertyAttribute` Bu sınırlamalara geçici bir çözüm olan ek bir doğrulama özniteliği sunar.</span><span class="sxs-lookup"><span data-stu-id="7d6dc-181">The [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) *experimental* package introduces an additional validation attribute, `ComparePropertyAttribute`, that works around these limitations.</span></span> <span data-ttu-id="7d6dc-182">Bir Blazor uygulamada, `[CompareProperty]` özniteliği için doğrudan değiştirme olur [`[Compare]`](xref:System.ComponentModel.DataAnnotations.CompareAttribute) .</span><span class="sxs-lookup"><span data-stu-id="7d6dc-182">In a Blazor app, `[CompareProperty]` is a direct replacement for the [`[Compare]`](xref:System.ComponentModel.DataAnnotations.CompareAttribute) attribute.</span></span>

### <a name="nested-models-collection-types-and-complex-types"></a><span data-ttu-id="7d6dc-183">İç içe modeller, koleksiyon türleri ve karmaşık türler</span><span class="sxs-lookup"><span data-stu-id="7d6dc-183">Nested models, collection types, and complex types</span></span>

Blazor<span data-ttu-id="7d6dc-184">yerleşik olan veri açıklamalarını kullanarak form girişini doğrulama desteği sağlar <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> .</span><span class="sxs-lookup"><span data-stu-id="7d6dc-184"> provides support for validating form input using data annotations with the built-in <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>.</span></span> <span data-ttu-id="7d6dc-185">Ancak, <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> yalnızca koleksiyonun üst düzey özelliklerini, koleksiyon veya karmaşık tür özellikleri olmayan forma doğrular.</span><span class="sxs-lookup"><span data-stu-id="7d6dc-185">However, the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> only validates top-level properties of the model bound to the form that aren't collection- or complex-type properties.</span></span>

<span data-ttu-id="7d6dc-186">Koleksiyon ve karmaşık tür özellikleri dahil olmak üzere, bağlantılı modelin tüm nesne grafiğini doğrulamak için, `ObjectGraphDataAnnotationsValidator` *deneysel* [Microsoft. Aspnetcore. components. dataaçıklamalarda. Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) Package tarafından sunulan öğesini kullanın:</span><span class="sxs-lookup"><span data-stu-id="7d6dc-186">To validate the bound model's entire object graph, including collection- and complex-type properties, use the `ObjectGraphDataAnnotationsValidator` provided by the *experimental* [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) package:</span></span>

```razor
<EditForm Model="@model" OnValidSubmit="HandleValidSubmit">
    <ObjectGraphDataAnnotationsValidator />
    ...
</EditForm>
```

<span data-ttu-id="7d6dc-187">İle model özelliklerine açıklama ekleyin `[ValidateComplexType]` .</span><span class="sxs-lookup"><span data-stu-id="7d6dc-187">Annotate model properties with `[ValidateComplexType]`.</span></span> <span data-ttu-id="7d6dc-188">Aşağıdaki model sınıflarında, `ShipDescription` sınıfı, model forma bağlandığında doğrulanacak ek veri açıklamalarını içerir:</span><span class="sxs-lookup"><span data-stu-id="7d6dc-188">In the following model classes, the `ShipDescription` class contains additional data annotations to validate when the model is bound to the form:</span></span>

<span data-ttu-id="7d6dc-189">*Starship.cs*:</span><span class="sxs-lookup"><span data-stu-id="7d6dc-189">*Starship.cs*:</span></span>

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

<span data-ttu-id="7d6dc-190">*ShipDescription.cs*:</span><span class="sxs-lookup"><span data-stu-id="7d6dc-190">*ShipDescription.cs*:</span></span>

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

### <a name="enable-the-submit-button-based-on-form-validation"></a><span data-ttu-id="7d6dc-191">Form doğrulamasına göre Gönder düğmesini etkinleştir</span><span class="sxs-lookup"><span data-stu-id="7d6dc-191">Enable the submit button based on form validation</span></span>

<span data-ttu-id="7d6dc-192">Form doğrulamasına göre Gönder düğmesini etkinleştirmek ve devre dışı bırakmak için:</span><span class="sxs-lookup"><span data-stu-id="7d6dc-192">To enable and disable the submit button based on form validation:</span></span>

* <span data-ttu-id="7d6dc-193"><xref:Microsoft.AspNetCore.Components.Forms.EditContext>Bileşen başlatıldığında modeli atamak için formunu kullanın.</span><span class="sxs-lookup"><span data-stu-id="7d6dc-193">Use the form's <xref:Microsoft.AspNetCore.Components.Forms.EditContext> to assign the model when the component is initialized.</span></span>
* <span data-ttu-id="7d6dc-194"><xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged>Gönder düğmesini etkinleştirmek ve devre dışı bırakmak için bağlam geri aramasında formu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="7d6dc-194">Validate the form in the context's <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged> callback to enable and disable the submit button.</span></span>
* <span data-ttu-id="7d6dc-195">Yöntemi içindeki olay işleyicisinin üstünden geri dön `Dispose` .</span><span class="sxs-lookup"><span data-stu-id="7d6dc-195">Unhook the event handler in the `Dispose` method.</span></span> <span data-ttu-id="7d6dc-196">Daha fazla bilgi için bkz. <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span><span class="sxs-lookup"><span data-stu-id="7d6dc-196">For more information, see <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span></span>

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

<span data-ttu-id="7d6dc-197">Yukarıdaki örnekte, şu şekilde ayarlayın `formInvalid` `false` :</span><span class="sxs-lookup"><span data-stu-id="7d6dc-197">In the preceding example, set `formInvalid` to `false` if:</span></span>

* <span data-ttu-id="7d6dc-198">Form geçerli varsayılan değerlerle önceden yüklenir.</span><span class="sxs-lookup"><span data-stu-id="7d6dc-198">The form is preloaded with valid default values.</span></span>
* <span data-ttu-id="7d6dc-199">Form yüklendiğinde Gönder düğmesinin etkinleştirilmesini istiyorsunuz.</span><span class="sxs-lookup"><span data-stu-id="7d6dc-199">You want the submit button enabled when the form loads.</span></span>

<span data-ttu-id="7d6dc-200">Önceki yaklaşımın yan etkisi, <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> Kullanıcı herhangi bir alanla etkileşime geçtiğinde bileşen geçersiz alanlarla doldurulmuştur.</span><span class="sxs-lookup"><span data-stu-id="7d6dc-200">A side effect of the preceding approach is that a <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component is populated with invalid fields after the user interacts with any one field.</span></span> <span data-ttu-id="7d6dc-201">Bu senaryoya aşağıdaki yollarla değinilerek şunlar olabilir:</span><span class="sxs-lookup"><span data-stu-id="7d6dc-201">This scenario can be addressed in either of the following ways:</span></span>

* <span data-ttu-id="7d6dc-202"><xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary>Form üzerinde bir bileşen kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="7d6dc-202">Don't use a <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component on the form.</span></span>
* <span data-ttu-id="7d6dc-203"><xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary>Gönder düğmesi seçildiğinde bileşeni görünür hale getirin (örneğin, bir `HandleValidSubmit` yöntemde).</span><span class="sxs-lookup"><span data-stu-id="7d6dc-203">Make the <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component visible when the submit button is selected (for example, in a `HandleValidSubmit` method).</span></span>

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

## <a name="troubleshoot"></a><span data-ttu-id="7d6dc-204">Sorun giderme</span><span class="sxs-lookup"><span data-stu-id="7d6dc-204">Troubleshoot</span></span>

> <span data-ttu-id="7d6dc-205">InvalidOperationException: EditForm bir model parametresi veya bir EditContext parametresi gerektiriyor, ancak her ikisini de içermemelidir.</span><span class="sxs-lookup"><span data-stu-id="7d6dc-205">InvalidOperationException: EditForm requires a Model parameter, or an EditContext parameter, but not both.</span></span>

<span data-ttu-id="7d6dc-206">Veya olduğunu doğrulayın <xref:Microsoft.AspNetCore.Components.Forms.EditForm> <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> <xref:Microsoft.AspNetCore.Components.Forms.EditContext> .</span><span class="sxs-lookup"><span data-stu-id="7d6dc-206">Confirm that the <xref:Microsoft.AspNetCore.Components.Forms.EditForm> has a <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> or <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span>

<span data-ttu-id="7d6dc-207">Forma atama yaparken <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> , aşağıdaki örnekte gösterildiği gibi model türünün örneği oluşturulmuş olduğunu doğrulayın:</span><span class="sxs-lookup"><span data-stu-id="7d6dc-207">When assigning a <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> to the form, confirm that the model type is instantiated, as the following example shows:</span></span>

```csharp
private ExampleModel exampleModel = new ExampleModel();
```
