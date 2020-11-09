---
title: 'ASP.NET Core Blazor veri bağlama'
author: guardrex
description: 'Uygulamalardaki bileşenler ve DOM öğeleri için veri bağlama özellikleri hakkında bilgi edinin Blazor .'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/22/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: blazor/components/data-binding
ms.openlocfilehash: f1730ed366fc81444ffe54e88bcd33147efb0aa7
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056302"
---
# <a name="aspnet-core-no-locblazor-data-binding"></a><span data-ttu-id="440c1-103">ASP.NET Core Blazor veri bağlama</span><span class="sxs-lookup"><span data-stu-id="440c1-103">ASP.NET Core Blazor data binding</span></span>

<span data-ttu-id="440c1-104">[Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27)ve [Steve Sanderson](https://github.com/SteveSandersonMS) tarafından</span><span class="sxs-lookup"><span data-stu-id="440c1-104">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), and [Steve Sanderson](https://github.com/SteveSandersonMS)</span></span>

<span data-ttu-id="440c1-105">Razor bileşenler, [`@bind`](xref:mvc/views/razor#bind) bir alan, özellik veya ifade değeri ile adlandırılmış BIR HTML öğesi özniteliği aracılığıyla veri bağlama özellikleri sağlar Razor .</span><span class="sxs-lookup"><span data-stu-id="440c1-105">Razor components provide data binding features via an HTML element attribute named [`@bind`](xref:mvc/views/razor#bind) with a field, property, or Razor expression value.</span></span>

<span data-ttu-id="440c1-106">Aşağıdaki örnek, bir `<input>` öğesini `currentValue` alana ve `<input>` öğesi `CurrentValue` özelliğine bağlar:</span><span class="sxs-lookup"><span data-stu-id="440c1-106">The following example binds an `<input>` element to the `currentValue` field and an `<input>` element to the `CurrentValue` property:</span></span>

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

<span data-ttu-id="440c1-107">Öğelerden biri odağı kaybettiğinde, ilişkili alanı veya özelliği güncellenir.</span><span class="sxs-lookup"><span data-stu-id="440c1-107">When one of the elements loses focus, its bound field or property is updated.</span></span>

<span data-ttu-id="440c1-108">Metin kutusu kullanıcı arabiriminde, alanın veya özelliğin değerini değiştirme yanıt olarak değil, yalnızca bileşen işlendiğinde güncellenir.</span><span class="sxs-lookup"><span data-stu-id="440c1-108">The text box is updated in the UI only when the component is rendered, not in response to changing the field's or property's value.</span></span> <span data-ttu-id="440c1-109">Bileşenler olay işleyicisi kodu yürütüldükten sonra kendilerini oluşturduğundan, alan ve özellik güncelleştirmeleri *genellikle* bir olay işleyicisi tetiklendikten hemen sonra Kullanıcı arabirimine yansıtılır.</span><span class="sxs-lookup"><span data-stu-id="440c1-109">Since components render themselves after event handler code executes, field and property updates are *usually* reflected in the UI immediately after an event handler is triggered.</span></span>

<span data-ttu-id="440c1-110">[`@bind`](xref:mvc/views/razor#bind) `CurrentValue` Özelliği () ile kullanmak, `<input @bind="CurrentValue" />` temelde aşağıdakilere eşdeğerdir:</span><span class="sxs-lookup"><span data-stu-id="440c1-110">Using [`@bind`](xref:mvc/views/razor#bind) with the `CurrentValue` property (`<input @bind="CurrentValue" />`) is essentially equivalent to the following:</span></span>

```razor
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = 
        __e.Value.ToString())" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="440c1-111">Bileşen işlendiğinde, `value` giriş öğesi `CurrentValue` özelliğinden gelir.</span><span class="sxs-lookup"><span data-stu-id="440c1-111">When the component is rendered, the `value` of the input element comes from the `CurrentValue` property.</span></span> <span data-ttu-id="440c1-112">Kullanıcı metin kutusuna yazdığında ve öğe odağını değiştirdiğinde, `onchange` olay tetiklenir ve `CurrentValue` özellik değiştirilen değere ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="440c1-112">When the user types in the text box and changes element focus, the `onchange` event is fired and the `CurrentValue` property is set to the changed value.</span></span> <span data-ttu-id="440c1-113">Gerçekte, kod oluşturma bu değerden daha karmaşıktır çünkü [`@bind`](xref:mvc/views/razor#bind) tür dönüştürmelerinden oluşan durumları işler.</span><span class="sxs-lookup"><span data-stu-id="440c1-113">In reality, the code generation is more complex than that because [`@bind`](xref:mvc/views/razor#bind) handles cases where type conversions are performed.</span></span> <span data-ttu-id="440c1-114">İlke ' de, [`@bind`](xref:mvc/views/razor#bind) bir ifadenin geçerli değerini bir `value` özniteliğiyle ilişkilendirir ve kayıtlı işleyiciyi kullanarak değişiklikleri işler.</span><span class="sxs-lookup"><span data-stu-id="440c1-114">In principle, [`@bind`](xref:mvc/views/razor#bind) associates the current value of an expression with a `value` attribute and handles changes using the registered handler.</span></span>

<span data-ttu-id="440c1-115">Parametre içeren bir özniteliği de ekleyerek diğer olaylardaki bir özelliği veya alanı bağlayın `@bind:event` `event` .</span><span class="sxs-lookup"><span data-stu-id="440c1-115">Bind a property or field on other events by also including an `@bind:event` attribute with an `event` parameter.</span></span> <span data-ttu-id="440c1-116">Aşağıdaki örnek, `CurrentValue` olayında özelliği bağlar `oninput` :</span><span class="sxs-lookup"><span data-stu-id="440c1-116">The following example binds the `CurrentValue` property on the `oninput` event:</span></span>

```razor
<input @bind="CurrentValue" @bind:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="440c1-117">`onchange`' In aksine, öğe odağı kaybettiğinde harekete geçirilir, `oninput` metin kutusunun değeri değiştiğinde harekete geçirilir.</span><span class="sxs-lookup"><span data-stu-id="440c1-117">Unlike `onchange`, which fires when the element loses focus, `oninput` fires when the value of the text box changes.</span></span>

<!-- Hold location for resolution of https://github.com/dotnet/AspNetCore.Docs/issues/19721 -->

<span data-ttu-id="440c1-118">Öznitelik bağlama büyük/küçük harfe duyarlıdır:</span><span class="sxs-lookup"><span data-stu-id="440c1-118">Attribute binding is case sensitive:</span></span>

* <span data-ttu-id="440c1-119">`@bind` geçerli.</span><span class="sxs-lookup"><span data-stu-id="440c1-119">`@bind` is valid.</span></span>
* <span data-ttu-id="440c1-120">`@Bind` ve `@BIND` geçersiz.</span><span class="sxs-lookup"><span data-stu-id="440c1-120">`@Bind` and `@BIND` are invalid.</span></span>

## <a name="unparsable-values"></a><span data-ttu-id="440c1-121">Ayrıştırılamayan değerler</span><span class="sxs-lookup"><span data-stu-id="440c1-121">Unparsable values</span></span>

<span data-ttu-id="440c1-122">Bir Kullanıcı, bir veri sınırlama öğesine ayrıştırılamayan bir değer sağlıyorsa, bağlama olayı tetiklendiğinde, çözümlenemeyen değer otomatik olarak önceki değerine döndürülür.</span><span class="sxs-lookup"><span data-stu-id="440c1-122">When a user provides an unparsable value to a databound element, the unparsable value is automatically reverted to its previous value when the bind event is triggered.</span></span>

<span data-ttu-id="440c1-123">Şu senaryoyu göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="440c1-123">Consider the following scenario:</span></span>

* <span data-ttu-id="440c1-124">Bir `<input>` öğesi, `int` Başlangıç değeri olan bir türe bağlanır `123` :</span><span class="sxs-lookup"><span data-stu-id="440c1-124">An `<input>` element is bound to an `int` type with an initial value of `123`:</span></span>

  ```razor
  <input @bind="inputValue" />

  @code {
      private int inputValue = 123;
  }
  ```

* <span data-ttu-id="440c1-125">Kullanıcı, öğesinin değerini sayfada olarak güncelleştirir `123.45` ve öğe odağını değiştirir.</span><span class="sxs-lookup"><span data-stu-id="440c1-125">The user updates the value of the element to `123.45` in the page and changes the element focus.</span></span>

<span data-ttu-id="440c1-126">Önceki senaryoda, öğenin değeri öğesine geri döndürülür `123` .</span><span class="sxs-lookup"><span data-stu-id="440c1-126">In the preceding scenario, the element's value is reverted to `123`.</span></span> <span data-ttu-id="440c1-127">Değeri `123.45` özgün değeri yararına reddedildiğinde `123` , Kullanıcı değerinin kabul edilmediğini anlamıştır.</span><span class="sxs-lookup"><span data-stu-id="440c1-127">When the value `123.45` is rejected in favor of the original value of `123`, the user understands that their value wasn't accepted.</span></span>

<span data-ttu-id="440c1-128">Varsayılan olarak, bağlama öğenin `onchange` olayına ( `@bind="{PROPERTY OR FIELD}"` ) uygulanır.</span><span class="sxs-lookup"><span data-stu-id="440c1-128">By default, binding applies to the element's `onchange` event (`@bind="{PROPERTY OR FIELD}"`).</span></span> <span data-ttu-id="440c1-129">`@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}`Farklı bir olayda bağlamayı tetiklemek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="440c1-129">Use `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` to trigger binding on a different event.</span></span> <span data-ttu-id="440c1-130">`oninput`Event () için `@bind:event="oninput"` yeniden sürüm, ayrıştırılamayan bir değer sunan herhangi bir tuş vuruşu sonrasında oluşur.</span><span class="sxs-lookup"><span data-stu-id="440c1-130">For the `oninput` event (`@bind:event="oninput"`), the reversion occurs after any keystroke that introduces an unparsable value.</span></span> <span data-ttu-id="440c1-131">`oninput`Olayı, ilişkili bir tür ile hedeflerken `int` , bir kullanıcının bir karakter yazmasının engellenmiş olması engellenir `.` .</span><span class="sxs-lookup"><span data-stu-id="440c1-131">When targeting the `oninput` event with an `int`-bound type, a user is prevented from typing a `.` character.</span></span> <span data-ttu-id="440c1-132">Bir `.` karakter hemen kaldırılır, bu nedenle Kullanıcı yalnızca tam sayılara izin verilen anında geri bildirim alır.</span><span class="sxs-lookup"><span data-stu-id="440c1-132">A `.` character is immediately removed, so the user receives immediate feedback that only whole numbers are permitted.</span></span> <span data-ttu-id="440c1-133">Olay üzerindeki değerin geri döndürülmesi `oninput` ideal değil (örneğin, kullanıcının ayrıştırılamayan bir değeri temizlemeye izin verilmesi gerektiği durumlarda) `<input>` .</span><span class="sxs-lookup"><span data-stu-id="440c1-133">There are scenarios where reverting the value on the `oninput` event isn't ideal, such as when the user should be allowed to clear an unparsable `<input>` value.</span></span> <span data-ttu-id="440c1-134">Alternatifler şunlardır:</span><span class="sxs-lookup"><span data-stu-id="440c1-134">Alternatives include:</span></span>

* <span data-ttu-id="440c1-135">`oninput`Olayı kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="440c1-135">Don't use the `oninput` event.</span></span> <span data-ttu-id="440c1-136">Varsayılan olayı kullanın `onchange` (yalnızca belirtin `@bind="{PROPERTY OR FIELD}"` ); burada, öğe odağı kaybetene kadar geçersiz bir değer geri döndürülemez.</span><span class="sxs-lookup"><span data-stu-id="440c1-136">Use the default `onchange` event (only specify `@bind="{PROPERTY OR FIELD}"`), where an invalid value isn't reverted until the element loses focus.</span></span>
* <span data-ttu-id="440c1-137">Veya gibi null yapılabilir bir türe bağlayın `int?` `string` ve geçersiz girdileri işlemek için özel mantık sağlayın.</span><span class="sxs-lookup"><span data-stu-id="440c1-137">Bind to a nullable type, such as `int?` or `string` and provide custom logic to handle invalid entries.</span></span>
* <span data-ttu-id="440c1-138">Veya gibi bir [form doğrulama bileşeni](xref:blazor/forms-validation)kullanın <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> .</span><span class="sxs-lookup"><span data-stu-id="440c1-138">Use a [form validation component](xref:blazor/forms-validation), such as <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> or <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601>.</span></span> <span data-ttu-id="440c1-139">Form doğrulama bileşenlerinde geçersiz girişleri yönetmek için yerleşik destek vardır.</span><span class="sxs-lookup"><span data-stu-id="440c1-139">Form validation components have built-in support to manage invalid inputs.</span></span> <span data-ttu-id="440c1-140">Daha fazla bilgi için bkz. <xref:blazor/forms-validation>.</span><span class="sxs-lookup"><span data-stu-id="440c1-140">For more information, see <xref:blazor/forms-validation>.</span></span> <span data-ttu-id="440c1-141">Form doğrulama bileşenleri:</span><span class="sxs-lookup"><span data-stu-id="440c1-141">Form validation components:</span></span>
  * <span data-ttu-id="440c1-142">Kullanıcının geçersiz giriş sağlamasına ve ilişkili doğrulama hatalarını almasına izin verin <xref:Microsoft.AspNetCore.Components.Forms.EditContext> .</span><span class="sxs-lookup"><span data-stu-id="440c1-142">Permit the user to provide invalid input and receive validation errors on the associated <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span>
  * <span data-ttu-id="440c1-143">Kullanıcı ek WebForm verisi girmeye uğramadan doğrulama hatalarını Kullanıcı ARABIRIMINDE görüntüleyin.</span><span class="sxs-lookup"><span data-stu-id="440c1-143">Display validation errors in the UI without interfering with the user entering additional webform data.</span></span>

## <a name="format-strings"></a><span data-ttu-id="440c1-144">Biçim dizeleri</span><span class="sxs-lookup"><span data-stu-id="440c1-144">Format strings</span></span>

<span data-ttu-id="440c1-145">Veri bağlama, <xref:System.DateTime> kullanılarak biçim dizeleriyle birlikte kullanılabilir `@bind:format` .</span><span class="sxs-lookup"><span data-stu-id="440c1-145">Data binding works with <xref:System.DateTime> format strings using `@bind:format`.</span></span> <span data-ttu-id="440c1-146">Para birimi veya sayı biçimleri gibi diğer biçim ifadeleri şu anda kullanılamaz.</span><span class="sxs-lookup"><span data-stu-id="440c1-146">Other format expressions, such as currency or number formats, aren't available at this time.</span></span>

```razor
<input @bind="startDate" @bind:format="yyyy-MM-dd" />

@code {
    private DateTime startDate = new DateTime(2020, 1, 1);
}
```

<span data-ttu-id="440c1-147">Yukarıdaki kodda, `<input>` öğesinin alan türü ( `type` ) varsayılan olarak olur `text` .</span><span class="sxs-lookup"><span data-stu-id="440c1-147">In the preceding code, the `<input>` element's field type (`type`) defaults to `text`.</span></span> <span data-ttu-id="440c1-148">`@bind:format` , aşağıdaki .NET türlerini bağlamak için desteklenir:</span><span class="sxs-lookup"><span data-stu-id="440c1-148">`@bind:format` is supported for binding the following .NET types:</span></span>

* <xref:System.DateTime?displayProperty=fullName>
* <span data-ttu-id="440c1-149"><xref:System.DateTime?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="440c1-149"><xref:System.DateTime?displayProperty=fullName>?</span></span>
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <span data-ttu-id="440c1-150"><xref:System.DateTimeOffset?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="440c1-150"><xref:System.DateTimeOffset?displayProperty=fullName>?</span></span>

<span data-ttu-id="440c1-151">`@bind:format`Özniteliği öğesi için uygulanacak tarih biçimini belirtir `value` `<input>` .</span><span class="sxs-lookup"><span data-stu-id="440c1-151">The `@bind:format` attribute specifies the date format to apply to the `value` of the `<input>` element.</span></span> <span data-ttu-id="440c1-152">Biçim Ayrıca bir olay gerçekleştiğinde değeri ayrıştırmak için de kullanılır `onchange` .</span><span class="sxs-lookup"><span data-stu-id="440c1-152">The format is also used to parse the value when an `onchange` event occurs.</span></span>

<span data-ttu-id="440c1-153">`date` Blazor Tarihleri biçimlendirmek için yerleşik destek içerdiğinden, alan türü için bir biçim belirtmenin kullanılması önerilmez.</span><span class="sxs-lookup"><span data-stu-id="440c1-153">Specifying a format for the `date` field type isn't recommended because Blazor has built-in support to format dates.</span></span> <span data-ttu-id="440c1-154">Önerinin artma içinde, `yyyy-MM-dd` alan türüyle bir biçim sağlanırsa yalnızca bağlama için tarih biçimini kullanın `date` :</span><span class="sxs-lookup"><span data-stu-id="440c1-154">In spite of the recommendation, only use the `yyyy-MM-dd` date format for binding to function correctly if a format is supplied with the `date` field type:</span></span>

```razor
<input type="date" @bind="startDate" @bind:format="yyyy-MM-dd">
```

## <a name="binding-with-component-parameters"></a><span data-ttu-id="440c1-155">Bileşen parametreleriyle bağlama</span><span class="sxs-lookup"><span data-stu-id="440c1-155">Binding with component parameters</span></span>

<span data-ttu-id="440c1-156">Yaygın bir senaryo, alt bileşendeki bir özelliği üst öğesinde bir özelliğe bağlamadır.</span><span class="sxs-lookup"><span data-stu-id="440c1-156">A common scenario is binding a property in a child component to a property in its parent.</span></span> <span data-ttu-id="440c1-157">Birden çok bağlama düzeyi aynı anda gerçekleştiğinden, bu senaryoya *zincirleme bağlama* denir.</span><span class="sxs-lookup"><span data-stu-id="440c1-157">This scenario is called a *chained bind* because multiple levels of binding occur simultaneously.</span></span>

<span data-ttu-id="440c1-158">Bileşen parametreleri bir üst bileşenin sözdizimi ile bağlama özelliklerine ve alanlarına izin verir `@bind-{PROPERTY OR FIELD}` .</span><span class="sxs-lookup"><span data-stu-id="440c1-158">Component parameters permit binding properties and fields of a parent component with `@bind-{PROPERTY OR FIELD}` syntax.</span></span>

<span data-ttu-id="440c1-159">Zincirli bağlamalar [`@bind`](xref:mvc/views/razor#bind) alt bileşendeki sözdizimiyle uygulanamaz.</span><span class="sxs-lookup"><span data-stu-id="440c1-159">Chained binds can't be implemented with [`@bind`](xref:mvc/views/razor#bind) syntax in the child component.</span></span> <span data-ttu-id="440c1-160">Alt bileşenden üst öğe içindeki özelliği güncelleştirmeyi desteklemek için bir olay işleyicisi ve değeri ayrı olarak belirtilmelidir.</span><span class="sxs-lookup"><span data-stu-id="440c1-160">An event handler and value must be specified separately to support updating the property in the parent from the child component.</span></span>

<span data-ttu-id="440c1-161">Üst bileşen, [`@bind`](xref:mvc/views/razor#bind) alt bileşenle veri bağlamayı ayarlamak için söz dizimini kullanır.</span><span class="sxs-lookup"><span data-stu-id="440c1-161">The parent component still leverages the [`@bind`](xref:mvc/views/razor#bind) syntax to set up the data-binding with the child component.</span></span>

<span data-ttu-id="440c1-162">Aşağıdaki `Child` bileşende ( `Shared/Child.razor` ) bir `Year` bileşen parametresi ve `YearChanged` geri çağırması vardır:</span><span class="sxs-lookup"><span data-stu-id="440c1-162">The following `Child` component (`Shared/Child.razor`) has a `Year` component parameter and `YearChanged` callback:</span></span>

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

<span data-ttu-id="440c1-163">Geri çağırma ( <xref:Microsoft.AspNetCore.Components.EventCallback%601> ), bileşen parametre adı olarak " `Changed` " soneki () ile adlandırılmalıdır `{PARAMETER NAME}Changed` .</span><span class="sxs-lookup"><span data-stu-id="440c1-163">The callback (<xref:Microsoft.AspNetCore.Components.EventCallback%601>) must be named as the component parameter name followed by the "`Changed`" suffix (`{PARAMETER NAME}Changed`).</span></span> <span data-ttu-id="440c1-164">Önceki örnekte, geri çağırma adlandırılır `YearChanged` .</span><span class="sxs-lookup"><span data-stu-id="440c1-164">In the preceding example, the callback is named `YearChanged`.</span></span> <span data-ttu-id="440c1-165"><xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A?displayProperty=nameWithType> Belirtilen bağımsız değişkenle bağlama ile ilişkili temsilciyi çağırır ve değiştirilen özellik için bir olay bildirimi gönderir.</span><span class="sxs-lookup"><span data-stu-id="440c1-165"><xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A?displayProperty=nameWithType> invokes the delegate associated with the binding with the provided argument and dispatches an event notification for the changed property.</span></span>

<span data-ttu-id="440c1-166">Aşağıdaki `Parent` bileşende ( `Parent.razor` ), `year` alanı `Year` alt bileşenin parametresine bağlanır:</span><span class="sxs-lookup"><span data-stu-id="440c1-166">In the following `Parent` component (`Parent.razor`), the `year` field is bound to the `Year` parameter of the child component:</span></span>

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

<span data-ttu-id="440c1-167">Parametrenin `Year` türüyle eşleşen bir yardımcı olayı olduğundan parametre bağlanabilir `YearChanged` `Year` .</span><span class="sxs-lookup"><span data-stu-id="440c1-167">The `Year` parameter is bindable because it has a companion `YearChanged` event that matches the type of the `Year` parameter.</span></span>

<span data-ttu-id="440c1-168">Kurala göre, bir özellik işleyiciye atanmış bir özniteliği ekleyerek karşılık gelen bir olay işleyicisine bağlanabilir `@bind-{PROPERTY}:event` .</span><span class="sxs-lookup"><span data-stu-id="440c1-168">By convention, a property can be bound to a corresponding event handler by including an `@bind-{PROPERTY}:event` attribute assigned to the handler.</span></span> <span data-ttu-id="440c1-169">`<Child @bind-Year="year" />` yazmaya eşittir:</span><span class="sxs-lookup"><span data-stu-id="440c1-169">`<Child @bind-Year="year" />` is equivalent to writing:</span></span>

```razor
<Child @bind-Year="year" @bind-Year:event="YearChanged" />
```

<span data-ttu-id="440c1-170">Daha karmaşık ve gerçek dünyada bir örnekte, aşağıdaki `PasswordField` bileşen ( `PasswordField.razor` ):</span><span class="sxs-lookup"><span data-stu-id="440c1-170">In a more sophisticated and real-world example, the following `PasswordField` component (`PasswordField.razor`):</span></span>

* <span data-ttu-id="440c1-171">Bir `<input>` öğenin değerini bir `password` alana ayarlar.</span><span class="sxs-lookup"><span data-stu-id="440c1-171">Sets an `<input>` element's value to a `password` field.</span></span>
* <span data-ttu-id="440c1-172">Bir özelliğin değişikliklerini, `Password` [`EventCallback`](xref:blazor/components/event-handling#eventcallback) alt öğenin geçerli değerinde bağımsız değişkeni olarak geçen bir üst bileşene gösterir `password` .</span><span class="sxs-lookup"><span data-stu-id="440c1-172">Exposes changes of a `Password` property to a parent component with an [`EventCallback`](xref:blazor/components/event-handling#eventcallback) that passes in the current value of the child's `password` field as its argument.</span></span>
* <span data-ttu-id="440c1-173">`onclick`Yöntemini tetiklemek için olayını kullanır `ToggleShowPassword` .</span><span class="sxs-lookup"><span data-stu-id="440c1-173">Uses the `onclick` event to trigger the `ToggleShowPassword` method.</span></span> <span data-ttu-id="440c1-174">Daha fazla bilgi için bkz. <xref:blazor/components/event-handling>.</span><span class="sxs-lookup"><span data-stu-id="440c1-174">For more information, see <xref:blazor/components/event-handling>.</span></span>

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

<span data-ttu-id="440c1-175">`PasswordField`Bileşen başka bir bileşende kullanılır:</span><span class="sxs-lookup"><span data-stu-id="440c1-175">The `PasswordField` component is used in another component:</span></span>

```razor
@page "/Parent"

<h1>Parent Component</h1>

<PasswordField @bind-Password="password" />

@code {
    private string password;
}
```

<span data-ttu-id="440c1-176">Bağlamanın temsilcisini çağıran yöntemde denetimler veya tuzak hataları gerçekleştirin.</span><span class="sxs-lookup"><span data-stu-id="440c1-176">Perform checks or trap errors in the method that invokes the binding's delegate.</span></span> <span data-ttu-id="440c1-177">Aşağıdaki örnek, parolanın değerinde bir boşluk kullanılmışsa kullanıcıya anında geri bildirim sağlar:</span><span class="sxs-lookup"><span data-stu-id="440c1-177">The following example provides immediate feedback to the user if a space is used in the password's value:</span></span>

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

<span data-ttu-id="440c1-178">Hakkında daha fazla bilgi için <xref:Microsoft.AspNetCore.Components.EventCallback%601> bkz <xref:blazor/components/event-handling#eventcallback> ..</span><span class="sxs-lookup"><span data-stu-id="440c1-178">For more information on <xref:Microsoft.AspNetCore.Components.EventCallback%601>, see <xref:blazor/components/event-handling#eventcallback>.</span></span>

## <a name="bind-across-more-than-two-components"></a><span data-ttu-id="440c1-179">İkiden fazla bileşen arasında bağlama</span><span class="sxs-lookup"><span data-stu-id="440c1-179">Bind across more than two components</span></span>

<span data-ttu-id="440c1-180">Herhangi bir sayıda iç içe bileşeni bağlayabilirsiniz, ancak tek yönlü verilerin akışını dikkate almanız gerekir:</span><span class="sxs-lookup"><span data-stu-id="440c1-180">You can bind through any number of nested components, but you must respect the one-way flow of data:</span></span>

* <span data-ttu-id="440c1-181">Değişiklik bildirimleri *hiyerarşinin akışını* .</span><span class="sxs-lookup"><span data-stu-id="440c1-181">Change notifications *flow up the hierarchy* .</span></span>
* <span data-ttu-id="440c1-182">Yeni parametre değerleri *hiyerarşinin altına akar* .</span><span class="sxs-lookup"><span data-stu-id="440c1-182">New parameter values *flow down the hierarchy* .</span></span>

<span data-ttu-id="440c1-183">Ortak ve önerilen bir yaklaşım, hangi durumun güncellenmesi gerektiğini öğrenmek için temel alınan verileri yalnızca üst bileşende depomaktır.</span><span class="sxs-lookup"><span data-stu-id="440c1-183">A common and recommended approach is to only store the underlying data in the parent component to avoid any confusion about what state must be updated.</span></span>

<span data-ttu-id="440c1-184">Aşağıdaki bileşenler önceki kavramları göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="440c1-184">The following components demonstrate the preceding concepts:</span></span>

<span data-ttu-id="440c1-185">`ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="440c1-185">`ParentComponent.razor`:</span></span>

```razor
<h1>Parent Component</h1>

<p>Parent Property: <b>@parentValue</b></p>

<p>
    <button @onclick="ChangeValue">Change from Parent</button>
</p>

<ChildComponent @bind-Property="parentValue" />

@code {
    private string parentValue = "Initial value set in Parent";

    private void ChangeValue()
    {
        parentValue = $"Set in Parent {DateTime.Now}";
    }
}
```

<span data-ttu-id="440c1-186">`ChildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="440c1-186">`ChildComponent.razor`:</span></span>

```razor
<div class="border rounded m-1 p-1">
    <h2>Child Component</h2>

    <p>Child Property: <b>@Property</b></p>

    <p>
        <button @onclick="ChangeValue">Change from Child</button>
    </p>

    <GrandchildComponent @bind-Property="BoundValue" />
</div>

@code {
    [Parameter]
    public string Property { get; set; }

    [Parameter]
    public EventCallback<string> PropertyChanged { get; set; }

    private string BoundValue
    {
        get => Property;
        set => PropertyChanged.InvokeAsync(value);
    }

    private async Task ChangeValue()
    {
        await PropertyChanged.InvokeAsync($"Set in Child {DateTime.Now}");
    }
}
```

<span data-ttu-id="440c1-187">`GrandchildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="440c1-187">`GrandchildComponent.razor`:</span></span>

```razor
<div class="border rounded m-1 p-1">
    <h3>Grandchild Component</h3>

    <p>Grandchild Property: <b>@Property</b></p>

    <p>
        <button @onclick="ChangeValue">Change from Grandchild</button>
    </p>
</div>

@code {
    [Parameter]
    public string Property { get; set; }

    [Parameter]
    public EventCallback<string> PropertyChanged { get; set; }

    private async Task ChangeValue()
    {
        await PropertyChanged.InvokeAsync($"Set in Grandchild {DateTime.Now}");
    }
}
```

<span data-ttu-id="440c1-188">İç içe geçmiş olmayan bileşenlerde verileri bellek içinde paylaşmaya uygun alternatif bir yaklaşım için bkz <xref:blazor/state-management#in-memory-state-container-service> ..</span><span class="sxs-lookup"><span data-stu-id="440c1-188">For an alternative approach suited to sharing data in-memory across components that aren't necessarily nested, see <xref:blazor/state-management#in-memory-state-container-service>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="440c1-189">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="440c1-189">Additional resources</span></span>

* [<span data-ttu-id="440c1-190">Bir formda radyo düğmelerine bağlama</span><span class="sxs-lookup"><span data-stu-id="440c1-190">Binding to radio buttons in a form</span></span>](xref:blazor/forms-validation#radio-buttons)
* [<span data-ttu-id="440c1-191">`<select>`Form Içindeki C# nesne değerlerine öğe seçenekleri bağlama `null`</span><span class="sxs-lookup"><span data-stu-id="440c1-191">Binding `<select>` element options to C# object `null` values in a form</span></span>](xref:blazor/forms-validation#binding-select-element-options-to-c-object-null-values)
