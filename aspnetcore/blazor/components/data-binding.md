---
title: ASP.NET Core Blazor veri bağlama
author: guardrex
description: Uygulamalardaki bileşenler ve DOM öğeleri için veri bağlama özellikleri hakkında bilgi edinin Blazor .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/26/2020
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
uid: blazor/components/data-binding
ms.openlocfilehash: 6f5ad6b8f225834c92d6e33d8bcf608b56678e67
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88014678"
---
# <a name="aspnet-core-no-locblazor-data-binding"></a><span data-ttu-id="c2a7a-103">ASP.NET Core Blazor veri bağlama</span><span class="sxs-lookup"><span data-stu-id="c2a7a-103">ASP.NET Core Blazor data binding</span></span>

<span data-ttu-id="c2a7a-104">, [Luke Latham](https://github.com/guardrex) ve [Daniel Roth](https://github.com/danroth27) tarafından</span><span class="sxs-lookup"><span data-stu-id="c2a7a-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="c2a7a-105">Razorbileşenler, [`@bind`](xref:mvc/views/razor#bind) bir alan, özellik veya ifade değeri ile adlandırılmış BIR HTML öğesi özniteliği aracılığıyla veri bağlama özellikleri sağlar Razor .</span><span class="sxs-lookup"><span data-stu-id="c2a7a-105">Razor components provide data binding features via an HTML element attribute named [`@bind`](xref:mvc/views/razor#bind) with a field, property, or Razor expression value.</span></span>

<span data-ttu-id="c2a7a-106">Aşağıdaki örnek, `CurrentValue` özelliğini metin kutusu değerine bağlar:</span><span class="sxs-lookup"><span data-stu-id="c2a7a-106">The following example binds the `CurrentValue` property to the text box's value:</span></span>

```razor
<input @bind="CurrentValue" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="c2a7a-107">Metin kutusu odağı kaybettiğinde, özelliğin değeri güncellenir.</span><span class="sxs-lookup"><span data-stu-id="c2a7a-107">When the text box loses focus, the property's value is updated.</span></span>

<span data-ttu-id="c2a7a-108">Metin kutusu kullanıcı arabiriminde, özelliğin değerini değiştirme yanıt olarak değil, yalnızca bileşen işlendiğinde güncelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="c2a7a-108">The text box is updated in the UI only when the component is rendered, not in response to changing the property's value.</span></span> <span data-ttu-id="c2a7a-109">Bileşenler olay işleyicisi kodu yürütüldükten sonra kendilerini oluşturduğundan, özellik güncelleştirmeleri *genellikle* olay işleyicisi tetiklendikten hemen sonra Kullanıcı arabirimine yansıtılır.</span><span class="sxs-lookup"><span data-stu-id="c2a7a-109">Since components render themselves after event handler code executes, property updates are *usually* reflected in the UI immediately after an event handler is triggered.</span></span>

<span data-ttu-id="c2a7a-110">[`@bind`](xref:mvc/views/razor#bind) `CurrentValue` Özelliği () ile kullanmak, `<input @bind="CurrentValue" />` temelde aşağıdakilere eşdeğerdir:</span><span class="sxs-lookup"><span data-stu-id="c2a7a-110">Using [`@bind`](xref:mvc/views/razor#bind) with the `CurrentValue` property (`<input @bind="CurrentValue" />`) is essentially equivalent to the following:</span></span>

```razor
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = 
        __e.Value.ToString())" />
        
