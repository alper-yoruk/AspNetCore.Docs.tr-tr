---
<span data-ttu-id="706a2-101">Başlık: ' ASP.NET Core Blazor veri bağlama ' Yazar: Açıklama: ' Apps 'teki bileşenler ve DOM öğeleri için veri bağlama özellikleri hakkında bilgi edinin Blazor . '</span><span class="sxs-lookup"><span data-stu-id="706a2-101">title: 'ASP.NET Core Blazor data binding' author: description: 'Learn about data binding features for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="706a2-102">monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="706a2-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="706a2-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="706a2-103">'Blazor'</span></span>
- <span data-ttu-id="706a2-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="706a2-104">'Identity'</span></span>
- <span data-ttu-id="706a2-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="706a2-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="706a2-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="706a2-106">'Razor'</span></span>
- <span data-ttu-id="706a2-107">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="706a2-107">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-data-binding"></a><span data-ttu-id="706a2-108">ASP.NET Core Blazor veri bağlama</span><span class="sxs-lookup"><span data-stu-id="706a2-108">ASP.NET Core Blazor data binding</span></span>

<span data-ttu-id="706a2-109">, [Luke Latham](https://github.com/guardrex) ve [Daniel Roth](https://github.com/danroth27) tarafından</span><span class="sxs-lookup"><span data-stu-id="706a2-109">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

Razor<span data-ttu-id="706a2-110">bileşenler, [`@bind`](xref:mvc/views/razor#bind) bir alan, özellik veya ifade değeri ile adlandırılmış BIR HTML öğesi özniteliği aracılığıyla veri bağlama özellikleri sağlar Razor .</span><span class="sxs-lookup"><span data-stu-id="706a2-110"> components provide data binding features via an HTML element attribute named [`@bind`](xref:mvc/views/razor#bind) with a field, property, or Razor expression value.</span></span>

<span data-ttu-id="706a2-111">Aşağıdaki örnek, `CurrentValue` özelliğini metin kutusu değerine bağlar:</span><span class="sxs-lookup"><span data-stu-id="706a2-111">The following example binds the `CurrentValue` property to the text box's value:</span></span>

```razor
<input @bind="CurrentValue" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="706a2-112">Metin kutusu odağı kaybettiğinde, özelliğin değeri güncellenir.</span><span class="sxs-lookup"><span data-stu-id="706a2-112">When the text box loses focus, the property's value is updated.</span></span>

<span data-ttu-id="706a2-113">Metin kutusu kullanıcı arabiriminde, özelliğin değerini değiştirme yanıt olarak değil, yalnızca bileşen işlendiğinde güncelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="706a2-113">The text box is updated in the UI only when the component is rendered, not in response to changing the property's value.</span></span> <span data-ttu-id="706a2-114">Bileşenler olay işleyicisi kodu yürütüldükten sonra kendilerini oluşturduğundan, özellik güncelleştirmeleri *genellikle* olay işleyicisi tetiklendikten hemen sonra Kullanıcı arabirimine yansıtılır.</span><span class="sxs-lookup"><span data-stu-id="706a2-114">Since components render themselves after event handler code executes, property updates are *usually* reflected in the UI immediately after an event handler is triggered.</span></span>

<span data-ttu-id="706a2-115">[`@bind`](xref:mvc/views/razor#bind) `CurrentValue` Özelliği () ile kullanmak, `<input @bind="CurrentValue" />` temelde aşağıdakilere eşdeğerdir:</span><span class="sxs-lookup"><span data-stu-id="706a2-115">Using [`@bind`](xref:mvc/views/razor#bind) with the `CurrentValue` property (`<input @bind="CurrentValue" />`) is essentially equivalent to the following:</span></span>

```razor
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = 
        __e.Value.ToString())" />
        
@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="706a2-116">Bileşen işlendiğinde, `value` giriş öğesi `CurrentValue` özelliğinden gelir.</span><span class="sxs-lookup"><span data-stu-id="706a2-116">When the component is rendered, the `value` of the input element comes from the `CurrentValue` property.</span></span> <span data-ttu-id="706a2-117">Kullanıcı metin kutusuna yazdığında ve öğe odağını değiştirdiğinde, `onchange` olay tetiklenir ve `CurrentValue` özellik değiştirilen değere ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="706a2-117">When the user types in the text box and changes element focus, the `onchange` event is fired and the `CurrentValue` property is set to the changed value.</span></span> <span data-ttu-id="706a2-118">[`@bind`](xref:mvc/views/razor#bind)Tür dönüştürmelerinde oluşan durumları işletiğinden, gerçekte kod oluşturma daha karmaşıktır.</span><span class="sxs-lookup"><span data-stu-id="706a2-118">In reality, the code generation is more complex because [`@bind`](xref:mvc/views/razor#bind) handles cases where type conversions are performed.</span></span> <span data-ttu-id="706a2-119">İlke ' de, [`@bind`](xref:mvc/views/razor#bind) bir ifadenin geçerli değerini bir `value` özniteliğiyle ilişkilendirir ve kayıtlı işleyiciyi kullanarak değişiklikleri işler.</span><span class="sxs-lookup"><span data-stu-id="706a2-119">In principle, [`@bind`](xref:mvc/views/razor#bind) associates the current value of an expression with a `value` attribute and handles changes using the registered handler.</span></span>

<span data-ttu-id="706a2-120">Parametre içeren bir özniteliği de ekleyerek diğer olaylardaki bir özelliği veya alanı bağlayın `@bind:event` `event` .</span><span class="sxs-lookup"><span data-stu-id="706a2-120">Bind a property or field on other events by also including an `@bind:event` attribute with an `event` parameter.</span></span> <span data-ttu-id="706a2-121">Aşağıdaki örnek, `CurrentValue` olayında özelliği bağlar `oninput` :</span><span class="sxs-lookup"><span data-stu-id="706a2-121">The following example binds the `CurrentValue` property on the `oninput` event:</span></span>

```razor
<input @bind="CurrentValue" @bind:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="706a2-122">`onchange`' In aksine, öğe odağı kaybettiğinde harekete geçirilir, `oninput` metin kutusunun değeri değiştiğinde harekete geçirilir.</span><span class="sxs-lookup"><span data-stu-id="706a2-122">Unlike `onchange`, which fires when the element loses focus, `oninput` fires when the value of the text box changes.</span></span>

<span data-ttu-id="706a2-123">`@bind-{ATTRIBUTE}` `@bind-{ATTRIBUTE}:event` Dışındaki öğe özniteliklerini bağlamak için sözdizimi ile kullanın `value` .</span><span class="sxs-lookup"><span data-stu-id="706a2-123">Use `@bind-{ATTRIBUTE}` with `@bind-{ATTRIBUTE}:event` syntax to bind element attributes other than `value`.</span></span> <span data-ttu-id="706a2-124">Aşağıdaki örnekte, paragrafın stili `paragraphStyle` değer değiştiğinde güncelleştirilir:</span><span class="sxs-lookup"><span data-stu-id="706a2-124">In the following example, the paragraph's style is updated when the `paragraphStyle` value changes:</span></span>

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

<span data-ttu-id="706a2-125">Öznitelik bağlama büyük/küçük harfe duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="706a2-125">Attribute binding is case sensitive.</span></span> <span data-ttu-id="706a2-126">Örneğin, [`@bind`](xref:mvc/views/razor#bind) geçerlidir ve [`@bind`](xref:mvc/views/razor#bind) geçersizdir.</span><span class="sxs-lookup"><span data-stu-id="706a2-126">For example, [`@bind`](xref:mvc/views/razor#bind) is valid, and [`@bind`](xref:mvc/views/razor#bind) is invalid.</span></span>

## <a name="unparsable-values"></a><span data-ttu-id="706a2-127">Ayrıştırılamayan değerler</span><span class="sxs-lookup"><span data-stu-id="706a2-127">Unparsable values</span></span>

<span data-ttu-id="706a2-128">Bir Kullanıcı, bir veri sınırlama öğesine ayrıştırılamayan bir değer sağlıyorsa, bağlama olayı tetiklendiğinde, çözümlenemeyen değer otomatik olarak önceki değerine döndürülür.</span><span class="sxs-lookup"><span data-stu-id="706a2-128">When a user provides an unparsable value to a databound element, the unparsable value is automatically reverted to its previous value when the bind event is triggered.</span></span>

<span data-ttu-id="706a2-129">Şu senaryoyu göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="706a2-129">Consider the following scenario:</span></span>

* <span data-ttu-id="706a2-130">Bir `<input>` öğesi, `int` Başlangıç değeri olan bir türe bağlanır `123` :</span><span class="sxs-lookup"><span data-stu-id="706a2-130">An `<input>` element is bound to an `int` type with an initial value of `123`:</span></span>

  ```razor
  <input @bind="MyProperty" />

  @code {
      [Parameter]
      public int MyProperty { get; set; } = 123;
  }
  ```
* <span data-ttu-id="706a2-131">Kullanıcı, öğesinin değerini sayfada olarak güncelleştirir `123.45` ve öğe odağını değiştirir.</span><span class="sxs-lookup"><span data-stu-id="706a2-131">The user updates the value of the element to `123.45` in the page and changes the element focus.</span></span>

<span data-ttu-id="706a2-132">Önceki senaryoda, öğenin değeri öğesine geri döndürülür `123` .</span><span class="sxs-lookup"><span data-stu-id="706a2-132">In the preceding scenario, the element's value is reverted to `123`.</span></span> <span data-ttu-id="706a2-133">Değeri `123.45` özgün değeri yararına reddedildiğinde `123` , Kullanıcı değerinin kabul edilmediğini anlamıştır.</span><span class="sxs-lookup"><span data-stu-id="706a2-133">When the value `123.45` is rejected in favor of the original value of `123`, the user understands that their value wasn't accepted.</span></span>

<span data-ttu-id="706a2-134">Varsayılan olarak, bağlama öğenin `onchange` olayına ( `@bind="{PROPERTY OR FIELD}"` ) uygulanır.</span><span class="sxs-lookup"><span data-stu-id="706a2-134">By default, binding applies to the element's `onchange` event (`@bind="{PROPERTY OR FIELD}"`).</span></span> <span data-ttu-id="706a2-135">`@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}`Farklı bir olayda bağlamayı tetiklemek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="706a2-135">Use `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` to trigger binding on a different event.</span></span> <span data-ttu-id="706a2-136">`oninput`Event () için `@bind:event="oninput"` yeniden sürüm, ayrıştırılamayan bir değer sunan herhangi bir tuş vuruşu sonrasında oluşur.</span><span class="sxs-lookup"><span data-stu-id="706a2-136">For the `oninput` event (`@bind:event="oninput"`), the reversion occurs after any keystroke that introduces an unparsable value.</span></span> <span data-ttu-id="706a2-137">`oninput`Olayı, ilişkili bir tür ile hedeflerken `int` , bir kullanıcının bir karakter yazmasının engellenmiş olması engellenir `.` .</span><span class="sxs-lookup"><span data-stu-id="706a2-137">When targeting the `oninput` event with an `int`-bound type, a user is prevented from typing a `.` character.</span></span> <span data-ttu-id="706a2-138">Bir `.` karakter hemen kaldırılır, bu nedenle Kullanıcı yalnızca tam sayılara izin verilen anında geri bildirim alır.</span><span class="sxs-lookup"><span data-stu-id="706a2-138">A `.` character is immediately removed, so the user receives immediate feedback that only whole numbers are permitted.</span></span> <span data-ttu-id="706a2-139">Olay üzerindeki değerin geri döndürülmesi `oninput` ideal değil (örneğin, kullanıcının ayrıştırılamayan bir değeri temizlemeye izin verilmesi gerektiği durumlarda) `<input>` .</span><span class="sxs-lookup"><span data-stu-id="706a2-139">There are scenarios where reverting the value on the `oninput` event isn't ideal, such as when the user should be allowed to clear an unparsable `<input>` value.</span></span> <span data-ttu-id="706a2-140">Alternatifler şunlardır:</span><span class="sxs-lookup"><span data-stu-id="706a2-140">Alternatives include:</span></span>

* <span data-ttu-id="706a2-141">`oninput`Olayı kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="706a2-141">Don't use the `oninput` event.</span></span> <span data-ttu-id="706a2-142">Varsayılan olayı kullanın `onchange` (yalnızca belirtin `@bind="{PROPERTY OR FIELD}"` ); burada, öğe odağı kaybetene kadar geçersiz bir değer geri döndürülemez.</span><span class="sxs-lookup"><span data-stu-id="706a2-142">Use the default `onchange` event (only specify `@bind="{PROPERTY OR FIELD}"`), where an invalid value isn't reverted until the element loses focus.</span></span>
* <span data-ttu-id="706a2-143">Veya gibi null yapılabilir bir türe bağlayın `int?` `string` ve geçersiz girdileri işlemek için özel mantık sağlayın.</span><span class="sxs-lookup"><span data-stu-id="706a2-143">Bind to a nullable type, such as `int?` or `string`, and provide custom logic to handle invalid entries.</span></span>
* <span data-ttu-id="706a2-144">Veya gibi bir [form doğrulama bileşeni](xref:blazor/forms-validation)kullanın <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> .</span><span class="sxs-lookup"><span data-stu-id="706a2-144">Use a [form validation component](xref:blazor/forms-validation), such as <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> or <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601>.</span></span> <span data-ttu-id="706a2-145">Form doğrulama bileşenlerinde geçersiz girişleri yönetmek için yerleşik destek vardır.</span><span class="sxs-lookup"><span data-stu-id="706a2-145">Form validation components have built-in support to manage invalid inputs.</span></span> <span data-ttu-id="706a2-146">Form doğrulama bileşenleri:</span><span class="sxs-lookup"><span data-stu-id="706a2-146">Form validation components:</span></span>
  * <span data-ttu-id="706a2-147">Kullanıcının geçersiz giriş sağlamasına ve ilişkili doğrulama hatalarını almasına izin verin <xref:Microsoft.AspNetCore.Components.Forms.EditContext> .</span><span class="sxs-lookup"><span data-stu-id="706a2-147">Permit the user to provide invalid input and receive validation errors on the associated <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span>
  * <span data-ttu-id="706a2-148">Kullanıcı ek WebForm verisi girmeye uğramadan doğrulama hatalarını Kullanıcı ARABIRIMINDE görüntüleyin.</span><span class="sxs-lookup"><span data-stu-id="706a2-148">Display validation errors in the UI without interfering with the user entering additional webform data.</span></span>

## <a name="format-strings"></a><span data-ttu-id="706a2-149">Biçim dizeleri</span><span class="sxs-lookup"><span data-stu-id="706a2-149">Format strings</span></span>

<span data-ttu-id="706a2-150">Veri bağlama, <xref:System.DateTime> kullanılarak biçim dizeleriyle birlikte kullanılabilir `@bind:format` .</span><span class="sxs-lookup"><span data-stu-id="706a2-150">Data binding works with <xref:System.DateTime> format strings using `@bind:format`.</span></span> <span data-ttu-id="706a2-151">Para birimi veya sayı biçimleri gibi diğer biçim ifadeleri şu anda kullanılamaz.</span><span class="sxs-lookup"><span data-stu-id="706a2-151">Other format expressions, such as currency or number formats, aren't available at this time.</span></span>

```razor
<input @bind="StartDate" @bind:format="yyyy-MM-dd" />

@code {
    [Parameter]
    public DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

<span data-ttu-id="706a2-152">Yukarıdaki kodda, `<input>` öğesinin alan türü ( `type` ) varsayılan olarak olur `text` .</span><span class="sxs-lookup"><span data-stu-id="706a2-152">In the preceding code, the `<input>` element's field type (`type`) defaults to `text`.</span></span> <span data-ttu-id="706a2-153">`@bind:format`, aşağıdaki .NET türlerini bağlamak için desteklenir:</span><span class="sxs-lookup"><span data-stu-id="706a2-153">`@bind:format` is supported for binding the following .NET types:</span></span>

* <xref:System.DateTime?displayProperty=fullName>
* <span data-ttu-id="706a2-154"><xref:System.DateTime?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="706a2-154"><xref:System.DateTime?displayProperty=fullName>?</span></span>
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <span data-ttu-id="706a2-155"><xref:System.DateTimeOffset?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="706a2-155"><xref:System.DateTimeOffset?displayProperty=fullName>?</span></span>

<span data-ttu-id="706a2-156">`@bind:format`Özniteliği öğesi için uygulanacak tarih biçimini belirtir `value` `<input>` .</span><span class="sxs-lookup"><span data-stu-id="706a2-156">The `@bind:format` attribute specifies the date format to apply to the `value` of the `<input>` element.</span></span> <span data-ttu-id="706a2-157">Biçim Ayrıca bir olay gerçekleştiğinde değeri ayrıştırmak için de kullanılır `onchange` .</span><span class="sxs-lookup"><span data-stu-id="706a2-157">The format is also used to parse the value when an `onchange` event occurs.</span></span>

<span data-ttu-id="706a2-158">`date` Blazor Tarihleri biçimlendirmek için yerleşik destek içerdiğinden, alan türü için bir biçim belirtmenin kullanılması önerilmez.</span><span class="sxs-lookup"><span data-stu-id="706a2-158">Specifying a format for the `date` field type isn't recommended because Blazor has built-in support to format dates.</span></span> <span data-ttu-id="706a2-159">Önerinin artma içinde, `yyyy-MM-dd` alan türüyle bir biçim sağlanırsa, bağlama için yalnızca tarih biçimini kullanın `date` :</span><span class="sxs-lookup"><span data-stu-id="706a2-159">In spite of the recommendation, only use the `yyyy-MM-dd` date format for binding to work correctly if a format is supplied with the `date` field type:</span></span>

```razor
<input type="date" @bind="StartDate" @bind:format="yyyy-MM-dd">
```

## <a name="parent-to-child-binding-with-component-parameters"></a><span data-ttu-id="706a2-160">Bileşen parametreleriyle üst-alt öğe bağlama</span><span class="sxs-lookup"><span data-stu-id="706a2-160">Parent-to-child binding with component parameters</span></span>

<span data-ttu-id="706a2-161">Bağlama, bir `@bind-{PROPERTY}` üst bileşenden bir özellik değerini alt bileşene doğru bir şekilde bağlayabileceği bileşen parametrelerini tanır.</span><span class="sxs-lookup"><span data-stu-id="706a2-161">Binding recognizes component parameters, where `@bind-{PROPERTY}` can bind a property value from a parent component down to a child component.</span></span> <span data-ttu-id="706a2-162">Bir alt öğeden üst öğeye bağlama, [zincirleme bağlama Ile alt-üst öğe bağlama](#child-to-parent-binding-with-chained-bind) bölümünde ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="706a2-162">Binding from a child to a parent is covered in the [Child-to-parent binding with chained bind](#child-to-parent-binding-with-chained-bind) section.</span></span>

<span data-ttu-id="706a2-163">Aşağıdaki alt bileşende ( `ChildComponent` ) bir `Year` bileşen parametresi ve `YearChanged` geri çağırması vardır:</span><span class="sxs-lookup"><span data-stu-id="706a2-163">The following child component (`ChildComponent`) has a `Year` component parameter and `YearChanged` callback:</span></span>

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

<span data-ttu-id="706a2-164"><xref:Microsoft.AspNetCore.Components.EventCallback%601>, bölümünde açıklanmıştır <xref:blazor/event-handling#eventcallback> .</span><span class="sxs-lookup"><span data-stu-id="706a2-164"><xref:Microsoft.AspNetCore.Components.EventCallback%601> is explained in <xref:blazor/event-handling#eventcallback>.</span></span>

<span data-ttu-id="706a2-165">Aşağıdaki üst bileşen şunları kullanır:</span><span class="sxs-lookup"><span data-stu-id="706a2-165">The following parent component uses:</span></span>

* <span data-ttu-id="706a2-166">`ChildComponent`ve `ParentYear` parametresini üst `Year` bileşenden alt bileşenin parametresine bağlar.</span><span class="sxs-lookup"><span data-stu-id="706a2-166">`ChildComponent` and binds the `ParentYear` parameter from the parent to the `Year` parameter on the child component.</span></span>
* <span data-ttu-id="706a2-167">`onclick`Olay, yöntemi tetiklemek için kullanılır `ChangeTheYear` .</span><span class="sxs-lookup"><span data-stu-id="706a2-167">The `onclick` event is used to trigger the `ChangeTheYear` method.</span></span> <span data-ttu-id="706a2-168">Daha fazla bilgi için bkz. <xref:blazor/event-handling>.</span><span class="sxs-lookup"><span data-stu-id="706a2-168">For more information, see <xref:blazor/event-handling>.</span></span>

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

<span data-ttu-id="706a2-169">Yüklemesi `ParentComponent` aşağıdaki biçimlendirmeyi üretir:</span><span class="sxs-lookup"><span data-stu-id="706a2-169">Loading the `ParentComponent` produces the following markup:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

<span data-ttu-id="706a2-170">`ParentYear`Özelliğin değeri, içindeki düğme seçilerek değiştirilirse, `ParentComponent` `Year` öğesinin özelliği `ChildComponent` güncellenir.</span><span class="sxs-lookup"><span data-stu-id="706a2-170">If the value of the `ParentYear` property is changed by selecting the button in the `ParentComponent`, the `Year` property of the `ChildComponent` is updated.</span></span> <span data-ttu-id="706a2-171">Yeni değeri, `Year` yeniden kullanıldığında kullanıcı arabiriminde işlenir `ParentComponent` :</span><span class="sxs-lookup"><span data-stu-id="706a2-171">The new value of `Year` is rendered in the UI when the `ParentComponent` is rerendered:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

<span data-ttu-id="706a2-172">Parametrenin `Year` türüyle eşleşen bir yardımcı olayı olduğundan parametre bağlanabilir `YearChanged` `Year` .</span><span class="sxs-lookup"><span data-stu-id="706a2-172">The `Year` parameter is bindable because it has a companion `YearChanged` event that matches the type of the `Year` parameter.</span></span>

<span data-ttu-id="706a2-173">Kurala göre, `<ChildComponent @bind-Year="ParentYear" />` temelde yazmaya eşdeğerdir:</span><span class="sxs-lookup"><span data-stu-id="706a2-173">By convention, `<ChildComponent @bind-Year="ParentYear" />` is essentially equivalent to writing:</span></span>

```razor
<ChildComponent @bind-Year="ParentYear" @bind-Year:event="YearChanged" />
```

<span data-ttu-id="706a2-174">Genel olarak, bir özellik bir özniteliği eklenerek ilgili olay işleyicisine bağlanabilir `@bind-{PROPRETY}:event` .</span><span class="sxs-lookup"><span data-stu-id="706a2-174">In general, a property can be bound to a corresponding event handler by including an `@bind-{PROPRETY}:event` attribute.</span></span> <span data-ttu-id="706a2-175">Örneğin, özelliği `MyProp` `MyEventHandler` aşağıdaki iki öznitelik kullanılarak bağlanabilir:</span><span class="sxs-lookup"><span data-stu-id="706a2-175">For example, the property `MyProp` can be bound to `MyEventHandler` using the following two attributes:</span></span>

```razor
<MyComponent @bind-MyProp="MyValue" @bind-MyProp:event="MyEventHandler" />
```

## <a name="child-to-parent-binding-with-chained-bind"></a><span data-ttu-id="706a2-176">Zincirli bağlama ile üstten üst öğe bağlama</span><span class="sxs-lookup"><span data-stu-id="706a2-176">Child-to-parent binding with chained bind</span></span>

<span data-ttu-id="706a2-177">Yaygın bir senaryo, bir veri bağlama parametresini bileşen çıkışında bir sayfa öğesine zincirlemesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="706a2-177">A common scenario is chaining a data-bound parameter to a page element in the component's output.</span></span> <span data-ttu-id="706a2-178">Birden çok bağlama düzeyi aynı anda gerçekleştiğinden, bu senaryoya *zincirleme bağlama* denir.</span><span class="sxs-lookup"><span data-stu-id="706a2-178">This scenario is called a *chained bind* because multiple levels of binding occur simultaneously.</span></span>

<span data-ttu-id="706a2-179">Bir zincir bağlama [`@bind`](xref:mvc/views/razor#bind) , sayfanın öğesinde sözdizimi ile uygulanamaz.</span><span class="sxs-lookup"><span data-stu-id="706a2-179">A chained bind can't be implemented with [`@bind`](xref:mvc/views/razor#bind) syntax in the page's element.</span></span> <span data-ttu-id="706a2-180">Olay işleyicisi ve değeri ayrı olarak belirtilmelidir.</span><span class="sxs-lookup"><span data-stu-id="706a2-180">The event handler and value must be specified separately.</span></span> <span data-ttu-id="706a2-181">Ancak, bir üst bileşen, bir [`@bind`](xref:mvc/views/razor#bind) sözdizimi bileşenin parametresiyle birlikte kullanabilir.</span><span class="sxs-lookup"><span data-stu-id="706a2-181">A parent component, however, can use [`@bind`](xref:mvc/views/razor#bind) syntax with the component's parameter.</span></span>

<span data-ttu-id="706a2-182">Aşağıdaki `PasswordField` bileşen (*passwordfield. Razor*):</span><span class="sxs-lookup"><span data-stu-id="706a2-182">The following `PasswordField` component (*PasswordField.razor*):</span></span>

* <span data-ttu-id="706a2-183">Bir `<input>` öğenin değerini bir `Password` özelliğe ayarlar.</span><span class="sxs-lookup"><span data-stu-id="706a2-183">Sets an `<input>` element's value to a `Password` property.</span></span>
* <span data-ttu-id="706a2-184">Özellik değişikliklerini bir `Password` [eventcallback](xref:blazor/event-handling#eventcallback)ile üst bileşene gösterir.</span><span class="sxs-lookup"><span data-stu-id="706a2-184">Exposes changes of the `Password` property to a parent component with an [EventCallback](xref:blazor/event-handling#eventcallback).</span></span>
* <span data-ttu-id="706a2-185">, `onclick` Yöntemini tetiklemek için kullanılan olayını kullanır `ToggleShowPassword` .</span><span class="sxs-lookup"><span data-stu-id="706a2-185">Uses the `onclick` event is used to trigger the `ToggleShowPassword` method.</span></span> <span data-ttu-id="706a2-186">Daha fazla bilgi için bkz. <xref:blazor/event-handling>.</span><span class="sxs-lookup"><span data-stu-id="706a2-186">For more information, see <xref:blazor/event-handling>.</span></span>

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

<span data-ttu-id="706a2-187">`PasswordField`Bileşen başka bir bileşende kullanılır:</span><span class="sxs-lookup"><span data-stu-id="706a2-187">The `PasswordField` component is used in another component:</span></span>

```razor
@page "/ParentComponent"

<h1>Parent Component</h1>

<PasswordField @bind-Password="password" />

@code {
    private string password;
}
```

<span data-ttu-id="706a2-188">Önceki örnekteki parolada denetim veya tuzak hataları gerçekleştirmek için:</span><span class="sxs-lookup"><span data-stu-id="706a2-188">To perform checks or trap errors on the password in the preceding example:</span></span>

* <span data-ttu-id="706a2-189">İçin bir yedekleme alanı oluşturun `Password` ( `password` Aşağıdaki örnek kodda).</span><span class="sxs-lookup"><span data-stu-id="706a2-189">Create a backing field for `Password` (`password` in the following example code).</span></span>
* <span data-ttu-id="706a2-190">Ayarlayıcıdaki denetimleri veya yakalama hatalarını gerçekleştirin `Password` .</span><span class="sxs-lookup"><span data-stu-id="706a2-190">Perform the checks or trap errors in the `Password` setter.</span></span>

<span data-ttu-id="706a2-191">Aşağıdaki örnek, parolanın değerinde bir boşluk kullanılmışsa kullanıcıya anında geri bildirim sağlar:</span><span class="sxs-lookup"><span data-stu-id="706a2-191">The following example provides immediate feedback to the user if a space is used in the password's value:</span></span>

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

## <a name="radio-buttons"></a><span data-ttu-id="706a2-192">Radyo düğmeleri</span><span class="sxs-lookup"><span data-stu-id="706a2-192">Radio buttons</span></span>

<span data-ttu-id="706a2-193">Bir form içindeki radyo düğmelerine bağlama hakkında bilgi için bkz <xref:blazor/forms-validation#work-with-radio-buttons> ..</span><span class="sxs-lookup"><span data-stu-id="706a2-193">For information on binding to radio buttons in a form, see <xref:blazor/forms-validation#work-with-radio-buttons>.</span></span>
