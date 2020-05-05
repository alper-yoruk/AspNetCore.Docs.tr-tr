---
title: ASP.NET Core Blazor veri bağlama
author: guardrex
description: Blazor Uygulamalardaki BILEŞENLER ve DOM öğeleri için veri bağlama özellikleri hakkında bilgi edinin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/26/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/data-binding
ms.openlocfilehash: 73e73869d58e4a22e9dbee059f69fd15605ce2ce
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82767557"
---
# <a name="aspnet-core-blazor-data-binding"></a><span data-ttu-id="1abe1-103">ASP.NET Core Blazor veri bağlama</span><span class="sxs-lookup"><span data-stu-id="1abe1-103">ASP.NET Core Blazor data binding</span></span>

<span data-ttu-id="1abe1-104">, [Luke Latham](https://github.com/guardrex) ve [Daniel Roth](https://github.com/danroth27) tarafından</span><span class="sxs-lookup"><span data-stu-id="1abe1-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

Razor<span data-ttu-id="1abe1-105">bileşenler, bir alan, özellik veya [`@bind`](xref:mvc/views/razor#bind) Razor ifade DEĞERI ile adlandırılmış bir HTML öğesi özniteliği aracılığıyla veri bağlama özellikleri sağlar.</span><span class="sxs-lookup"><span data-stu-id="1abe1-105"> components provide data binding features via an HTML element attribute named [`@bind`](xref:mvc/views/razor#bind) with a field, property, or Razor expression value.</span></span>

<span data-ttu-id="1abe1-106">Aşağıdaki örnek, `CurrentValue` özelliğini metin kutusu değerine bağlar:</span><span class="sxs-lookup"><span data-stu-id="1abe1-106">The following example binds the `CurrentValue` property to the text box's value:</span></span>

```razor
<input @bind="CurrentValue" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="1abe1-107">Metin kutusu odağı kaybettiğinde, özelliğin değeri güncellenir.</span><span class="sxs-lookup"><span data-stu-id="1abe1-107">When the text box loses focus, the property's value is updated.</span></span>

<span data-ttu-id="1abe1-108">Metin kutusu kullanıcı arabiriminde, özelliğin değerini değiştirme yanıt olarak değil, yalnızca bileşen işlendiğinde güncelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="1abe1-108">The text box is updated in the UI only when the component is rendered, not in response to changing the property's value.</span></span> <span data-ttu-id="1abe1-109">Bileşenler olay işleyicisi kodu yürütüldükten sonra kendilerini oluşturduğundan, özellik güncelleştirmeleri *genellikle* olay işleyicisi tetiklendikten hemen sonra Kullanıcı arabirimine yansıtılır.</span><span class="sxs-lookup"><span data-stu-id="1abe1-109">Since components render themselves after event handler code executes, property updates are *usually* reflected in the UI immediately after an event handler is triggered.</span></span>

<span data-ttu-id="1abe1-110">`CurrentValue` Özelliği (`<input @bind="CurrentValue" />`) ile kullanmak `@bind` , temelde aşağıdakilere eşdeğerdir:</span><span class="sxs-lookup"><span data-stu-id="1abe1-110">Using `@bind` with the `CurrentValue` property (`<input @bind="CurrentValue" />`) is essentially equivalent to the following:</span></span>

```razor
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = 
        __e.Value.ToString())" />
        
@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="1abe1-111">Bileşen işlendiğinde, `value` giriş öğesi `CurrentValue` özelliğinden gelir.</span><span class="sxs-lookup"><span data-stu-id="1abe1-111">When the component is rendered, the `value` of the input element comes from the `CurrentValue` property.</span></span> <span data-ttu-id="1abe1-112">Kullanıcı metin kutusuna yazdığında ve öğe odağını değiştirdiğinde, `onchange` olay tetiklenir ve `CurrentValue` Özellik değiştirilen değere ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="1abe1-112">When the user types in the text box and changes element focus, the `onchange` event is fired and the `CurrentValue` property is set to the changed value.</span></span> <span data-ttu-id="1abe1-113">Tür dönüştürmelerinde oluşan durumları işletiğinden `@bind` , gerçekte kod oluşturma daha karmaşıktır.</span><span class="sxs-lookup"><span data-stu-id="1abe1-113">In reality, the code generation is more complex because `@bind` handles cases where type conversions are performed.</span></span> <span data-ttu-id="1abe1-114">İlke ' de `@bind` , bir ifadenin geçerli değerini bir `value` özniteliğiyle ilişkilendirir ve kayıtlı işleyiciyi kullanarak değişiklikleri işler.</span><span class="sxs-lookup"><span data-stu-id="1abe1-114">In principle, `@bind` associates the current value of an expression with a `value` attribute and handles changes using the registered handler.</span></span>

<span data-ttu-id="1abe1-115">`event` Parametre içeren bir `@bind:event` özniteliği de ekleyerek diğer olaylardaki bir özelliği veya alanı bağlayın.</span><span class="sxs-lookup"><span data-stu-id="1abe1-115">Bind a property or field on other events by also including an `@bind:event` attribute with an `event` parameter.</span></span> <span data-ttu-id="1abe1-116">Aşağıdaki örnek, `CurrentValue` `oninput` olayında özelliği bağlar:</span><span class="sxs-lookup"><span data-stu-id="1abe1-116">The following example binds the `CurrentValue` property on the `oninput` event:</span></span>

```razor
<input @bind="CurrentValue" @bind:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="1abe1-117">' `onchange`In aksine, öğe odağı kaybettiğinde harekete geçirilir, `oninput` metin kutusunun değeri değiştiğinde harekete geçirilir.</span><span class="sxs-lookup"><span data-stu-id="1abe1-117">Unlike `onchange`, which fires when the element loses focus, `oninput` fires when the value of the text box changes.</span></span>

<span data-ttu-id="1abe1-118">Dışındaki `@bind-{ATTRIBUTE}` `value`öğe `@bind-{ATTRIBUTE}:event` özniteliklerini bağlamak için sözdizimi ile kullanın.</span><span class="sxs-lookup"><span data-stu-id="1abe1-118">Use `@bind-{ATTRIBUTE}` with `@bind-{ATTRIBUTE}:event` syntax to bind element attributes other than `value`.</span></span> <span data-ttu-id="1abe1-119">Aşağıdaki örnekte, paragrafın stili `_paragraphStyle` değer değiştiğinde güncelleştirilir:</span><span class="sxs-lookup"><span data-stu-id="1abe1-119">In the following example, the paragraph's style is updated when the `_paragraphStyle` value changes:</span></span>

```razor
@page "/binding-example"

<p>
    <input type="text" @bind="_paragraphStyle" />
</p>

<p @bind-style="_paragraphStyle" @bind-style:event="onchange">
    Blazorify the app!
</p>

@code {
    private string _paragraphStyle = "color:red";
}
```

<span data-ttu-id="1abe1-120">Öznitelik bağlama büyük/küçük harfe duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="1abe1-120">Attribute binding is case sensitive.</span></span> <span data-ttu-id="1abe1-121">Örneğin, `@bind` geçerlidir ve `@Bind` geçersizdir.</span><span class="sxs-lookup"><span data-stu-id="1abe1-121">For example, `@bind` is valid, and `@Bind` is invalid.</span></span>

## <a name="unparsable-values"></a><span data-ttu-id="1abe1-122">Ayrıştırılamayan değerler</span><span class="sxs-lookup"><span data-stu-id="1abe1-122">Unparsable values</span></span>

<span data-ttu-id="1abe1-123">Bir Kullanıcı, bir veri sınırlama öğesine ayrıştırılamayan bir değer sağlıyorsa, bağlama olayı tetiklendiğinde, çözümlenemeyen değer otomatik olarak önceki değerine döndürülür.</span><span class="sxs-lookup"><span data-stu-id="1abe1-123">When a user provides an unparsable value to a databound element, the unparsable value is automatically reverted to its previous value when the bind event is triggered.</span></span>

<span data-ttu-id="1abe1-124">Şu senaryoyu göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="1abe1-124">Consider the following scenario:</span></span>

* <span data-ttu-id="1abe1-125">Bir `<input>` öğesi, başlangıç değeri olan `int` bir türe bağlanır `123`:</span><span class="sxs-lookup"><span data-stu-id="1abe1-125">An `<input>` element is bound to an `int` type with an initial value of `123`:</span></span>

  ```razor
  <input @bind="MyProperty" />

  @code {
      [Parameter]
      public int MyProperty { get; set; } = 123;
  }
  ```
* <span data-ttu-id="1abe1-126">Kullanıcı, öğesinin değerini sayfada olarak `123.45` güncelleştirir ve öğe odağını değiştirir.</span><span class="sxs-lookup"><span data-stu-id="1abe1-126">The user updates the value of the element to `123.45` in the page and changes the element focus.</span></span>

<span data-ttu-id="1abe1-127">Önceki senaryoda, öğenin değeri öğesine `123`geri döndürülür.</span><span class="sxs-lookup"><span data-stu-id="1abe1-127">In the preceding scenario, the element's value is reverted to `123`.</span></span> <span data-ttu-id="1abe1-128">Değeri `123.45` özgün değeri yararına reddedildiğinde `123`, Kullanıcı değerinin kabul edilmediğini anlamıştır.</span><span class="sxs-lookup"><span data-stu-id="1abe1-128">When the value `123.45` is rejected in favor of the original value of `123`, the user understands that their value wasn't accepted.</span></span>

<span data-ttu-id="1abe1-129">Varsayılan olarak, bağlama öğenin `onchange` olayına (`@bind="{PROPERTY OR FIELD}"`) uygulanır.</span><span class="sxs-lookup"><span data-stu-id="1abe1-129">By default, binding applies to the element's `onchange` event (`@bind="{PROPERTY OR FIELD}"`).</span></span> <span data-ttu-id="1abe1-130">Farklı `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` bir olayda bağlamayı tetiklemek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="1abe1-130">Use `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` to trigger binding on a different event.</span></span> <span data-ttu-id="1abe1-131">`oninput` Event (`@bind:event="oninput"`) için yeniden sürüm, ayrıştırılamayan bir değer sunan herhangi bir tuş vuruşu sonrasında oluşur.</span><span class="sxs-lookup"><span data-stu-id="1abe1-131">For the `oninput` event (`@bind:event="oninput"`), the reversion occurs after any keystroke that introduces an unparsable value.</span></span> <span data-ttu-id="1abe1-132">`oninput` Olayı, ilişkili bir `int`tür ile hedeflerken, bir kullanıcının bir `.` karakter yazmasının engellenmiş olması engellenir.</span><span class="sxs-lookup"><span data-stu-id="1abe1-132">When targeting the `oninput` event with an `int`-bound type, a user is prevented from typing a `.` character.</span></span> <span data-ttu-id="1abe1-133">Bir `.` karakter hemen kaldırılır, bu nedenle Kullanıcı yalnızca tam sayılara izin verilen anında geri bildirim alır.</span><span class="sxs-lookup"><span data-stu-id="1abe1-133">A `.` character is immediately removed, so the user receives immediate feedback that only whole numbers are permitted.</span></span> <span data-ttu-id="1abe1-134">`oninput` Olay üzerindeki değerin geri döndürülmesi ideal değil (örneğin, kullanıcının ayrıştırılamayan `<input>` bir değeri temizlemeye izin verilmesi gerektiği durumlarda).</span><span class="sxs-lookup"><span data-stu-id="1abe1-134">There are scenarios where reverting the value on the `oninput` event isn't ideal, such as when the user should be allowed to clear an unparsable `<input>` value.</span></span> <span data-ttu-id="1abe1-135">Alternatifler şunlardır:</span><span class="sxs-lookup"><span data-stu-id="1abe1-135">Alternatives include:</span></span>

* <span data-ttu-id="1abe1-136">`oninput` Olayı kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="1abe1-136">Don't use the `oninput` event.</span></span> <span data-ttu-id="1abe1-137">Varsayılan `onchange` olayı kullanın (yalnızca belirtin `@bind="{PROPERTY OR FIELD}"`); burada, öğe odağı kaybetene kadar geçersiz bir değer geri döndürülemez.</span><span class="sxs-lookup"><span data-stu-id="1abe1-137">Use the default `onchange` event (only specify `@bind="{PROPERTY OR FIELD}"`), where an invalid value isn't reverted until the element loses focus.</span></span>
* <span data-ttu-id="1abe1-138">`int?` Veya `string`gibi null yapılabilir bir türe bağlayın ve geçersiz girdileri işlemek için özel mantık sağlayın.</span><span class="sxs-lookup"><span data-stu-id="1abe1-138">Bind to a nullable type, such as `int?` or `string`, and provide custom logic to handle invalid entries.</span></span>
* <span data-ttu-id="1abe1-139">Veya gibi bir [form doğrulama bileşeni](xref:blazor/forms-validation) `InputDate` `InputNumber` kullanın.</span><span class="sxs-lookup"><span data-stu-id="1abe1-139">Use a [form validation component](xref:blazor/forms-validation), such as `InputNumber` or `InputDate`.</span></span> <span data-ttu-id="1abe1-140">Form doğrulama bileşenlerinde geçersiz girişleri yönetmek için yerleşik destek vardır.</span><span class="sxs-lookup"><span data-stu-id="1abe1-140">Form validation components have built-in support to manage invalid inputs.</span></span> <span data-ttu-id="1abe1-141">Form doğrulama bileşenleri:</span><span class="sxs-lookup"><span data-stu-id="1abe1-141">Form validation components:</span></span>
  * <span data-ttu-id="1abe1-142">Kullanıcının geçersiz giriş sağlamasına ve ilişkili `EditContext`doğrulama hatalarını almasına izin verin.</span><span class="sxs-lookup"><span data-stu-id="1abe1-142">Permit the user to provide invalid input and receive validation errors on the associated `EditContext`.</span></span>
  * <span data-ttu-id="1abe1-143">Kullanıcı ek WebForm verisi girmeye uğramadan doğrulama hatalarını Kullanıcı ARABIRIMINDE görüntüleyin.</span><span class="sxs-lookup"><span data-stu-id="1abe1-143">Display validation errors in the UI without interfering with the user entering additional webform data.</span></span>

## <a name="format-strings"></a><span data-ttu-id="1abe1-144">Biçim dizeleri</span><span class="sxs-lookup"><span data-stu-id="1abe1-144">Format strings</span></span>

<span data-ttu-id="1abe1-145">Veri bağlama, kullanılarak <xref:System.DateTime> [`@bind:format`](xref:mvc/views/razor#bind)biçim dizeleriyle birlikte kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="1abe1-145">Data binding works with <xref:System.DateTime> format strings using [`@bind:format`](xref:mvc/views/razor#bind).</span></span> <span data-ttu-id="1abe1-146">Para birimi veya sayı biçimleri gibi diğer biçim ifadeleri şu anda kullanılamaz.</span><span class="sxs-lookup"><span data-stu-id="1abe1-146">Other format expressions, such as currency or number formats, aren't available at this time.</span></span>

```razor
<input @bind="StartDate" @bind:format="yyyy-MM-dd" />

@code {
    [Parameter]
    public DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

<span data-ttu-id="1abe1-147">Yukarıdaki kodda, `<input>` öğesinin alan türü (`type`) varsayılan olarak `text`olur.</span><span class="sxs-lookup"><span data-stu-id="1abe1-147">In the preceding code, the `<input>` element's field type (`type`) defaults to `text`.</span></span> <span data-ttu-id="1abe1-148">`@bind:format`, aşağıdaki .NET türlerini bağlamak için desteklenir:</span><span class="sxs-lookup"><span data-stu-id="1abe1-148">`@bind:format` is supported for binding the following .NET types:</span></span>

* <xref:System.DateTime?displayProperty=fullName>
* <span data-ttu-id="1abe1-149"><xref:System.DateTime?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="1abe1-149"><xref:System.DateTime?displayProperty=fullName>?</span></span>
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <span data-ttu-id="1abe1-150"><xref:System.DateTimeOffset?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="1abe1-150"><xref:System.DateTimeOffset?displayProperty=fullName>?</span></span>

<span data-ttu-id="1abe1-151">`@bind:format` `value` Özniteliği `<input>` öğesi için uygulanacak tarih biçimini belirtir.</span><span class="sxs-lookup"><span data-stu-id="1abe1-151">The `@bind:format` attribute specifies the date format to apply to the `value` of the `<input>` element.</span></span> <span data-ttu-id="1abe1-152">Biçim Ayrıca bir `onchange` olay gerçekleştiğinde değeri ayrıştırmak için de kullanılır.</span><span class="sxs-lookup"><span data-stu-id="1abe1-152">The format is also used to parse the value when an `onchange` event occurs.</span></span>

<span data-ttu-id="1abe1-153">Tarihleri biçimlendirmek için yerleşik destek `date` içerdiğinden Blazor , alan türü için bir biçim belirtmenin kullanılması önerilmez.</span><span class="sxs-lookup"><span data-stu-id="1abe1-153">Specifying a format for the `date` field type isn't recommended because Blazor has built-in support to format dates.</span></span> <span data-ttu-id="1abe1-154">Önerinin artma içinde, `yyyy-MM-dd` `date` alan türüyle bir biçim sağlanırsa, bağlama için yalnızca tarih biçimini kullanın:</span><span class="sxs-lookup"><span data-stu-id="1abe1-154">In spite of the recommendation, only use the `yyyy-MM-dd` date format for binding to work correctly if a format is supplied with the `date` field type:</span></span>

```razor
<input type="date" @bind="StartDate" @bind:format="yyyy-MM-dd">
```

## <a name="parent-to-child-binding-with-component-parameters"></a><span data-ttu-id="1abe1-155">Bileşen parametreleriyle üst-alt öğe bağlama</span><span class="sxs-lookup"><span data-stu-id="1abe1-155">Parent-to-child binding with component parameters</span></span>

<span data-ttu-id="1abe1-156">Bağlama, bir üst bileşenden bir `@bind-{PROPERTY}` özellik değerini alt bileşene doğru bir şekilde bağlayabileceği bileşen parametrelerini tanır.</span><span class="sxs-lookup"><span data-stu-id="1abe1-156">Binding recognizes component parameters, where `@bind-{PROPERTY}` can bind a property value from a parent component down to a child component.</span></span> <span data-ttu-id="1abe1-157">Bir alt öğeden üst öğeye bağlama, [zincirleme bağlama Ile alt-üst öğe bağlama](#child-to-parent-binding-with-chained-bind) bölümünde ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="1abe1-157">Binding from a child to a parent is covered in the [Child-to-parent binding with chained bind](#child-to-parent-binding-with-chained-bind) section.</span></span>

<span data-ttu-id="1abe1-158">Aşağıdaki alt bileşende (`ChildComponent`) bir `Year` bileşen parametresi ve `YearChanged` geri çağırması vardır:</span><span class="sxs-lookup"><span data-stu-id="1abe1-158">The following child component (`ChildComponent`) has a `Year` component parameter and `YearChanged` callback:</span></span>

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

<span data-ttu-id="1abe1-159">`EventCallback<T>`, bölümünde <xref:blazor/event-handling#eventcallback>açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="1abe1-159">`EventCallback<T>` is explained in <xref:blazor/event-handling#eventcallback>.</span></span>

<span data-ttu-id="1abe1-160">Aşağıdaki üst bileşen şunları kullanır:</span><span class="sxs-lookup"><span data-stu-id="1abe1-160">The following parent component uses:</span></span>

* <span data-ttu-id="1abe1-161">`ChildComponent`ve `ParentYear` parametresini üst bileşenden alt bileşenin `Year` parametresine bağlar.</span><span class="sxs-lookup"><span data-stu-id="1abe1-161">`ChildComponent` and binds the `ParentYear` parameter from the parent to the `Year` parameter on the child component.</span></span>
* <span data-ttu-id="1abe1-162">`onclick` Olay, `ChangeTheYear` yöntemi tetiklemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="1abe1-162">The `onclick` event is used to trigger the `ChangeTheYear` method.</span></span> <span data-ttu-id="1abe1-163">Daha fazla bilgi için bkz. <xref:blazor/event-handling>.</span><span class="sxs-lookup"><span data-stu-id="1abe1-163">For more information, see <xref:blazor/event-handling>.</span></span>

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

<span data-ttu-id="1abe1-164">Yüklemesi aşağıdaki `ParentComponent` biçimlendirmeyi üretir:</span><span class="sxs-lookup"><span data-stu-id="1abe1-164">Loading the `ParentComponent` produces the following markup:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

<span data-ttu-id="1abe1-165">`ParentYear` Özelliğin değeri, `ParentComponent`içindeki düğme seçilerek değiştirilirse, öğesinin `Year` `ChildComponent` özelliği güncellenir.</span><span class="sxs-lookup"><span data-stu-id="1abe1-165">If the value of the `ParentYear` property is changed by selecting the button in the `ParentComponent`, the `Year` property of the `ChildComponent` is updated.</span></span> <span data-ttu-id="1abe1-166">Yeni değeri `Year` , `ParentComponent` yeniden kullanıldığında kullanıcı arabiriminde işlenir:</span><span class="sxs-lookup"><span data-stu-id="1abe1-166">The new value of `Year` is rendered in the UI when the `ParentComponent` is rerendered:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

<span data-ttu-id="1abe1-167">Parametrenin `Year` türüyle `YearChanged` `Year` eşleşen bir yardımcı olayı olduğundan parametre bağlanabilir.</span><span class="sxs-lookup"><span data-stu-id="1abe1-167">The `Year` parameter is bindable because it has a companion `YearChanged` event that matches the type of the `Year` parameter.</span></span>

<span data-ttu-id="1abe1-168">Kurala göre, `<ChildComponent @bind-Year="ParentYear" />` temelde yazmaya eşdeğerdir:</span><span class="sxs-lookup"><span data-stu-id="1abe1-168">By convention, `<ChildComponent @bind-Year="ParentYear" />` is essentially equivalent to writing:</span></span>

```razor
<ChildComponent @bind-Year="ParentYear" @bind-Year:event="YearChanged" />
```

<span data-ttu-id="1abe1-169">Genel olarak, bir özellik bir `@bind-{PROPRETY}:event` özniteliği eklenerek ilgili olay işleyicisine bağlanabilir.</span><span class="sxs-lookup"><span data-stu-id="1abe1-169">In general, a property can be bound to a corresponding event handler by including an `@bind-{PROPRETY}:event` attribute.</span></span> <span data-ttu-id="1abe1-170">Örneğin, özelliği `MyProp` aşağıdaki iki öznitelik `MyEventHandler` kullanılarak bağlanabilir:</span><span class="sxs-lookup"><span data-stu-id="1abe1-170">For example, the property `MyProp` can be bound to `MyEventHandler` using the following two attributes:</span></span>

```razor
<MyComponent @bind-MyProp="MyValue" @bind-MyProp:event="MyEventHandler" />
```

## <a name="child-to-parent-binding-with-chained-bind"></a><span data-ttu-id="1abe1-171">Zincirli bağlama ile üstten üst öğe bağlama</span><span class="sxs-lookup"><span data-stu-id="1abe1-171">Child-to-parent binding with chained bind</span></span>

<span data-ttu-id="1abe1-172">Yaygın bir senaryo, bir veri bağlama parametresini bileşen çıkışında bir sayfa öğesine zincirlemesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="1abe1-172">A common scenario is chaining a data-bound parameter to a page element in the component's output.</span></span> <span data-ttu-id="1abe1-173">Birden çok bağlama düzeyi aynı anda gerçekleştiğinden, bu senaryoya *zincirleme bağlama* denir.</span><span class="sxs-lookup"><span data-stu-id="1abe1-173">This scenario is called a *chained bind* because multiple levels of binding occur simultaneously.</span></span>

<span data-ttu-id="1abe1-174">Bir zincir bağlama, sayfanın öğesinde sözdizimi `@bind` ile uygulanamaz.</span><span class="sxs-lookup"><span data-stu-id="1abe1-174">A chained bind can't be implemented with `@bind` syntax in the page's element.</span></span> <span data-ttu-id="1abe1-175">Olay işleyicisi ve değeri ayrı olarak belirtilmelidir.</span><span class="sxs-lookup"><span data-stu-id="1abe1-175">The event handler and value must be specified separately.</span></span> <span data-ttu-id="1abe1-176">Ancak, bir üst bileşen, bir sözdizimi `@bind` bileşenin parametresiyle birlikte kullanabilir.</span><span class="sxs-lookup"><span data-stu-id="1abe1-176">A parent component, however, can use `@bind` syntax with the component's parameter.</span></span>

<span data-ttu-id="1abe1-177">Aşağıdaki `PasswordField` bileşen (*passwordfield. Razor*):</span><span class="sxs-lookup"><span data-stu-id="1abe1-177">The following `PasswordField` component (*PasswordField.razor*):</span></span>

* <span data-ttu-id="1abe1-178">Bir `<input>` öğenin değerini bir `Password` özelliğe ayarlar.</span><span class="sxs-lookup"><span data-stu-id="1abe1-178">Sets an `<input>` element's value to a `Password` property.</span></span>
* <span data-ttu-id="1abe1-179">`Password` Özellik değişikliklerini bir [eventcallback](xref:blazor/event-handling#eventcallback)ile üst bileşene gösterir.</span><span class="sxs-lookup"><span data-stu-id="1abe1-179">Exposes changes of the `Password` property to a parent component with an [EventCallback](xref:blazor/event-handling#eventcallback).</span></span>
* <span data-ttu-id="1abe1-180">, `ToggleShowPassword` Yöntemini `onclick` tetiklemek için kullanılan olayını kullanır.</span><span class="sxs-lookup"><span data-stu-id="1abe1-180">Uses the `onclick` event is used to trigger the `ToggleShowPassword` method.</span></span> <span data-ttu-id="1abe1-181">Daha fazla bilgi için bkz. <xref:blazor/event-handling>.</span><span class="sxs-lookup"><span data-stu-id="1abe1-181">For more information, see <xref:blazor/event-handling>.</span></span>

```razor
<h1>Child Component</h1>

Password: 

<input @oninput="OnPasswordChanged" 
       required 
       type="@(_showPassword ? "text" : "password")" 
       value="@Password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

@code {
    private bool _showPassword;

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
        _showPassword = !_showPassword;
    }
}
```

<span data-ttu-id="1abe1-182">`PasswordField` Bileşen başka bir bileşende kullanılır:</span><span class="sxs-lookup"><span data-stu-id="1abe1-182">The `PasswordField` component is used in another component:</span></span>

```razor
@page "/ParentComponent"

<h1>Parent Component</h1>

<PasswordField @bind-Password="_password" />

@code {
    private string _password;
}
```

<span data-ttu-id="1abe1-183">Önceki örnekteki parolada denetim veya tuzak hataları gerçekleştirmek için:</span><span class="sxs-lookup"><span data-stu-id="1abe1-183">To perform checks or trap errors on the password in the preceding example:</span></span>

* <span data-ttu-id="1abe1-184">İçin `Password` bir yedekleme alanı oluşturun (`_password` aşağıdaki örnek kodda).</span><span class="sxs-lookup"><span data-stu-id="1abe1-184">Create a backing field for `Password` (`_password` in the following example code).</span></span>
* <span data-ttu-id="1abe1-185">`Password` Ayarlayıcıdaki denetimleri veya yakalama hatalarını gerçekleştirin.</span><span class="sxs-lookup"><span data-stu-id="1abe1-185">Perform the checks or trap errors in the `Password` setter.</span></span>

<span data-ttu-id="1abe1-186">Aşağıdaki örnek, parolanın değerinde bir boşluk kullanılmışsa kullanıcıya anında geri bildirim sağlar:</span><span class="sxs-lookup"><span data-stu-id="1abe1-186">The following example provides immediate feedback to the user if a space is used in the password's value:</span></span>

```razor
<h1>Child Component</h1>

Password: 

<input @oninput="OnPasswordChanged" 
       required 
       type="@(_showPassword ? "text" : "password")" 
       value="@Password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

<span class="text-danger">@_validationMessage</span>

@code {
    private bool _showPassword;
    private string _password;
    private string _validationMessage;

    [Parameter]
    public string Password
    {
        get { return _password ?? string.Empty; }
        set
        {
            if (_password != value)
            {
                if (value.Contains(' '))
                {
                    _validationMessage = "Spaces not allowed!";
                }
                else
                {
                    _password = value;
                    _validationMessage = string.Empty;
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
        _showPassword = !_showPassword;
    }
}
```

## <a name="radio-buttons"></a><span data-ttu-id="1abe1-187">Radyo düğmeleri</span><span class="sxs-lookup"><span data-stu-id="1abe1-187">Radio buttons</span></span>

<span data-ttu-id="1abe1-188">Bir form içindeki radyo düğmelerine bağlama hakkında bilgi için bkz <xref:blazor/forms-validation#work-with-radio-buttons>..</span><span class="sxs-lookup"><span data-stu-id="1abe1-188">For information on binding to radio buttons in a form, see <xref:blazor/forms-validation#work-with-radio-buttons>.</span></span>