@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="c2a7a-111">Bileşen işlendiğinde, `value` giriş öğesi `CurrentValue` özelliğinden gelir.</span><span class="sxs-lookup"><span data-stu-id="c2a7a-111">When the component is rendered, the `value` of the input element comes from the `CurrentValue` property.</span></span> <span data-ttu-id="c2a7a-112">Kullanıcı metin kutusuna yazdığında ve öğe odağını değiştirdiğinde, `onchange` olay tetiklenir ve `CurrentValue` özellik değiştirilen değere ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="c2a7a-112">When the user types in the text box and changes element focus, the `onchange` event is fired and the `CurrentValue` property is set to the changed value.</span></span> <span data-ttu-id="c2a7a-113">[`@bind`](xref:mvc/views/razor#bind)Tür dönüştürmelerinde oluşan durumları işletiğinden, gerçekte kod oluşturma daha karmaşıktır.</span><span class="sxs-lookup"><span data-stu-id="c2a7a-113">In reality, the code generation is more complex because [`@bind`](xref:mvc/views/razor#bind) handles cases where type conversions are performed.</span></span> <span data-ttu-id="c2a7a-114">İlke ' de, [`@bind`](xref:mvc/views/razor#bind) bir ifadenin geçerli değerini bir `value` özniteliğiyle ilişkilendirir ve kayıtlı işleyiciyi kullanarak değişiklikleri işler.</span><span class="sxs-lookup"><span data-stu-id="c2a7a-114">In principle, [`@bind`](xref:mvc/views/razor#bind) associates the current value of an expression with a `value` attribute and handles changes using the registered handler.</span></span>

<span data-ttu-id="c2a7a-115">Parametre içeren bir özniteliği de ekleyerek diğer olaylardaki bir özelliği veya alanı bağlayın `@bind:event` `event` .</span><span class="sxs-lookup"><span data-stu-id="c2a7a-115">Bind a property or field on other events by also including an `@bind:event` attribute with an `event` parameter.</span></span> <span data-ttu-id="c2a7a-116">Aşağıdaki örnek, `CurrentValue` olayında özelliği bağlar `oninput` :</span><span class="sxs-lookup"><span data-stu-id="c2a7a-116">The following example binds the `CurrentValue` property on the `oninput` event:</span></span>

```razor
<input @bind="CurrentValue" @bind:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="c2a7a-117">`onchange`' In aksine, öğe odağı kaybettiğinde harekete geçirilir, `oninput` metin kutusunun değeri değiştiğinde harekete geçirilir.</span><span class="sxs-lookup"><span data-stu-id="c2a7a-117">Unlike `onchange`, which fires when the element loses focus, `oninput` fires when the value of the text box changes.</span></span>

<span data-ttu-id="c2a7a-118">`@bind-{ATTRIBUTE}` `@bind-{ATTRIBUTE}:event` Dışındaki öğe özniteliklerini bağlamak için sözdizimi ile kullanın `value` .</span><span class="sxs-lookup"><span data-stu-id="c2a7a-118">Use `@bind-{ATTRIBUTE}` with `@bind-{ATTRIBUTE}:event` syntax to bind element attributes other than `value`.</span></span> <span data-ttu-id="c2a7a-119">Aşağıdaki örnekte, paragrafın stili `paragraphStyle` değer değiştiğinde güncelleştirilir:</span><span class="sxs-lookup"><span data-stu-id="c2a7a-119">In the following example, the paragraph's style is updated when the `paragraphStyle` value changes:</span></span>

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

<span data-ttu-id="c2a7a-120">Öznitelik bağlama büyük/küçük harfe duyarlıdır:</span><span class="sxs-lookup"><span data-stu-id="c2a7a-120">Attribute binding is case sensitive:</span></span>

* <span data-ttu-id="c2a7a-121">`@bind`geçerli.</span><span class="sxs-lookup"><span data-stu-id="c2a7a-121">`@bind` is valid.</span></span>
* <span data-ttu-id="c2a7a-122">`@Bind`ve `@BIND` geçersiz.</span><span class="sxs-lookup"><span data-stu-id="c2a7a-122">`@Bind` and `@BIND` are invalid.</span></span>

## <a name="unparsable-values"></a><span data-ttu-id="c2a7a-123">Ayrıştırılamayan değerler</span><span class="sxs-lookup"><span data-stu-id="c2a7a-123">Unparsable values</span></span>

<span data-ttu-id="c2a7a-124">Bir Kullanıcı, bir veri sınırlama öğesine ayrıştırılamayan bir değer sağlıyorsa, bağlama olayı tetiklendiğinde, çözümlenemeyen değer otomatik olarak önceki değerine döndürülür.</span><span class="sxs-lookup"><span data-stu-id="c2a7a-124">When a user provides an unparsable value to a databound element, the unparsable value is automatically reverted to its previous value when the bind event is triggered.</span></span>

<span data-ttu-id="c2a7a-125">Şu senaryoyu göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="c2a7a-125">Consider the following scenario:</span></span>

* <span data-ttu-id="c2a7a-126">Bir `<input>` öğesi, `int` Başlangıç değeri olan bir türe bağlanır `123` :</span><span class="sxs-lookup"><span data-stu-id="c2a7a-126">An `<input>` element is bound to an `int` type with an initial value of `123`:</span></span>

  ```razor
  <input @bind="MyProperty" />

  @code {
      [Parameter]
      public int MyProperty { get; set; } = 123;
  }
  ```
* <span data-ttu-id="c2a7a-127">Kullanıcı, öğesinin değerini sayfada olarak güncelleştirir `123.45` ve öğe odağını değiştirir.</span><span class="sxs-lookup"><span data-stu-id="c2a7a-127">The user updates the value of the element to `123.45` in the page and changes the element focus.</span></span>

<span data-ttu-id="c2a7a-128">Önceki senaryoda, öğenin değeri öğesine geri döndürülür `123` .</span><span class="sxs-lookup"><span data-stu-id="c2a7a-128">In the preceding scenario, the element's value is reverted to `123`.</span></span> <span data-ttu-id="c2a7a-129">Değeri `123.45` özgün değeri yararına reddedildiğinde `123` , Kullanıcı değerinin kabul edilmediğini anlamıştır.</span><span class="sxs-lookup"><span data-stu-id="c2a7a-129">When the value `123.45` is rejected in favor of the original value of `123`, the user understands that their value wasn't accepted.</span></span>

<span data-ttu-id="c2a7a-130">Varsayılan olarak, bağlama öğenin `onchange` olayına ( `@bind="{PROPERTY OR FIELD}"` ) uygulanır.</span><span class="sxs-lookup"><span data-stu-id="c2a7a-130">By default, binding applies to the element's `onchange` event (`@bind="{PROPERTY OR FIELD}"`).</span></span> <span data-ttu-id="c2a7a-131">`@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}`Farklı bir olayda bağlamayı tetiklemek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="c2a7a-131">Use `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` to trigger binding on a different event.</span></span> <span data-ttu-id="c2a7a-132">`oninput`Event () için `@bind:event="oninput"` yeniden sürüm, ayrıştırılamayan bir değer sunan herhangi bir tuş vuruşu sonrasında oluşur.</span><span class="sxs-lookup"><span data-stu-id="c2a7a-132">For the `oninput` event (`@bind:event="oninput"`), the reversion occurs after any keystroke that introduces an unparsable value.</span></span> <span data-ttu-id="c2a7a-133">`oninput`Olayı, ilişkili bir tür ile hedeflerken `int` , bir kullanıcının bir karakter yazmasının engellenmiş olması engellenir `.` .</span><span class="sxs-lookup"><span data-stu-id="c2a7a-133">When targeting the `oninput` event with an `int`-bound type, a user is prevented from typing a `.` character.</span></span> <span data-ttu-id="c2a7a-134">Bir `.` karakter hemen kaldırılır, bu nedenle Kullanıcı yalnızca tam sayılara izin verilen anında geri bildirim alır.</span><span class="sxs-lookup"><span data-stu-id="c2a7a-134">A `.` character is immediately removed, so the user receives immediate feedback that only whole numbers are permitted.</span></span> <span data-ttu-id="c2a7a-135">Olay üzerindeki değerin geri döndürülmesi `oninput` ideal değil (örneğin, kullanıcının ayrıştırılamayan bir değeri temizlemeye izin verilmesi gerektiği durumlarda) `<input>` .</span><span class="sxs-lookup"><span data-stu-id="c2a7a-135">There are scenarios where reverting the value on the `oninput` event isn't ideal, such as when the user should be allowed to clear an unparsable `<input>` value.</span></span> <span data-ttu-id="c2a7a-136">Alternatifler şunlardır:</span><span class="sxs-lookup"><span data-stu-id="c2a7a-136">Alternatives include:</span></span>

* <span data-ttu-id="c2a7a-137">`oninput`Olayı kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="c2a7a-137">Don't use the `oninput` event.</span></span> <span data-ttu-id="c2a7a-138">Varsayılan olayı kullanın `onchange` (yalnızca belirtin `@bind="{PROPERTY OR FIELD}"` ); burada, öğe odağı kaybetene kadar geçersiz bir değer geri döndürülemez.</span><span class="sxs-lookup"><span data-stu-id="c2a7a-138">Use the default `onchange` event (only specify `@bind="{PROPERTY OR FIELD}"`), where an invalid value isn't reverted until the element loses focus.</span></span>
* <span data-ttu-id="c2a7a-139">Veya gibi null yapılabilir bir türe bağlayın `int?` `string` ve geçersiz girdileri işlemek için özel mantık sağlayın.</span><span class="sxs-lookup"><span data-stu-id="c2a7a-139">Bind to a nullable type, such as `int?` or `string`, and provide custom logic to handle invalid entries.</span></span>
* <span data-ttu-id="c2a7a-140">Veya gibi bir [form doğrulama bileşeni](xref:blazor/forms-validation)kullanın <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> .</span><span class="sxs-lookup"><span data-stu-id="c2a7a-140">Use a [form validation component](xref:blazor/forms-validation), such as <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> or <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601>.</span></span> <span data-ttu-id="c2a7a-141">Form doğrulama bileşenlerinde geçersiz girişleri yönetmek için yerleşik destek vardır.</span><span class="sxs-lookup"><span data-stu-id="c2a7a-141">Form validation components have built-in support to manage invalid inputs.</span></span> <span data-ttu-id="c2a7a-142">Form doğrulama bileşenleri:</span><span class="sxs-lookup"><span data-stu-id="c2a7a-142">Form validation components:</span></span>
  * <span data-ttu-id="c2a7a-143">Kullanıcının geçersiz giriş sağlamasına ve ilişkili doğrulama hatalarını almasına izin verin <xref:Microsoft.AspNetCore.Components.Forms.EditContext> .</span><span class="sxs-lookup"><span data-stu-id="c2a7a-143">Permit the user to provide invalid input and receive validation errors on the associated <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span>
  * <span data-ttu-id="c2a7a-144">Kullanıcı ek WebForm verisi girmeye uğramadan doğrulama hatalarını Kullanıcı ARABIRIMINDE görüntüleyin.</span><span class="sxs-lookup"><span data-stu-id="c2a7a-144">Display validation errors in the UI without interfering with the user entering additional webform data.</span></span>

## <a name="format-strings"></a><span data-ttu-id="c2a7a-145">Biçim dizeleri</span><span class="sxs-lookup"><span data-stu-id="c2a7a-145">Format strings</span></span>

<span data-ttu-id="c2a7a-146">Veri bağlama, <xref:System.DateTime> kullanılarak biçim dizeleriyle birlikte kullanılabilir `@bind:format` .</span><span class="sxs-lookup"><span data-stu-id="c2a7a-146">Data binding works with <xref:System.DateTime> format strings using `@bind:format`.</span></span> <span data-ttu-id="c2a7a-147">Para birimi veya sayı biçimleri gibi diğer biçim ifadeleri şu anda kullanılamaz.</span><span class="sxs-lookup"><span data-stu-id="c2a7a-147">Other format expressions, such as currency or number formats, aren't available at this time.</span></span>

```razor
<input @bind="StartDate" @bind:format="yyyy-MM-dd" />

