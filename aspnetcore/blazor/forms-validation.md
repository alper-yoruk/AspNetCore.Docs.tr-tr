---
<span data-ttu-id="6989a-101">Başlık: ' ASP.NET Core Blazor form ve doğrulama ' Yazar: Açıklama: ' içinde formları ve alan doğrulama senaryolarını kullanmayı öğrenin Blazor . '</span><span class="sxs-lookup"><span data-stu-id="6989a-101">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="6989a-102">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6989a-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6989a-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6989a-103">'Blazor'</span></span>
- <span data-ttu-id="6989a-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6989a-104">'Identity'</span></span>
- <span data-ttu-id="6989a-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6989a-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="6989a-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6989a-106">'Razor'</span></span>
- <span data-ttu-id="6989a-107">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6989a-107">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-forms-and-validation"></a><span data-ttu-id="6989a-108">BlazorForms ve doğrulama ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6989a-108">ASP.NET Core Blazor forms and validation</span></span>

<span data-ttu-id="6989a-109">[Daniel Roth](https://github.com/danroth27) ve [Luke Latham](https://github.com/guardrex) tarafından</span><span class="sxs-lookup"><span data-stu-id="6989a-109">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="6989a-110">Formlar ve doğrulama, Blazor [veri ek açıklamaları](xref:mvc/models/validation)kullanılarak desteklenir.</span><span class="sxs-lookup"><span data-stu-id="6989a-110">Forms and validation are supported in Blazor using [data annotations](xref:mvc/models/validation).</span></span>

<span data-ttu-id="6989a-111">Aşağıdaki `ExampleModel` tür, veri ek açıklamalarını kullanarak doğrulama mantığını tanımlar:</span><span class="sxs-lookup"><span data-stu-id="6989a-111">The following `ExampleModel` type defines validation logic using data annotations:</span></span>

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

<span data-ttu-id="6989a-112">Bir form, bileşeni kullanılarak tanımlanır <xref:Microsoft.AspNetCore.Components.Forms.EditForm> .</span><span class="sxs-lookup"><span data-stu-id="6989a-112">A form is defined using the <xref:Microsoft.AspNetCore.Components.Forms.EditForm> component.</span></span> <span data-ttu-id="6989a-113">Aşağıdaki form tipik öğeleri, bileşenleri ve Razor kodu gösterir:</span><span class="sxs-lookup"><span data-stu-id="6989a-113">The following form demonstrates typical elements, components, and Razor code:</span></span>

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

<span data-ttu-id="6989a-114">Yukarıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="6989a-114">In the preceding example:</span></span>

* <span data-ttu-id="6989a-115">Form, `name` türünde tanımlanan doğrulamayı kullanarak alanda Kullanıcı girişini doğrular `ExampleModel` .</span><span class="sxs-lookup"><span data-stu-id="6989a-115">The form validates user input in the `name` field using the validation defined in the `ExampleModel` type.</span></span> <span data-ttu-id="6989a-116">Model bileşen `@code` bloğunda oluşturulur ve özel bir alanda ( `exampleModel` ) tutulur.</span><span class="sxs-lookup"><span data-stu-id="6989a-116">The model is created in the component's `@code` block and held in a private field (`exampleModel`).</span></span> <span data-ttu-id="6989a-117">Alanı, `Model` öğesinin özniteliğine atanır `<EditForm>` .</span><span class="sxs-lookup"><span data-stu-id="6989a-117">The field is assigned to the `Model` attribute of the `<EditForm>` element.</span></span>
* <span data-ttu-id="6989a-118"><xref:Microsoft.AspNetCore.Components.Forms.InputText>Bileşenin `@bind-Value` bağlamaları:</span><span class="sxs-lookup"><span data-stu-id="6989a-118">The <xref:Microsoft.AspNetCore.Components.Forms.InputText> component's `@bind-Value` binds:</span></span>
  * <span data-ttu-id="6989a-119">Model özelliği ( `exampleModel.Name` ) <xref:Microsoft.AspNetCore.Components.Forms.InputText> bileşen `Value` özelliğine.</span><span class="sxs-lookup"><span data-stu-id="6989a-119">The model property (`exampleModel.Name`) to the <xref:Microsoft.AspNetCore.Components.Forms.InputText> component's `Value` property.</span></span> <span data-ttu-id="6989a-120">Özellik bağlama hakkında daha fazla bilgi için bkz <xref:blazor/data-binding#parent-to-child-binding-with-component-parameters> ..</span><span class="sxs-lookup"><span data-stu-id="6989a-120">For more information on property binding, see <xref:blazor/data-binding#parent-to-child-binding-with-component-parameters>.</span></span>
  * <span data-ttu-id="6989a-121">Bileşen özelliğine bir değişiklik olayı temsilcisi <xref:Microsoft.AspNetCore.Components.Forms.InputText> `ValueChanged` .</span><span class="sxs-lookup"><span data-stu-id="6989a-121">A change event delegate to the <xref:Microsoft.AspNetCore.Components.Forms.InputText> component's `ValueChanged` property.</span></span>
* <span data-ttu-id="6989a-122"><xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>Bileşen, veri ek açıklamalarını kullanarak doğrulama desteği ekler.</span><span class="sxs-lookup"><span data-stu-id="6989a-122">The <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component attaches validation support using data annotations.</span></span>
* <span data-ttu-id="6989a-123"><xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary>Bileşen doğrulama iletilerini özetler.</span><span class="sxs-lookup"><span data-stu-id="6989a-123">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component summarizes validation messages.</span></span>
* <span data-ttu-id="6989a-124">`HandleValidSubmit`Form başarıyla gönderdiğinde tetiklenir (doğrulamayı geçirir).</span><span class="sxs-lookup"><span data-stu-id="6989a-124">`HandleValidSubmit` is triggered when the form successfully submits (passes validation).</span></span>

<span data-ttu-id="6989a-125">Kullanıcı girişini almak ve doğrulamak için yerleşik bir giriş bileşenleri kümesi vardır.</span><span class="sxs-lookup"><span data-stu-id="6989a-125">A set of built-in input components are available to receive and validate user input.</span></span> <span data-ttu-id="6989a-126">Girişler değiştirildiklerinde ve bir form gönderildiğinde onaylanır.</span><span class="sxs-lookup"><span data-stu-id="6989a-126">Inputs are validated when they're changed and when a form is submitted.</span></span> <span data-ttu-id="6989a-127">Kullanılabilir giriş bileşenleri aşağıdaki tabloda gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="6989a-127">Available input components are shown in the following table.</span></span>

| <span data-ttu-id="6989a-128">Giriş bileşeni</span><span class="sxs-lookup"><span data-stu-id="6989a-128">Input component</span></span> | <span data-ttu-id="6989a-129">Olarak işlendi&hellip;</span><span class="sxs-lookup"><span data-stu-id="6989a-129">Rendered as&hellip;</span></span> |
| ---
<span data-ttu-id="6989a-130">Başlık: ' ASP.NET Core Blazor form ve doğrulama ' Yazar: Açıklama: ' içinde formları ve alan doğrulama senaryolarını kullanmayı öğrenin Blazor . '</span><span class="sxs-lookup"><span data-stu-id="6989a-130">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="6989a-131">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6989a-131">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6989a-132">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6989a-132">'Blazor'</span></span>
- <span data-ttu-id="6989a-133">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6989a-133">'Identity'</span></span>
- <span data-ttu-id="6989a-134">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6989a-134">'Let's Encrypt'</span></span>
- <span data-ttu-id="6989a-135">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6989a-135">'Razor'</span></span>
- <span data-ttu-id="6989a-136">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6989a-136">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6989a-137">Başlık: ' ASP.NET Core Blazor form ve doğrulama ' Yazar: Açıklama: ' içinde formları ve alan doğrulama senaryolarını kullanmayı öğrenin Blazor . '</span><span class="sxs-lookup"><span data-stu-id="6989a-137">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="6989a-138">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6989a-138">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6989a-139">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6989a-139">'Blazor'</span></span>
- <span data-ttu-id="6989a-140">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6989a-140">'Identity'</span></span>
- <span data-ttu-id="6989a-141">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6989a-141">'Let's Encrypt'</span></span>
- <span data-ttu-id="6989a-142">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6989a-142">'Razor'</span></span>
- <span data-ttu-id="6989a-143">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6989a-143">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6989a-144">Başlık: ' ASP.NET Core Blazor form ve doğrulama ' Yazar: Açıklama: ' içinde formları ve alan doğrulama senaryolarını kullanmayı öğrenin Blazor . '</span><span class="sxs-lookup"><span data-stu-id="6989a-144">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="6989a-145">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6989a-145">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6989a-146">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6989a-146">'Blazor'</span></span>
- <span data-ttu-id="6989a-147">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6989a-147">'Identity'</span></span>
- <span data-ttu-id="6989a-148">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6989a-148">'Let's Encrypt'</span></span>
- <span data-ttu-id="6989a-149">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6989a-149">'Razor'</span></span>
- <span data-ttu-id="6989a-150">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6989a-150">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6989a-151">Başlık: ' ASP.NET Core Blazor form ve doğrulama ' Yazar: Açıklama: ' içinde formları ve alan doğrulama senaryolarını kullanmayı öğrenin Blazor . '</span><span class="sxs-lookup"><span data-stu-id="6989a-151">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="6989a-152">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6989a-152">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6989a-153">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6989a-153">'Blazor'</span></span>
- <span data-ttu-id="6989a-154">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6989a-154">'Identity'</span></span>
- <span data-ttu-id="6989a-155">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6989a-155">'Let's Encrypt'</span></span>
- <span data-ttu-id="6989a-156">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6989a-156">'Razor'</span></span>
- <span data-ttu-id="6989a-157">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6989a-157">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6989a-158">Başlık: ' ASP.NET Core Blazor form ve doğrulama ' Yazar: Açıklama: ' içinde formları ve alan doğrulama senaryolarını kullanmayı öğrenin Blazor . '</span><span class="sxs-lookup"><span data-stu-id="6989a-158">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="6989a-159">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6989a-159">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6989a-160">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6989a-160">'Blazor'</span></span>
- <span data-ttu-id="6989a-161">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6989a-161">'Identity'</span></span>
- <span data-ttu-id="6989a-162">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6989a-162">'Let's Encrypt'</span></span>
- <span data-ttu-id="6989a-163">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6989a-163">'Razor'</span></span>
- <span data-ttu-id="6989a-164">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6989a-164">'SignalR' uid:</span></span> 

<span data-ttu-id="6989a-165">-------- | ---title: ' ASP.NET Core Blazor form ve doğrulama ' Yazar: Açıklama: ' içinde formları ve alan doğrulama senaryolarını kullanmayı öğrenin Blazor . '</span><span class="sxs-lookup"><span data-stu-id="6989a-165">-------- | --- title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="6989a-166">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6989a-166">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6989a-167">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6989a-167">'Blazor'</span></span>
- <span data-ttu-id="6989a-168">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6989a-168">'Identity'</span></span>
- <span data-ttu-id="6989a-169">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6989a-169">'Let's Encrypt'</span></span>
- <span data-ttu-id="6989a-170">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6989a-170">'Razor'</span></span>
- <span data-ttu-id="6989a-171">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6989a-171">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6989a-172">Başlık: ' ASP.NET Core Blazor form ve doğrulama ' Yazar: Açıklama: ' içinde formları ve alan doğrulama senaryolarını kullanmayı öğrenin Blazor . '</span><span class="sxs-lookup"><span data-stu-id="6989a-172">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="6989a-173">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6989a-173">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6989a-174">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6989a-174">'Blazor'</span></span>
- <span data-ttu-id="6989a-175">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6989a-175">'Identity'</span></span>
- <span data-ttu-id="6989a-176">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6989a-176">'Let's Encrypt'</span></span>
- <span data-ttu-id="6989a-177">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6989a-177">'Razor'</span></span>
- <span data-ttu-id="6989a-178">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6989a-178">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6989a-179">Başlık: ' ASP.NET Core Blazor form ve doğrulama ' Yazar: Açıklama: ' içinde formları ve alan doğrulama senaryolarını kullanmayı öğrenin Blazor . '</span><span class="sxs-lookup"><span data-stu-id="6989a-179">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="6989a-180">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6989a-180">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6989a-181">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6989a-181">'Blazor'</span></span>
- <span data-ttu-id="6989a-182">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6989a-182">'Identity'</span></span>
- <span data-ttu-id="6989a-183">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6989a-183">'Let's Encrypt'</span></span>
- <span data-ttu-id="6989a-184">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6989a-184">'Razor'</span></span>
- <span data-ttu-id="6989a-185">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6989a-185">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6989a-186">Başlık: ' ASP.NET Core Blazor form ve doğrulama ' Yazar: Açıklama: ' içinde formları ve alan doğrulama senaryolarını kullanmayı öğrenin Blazor . '</span><span class="sxs-lookup"><span data-stu-id="6989a-186">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="6989a-187">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6989a-187">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6989a-188">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6989a-188">'Blazor'</span></span>
- <span data-ttu-id="6989a-189">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6989a-189">'Identity'</span></span>
- <span data-ttu-id="6989a-190">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6989a-190">'Let's Encrypt'</span></span>
- <span data-ttu-id="6989a-191">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6989a-191">'Razor'</span></span>
- <span data-ttu-id="6989a-192">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6989a-192">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6989a-193">Başlık: ' ASP.NET Core Blazor form ve doğrulama ' Yazar: Açıklama: ' içinde formları ve alan doğrulama senaryolarını kullanmayı öğrenin Blazor . '</span><span class="sxs-lookup"><span data-stu-id="6989a-193">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="6989a-194">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6989a-194">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6989a-195">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6989a-195">'Blazor'</span></span>
- <span data-ttu-id="6989a-196">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6989a-196">'Identity'</span></span>
- <span data-ttu-id="6989a-197">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6989a-197">'Let's Encrypt'</span></span>
- <span data-ttu-id="6989a-198">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6989a-198">'Razor'</span></span>
- <span data-ttu-id="6989a-199">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6989a-199">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6989a-200">Başlık: ' ASP.NET Core Blazor form ve doğrulama ' Yazar: Açıklama: ' içinde formları ve alan doğrulama senaryolarını kullanmayı öğrenin Blazor . '</span><span class="sxs-lookup"><span data-stu-id="6989a-200">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="6989a-201">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6989a-201">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6989a-202">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6989a-202">'Blazor'</span></span>
- <span data-ttu-id="6989a-203">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6989a-203">'Identity'</span></span>
- <span data-ttu-id="6989a-204">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6989a-204">'Let's Encrypt'</span></span>
- <span data-ttu-id="6989a-205">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6989a-205">'Razor'</span></span>
- <span data-ttu-id="6989a-206">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6989a-206">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6989a-207">Başlık: ' ASP.NET Core Blazor form ve doğrulama ' Yazar: Açıklama: ' içinde formları ve alan doğrulama senaryolarını kullanmayı öğrenin Blazor . '</span><span class="sxs-lookup"><span data-stu-id="6989a-207">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="6989a-208">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6989a-208">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6989a-209">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6989a-209">'Blazor'</span></span>
- <span data-ttu-id="6989a-210">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6989a-210">'Identity'</span></span>
- <span data-ttu-id="6989a-211">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6989a-211">'Let's Encrypt'</span></span>
- <span data-ttu-id="6989a-212">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6989a-212">'Razor'</span></span>
- <span data-ttu-id="6989a-213">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6989a-213">'SignalR' uid:</span></span> 

<span data-ttu-id="6989a-214">---------- | | <xref:Microsoft.AspNetCore.Components.Forms.InputText> | `<input>` | | <xref:Microsoft.AspNetCore.Components.Forms.InputTextArea> | `<textarea>` | | <xref:Microsoft.AspNetCore.Components.Forms.InputSelect%601> | `<select>` | | <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> | `<input type="number">` | | <xref:Microsoft.AspNetCore.Components.Forms.InputCheckbox> | `<input type="checkbox">` | | <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> | `<input type="date">` |</span><span class="sxs-lookup"><span data-stu-id="6989a-214">---------- | | <xref:Microsoft.AspNetCore.Components.Forms.InputText> | `<input>` | | <xref:Microsoft.AspNetCore.Components.Forms.InputTextArea> | `<textarea>` | | <xref:Microsoft.AspNetCore.Components.Forms.InputSelect%601> | `<select>` | | <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> | `<input type="number">` | | <xref:Microsoft.AspNetCore.Components.Forms.InputCheckbox> | `<input type="checkbox">` | | <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> | `<input type="date">` |</span></span>

<span data-ttu-id="6989a-215">Dahil olmak üzere tüm giriş bileşenleri, <xref:Microsoft.AspNetCore.Components.Forms.EditForm> rastgele öznitelikleri destekler.</span><span class="sxs-lookup"><span data-stu-id="6989a-215">All of the input components, including <xref:Microsoft.AspNetCore.Components.Forms.EditForm>, support arbitrary attributes.</span></span> <span data-ttu-id="6989a-216">Bir bileşen parametresiyle eşleşmeyen herhangi bir öznitelik işlenmiş HTML öğesine eklenir.</span><span class="sxs-lookup"><span data-stu-id="6989a-216">Any attribute that doesn't match a component parameter is added to the rendered HTML element.</span></span>

<span data-ttu-id="6989a-217">Giriş bileşenleri, düzenleme sırasında doğrulamak ve CSS sınıfını alan durumunu yansıtacak şekilde değiştirmek için varsayılan davranışı sağlar.</span><span class="sxs-lookup"><span data-stu-id="6989a-217">Input components provide default behavior for validating on edit and changing their CSS class to reflect the field state.</span></span> <span data-ttu-id="6989a-218">Bazı bileşenler, yararlı ayrıştırma mantığını içerir.</span><span class="sxs-lookup"><span data-stu-id="6989a-218">Some components include useful parsing logic.</span></span> <span data-ttu-id="6989a-219">Örneğin, <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> bunları doğrulama hatası olarak kaydederek düzeltilemez değerleri düzgün şekilde işleyin.</span><span class="sxs-lookup"><span data-stu-id="6989a-219">For example, <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> and <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> handle unparseable values gracefully by registering them as validation errors.</span></span> <span data-ttu-id="6989a-220">Null değerleri kabul edebilecek türler, hedef alanın null değer alabilme durumunu da destekler (örneğin, `int?` ).</span><span class="sxs-lookup"><span data-stu-id="6989a-220">Types that can accept null values also support nullability of the target field (for example, `int?`).</span></span>

<span data-ttu-id="6989a-221">Aşağıdaki tür, daha `Starship` önce daha büyük bir özellik kümesi ve daha önceki veri açıklamalarını kullanarak doğrulama mantığını tanımlar `ExampleModel` :</span><span class="sxs-lookup"><span data-stu-id="6989a-221">The following `Starship` type defines validation logic using a larger set of properties and data annotations than the earlier `ExampleModel`:</span></span>

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

<span data-ttu-id="6989a-222">Önceki örnekte, `Description` hiçbir veri ek açıklaması mevcut olmadığından isteğe bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="6989a-222">In the preceding example, `Description` is optional because no data annotations are present.</span></span>

<span data-ttu-id="6989a-223">Aşağıdaki form, modelde tanımlanan doğrulamayı kullanarak Kullanıcı girişini doğrular `Starship` :</span><span class="sxs-lookup"><span data-stu-id="6989a-223">The following form validates user input using the validation defined in the `Starship` model:</span></span>

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

<span data-ttu-id="6989a-224">, <xref:Microsoft.AspNetCore.Components.Forms.EditForm> <xref:Microsoft.AspNetCore.Components.Forms.EditContext> Hangi alanların değiştirildiği ve geçerli doğrulama iletileri de dahil olmak üzere düzenleme işlemiyle ilgili meta verileri izleyen [basamaklı bir değer](xref:blazor/components#cascading-values-and-parameters) olarak oluşturur.</span><span class="sxs-lookup"><span data-stu-id="6989a-224">The <xref:Microsoft.AspNetCore.Components.Forms.EditForm> creates an <xref:Microsoft.AspNetCore.Components.Forms.EditContext> as a [cascading value](xref:blazor/components#cascading-values-and-parameters) that tracks metadata about the edit process, including which fields have been modified and the current validation messages.</span></span> <span data-ttu-id="6989a-225"><xref:Microsoft.AspNetCore.Components.Forms.EditForm>Ayrıca geçerli ve geçersiz Gönderimlerle (,) uygun olaylar <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnValidSubmit> sağlar <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnInvalidSubmit> .</span><span class="sxs-lookup"><span data-stu-id="6989a-225">The <xref:Microsoft.AspNetCore.Components.Forms.EditForm> also provides convenient events for valid and invalid submits (<xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnValidSubmit>, <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnInvalidSubmit>).</span></span> <span data-ttu-id="6989a-226">Alternatif olarak, <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit> doğrulamayı tetiklemek ve alan değerlerini özel doğrulama kodu ile denetlemek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="6989a-226">Alternatively, use <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit> to trigger the validation and check field values with custom validation code.</span></span>

<span data-ttu-id="6989a-227">Aşağıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="6989a-227">In the following example:</span></span>

* <span data-ttu-id="6989a-228">`HandleSubmit`Yöntemi, **Gönder** düğmesi seçildiğinde çalışır.</span><span class="sxs-lookup"><span data-stu-id="6989a-228">The `HandleSubmit` method runs when the **Submit** button is selected.</span></span>
* <span data-ttu-id="6989a-229">Form, formun ' i kullanılarak onaylanır <xref:Microsoft.AspNetCore.Components.Forms.EditContext> .</span><span class="sxs-lookup"><span data-stu-id="6989a-229">The form is validated using the form's <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span>
* <span data-ttu-id="6989a-230">Form, <xref:Microsoft.AspNetCore.Components.Forms.EditContext> `ServerValidate` sunucusunda BIR Web API uç noktası çağıran yönteme geçerek daha sonra onaylanır (*gösterilmez*).</span><span class="sxs-lookup"><span data-stu-id="6989a-230">The form is further validated by passing the <xref:Microsoft.AspNetCore.Components.Forms.EditContext> to the `ServerValidate` method that calls a web API endpoint on the server (*not shown*).</span></span>
* <span data-ttu-id="6989a-231">Ek kod, istemci ve sunucu tarafı doğrulamasının sonucuna bağlı olarak çalıştırılır `isValid` .</span><span class="sxs-lookup"><span data-stu-id="6989a-231">Additional code is run depending on the result of the client- and server-side validation by checking `isValid`.</span></span>

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

## <a name="inputtext-based-on-the-input-event"></a><span data-ttu-id="6989a-232">Giriş olayına göre InputText</span><span class="sxs-lookup"><span data-stu-id="6989a-232">InputText based on the input event</span></span>

<span data-ttu-id="6989a-233"><xref:Microsoft.AspNetCore.Components.Forms.InputText>Olayı yerine olayını kullanan özel bir bileşen oluşturmak için bileşenini kullanın `input` `change` .</span><span class="sxs-lookup"><span data-stu-id="6989a-233">Use the <xref:Microsoft.AspNetCore.Components.Forms.InputText> component to create a custom component that uses the `input` event instead of the `change` event.</span></span>

<span data-ttu-id="6989a-234">Aşağıdaki biçimlendirmeye sahip bir bileşen oluşturun ve bileşeni tıpkı kullanıldığı gibi kullanın <xref:Microsoft.AspNetCore.Components.Forms.InputText> :</span><span class="sxs-lookup"><span data-stu-id="6989a-234">Create a component with the following markup, and use the component just as <xref:Microsoft.AspNetCore.Components.Forms.InputText> is used:</span></span>

```razor
@inherits InputText

<input 
    @attributes="AdditionalAttributes" 
    class="@CssClass" 
    value="@CurrentValue" 
    @oninput="EventCallback.Factory.CreateBinder<string>(
        this, __value => CurrentValueAsString = __value, CurrentValueAsString)" />
```

## <a name="work-with-radio-buttons"></a><span data-ttu-id="6989a-235">Radyo düğmeleriyle çalışma</span><span class="sxs-lookup"><span data-stu-id="6989a-235">Work with radio buttons</span></span>

<span data-ttu-id="6989a-236">Bir formda radyo düğmeleriyle çalışırken, radyo düğmeleri bir grup olarak değerlendirildiğinden veri bağlama diğer öğelerden farklı işlenir.</span><span class="sxs-lookup"><span data-stu-id="6989a-236">When working with radio buttons in a form, data binding is handled differently than other elements because radio buttons are evaluated as a group.</span></span> <span data-ttu-id="6989a-237">Her radyo düğmesinin değeri sabittir, ancak radyo düğmesi grubunun değeri seçili radyo düğmesinin değeridir.</span><span class="sxs-lookup"><span data-stu-id="6989a-237">The value of each radio button is fixed, but the value of the radio button group is the value of the selected radio button.</span></span> <span data-ttu-id="6989a-238">Aşağıdaki örnek, aşağıdakilerin nasıl yapılacağını göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="6989a-238">The following example shows how to:</span></span>

* <span data-ttu-id="6989a-239">Radyo düğmesi grubu için veri bağlamayı işleyin.</span><span class="sxs-lookup"><span data-stu-id="6989a-239">Handle data binding for a radio button group.</span></span>
* <span data-ttu-id="6989a-240">Özel bir bileşen kullanarak doğrulamayı destekler `InputRadio` .</span><span class="sxs-lookup"><span data-stu-id="6989a-240">Support validation using a custom `InputRadio` component.</span></span>

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

<span data-ttu-id="6989a-241">Aşağıdaki, <xref:Microsoft.AspNetCore.Components.Forms.EditForm> `InputRadio` kullanıcıdan bir derecelendirme almak ve doğrulamak için önceki bileşeni kullanır:</span><span class="sxs-lookup"><span data-stu-id="6989a-241">The following <xref:Microsoft.AspNetCore.Components.Forms.EditForm> uses the preceding `InputRadio` component to obtain and validate a rating from the user:</span></span>

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

## <a name="validation-support"></a><span data-ttu-id="6989a-242">Doğrulama desteği</span><span class="sxs-lookup"><span data-stu-id="6989a-242">Validation support</span></span>

<span data-ttu-id="6989a-243"><xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>Bileşen, Basamaklandırılan veri açıklamalarını kullanarak doğrulama desteğini iliştirir <xref:Microsoft.AspNetCore.Components.Forms.EditContext> .</span><span class="sxs-lookup"><span data-stu-id="6989a-243">The <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component attaches validation support using data annotations to the cascaded <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span> <span data-ttu-id="6989a-244">Veri ek açıklamalarını kullanarak doğrulama desteğinin etkinleştirilmesi bu açık hareketi gerektirir.</span><span class="sxs-lookup"><span data-stu-id="6989a-244">Enabling support for validation using data annotations requires this explicit gesture.</span></span> <span data-ttu-id="6989a-245">Veri ek açıklamalarıyla farklı bir doğrulama sistemi kullanmak için, öğesini <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> özel bir uygulamayla değiştirin.</span><span class="sxs-lookup"><span data-stu-id="6989a-245">To use a different validation system than data annotations, replace the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> with a custom implementation.</span></span> <span data-ttu-id="6989a-246">ASP.NET Core uygulama, başvuru kaynağında İnceleme için kullanılabilir: [dataannotationsvalidator](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs) / [adddataannotationsvalidation](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="6989a-246">The ASP.NET Core implementation is available for inspection in the reference source: [DataAnnotationsValidator](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).</span></span>

Blazor<span data-ttu-id="6989a-247">iki tür doğrulama gerçekleştirir:</span><span class="sxs-lookup"><span data-stu-id="6989a-247"> performs two types of validation:</span></span>

* <span data-ttu-id="6989a-248">*Alan doğrulama* , Kullanıcı bir alanın dışına eklendiğinde gerçekleştirilir.</span><span class="sxs-lookup"><span data-stu-id="6989a-248">*Field validation* is performed when the user tabs out of a field.</span></span> <span data-ttu-id="6989a-249">Alan doğrulama sırasında, <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> bileşen bildirilen tüm doğrulama sonuçlarını alanla ilişkilendirir.</span><span class="sxs-lookup"><span data-stu-id="6989a-249">During field validation, the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component associates all reported validation results with the field.</span></span>
* <span data-ttu-id="6989a-250">Kullanıcı formu gönderdiğinde *model doğrulaması* gerçekleştirilir.</span><span class="sxs-lookup"><span data-stu-id="6989a-250">*Model validation* is performed when the user submits the form.</span></span> <span data-ttu-id="6989a-251">Model doğrulama sırasında, <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> Bileşen, doğrulama sonucunun raporlandığı üye adına göre alanı belirlemeyi dener.</span><span class="sxs-lookup"><span data-stu-id="6989a-251">During model validation, the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component attempts to determine the field based on the member name that the validation result reports.</span></span> <span data-ttu-id="6989a-252">Tek bir üyeyle ilişkilendirilmeyen doğrulama sonuçları, bir alan yerine modeliyle ilişkilendirilir.</span><span class="sxs-lookup"><span data-stu-id="6989a-252">Validation results that aren't associated with an individual member are associated with the model rather than a field.</span></span>

### <a name="validation-summary-and-validation-message-components"></a><span data-ttu-id="6989a-253">Doğrulama özeti ve doğrulama Iletisi bileşenleri</span><span class="sxs-lookup"><span data-stu-id="6989a-253">Validation Summary and Validation Message components</span></span>

<span data-ttu-id="6989a-254"><xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary>Bileşen, [doğrulama özeti etiketi Yardımcısı](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper)'na benzer olan tüm doğrulama iletilerini özetler:</span><span class="sxs-lookup"><span data-stu-id="6989a-254">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component summarizes all validation messages, which is similar to the [Validation Summary Tag Helper](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper):</span></span>

```razor
<ValidationSummary />
```

<span data-ttu-id="6989a-255">Parametresi ile belirli bir model için çıkış doğrulama iletileri `Model` :</span><span class="sxs-lookup"><span data-stu-id="6989a-255">Output validation messages for a specific model with the `Model` parameter:</span></span>
  
```razor
<ValidationSummary Model="@starship" />
```

<span data-ttu-id="6989a-256"><xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601>Bileşeni, [doğrulama Iletisi etiketi Yardımcısı](xref:mvc/views/working-with-forms#the-validation-message-tag-helper)'na benzer şekilde belirli bir alan için doğrulama iletileri görüntüler.</span><span class="sxs-lookup"><span data-stu-id="6989a-256">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> component displays validation messages for a specific field, which is similar to the [Validation Message Tag Helper](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span></span> <span data-ttu-id="6989a-257">Özniteliği ile doğrulama için alanı `For` ve model özelliğini adlandırırken bir lambda ifadesini belirtin:</span><span class="sxs-lookup"><span data-stu-id="6989a-257">Specify the field for validation with the `For` attribute and a lambda expression naming the model property:</span></span>

```razor
<ValidationMessage For="@(() => starship.MaximumAccommodation)" />
```

<span data-ttu-id="6989a-258"><xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601>Ve <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> bileşenleri, rastgele öznitelikleri destekler.</span><span class="sxs-lookup"><span data-stu-id="6989a-258">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> and <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> components support arbitrary attributes.</span></span> <span data-ttu-id="6989a-259">Bir bileşen parametresiyle eşleşmeyen herhangi bir öznitelik oluşturulan `<div>` or `<ul>` öğesine eklenir.</span><span class="sxs-lookup"><span data-stu-id="6989a-259">Any attribute that doesn't match a component parameter is added to the generated `<div>` or `<ul>` element.</span></span>

### <a name="custom-validation-attributes"></a><span data-ttu-id="6989a-260">Özel doğrulama öznitelikleri</span><span class="sxs-lookup"><span data-stu-id="6989a-260">Custom validation attributes</span></span>

<span data-ttu-id="6989a-261">Bir doğrulama sonucunun [özel bir doğrulama özniteliği](xref:mvc/models/validation#custom-attributes)kullanılırken bir alanla doğru şekilde ilişkilendirildiğinden emin olmak için, şunu oluştururken doğrulama bağlamını geçirin <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> <xref:System.ComponentModel.DataAnnotations.ValidationResult> :</span><span class="sxs-lookup"><span data-stu-id="6989a-261">To ensure that a validation result is correctly associated with a field when using a [custom validation attribute](xref:mvc/models/validation#custom-attributes), pass the validation context's <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> when creating the <xref:System.ComponentModel.DataAnnotations.ValidationResult>:</span></span>

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

### <a name="blazor-data-annotations-validation-package"></a>Blazor<span data-ttu-id="6989a-262">veri ek açıklamaları doğrulama paketi</span><span class="sxs-lookup"><span data-stu-id="6989a-262"> data annotations validation package</span></span>

<span data-ttu-id="6989a-263">[Microsoft. AspNetCore. components. Dataaçıklamalarda. Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) , bileşeni kullanarak doğrulama deneyimini boşlukları dolduran bir pakettir <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> .</span><span class="sxs-lookup"><span data-stu-id="6989a-263">The [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) is a package that fills validation experience gaps using the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component.</span></span> <span data-ttu-id="6989a-264">Paket şu anda *deneysel*.</span><span class="sxs-lookup"><span data-stu-id="6989a-264">The package is currently *experimental*.</span></span>

### <a name="compareproperty-attribute"></a><span data-ttu-id="6989a-265">[CompareProperty] özniteliği</span><span class="sxs-lookup"><span data-stu-id="6989a-265">[CompareProperty] attribute</span></span>

<span data-ttu-id="6989a-266"><xref:System.ComponentModel.DataAnnotations.CompareAttribute> <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> Doğrulama sonucunu belirli bir üyeyle ilişkilendirmediği için bileşen ile iyi çalışmaz.</span><span class="sxs-lookup"><span data-stu-id="6989a-266">The <xref:System.ComponentModel.DataAnnotations.CompareAttribute> doesn't work well with the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component because it doesn't associate the validation result with a specific member.</span></span> <span data-ttu-id="6989a-267">Bu, alan düzeyi doğrulama ve tüm modelin bir gönderme sırasında doğrulanması arasındaki tutarsız davranışa neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="6989a-267">This can result in inconsistent behavior between field-level validation and when the entire model is validated on a submit.</span></span> <span data-ttu-id="6989a-268">[Microsoft. AspNetCore. components. Dataaçıklamalarda. Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) *deneysel* Package, `ComparePropertyAttribute` Bu sınırlamalara geçici bir çözüm olan ek bir doğrulama özniteliği sunar.</span><span class="sxs-lookup"><span data-stu-id="6989a-268">The [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) *experimental* package introduces an additional validation attribute, `ComparePropertyAttribute`, that works around these limitations.</span></span> <span data-ttu-id="6989a-269">Bir Blazor uygulamada, `[CompareProperty]` özniteliği için doğrudan değiştirme olur [`[Compare]`](xref:System.ComponentModel.DataAnnotations.CompareAttribute) .</span><span class="sxs-lookup"><span data-stu-id="6989a-269">In a Blazor app, `[CompareProperty]` is a direct replacement for the [`[Compare]`](xref:System.ComponentModel.DataAnnotations.CompareAttribute) attribute.</span></span>

### <a name="nested-models-collection-types-and-complex-types"></a><span data-ttu-id="6989a-270">İç içe modeller, koleksiyon türleri ve karmaşık türler</span><span class="sxs-lookup"><span data-stu-id="6989a-270">Nested models, collection types, and complex types</span></span>

Blazor<span data-ttu-id="6989a-271">yerleşik olan veri açıklamalarını kullanarak form girişini doğrulama desteği sağlar <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> .</span><span class="sxs-lookup"><span data-stu-id="6989a-271"> provides support for validating form input using data annotations with the built-in <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>.</span></span> <span data-ttu-id="6989a-272">Ancak, <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> yalnızca koleksiyonun üst düzey özelliklerini, koleksiyon veya karmaşık tür özellikleri olmayan forma doğrular.</span><span class="sxs-lookup"><span data-stu-id="6989a-272">However, the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> only validates top-level properties of the model bound to the form that aren't collection- or complex-type properties.</span></span>

<span data-ttu-id="6989a-273">Koleksiyon ve karmaşık tür özellikleri dahil olmak üzere, bağlantılı modelin tüm nesne grafiğini doğrulamak için, `ObjectGraphDataAnnotationsValidator` *deneysel* [Microsoft. Aspnetcore. components. dataaçıklamalarda. Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) Package tarafından sunulan öğesini kullanın:</span><span class="sxs-lookup"><span data-stu-id="6989a-273">To validate the bound model's entire object graph, including collection- and complex-type properties, use the `ObjectGraphDataAnnotationsValidator` provided by the *experimental* [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) package:</span></span>

```razor
<EditForm Model="@model" OnValidSubmit="HandleValidSubmit">
    <ObjectGraphDataAnnotationsValidator />
    ...
</EditForm>
```

<span data-ttu-id="6989a-274">İle model özelliklerine açıklama ekleyin `[ValidateComplexType]` .</span><span class="sxs-lookup"><span data-stu-id="6989a-274">Annotate model properties with `[ValidateComplexType]`.</span></span> <span data-ttu-id="6989a-275">Aşağıdaki model sınıflarında, `ShipDescription` sınıfı, model forma bağlandığında doğrulanacak ek veri açıklamalarını içerir:</span><span class="sxs-lookup"><span data-stu-id="6989a-275">In the following model classes, the `ShipDescription` class contains additional data annotations to validate when the model is bound to the form:</span></span>

<span data-ttu-id="6989a-276">*Starship.cs*:</span><span class="sxs-lookup"><span data-stu-id="6989a-276">*Starship.cs*:</span></span>

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

<span data-ttu-id="6989a-277">*ShipDescription.cs*:</span><span class="sxs-lookup"><span data-stu-id="6989a-277">*ShipDescription.cs*:</span></span>

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

### <a name="enable-the-submit-button-based-on-form-validation"></a><span data-ttu-id="6989a-278">Form doğrulamasına göre Gönder düğmesini etkinleştir</span><span class="sxs-lookup"><span data-stu-id="6989a-278">Enable the submit button based on form validation</span></span>

<span data-ttu-id="6989a-279">Form doğrulamasına göre Gönder düğmesini etkinleştirmek ve devre dışı bırakmak için:</span><span class="sxs-lookup"><span data-stu-id="6989a-279">To enable and disable the submit button based on form validation:</span></span>

* <span data-ttu-id="6989a-280"><xref:Microsoft.AspNetCore.Components.Forms.EditContext>Bileşen başlatıldığında modeli atamak için formunu kullanın.</span><span class="sxs-lookup"><span data-stu-id="6989a-280">Use the form's <xref:Microsoft.AspNetCore.Components.Forms.EditContext> to assign the model when the component is initialized.</span></span>
* <span data-ttu-id="6989a-281"><xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged>Gönder düğmesini etkinleştirmek ve devre dışı bırakmak için bağlam geri aramasında formu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="6989a-281">Validate the form in the context's <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged> callback to enable and disable the submit button.</span></span>
* <span data-ttu-id="6989a-282">Yöntemi içindeki olay işleyicisinin üstünden geri dön `Dispose` .</span><span class="sxs-lookup"><span data-stu-id="6989a-282">Unhook the event handler in the `Dispose` method.</span></span> <span data-ttu-id="6989a-283">Daha fazla bilgi için bkz. <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span><span class="sxs-lookup"><span data-stu-id="6989a-283">For more information, see <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span></span>

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

<span data-ttu-id="6989a-284">Yukarıdaki örnekte, şu şekilde ayarlayın `formInvalid` `false` :</span><span class="sxs-lookup"><span data-stu-id="6989a-284">In the preceding example, set `formInvalid` to `false` if:</span></span>

* <span data-ttu-id="6989a-285">Form geçerli varsayılan değerlerle önceden yüklenir.</span><span class="sxs-lookup"><span data-stu-id="6989a-285">The form is preloaded with valid default values.</span></span>
* <span data-ttu-id="6989a-286">Form yüklendiğinde Gönder düğmesinin etkinleştirilmesini istiyorsunuz.</span><span class="sxs-lookup"><span data-stu-id="6989a-286">You want the submit button enabled when the form loads.</span></span>

<span data-ttu-id="6989a-287">Önceki yaklaşımın yan etkisi, <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> Kullanıcı herhangi bir alanla etkileşime geçtiğinde bileşen geçersiz alanlarla doldurulmuştur.</span><span class="sxs-lookup"><span data-stu-id="6989a-287">A side effect of the preceding approach is that a <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component is populated with invalid fields after the user interacts with any one field.</span></span> <span data-ttu-id="6989a-288">Bu senaryoya aşağıdaki yollarla değinilerek şunlar olabilir:</span><span class="sxs-lookup"><span data-stu-id="6989a-288">This scenario can be addressed in either of the following ways:</span></span>

* <span data-ttu-id="6989a-289"><xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary>Form üzerinde bir bileşen kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="6989a-289">Don't use a <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component on the form.</span></span>
* <span data-ttu-id="6989a-290"><xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary>Gönder düğmesi seçildiğinde bileşeni görünür hale getirin (örneğin, bir `HandleValidSubmit` yöntemde).</span><span class="sxs-lookup"><span data-stu-id="6989a-290">Make the <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component visible when the submit button is selected (for example, in a `HandleValidSubmit` method).</span></span>

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
