---
title: ASP.NET Core Blazor veri bağlama
author: guardrex
description: Uygulamalardaki bileşenler ve DOM öğeleri için veri bağlama özellikleri hakkında bilgi edinin Blazor .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/19/2020
no-loc:
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
ms.openlocfilehash: d88cad10314872271250cd43212a64698f485381
ms.sourcegitcommit: 8ed9a413bdc2d665ad11add8828898d726ccb106
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89280406"
---
# <a name="aspnet-core-no-locblazor-data-binding"></a><span data-ttu-id="3f0a9-103">ASP.NET Core Blazor veri bağlama</span><span class="sxs-lookup"><span data-stu-id="3f0a9-103">ASP.NET Core Blazor data binding</span></span>

<span data-ttu-id="3f0a9-104">, [Luke Latham](https://github.com/guardrex) ve [Daniel Roth](https://github.com/danroth27) tarafından</span><span class="sxs-lookup"><span data-stu-id="3f0a9-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="3f0a9-105">Razor bileşenler, [`@bind`](xref:mvc/views/razor#bind) bir alan, özellik veya ifade değeri ile adlandırılmış BIR HTML öğesi özniteliği aracılığıyla veri bağlama özellikleri sağlar Razor .</span><span class="sxs-lookup"><span data-stu-id="3f0a9-105">Razor components provide data binding features via an HTML element attribute named [`@bind`](xref:mvc/views/razor#bind) with a field, property, or Razor expression value.</span></span>

<span data-ttu-id="3f0a9-106">Aşağıdaki örnek, bir `<input>` öğesini `currentValue` alana ve `<input>` öğesi `CurrentValue` özelliğine bağlar:</span><span class="sxs-lookup"><span data-stu-id="3f0a9-106">The following example binds an `<input>` element to the `currentValue` field and an `<input>` element to the `CurrentValue` property:</span></span>

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

<span data-ttu-id="3f0a9-107">Öğelerden biri odağı kaybettiğinde, ilişkili alanı veya özelliği güncellenir.</span><span class="sxs-lookup"><span data-stu-id="3f0a9-107">When one of the elements looses focus, its bound field or property is updated.</span></span>

<span data-ttu-id="3f0a9-108">Metin kutusu kullanıcı arabiriminde, alanın veya özelliğin değerini değiştirme yanıt olarak değil, yalnızca bileşen işlendiğinde güncellenir.</span><span class="sxs-lookup"><span data-stu-id="3f0a9-108">The text box is updated in the UI only when the component is rendered, not in response to changing the field's or property's value.</span></span> <span data-ttu-id="3f0a9-109">Bileşenler olay işleyicisi kodu yürütüldükten sonra kendilerini oluşturduğundan, alan ve özellik güncelleştirmeleri *genellikle* bir olay işleyicisi tetiklendikten hemen sonra Kullanıcı arabirimine yansıtılır.</span><span class="sxs-lookup"><span data-stu-id="3f0a9-109">Since components render themselves after event handler code executes, field and property updates are *usually* reflected in the UI immediately after an event handler is triggered.</span></span>

<span data-ttu-id="3f0a9-110">[`@bind`](xref:mvc/views/razor#bind) `CurrentValue` Özelliği () ile kullanmak, `<input @bind="CurrentValue" />` temelde aşağıdakilere eşdeğerdir:</span><span class="sxs-lookup"><span data-stu-id="3f0a9-110">Using [`@bind`](xref:mvc/views/razor#bind) with the `CurrentValue` property (`<input @bind="CurrentValue" />`) is essentially equivalent to the following:</span></span>

```razor
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = 
        __e.Value.ToString())" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="3f0a9-111">Bileşen işlendiğinde, `value` giriş öğesi `CurrentValue` özelliğinden gelir.</span><span class="sxs-lookup"><span data-stu-id="3f0a9-111">When the component is rendered, the `value` of the input element comes from the `CurrentValue` property.</span></span> <span data-ttu-id="3f0a9-112">Kullanıcı metin kutusuna yazdığında ve öğe odağını değiştirdiğinde, `onchange` olay tetiklenir ve `CurrentValue` özellik değiştirilen değere ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="3f0a9-112">When the user types in the text box and changes element focus, the `onchange` event is fired and the `CurrentValue` property is set to the changed value.</span></span> <span data-ttu-id="3f0a9-113">Gerçekte, kod oluşturma bu değerden daha karmaşıktır çünkü [`@bind`](xref:mvc/views/razor#bind) tür dönüştürmelerinden oluşan durumları işler.</span><span class="sxs-lookup"><span data-stu-id="3f0a9-113">In reality, the code generation is more complex than that because [`@bind`](xref:mvc/views/razor#bind) handles cases where type conversions are performed.</span></span> <span data-ttu-id="3f0a9-114">İlke ' de, [`@bind`](xref:mvc/views/razor#bind) bir ifadenin geçerli değerini bir `value` özniteliğiyle ilişkilendirir ve kayıtlı işleyiciyi kullanarak değişiklikleri işler.</span><span class="sxs-lookup"><span data-stu-id="3f0a9-114">In principle, [`@bind`](xref:mvc/views/razor#bind) associates the current value of an expression with a `value` attribute and handles changes using the registered handler.</span></span>

<span data-ttu-id="3f0a9-115">Parametre içeren bir özniteliği de ekleyerek diğer olaylardaki bir özelliği veya alanı bağlayın `@bind:event` `event` .</span><span class="sxs-lookup"><span data-stu-id="3f0a9-115">Bind a property or field on other events by also including an `@bind:event` attribute with an `event` parameter.</span></span> <span data-ttu-id="3f0a9-116">Aşağıdaki örnek, `CurrentValue` olayında özelliği bağlar `oninput` :</span><span class="sxs-lookup"><span data-stu-id="3f0a9-116">The following example binds the `CurrentValue` property on the `oninput` event:</span></span>

```razor
<input @bind="CurrentValue" @bind:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="3f0a9-117">`onchange`' In aksine, öğe odağı kaybettiğinde harekete geçirilir, `oninput` metin kutusunun değeri değiştiğinde harekete geçirilir.</span><span class="sxs-lookup"><span data-stu-id="3f0a9-117">Unlike `onchange`, which fires when the element loses focus, `oninput` fires when the value of the text box changes.</span></span>

<!-- Hold location for resolution of https://github.com/dotnet/AspNetCore.Docs/issues/19721 -->

<span data-ttu-id="3f0a9-118">Öznitelik bağlama büyük/küçük harfe duyarlıdır:</span><span class="sxs-lookup"><span data-stu-id="3f0a9-118">Attribute binding is case sensitive:</span></span>

* <span data-ttu-id="3f0a9-119">`@bind` geçerli.</span><span class="sxs-lookup"><span data-stu-id="3f0a9-119">`@bind` is valid.</span></span>
* <span data-ttu-id="3f0a9-120">`@Bind` ve `@BIND` geçersiz.</span><span class="sxs-lookup"><span data-stu-id="3f0a9-120">`@Bind` and `@BIND` are invalid.</span></span>

## <a name="unparsable-values"></a><span data-ttu-id="3f0a9-121">Ayrıştırılamayan değerler</span><span class="sxs-lookup"><span data-stu-id="3f0a9-121">Unparsable values</span></span>

<span data-ttu-id="3f0a9-122">Bir Kullanıcı, bir veri sınırlama öğesine ayrıştırılamayan bir değer sağlıyorsa, bağlama olayı tetiklendiğinde, çözümlenemeyen değer otomatik olarak önceki değerine döndürülür.</span><span class="sxs-lookup"><span data-stu-id="3f0a9-122">When a user provides an unparsable value to a databound element, the unparsable value is automatically reverted to its previous value when the bind event is triggered.</span></span>

<span data-ttu-id="3f0a9-123">Şu senaryoyu göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="3f0a9-123">Consider the following scenario:</span></span>

* <span data-ttu-id="3f0a9-124">Bir `<input>` öğesi, `int` Başlangıç değeri olan bir türe bağlanır `123` :</span><span class="sxs-lookup"><span data-stu-id="3f0a9-124">An `<input>` element is bound to an `int` type with an initial value of `123`:</span></span>

  ```razor
  <input @bind="inputValue" />

  @code {
      private int inputValue = 123;
  }
  ```

* <span data-ttu-id="3f0a9-125">Kullanıcı, öğesinin değerini sayfada olarak güncelleştirir `123.45` ve öğe odağını değiştirir.</span><span class="sxs-lookup"><span data-stu-id="3f0a9-125">The user updates the value of the element to `123.45` in the page and changes the element focus.</span></span>

<span data-ttu-id="3f0a9-126">Önceki senaryoda, öğenin değeri öğesine geri döndürülür `123` .</span><span class="sxs-lookup"><span data-stu-id="3f0a9-126">In the preceding scenario, the element's value is reverted to `123`.</span></span> <span data-ttu-id="3f0a9-127">Değeri `123.45` özgün değeri yararına reddedildiğinde `123` , Kullanıcı değerinin kabul edilmediğini anlamıştır.</span><span class="sxs-lookup"><span data-stu-id="3f0a9-127">When the value `123.45` is rejected in favor of the original value of `123`, the user understands that their value wasn't accepted.</span></span>

<span data-ttu-id="3f0a9-128">Varsayılan olarak, bağlama öğenin `onchange` olayına ( `@bind="{PROPERTY OR FIELD}"` ) uygulanır.</span><span class="sxs-lookup"><span data-stu-id="3f0a9-128">By default, binding applies to the element's `onchange` event (`@bind="{PROPERTY OR FIELD}"`).</span></span> <span data-ttu-id="3f0a9-129">`@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}`Farklı bir olayda bağlamayı tetiklemek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="3f0a9-129">Use `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` to trigger binding on a different event.</span></span> <span data-ttu-id="3f0a9-130">`oninput`Event () için `@bind:event="oninput"` yeniden sürüm, ayrıştırılamayan bir değer sunan herhangi bir tuş vuruşu sonrasında oluşur.</span><span class="sxs-lookup"><span data-stu-id="3f0a9-130">For the `oninput` event (`@bind:event="oninput"`), the reversion occurs after any keystroke that introduces an unparsable value.</span></span> <span data-ttu-id="3f0a9-131">`oninput`Olayı, ilişkili bir tür ile hedeflerken `int` , bir kullanıcının bir karakter yazmasının engellenmiş olması engellenir `.` .</span><span class="sxs-lookup"><span data-stu-id="3f0a9-131">When targeting the `oninput` event with an `int`-bound type, a user is prevented from typing a `.` character.</span></span> <span data-ttu-id="3f0a9-132">Bir `.` karakter hemen kaldırılır, bu nedenle Kullanıcı yalnızca tam sayılara izin verilen anında geri bildirim alır.</span><span class="sxs-lookup"><span data-stu-id="3f0a9-132">A `.` character is immediately removed, so the user receives immediate feedback that only whole numbers are permitted.</span></span> <span data-ttu-id="3f0a9-133">Olay üzerindeki değerin geri döndürülmesi `oninput` ideal değil (örneğin, kullanıcının ayrıştırılamayan bir değeri temizlemeye izin verilmesi gerektiği durumlarda) `<input>` .</span><span class="sxs-lookup"><span data-stu-id="3f0a9-133">There are scenarios where reverting the value on the `oninput` event isn't ideal, such as when the user should be allowed to clear an unparsable `<input>` value.</span></span> <span data-ttu-id="3f0a9-134">Alternatifler şunlardır:</span><span class="sxs-lookup"><span data-stu-id="3f0a9-134">Alternatives include:</span></span>

* <span data-ttu-id="3f0a9-135">`oninput`Olayı kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="3f0a9-135">Don't use the `oninput` event.</span></span> <span data-ttu-id="3f0a9-136">Varsayılan olayı kullanın `onchange` (yalnızca belirtin `@bind="{PROPERTY OR FIELD}"` ); burada, öğe odağı kaybetene kadar geçersiz bir değer geri döndürülemez.</span><span class="sxs-lookup"><span data-stu-id="3f0a9-136">Use the default `onchange` event (only specify `@bind="{PROPERTY OR FIELD}"`), where an invalid value isn't reverted until the element loses focus.</span></span>
* <span data-ttu-id="3f0a9-137">Veya gibi null yapılabilir bir türe bağlayın `int?` `string` ve geçersiz girdileri işlemek için özel mantık sağlayın.</span><span class="sxs-lookup"><span data-stu-id="3f0a9-137">Bind to a nullable type, such as `int?` or `string` and provide custom logic to handle invalid entries.</span></span>
* <span data-ttu-id="3f0a9-138">Veya gibi bir [form doğrulama bileşeni](xref:blazor/forms-validation)kullanın <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> .</span><span class="sxs-lookup"><span data-stu-id="3f0a9-138">Use a [form validation component](xref:blazor/forms-validation), such as <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> or <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601>.</span></span> <span data-ttu-id="3f0a9-139">Form doğrulama bileşenlerinde geçersiz girişleri yönetmek için yerleşik destek vardır.</span><span class="sxs-lookup"><span data-stu-id="3f0a9-139">Form validation components have built-in support to manage invalid inputs.</span></span> <span data-ttu-id="3f0a9-140">Daha fazla bilgi için bkz. <xref:blazor/forms-validation>.</span><span class="sxs-lookup"><span data-stu-id="3f0a9-140">For more information, see <xref:blazor/forms-validation>.</span></span> <span data-ttu-id="3f0a9-141">Form doğrulama bileşenleri:</span><span class="sxs-lookup"><span data-stu-id="3f0a9-141">Form validation components:</span></span>
  * <span data-ttu-id="3f0a9-142">Kullanıcının geçersiz giriş sağlamasına ve ilişkili doğrulama hatalarını almasına izin verin <xref:Microsoft.AspNetCore.Components.Forms.EditContext> .</span><span class="sxs-lookup"><span data-stu-id="3f0a9-142">Permit the user to provide invalid input and receive validation errors on the associated <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span>
  * <span data-ttu-id="3f0a9-143">Kullanıcı ek WebForm verisi girmeye uğramadan doğrulama hatalarını Kullanıcı ARABIRIMINDE görüntüleyin.</span><span class="sxs-lookup"><span data-stu-id="3f0a9-143">Display validation errors in the UI without interfering with the user entering additional webform data.</span></span>

## <a name="format-strings"></a><span data-ttu-id="3f0a9-144">Biçim dizeleri</span><span class="sxs-lookup"><span data-stu-id="3f0a9-144">Format strings</span></span>

<span data-ttu-id="3f0a9-145">Veri bağlama, <xref:System.DateTime> kullanılarak biçim dizeleriyle birlikte kullanılabilir `@bind:format` .</span><span class="sxs-lookup"><span data-stu-id="3f0a9-145">Data binding works with <xref:System.DateTime> format strings using `@bind:format`.</span></span> <span data-ttu-id="3f0a9-146">Para birimi veya sayı biçimleri gibi diğer biçim ifadeleri şu anda kullanılamaz.</span><span class="sxs-lookup"><span data-stu-id="3f0a9-146">Other format expressions, such as currency or number formats, aren't available at this time.</span></span>

```razor
<input @bind="startDate" @bind:format="yyyy-MM-dd" />

@code {
    private DateTime startDate = new DateTime(2020, 1, 1);
}
```

<span data-ttu-id="3f0a9-147">Yukarıdaki kodda, `<input>` öğesinin alan türü ( `type` ) varsayılan olarak olur `text` .</span><span class="sxs-lookup"><span data-stu-id="3f0a9-147">In the preceding code, the `<input>` element's field type (`type`) defaults to `text`.</span></span> <span data-ttu-id="3f0a9-148">`@bind:format` , aşağıdaki .NET türlerini bağlamak için desteklenir:</span><span class="sxs-lookup"><span data-stu-id="3f0a9-148">`@bind:format` is supported for binding the following .NET types:</span></span>

* <xref:System.DateTime?displayProperty=fullName>
* <span data-ttu-id="3f0a9-149"><xref:System.DateTime?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="3f0a9-149"><xref:System.DateTime?displayProperty=fullName>?</span></span>
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <span data-ttu-id="3f0a9-150"><xref:System.DateTimeOffset?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="3f0a9-150"><xref:System.DateTimeOffset?displayProperty=fullName>?</span></span>

<span data-ttu-id="3f0a9-151">`@bind:format`Özniteliği öğesi için uygulanacak tarih biçimini belirtir `value` `<input>` .</span><span class="sxs-lookup"><span data-stu-id="3f0a9-151">The `@bind:format` attribute specifies the date format to apply to the `value` of the `<input>` element.</span></span> <span data-ttu-id="3f0a9-152">Biçim Ayrıca bir olay gerçekleştiğinde değeri ayrıştırmak için de kullanılır `onchange` .</span><span class="sxs-lookup"><span data-stu-id="3f0a9-152">The format is also used to parse the value when an `onchange` event occurs.</span></span>

<span data-ttu-id="3f0a9-153">`date` Blazor Tarihleri biçimlendirmek için yerleşik destek içerdiğinden, alan türü için bir biçim belirtmenin kullanılması önerilmez.</span><span class="sxs-lookup"><span data-stu-id="3f0a9-153">Specifying a format for the `date` field type isn't recommended because Blazor has built-in support to format dates.</span></span> <span data-ttu-id="3f0a9-154">Önerinin artma içinde, `yyyy-MM-dd` alan türüyle bir biçim sağlanırsa yalnızca bağlama için tarih biçimini kullanın `date` :</span><span class="sxs-lookup"><span data-stu-id="3f0a9-154">In spite of the recommendation, only use the `yyyy-MM-dd` date format for binding to function correctly if a format is supplied with the `date` field type:</span></span>

```razor
<input type="date" @bind="startDate" @bind:format="yyyy-MM-dd">
```

## <a name="parent-to-child-binding-with-component-parameters"></a><span data-ttu-id="3f0a9-155">Bileşen parametreleriyle üst-alt öğe bağlama</span><span class="sxs-lookup"><span data-stu-id="3f0a9-155">Parent-to-child binding with component parameters</span></span>

<span data-ttu-id="3f0a9-156">Bileşen parametreleri bir üst bileşenin sözdizimi ile bağlama özelliklerine ve alanlarına izin verir `@bind-{PROPERTY OR FIELD}` .</span><span class="sxs-lookup"><span data-stu-id="3f0a9-156">Component parameters permit binding properties and fields of a parent component with `@bind-{PROPERTY OR FIELD}` syntax.</span></span>

<span data-ttu-id="3f0a9-157">Aşağıdaki `Child` bileşende ( `Child.razor` ) bir `Year` bileşen parametresi ve `YearChanged` geri çağırması vardır:</span><span class="sxs-lookup"><span data-stu-id="3f0a9-157">The following `Child` component (`Child.razor`) has a `Year` component parameter and `YearChanged` callback:</span></span>

```razor
<div class="card bg-light mt-3" style="width:18rem ">
    <div class="card-body">
        <h3 class="card-title">Child Component</h3>
        <p class="card-text">Child <code>Year</code>: @Year</p>
        <p>
            <button @onclick="UpdateYear">
                Update Child <code>Year</code> and call 
                <code>YearChanged.InvokeAsync(Year)</code>
            </button>
        </p>
    </div>
</div>

@code {
    private Random r = new Random();

    [Parameter]
    public int Year { get; set; }

    [Parameter]
    public EventCallback<int> YearChanged { get; set; }

    private Task UpdateYear()
    {
        Year = r.Next(10050, 12021);

        return YearChanged.InvokeAsync(Year);
    }
}
```

<span data-ttu-id="3f0a9-158">Geri çağırma ( <xref:Microsoft.AspNetCore.Components.EventCallback%601> ), bileşen parametre adı olarak " `Changed` " soneki () ile adlandırılmalıdır `{PARAMETER NAME}Changed` .</span><span class="sxs-lookup"><span data-stu-id="3f0a9-158">The callback (<xref:Microsoft.AspNetCore.Components.EventCallback%601>) must be named as the component parameter name followed by the "`Changed`" suffix (`{PARAMETER NAME}Changed`).</span></span> <span data-ttu-id="3f0a9-159">Önceki örnekte, geri çağırma adlandırılır `YearChanged` .</span><span class="sxs-lookup"><span data-stu-id="3f0a9-159">In the preceding example, the callback is named `YearChanged`.</span></span> <span data-ttu-id="3f0a9-160">Hakkında daha fazla bilgi için <xref:Microsoft.AspNetCore.Components.EventCallback%601> bkz <xref:blazor/components/event-handling#eventcallback> ..</span><span class="sxs-lookup"><span data-stu-id="3f0a9-160">For more information on <xref:Microsoft.AspNetCore.Components.EventCallback%601>, see <xref:blazor/components/event-handling#eventcallback>.</span></span>

<span data-ttu-id="3f0a9-161">Aşağıdaki `Parent` bileşende ( `Parent.razor` ), `year` alanı `Year` alt bileşenin parametresine bağlanır:</span><span class="sxs-lookup"><span data-stu-id="3f0a9-161">In the following `Parent` component (`Parent.razor`), the `year` field is bound to the `Year` parameter of the child component:</span></span>

```razor
@page "/Parent"

<h1>Parent Component</h1>

<p>Parent <code>year</code>: @year</p>

<button @onclick="UpdateYear">Update Parent <code>year</code></button>

<Child @bind-Year="year" />

@code {
    private Random r = new Random();
    private int year = 1978;

    private void UpdateYear()
    {
        year = r.Next(1950, 2021);
    }
}
```

<span data-ttu-id="3f0a9-162">Parametrenin `Year` türüyle eşleşen bir yardımcı olayı olduğundan parametre bağlanabilir `YearChanged` `Year` .</span><span class="sxs-lookup"><span data-stu-id="3f0a9-162">The `Year` parameter is bindable because it has a companion `YearChanged` event that matches the type of the `Year` parameter.</span></span>

<span data-ttu-id="3f0a9-163">Kurala göre, bir özellik işleyiciye atanmış bir özniteliği ekleyerek karşılık gelen bir olay işleyicisine bağlanabilir `@bind-{PROPERTY}:event` .</span><span class="sxs-lookup"><span data-stu-id="3f0a9-163">By convention, a property can be bound to a corresponding event handler by including an `@bind-{PROPERTY}:event` attribute assigned to the handler.</span></span> <span data-ttu-id="3f0a9-164">`<Child @bind-Year="year" />` yazmaya eşittir:</span><span class="sxs-lookup"><span data-stu-id="3f0a9-164">`<Child @bind-Year="year" />` is equivalent to writing:</span></span>

```razor
<Child @bind-Year="year" @bind-Year:event="YearChanged" />
```

## <a name="child-to-parent-binding-with-chained-bind"></a><span data-ttu-id="3f0a9-165">Zincirli bağlama ile üstten üst öğe bağlama</span><span class="sxs-lookup"><span data-stu-id="3f0a9-165">Child-to-parent binding with chained bind</span></span>

<span data-ttu-id="3f0a9-166">Yaygın bir senaryo, bir veri bağlama parametresini bileşen çıkışında bir sayfa öğesine zincirlemesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="3f0a9-166">A common scenario is chaining a data-bound parameter to a page element in the component's output.</span></span> <span data-ttu-id="3f0a9-167">Birden çok bağlama düzeyi aynı anda gerçekleştiğinden, bu senaryoya *zincirleme bağlama* denir.</span><span class="sxs-lookup"><span data-stu-id="3f0a9-167">This scenario is called a *chained bind* because multiple levels of binding occur simultaneously.</span></span>

<span data-ttu-id="3f0a9-168">Bir zincir bağlama [`@bind`](xref:mvc/views/razor#bind) alt bileşende sözdizimi ile uygulanamaz.</span><span class="sxs-lookup"><span data-stu-id="3f0a9-168">A chained bind can't be implemented with [`@bind`](xref:mvc/views/razor#bind) syntax in the child component.</span></span> <span data-ttu-id="3f0a9-169">Olay işleyicisi ve değeri ayrı olarak belirtilmelidir.</span><span class="sxs-lookup"><span data-stu-id="3f0a9-169">The event handler and value must be specified separately.</span></span> <span data-ttu-id="3f0a9-170">Ancak bir üst bileşen, [`@bind`](xref:mvc/views/razor#bind) alt bileşenin parametresiyle birlikte sözdizimini kullanabilir.</span><span class="sxs-lookup"><span data-stu-id="3f0a9-170">A parent component, however, can use [`@bind`](xref:mvc/views/razor#bind) syntax with the child component's parameter.</span></span>

<span data-ttu-id="3f0a9-171">Aşağıdaki `PasswordField` bileşen ( `PasswordField.razor` ):</span><span class="sxs-lookup"><span data-stu-id="3f0a9-171">The following `PasswordField` component (`PasswordField.razor`):</span></span>

* <span data-ttu-id="3f0a9-172">Bir `<input>` öğenin değerini bir `Password` özelliğe ayarlar.</span><span class="sxs-lookup"><span data-stu-id="3f0a9-172">Sets an `<input>` element's value to a `Password` property.</span></span>
* <span data-ttu-id="3f0a9-173">`Password`Özelliği ile bir üst bileşen arasındaki değişiklikleri gösterir [`EventCallback`](xref:blazor/components/event-handling#eventcallback) .</span><span class="sxs-lookup"><span data-stu-id="3f0a9-173">Exposes changes of the `Password` property to a parent component with an [`EventCallback`](xref:blazor/components/event-handling#eventcallback).</span></span>
* <span data-ttu-id="3f0a9-174">`onclick`Yöntemini tetiklemek için olayını kullanır `ToggleShowPassword` .</span><span class="sxs-lookup"><span data-stu-id="3f0a9-174">Uses the `onclick` event to trigger the `ToggleShowPassword` method.</span></span> <span data-ttu-id="3f0a9-175">Daha fazla bilgi için bkz. <xref:blazor/components/event-handling>.</span><span class="sxs-lookup"><span data-stu-id="3f0a9-175">For more information, see <xref:blazor/components/event-handling>.</span></span>

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

<span data-ttu-id="3f0a9-176">`PasswordField`Bileşen başka bir bileşende kullanılır:</span><span class="sxs-lookup"><span data-stu-id="3f0a9-176">The `PasswordField` component is used in another component:</span></span>

```razor
@page "/Parent"

<h1>Parent Component</h1>

<PasswordField @bind-Password="password" />

@code {
    private string password;
}
```

<span data-ttu-id="3f0a9-177">Önceki örnekteki parolada denetim veya tuzak hataları gerçekleştirmek için:</span><span class="sxs-lookup"><span data-stu-id="3f0a9-177">To perform checks or trap errors on the password in the preceding example:</span></span>

* <span data-ttu-id="3f0a9-178">İçin bir yedekleme alanı oluşturun `Password` ( `password` Aşağıdaki örnek kodda).</span><span class="sxs-lookup"><span data-stu-id="3f0a9-178">Create a backing field for `Password` (`password` in the following example code).</span></span>
* <span data-ttu-id="3f0a9-179">Ayarlayıcıdaki denetimleri veya yakalama hatalarını gerçekleştirin `Password` .</span><span class="sxs-lookup"><span data-stu-id="3f0a9-179">Perform the checks or trap errors in the `Password` setter.</span></span>

<span data-ttu-id="3f0a9-180">Aşağıdaki örnek, parolanın değerinde bir boşluk kullanılmışsa kullanıcıya anında geri bildirim sağlar:</span><span class="sxs-lookup"><span data-stu-id="3f0a9-180">The following example provides immediate feedback to the user if a space is used in the password's value:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="3f0a9-181">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="3f0a9-181">Additional resources</span></span>

* [<span data-ttu-id="3f0a9-182">Bir formda radyo düğmelerine bağlama</span><span class="sxs-lookup"><span data-stu-id="3f0a9-182">Binding to radio buttons in a form</span></span>](xref:blazor/forms-validation#radio-buttons)
* [<span data-ttu-id="3f0a9-183">`<select>`Form Içindeki C# nesne değerlerine öğe seçenekleri bağlama `null`</span><span class="sxs-lookup"><span data-stu-id="3f0a9-183">Binding `<select>` element options to C# object `null` values in a form</span></span>](xref:blazor/forms-validation#binding-select-element-options-to-c-object-null-values)