@code {
    [Parameter]
    public DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

<span data-ttu-id="c2a7a-148">Yukarıdaki kodda, `<input>` öğesinin alan türü ( `type` ) varsayılan olarak olur `text` .</span><span class="sxs-lookup"><span data-stu-id="c2a7a-148">In the preceding code, the `<input>` element's field type (`type`) defaults to `text`.</span></span> <span data-ttu-id="c2a7a-149">`@bind:format`, aşağıdaki .NET türlerini bağlamak için desteklenir:</span><span class="sxs-lookup"><span data-stu-id="c2a7a-149">`@bind:format` is supported for binding the following .NET types:</span></span>

* <xref:System.DateTime?displayProperty=fullName>
* <span data-ttu-id="c2a7a-150"><xref:System.DateTime?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="c2a7a-150"><xref:System.DateTime?displayProperty=fullName>?</span></span>
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <span data-ttu-id="c2a7a-151"><xref:System.DateTimeOffset?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="c2a7a-151"><xref:System.DateTimeOffset?displayProperty=fullName>?</span></span>

<span data-ttu-id="c2a7a-152">`@bind:format`Özniteliği öğesi için uygulanacak tarih biçimini belirtir `value` `<input>` .</span><span class="sxs-lookup"><span data-stu-id="c2a7a-152">The `@bind:format` attribute specifies the date format to apply to the `value` of the `<input>` element.</span></span> <span data-ttu-id="c2a7a-153">Biçim Ayrıca bir olay gerçekleştiğinde değeri ayrıştırmak için de kullanılır `onchange` .</span><span class="sxs-lookup"><span data-stu-id="c2a7a-153">The format is also used to parse the value when an `onchange` event occurs.</span></span>

<span data-ttu-id="c2a7a-154">`date` Blazor Tarihleri biçimlendirmek için yerleşik destek içerdiğinden, alan türü için bir biçim belirtmenin kullanılması önerilmez.</span><span class="sxs-lookup"><span data-stu-id="c2a7a-154">Specifying a format for the `date` field type isn't recommended because Blazor has built-in support to format dates.</span></span> <span data-ttu-id="c2a7a-155">Önerinin artma içinde, `yyyy-MM-dd` alan türüyle bir biçim sağlanırsa, bağlama için yalnızca tarih biçimini kullanın `date` :</span><span class="sxs-lookup"><span data-stu-id="c2a7a-155">In spite of the recommendation, only use the `yyyy-MM-dd` date format for binding to work correctly if a format is supplied with the `date` field type:</span></span>

```razor
<input type="date" @bind="StartDate" @bind:format="yyyy-MM-dd">
```

## <a name="parent-to-child-binding-with-component-parameters"></a><span data-ttu-id="c2a7a-156">Bileşen parametreleriyle üst-alt öğe bağlama</span><span class="sxs-lookup"><span data-stu-id="c2a7a-156">Parent-to-child binding with component parameters</span></span>

<span data-ttu-id="c2a7a-157">Bağlama, bir `@bind-{PROPERTY}` üst bileşenden bir özellik değerini alt bileşene doğru bir şekilde bağlayabileceği bileşen parametrelerini tanır.</span><span class="sxs-lookup"><span data-stu-id="c2a7a-157">Binding recognizes component parameters, where `@bind-{PROPERTY}` can bind a property value from a parent component down to a child component.</span></span> <span data-ttu-id="c2a7a-158">Bir alt öğeden üst öğeye bağlama, [zincirleme bağlama Ile alt-üst öğe bağlama](#child-to-parent-binding-with-chained-bind) bölümünde ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="c2a7a-158">Binding from a child to a parent is covered in the [Child-to-parent binding with chained bind](#child-to-parent-binding-with-chained-bind) section.</span></span>

<span data-ttu-id="c2a7a-159">Aşağıdaki alt bileşende ( `ChildComponent` ) bir `Year` bileşen parametresi ve `YearChanged` geri çağırması vardır:</span><span class="sxs-lookup"><span data-stu-id="c2a7a-159">The following child component (`ChildComponent`) has a `Year` component parameter and `YearChanged` callback:</span></span>

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

<span data-ttu-id="c2a7a-160"><xref:Microsoft.AspNetCore.Components.EventCallback%601>Önceki örnekte, bileşen parametresi adı, ardından `Changed` soneki () tarafından adlandırılmalıdır `{PARAMETER NAME}Changed` `YearChanged` .</span><span class="sxs-lookup"><span data-stu-id="c2a7a-160">The <xref:Microsoft.AspNetCore.Components.EventCallback%601> must be named as the component parameter name followed by the `Changed` suffix (`{PARAMETER NAME}Changed`), `YearChanged` in the preceding example.</span></span> <span data-ttu-id="c2a7a-161">Hakkında daha fazla bilgi için <xref:Microsoft.AspNetCore.Components.EventCallback%601> bkz <xref:blazor/components/event-handling#eventcallback> ..</span><span class="sxs-lookup"><span data-stu-id="c2a7a-161">For more information on <xref:Microsoft.AspNetCore.Components.EventCallback%601>, see <xref:blazor/components/event-handling#eventcallback>.</span></span>

<span data-ttu-id="c2a7a-162">Aşağıdaki üst bileşen şunları kullanır:</span><span class="sxs-lookup"><span data-stu-id="c2a7a-162">The following parent component uses:</span></span>

* <span data-ttu-id="c2a7a-163">`ChildComponent`ve `ParentYear` parametresini üst `Year` bileşenden alt bileşenin parametresine bağlar.</span><span class="sxs-lookup"><span data-stu-id="c2a7a-163">`ChildComponent` and binds the `ParentYear` parameter from the parent to the `Year` parameter on the child component.</span></span>
* <span data-ttu-id="c2a7a-164">`onclick`Olay, yöntemi tetiklemek için kullanılır `ChangeTheYear` .</span><span class="sxs-lookup"><span data-stu-id="c2a7a-164">The `onclick` event is used to trigger the `ChangeTheYear` method.</span></span> <span data-ttu-id="c2a7a-165">Daha fazla bilgi için bkz. <xref:blazor/components/event-handling>.</span><span class="sxs-lookup"><span data-stu-id="c2a7a-165">For more information, see <xref:blazor/components/event-handling>.</span></span>

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

<span data-ttu-id="c2a7a-166">Yüklemesi `ParentComponent` aşağıdaki biçimlendirmeyi üretir:</span><span class="sxs-lookup"><span data-stu-id="c2a7a-166">Loading the `ParentComponent` produces the following markup:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

<span data-ttu-id="c2a7a-167">`ParentYear`Özelliğin değeri, içindeki düğme seçilerek değiştirilirse, `ParentComponent` `Year` öğesinin özelliği `ChildComponent` güncellenir.</span><span class="sxs-lookup"><span data-stu-id="c2a7a-167">If the value of the `ParentYear` property is changed by selecting the button in the `ParentComponent`, the `Year` property of the `ChildComponent` is updated.</span></span> <span data-ttu-id="c2a7a-168">Yeni değeri, `Year` yeniden kullanıldığında kullanıcı arabiriminde işlenir `ParentComponent` :</span><span class="sxs-lookup"><span data-stu-id="c2a7a-168">The new value of `Year` is rendered in the UI when the `ParentComponent` is rerendered:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

<span data-ttu-id="c2a7a-169">Parametrenin `Year` türüyle eşleşen bir yardımcı olayı olduğundan parametre bağlanabilir `YearChanged` `Year` .</span><span class="sxs-lookup"><span data-stu-id="c2a7a-169">The `Year` parameter is bindable because it has a companion `YearChanged` event that matches the type of the `Year` parameter.</span></span>

<span data-ttu-id="c2a7a-170">Kurala göre, `<ChildComponent @bind-Year="ParentYear" />` temelde yazmaya eşdeğerdir:</span><span class="sxs-lookup"><span data-stu-id="c2a7a-170">By convention, `<ChildComponent @bind-Year="ParentYear" />` is essentially equivalent to writing:</span></span>

```razor
<ChildComponent @bind-Year="ParentYear" @bind-Year:event="YearChanged" />
```

<span data-ttu-id="c2a7a-171">Genel olarak, bir özellik bir özniteliği eklenerek ilgili olay işleyicisine bağlanabilir `@bind-{PROPRETY}:event` .</span><span class="sxs-lookup"><span data-stu-id="c2a7a-171">In general, a property can be bound to a corresponding event handler by including an `@bind-{PROPRETY}:event` attribute.</span></span> <span data-ttu-id="c2a7a-172">Örneğin, özelliği `MyProp` `MyEventHandler` aşağıdaki iki öznitelik kullanılarak bağlanabilir:</span><span class="sxs-lookup"><span data-stu-id="c2a7a-172">For example, the property `MyProp` can be bound to `MyEventHandler` using the following two attributes:</span></span>

```razor
<MyComponent @bind-MyProp="MyValue" @bind-MyProp:event="MyEventHandler" />
```

## <a name="child-to-parent-binding-with-chained-bind"></a><span data-ttu-id="c2a7a-173">Zincirli bağlama ile üstten üst öğe bağlama</span><span class="sxs-lookup"><span data-stu-id="c2a7a-173">Child-to-parent binding with chained bind</span></span>

<span data-ttu-id="c2a7a-174">Yaygın bir senaryo, bir veri bağlama parametresini bileşen çıkışında bir sayfa öğesine zincirlemesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="c2a7a-174">A common scenario is chaining a data-bound parameter to a page element in the component's output.</span></span> <span data-ttu-id="c2a7a-175">Birden çok bağlama düzeyi aynı anda gerçekleştiğinden, bu senaryoya *zincirleme bağlama* denir.</span><span class="sxs-lookup"><span data-stu-id="c2a7a-175">This scenario is called a *chained bind* because multiple levels of binding occur simultaneously.</span></span>

<span data-ttu-id="c2a7a-176">Bir zincir bağlama [`@bind`](xref:mvc/views/razor#bind) , sayfanın öğesinde sözdizimi ile uygulanamaz.</span><span class="sxs-lookup"><span data-stu-id="c2a7a-176">A chained bind can't be implemented with [`@bind`](xref:mvc/views/razor#bind) syntax in the page's element.</span></span> <span data-ttu-id="c2a7a-177">Olay işleyicisi ve değeri ayrı olarak belirtilmelidir.</span><span class="sxs-lookup"><span data-stu-id="c2a7a-177">The event handler and value must be specified separately.</span></span> <span data-ttu-id="c2a7a-178">Ancak, bir üst bileşen, bir [`@bind`](xref:mvc/views/razor#bind) sözdizimi bileşenin parametresiyle birlikte kullanabilir.</span><span class="sxs-lookup"><span data-stu-id="c2a7a-178">A parent component, however, can use [`@bind`](xref:mvc/views/razor#bind) syntax with the component's parameter.</span></span>

<span data-ttu-id="c2a7a-179">Aşağıdaki `PasswordField` bileşen ( `PasswordField.razor` ):</span><span class="sxs-lookup"><span data-stu-id="c2a7a-179">The following `PasswordField` component (`PasswordField.razor`):</span></span>

* <span data-ttu-id="c2a7a-180">Bir `<input>` öğenin değerini bir `Password` özelliğe ayarlar.</span><span class="sxs-lookup"><span data-stu-id="c2a7a-180">Sets an `<input>` element's value to a `Password` property.</span></span>
* <span data-ttu-id="c2a7a-181">`Password`Özelliği ile bir üst bileşen arasındaki değişiklikleri gösterir [`EventCallback`](xref:blazor/components/event-handling#eventcallback) .</span><span class="sxs-lookup"><span data-stu-id="c2a7a-181">Exposes changes of the `Password` property to a parent component with an [`EventCallback`](xref:blazor/components/event-handling#eventcallback).</span></span>
* <span data-ttu-id="c2a7a-182">`onclick`Yöntemini tetiklemek için olayını kullanır `ToggleShowPassword` .</span><span class="sxs-lookup"><span data-stu-id="c2a7a-182">Uses the `onclick` event to trigger the `ToggleShowPassword` method.</span></span> <span data-ttu-id="c2a7a-183">Daha fazla bilgi için bkz. <xref:blazor/components/event-handling>.</span><span class="sxs-lookup"><span data-stu-id="c2a7a-183">For more information, see <xref:blazor/components/event-handling>.</span></span>

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

<span data-ttu-id="c2a7a-184">`PasswordField`Bileşen başka bir bileşende kullanılır:</span><span class="sxs-lookup"><span data-stu-id="c2a7a-184">The `PasswordField` component is used in another component:</span></span>

```razor
@page "/ParentComponent"

<h1>Parent Component</h1>

<PasswordField @bind-Password="password" />

@code {
    private string password;
}
```

<span data-ttu-id="c2a7a-185">Önceki örnekteki parolada denetim veya tuzak hataları gerçekleştirmek için:</span><span class="sxs-lookup"><span data-stu-id="c2a7a-185">To perform checks or trap errors on the password in the preceding example:</span></span>

* <span data-ttu-id="c2a7a-186">İçin bir yedekleme alanı oluşturun `Password` ( `password` Aşağıdaki örnek kodda).</span><span class="sxs-lookup"><span data-stu-id="c2a7a-186">Create a backing field for `Password` (`password` in the following example code).</span></span>
* <span data-ttu-id="c2a7a-187">Ayarlayıcıdaki denetimleri veya yakalama hatalarını gerçekleştirin `Password` .</span><span class="sxs-lookup"><span data-stu-id="c2a7a-187">Perform the checks or trap errors in the `Password` setter.</span></span>

<span data-ttu-id="c2a7a-188">Aşağıdaki örnek, parolanın değerinde bir boşluk kullanılmışsa kullanıcıya anında geri bildirim sağlar:</span><span class="sxs-lookup"><span data-stu-id="c2a7a-188">The following example provides immediate feedback to the user if a space is used in the password's value:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="c2a7a-189">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="c2a7a-189">Additional resources</span></span>

* [<span data-ttu-id="c2a7a-190">Bir formda radyo düğmelerine bağlama</span><span class="sxs-lookup"><span data-stu-id="c2a7a-190">Binding to radio buttons in a form</span></span>](xref:blazor/forms-validation#radio-buttons)
* [<span data-ttu-id="c2a7a-191">`<select>`Form Içindeki C# nesne değerlerine öğe seçenekleri bağlama `null`</span><span class="sxs-lookup"><span data-stu-id="c2a7a-191">Binding `<select>` element options to C# object `null` values in a form</span></span>](xref:blazor/forms-validation#binding-select-element-options-to-c-object-null-values)
