---
title: ASP.NET Blazor Çekirdek veri bağlama
author: guardrex
description: Uygulamalardaki Blazor bileşenler ve DOM öğeleri için veri bağlama özellikleri hakkında bilgi edinin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/26/2020
no-loc:
- Blazor
- SignalR
uid: blazor/data-binding
ms.openlocfilehash: a7b3730dad48b5bbb6134dab181051da4e3651b4
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80320958"
---
# <a name="aspnet-core-opno-locblazor-data-binding"></a><span data-ttu-id="6df00-103">ASP.NET Blazor Çekirdek veri bağlama</span><span class="sxs-lookup"><span data-stu-id="6df00-103">ASP.NET Core Blazor data binding</span></span>

<span data-ttu-id="6df00-104">Yazar: [Luke Latham](https://github.com/guardrex) ve [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="6df00-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="6df00-105">Razor bileşenleri, alan, özellik veya Jilet ifade değeriyle birlikte adlı [`@bind`](xref:mvc/views/razor#bind) bir HTML öğesi özniteliği aracılığıyla veri bağlama özellikleri sağlar.</span><span class="sxs-lookup"><span data-stu-id="6df00-105">Razor components provide data binding features via an HTML element attribute named [`@bind`](xref:mvc/views/razor#bind) with a field, property, or Razor expression value.</span></span>

<span data-ttu-id="6df00-106">Aşağıdaki örnek özelliği metin `CurrentValue` kutusunun değerine bağlar:</span><span class="sxs-lookup"><span data-stu-id="6df00-106">The following example binds the `CurrentValue` property to the text box's value:</span></span>

```razor
<input @bind="CurrentValue" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="6df00-107">Metin kutusu odağı kaybettiğinde, özelliğin değeri güncelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="6df00-107">When the text box loses focus, the property's value is updated.</span></span>

<span data-ttu-id="6df00-108">Metin kutusu, yalnızca bileşen işlendiğinde, özelliğin değerini değiştirmeye yanıt olarak değil, Kullanıcı Arası Bilgi Birimi'nde güncelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="6df00-108">The text box is updated in the UI only when the component is rendered, not in response to changing the property's value.</span></span> <span data-ttu-id="6df00-109">Bileşenler olay işleyicisi kodu yürütülmeden sonra kendilerini işlediğinden, özellik güncelleştirmeleri *genellikle* bir olay işleyicisi tetiklendikten hemen sonra UI'ye yansıtılır.</span><span class="sxs-lookup"><span data-stu-id="6df00-109">Since components render themselves after event handler code executes, property updates are *usually* reflected in the UI immediately after an event handler is triggered.</span></span>

<span data-ttu-id="6df00-110">Özellik ile birlikte kullanmak `@bind` aslında aşağıdakilere eşdeğerdir:`<input @bind="CurrentValue" />` `CurrentValue`</span><span class="sxs-lookup"><span data-stu-id="6df00-110">Using `@bind` with the `CurrentValue` property (`<input @bind="CurrentValue" />`) is essentially equivalent to the following:</span></span>

```razor
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = 
        __e.Value.ToString())" />
        
@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="6df00-111">Bileşen işlendiğinde, giriş `value` öğesinin `CurrentValue` özelliği nden gelir.</span><span class="sxs-lookup"><span data-stu-id="6df00-111">When the component is rendered, the `value` of the input element comes from the `CurrentValue` property.</span></span> <span data-ttu-id="6df00-112">Kullanıcı metin kutusunda ki öğe odağında yazıp `onchange` değiştirdiğinde, `CurrentValue` olay ateşlenir ve özellik değiştirilen değere ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="6df00-112">When the user types in the text box and changes element focus, the `onchange` event is fired and the `CurrentValue` property is set to the changed value.</span></span> <span data-ttu-id="6df00-113">Gerçekte, tür dönüşümlerinin gerçekleştirildiği `@bind` durumları işlediği için kod oluşturma daha karmaşıktır.</span><span class="sxs-lookup"><span data-stu-id="6df00-113">In reality, the code generation is more complex because `@bind` handles cases where type conversions are performed.</span></span> <span data-ttu-id="6df00-114">Prensip `@bind` olarak, bir ifadenin geçerli `value` değerini bir öznitelikle ilişkilendirer ve değişiklikleri kayıtlı işleyiciyi kullanarak işler.</span><span class="sxs-lookup"><span data-stu-id="6df00-114">In principle, `@bind` associates the current value of an expression with a `value` attribute and handles changes using the registered handler.</span></span>

<span data-ttu-id="6df00-115">`event` Parametreli bir `@bind:event` öznitelik de ekleyerek bir özelliği veya alanı diğer olaylara bağla.</span><span class="sxs-lookup"><span data-stu-id="6df00-115">Bind a property or field on other events by also including an `@bind:event` attribute with an `event` parameter.</span></span> <span data-ttu-id="6df00-116">Aşağıdaki örnek `CurrentValue` `oninput` olay özelliği bağlar:</span><span class="sxs-lookup"><span data-stu-id="6df00-116">The following example binds the `CurrentValue` property on the `oninput` event:</span></span>

```razor
<input @bind="CurrentValue" @bind:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="6df00-117">Öğe `onchange`odağı kaybettiğinde ateşlenen, `oninput` metin kutusunun değeri değiştiğinde çıkanın aksine.</span><span class="sxs-lookup"><span data-stu-id="6df00-117">Unlike `onchange`, which fires when the element loses focus, `oninput` fires when the value of the text box changes.</span></span>

<span data-ttu-id="6df00-118">Öğe `@bind-{ATTRIBUTE}` `@bind-{ATTRIBUTE}:event` özniteliklerini başka bir öğe `value`öznitelikleribağlamak için sözdizimi ile kullanın.</span><span class="sxs-lookup"><span data-stu-id="6df00-118">Use `@bind-{ATTRIBUTE}` with `@bind-{ATTRIBUTE}:event` syntax to bind element attributes other than `value`.</span></span> <span data-ttu-id="6df00-119">Aşağıdaki örnekte, `_paragraphStyle` değer değiştiğinde paragrafın stili güncelleştirilir:</span><span class="sxs-lookup"><span data-stu-id="6df00-119">In the following example, the paragraph's style is updated when the `_paragraphStyle` value changes:</span></span>

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

<span data-ttu-id="6df00-120">Öznitelik bağlama büyük/küçük harf duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="6df00-120">Attribute binding is case sensitive.</span></span> <span data-ttu-id="6df00-121">Örneğin, `@bind` geçerli dir `@Bind` ve geçersizdir.</span><span class="sxs-lookup"><span data-stu-id="6df00-121">For example, `@bind` is valid, and `@Bind` is invalid.</span></span>

## <a name="unparsable-values"></a><span data-ttu-id="6df00-122">Parlanamaz değerler</span><span class="sxs-lookup"><span data-stu-id="6df00-122">Unparsable values</span></span>

<span data-ttu-id="6df00-123">Bir kullanıcı veri yle dolu bir öğeye ayrı ayrı değer verdiğinde, bağlama olayı tetiklendiğinde, ayrıştırılamaz değer otomatik olarak önceki değerine geri döndürülür.</span><span class="sxs-lookup"><span data-stu-id="6df00-123">When a user provides an unparsable value to a databound element, the unparsable value is automatically reverted to its previous value when the bind event is triggered.</span></span>

<span data-ttu-id="6df00-124">Şu senaryoyu göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="6df00-124">Consider the following scenario:</span></span>

* <span data-ttu-id="6df00-125">Bir `<input>` öğe, başlangıç `int` değeri olan bir `123`türe bağlıdır:</span><span class="sxs-lookup"><span data-stu-id="6df00-125">An `<input>` element is bound to an `int` type with an initial value of `123`:</span></span>

  ```razor
  <input @bind="MyProperty" />

  @code {
      [Parameter]
      public int MyProperty { get; set; } = 123;
  }
  ```
* <span data-ttu-id="6df00-126">Kullanıcı öğenin değerini `123.45` sayfaya güncelleştirir ve öğe odağı değişir.</span><span class="sxs-lookup"><span data-stu-id="6df00-126">The user updates the value of the element to `123.45` in the page and changes the element focus.</span></span>

<span data-ttu-id="6df00-127">Önceki senaryoda, öğenin değeri `123`.</span><span class="sxs-lookup"><span data-stu-id="6df00-127">In the preceding scenario, the element's value is reverted to `123`.</span></span> <span data-ttu-id="6df00-128">Değer `123.45` orijinal değeri lehine `123`reddedildiğinde, kullanıcı değerinin kabul olmadığını anlar.</span><span class="sxs-lookup"><span data-stu-id="6df00-128">When the value `123.45` is rejected in favor of the original value of `123`, the user understands that their value wasn't accepted.</span></span>

<span data-ttu-id="6df00-129">Varsayılan olarak, bağlama öğenin `onchange` olay (`@bind="{PROPERTY OR FIELD}"`için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="6df00-129">By default, binding applies to the element's `onchange` event (`@bind="{PROPERTY OR FIELD}"`).</span></span> <span data-ttu-id="6df00-130">Farklı `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` bir olay üzerinde bağlamayı tetiklemek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="6df00-130">Use `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` to trigger binding on a different event.</span></span> <span data-ttu-id="6df00-131">`oninput` Olay için`@bind:event="oninput"`( ), reversion ayrılmaz bir değer tanıtan herhangi bir tuş vuruşundan sonra oluşur.</span><span class="sxs-lookup"><span data-stu-id="6df00-131">For the `oninput` event (`@bind:event="oninput"`), the reversion occurs after any keystroke that introduces an unparsable value.</span></span> <span data-ttu-id="6df00-132">Olayı bağlı `oninput` bir `int`türle hedeflenirken, kullanıcının karakter `.` yazması engellenir.</span><span class="sxs-lookup"><span data-stu-id="6df00-132">When targeting the `oninput` event with an `int`-bound type, a user is prevented from typing a `.` character.</span></span> <span data-ttu-id="6df00-133">Bir `.` karakter hemen kaldırılır, böylece kullanıcı yalnızca tam sayılara izin verilen anında geri bildirim alır.</span><span class="sxs-lookup"><span data-stu-id="6df00-133">A `.` character is immediately removed, so the user receives immediate feedback that only whole numbers are permitted.</span></span> <span data-ttu-id="6df00-134">`oninput` Kullanıcının ayrı ayrı bir `<input>` değeri temizlemesine izin verilmesi gerektiği gibi, olaydaki değeri geri almanın ideal olmadığı senaryolar vardır.</span><span class="sxs-lookup"><span data-stu-id="6df00-134">There are scenarios where reverting the value on the `oninput` event isn't ideal, such as when the user should be allowed to clear an unparsable `<input>` value.</span></span> <span data-ttu-id="6df00-135">Alternatifler şunlardır:</span><span class="sxs-lookup"><span data-stu-id="6df00-135">Alternatives include:</span></span>

* <span data-ttu-id="6df00-136">`oninput` Olayı kullanma.</span><span class="sxs-lookup"><span data-stu-id="6df00-136">Don't use the `oninput` event.</span></span> <span data-ttu-id="6df00-137">Öğe odağı `onchange` kaybedene `@bind="{PROPERTY OR FIELD}"`kadar geçersiz bir değerin geri döndürülmediği varsayılan olayı (yalnızca belirtin) kullanın.</span><span class="sxs-lookup"><span data-stu-id="6df00-137">Use the default `onchange` event (only specify `@bind="{PROPERTY OR FIELD}"`), where an invalid value isn't reverted until the element loses focus.</span></span>
* <span data-ttu-id="6df00-138">Geçersiz girişleri işlemek için özel `int?` mantık `string`sağlayın, örneğin, geçersiz bir türe bağlayın.</span><span class="sxs-lookup"><span data-stu-id="6df00-138">Bind to a nullable type, such as `int?` or `string`, and provide custom logic to handle invalid entries.</span></span>
* <span data-ttu-id="6df00-139">Bir [form doğrulama bileşeni](xref:blazor/forms-validation)kullanın `InputNumber` `InputDate`, gibi veya .</span><span class="sxs-lookup"><span data-stu-id="6df00-139">Use a [form validation component](xref:blazor/forms-validation), such as `InputNumber` or `InputDate`.</span></span> <span data-ttu-id="6df00-140">Form doğrulama bileşenleri, geçersiz girişleri yönetmek için yerleşik desteğe sahiptir.</span><span class="sxs-lookup"><span data-stu-id="6df00-140">Form validation components have built-in support to manage invalid inputs.</span></span> <span data-ttu-id="6df00-141">Form doğrulama bileşenleri:</span><span class="sxs-lookup"><span data-stu-id="6df00-141">Form validation components:</span></span>
  * <span data-ttu-id="6df00-142">Kullanıcının geçersiz giriş sağlamasına ve ilişkili `EditContext`üzerinde doğrulama hataları almasına izin ver.</span><span class="sxs-lookup"><span data-stu-id="6df00-142">Permit the user to provide invalid input and receive validation errors on the associated `EditContext`.</span></span>
  * <span data-ttu-id="6df00-143">Kullanıcının ek web form verileri girmesini engellemeden Kullanıcı Arama Bilgisi'ndeki doğrulama hatalarını görüntüleyin.</span><span class="sxs-lookup"><span data-stu-id="6df00-143">Display validation errors in the UI without interfering with the user entering additional webform data.</span></span>

## <a name="format-strings"></a><span data-ttu-id="6df00-144">Dizeleri biçimlendirme</span><span class="sxs-lookup"><span data-stu-id="6df00-144">Format strings</span></span>

<span data-ttu-id="6df00-145">Veri bağlama <xref:System.DateTime> kullanarak [`@bind:format`](xref:mvc/views/razor#bind)biçim dizeleri ile çalışır.</span><span class="sxs-lookup"><span data-stu-id="6df00-145">Data binding works with <xref:System.DateTime> format strings using [`@bind:format`](xref:mvc/views/razor#bind).</span></span> <span data-ttu-id="6df00-146">Para birimi veya sayı biçimleri gibi diğer biçim ifadeleri şu anda kullanılamaz.</span><span class="sxs-lookup"><span data-stu-id="6df00-146">Other format expressions, such as currency or number formats, aren't available at this time.</span></span>

```razor
<input @bind="StartDate" @bind:format="yyyy-MM-dd" />

@code {
    [Parameter]
    public DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

<span data-ttu-id="6df00-147">Önceki kodda, öğenin `<input>` alan türü`type`( ) `text`varsayılan olarak .</span><span class="sxs-lookup"><span data-stu-id="6df00-147">In the preceding code, the `<input>` element's field type (`type`) defaults to `text`.</span></span> <span data-ttu-id="6df00-148">`@bind:format`aşağıdaki .NET türlerinin bağlanması için desteklenir:</span><span class="sxs-lookup"><span data-stu-id="6df00-148">`@bind:format` is supported for binding the following .NET types:</span></span>

* <xref:System.DateTime?displayProperty=fullName>
* <span data-ttu-id="6df00-149"><xref:System.DateTime?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="6df00-149"><xref:System.DateTime?displayProperty=fullName>?</span></span>
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <span data-ttu-id="6df00-150"><xref:System.DateTimeOffset?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="6df00-150"><xref:System.DateTimeOffset?displayProperty=fullName>?</span></span>

<span data-ttu-id="6df00-151">Öznitelik, `@bind:format` `value` `<input>` öğenin uygulanması için tarih biçimini belirtir.</span><span class="sxs-lookup"><span data-stu-id="6df00-151">The `@bind:format` attribute specifies the date format to apply to the `value` of the `<input>` element.</span></span> <span data-ttu-id="6df00-152">Biçim, bir `onchange` olay oluştuğunda değeri ayrıştırmak için de kullanılır.</span><span class="sxs-lookup"><span data-stu-id="6df00-152">The format is also used to parse the value when an `onchange` event occurs.</span></span>

<span data-ttu-id="6df00-153">Tarihleri biçimlendirmek `date` için yerleşik desteği Blazor olduğundan alan türü için bir biçim belirtmek önerilmez.</span><span class="sxs-lookup"><span data-stu-id="6df00-153">Specifying a format for the `date` field type isn't recommended because Blazor has built-in support to format dates.</span></span> <span data-ttu-id="6df00-154">Öneriye rağmen, `yyyy-MM-dd` `date` alan türüyle birlikte bir biçim sağlanıyorsa, yalnızca doğru çalışması için tarih biçimini kullanın:</span><span class="sxs-lookup"><span data-stu-id="6df00-154">In spite of the recommendation, only use the `yyyy-MM-dd` date format for binding to work correctly if a format is supplied with the `date` field type:</span></span>

```razor
<input type="date" @bind="StartDate" @bind:format="yyyy-MM-dd">
```

## <a name="parent-to-child-binding-with-component-parameters"></a><span data-ttu-id="6df00-155">Bileşen parametreleri ile üst-alt bağlama</span><span class="sxs-lookup"><span data-stu-id="6df00-155">Parent-to-child binding with component parameters</span></span>

<span data-ttu-id="6df00-156">Bağlama, bir üst `@bind-{PROPERTY}` bileşenden alt bileşene bir özellik değeri bağlayabilirsiniz bileşen parametrelerini tanır.</span><span class="sxs-lookup"><span data-stu-id="6df00-156">Binding recognizes component parameters, where `@bind-{PROPERTY}` can bind a property value from a parent component down to a child component.</span></span> <span data-ttu-id="6df00-157">Bir çocuktan bir ebeveyne bağlanma, [zincirli bağlama bölümüyle çocuktan ebeveyne bağlama](#child-to-parent-binding-with-chained-bind) kapsamındadır.</span><span class="sxs-lookup"><span data-stu-id="6df00-157">Binding from a child to a parent is covered in the [Child-to-parent binding with chained bind](#child-to-parent-binding-with-chained-bind) section.</span></span>

<span data-ttu-id="6df00-158">Aşağıdaki alt bileşen`ChildComponent`( `Year` ) bir `YearChanged` bileşen parametresi ve geri arama vardır:</span><span class="sxs-lookup"><span data-stu-id="6df00-158">The following child component (`ChildComponent`) has a `Year` component parameter and `YearChanged` callback:</span></span>

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

<span data-ttu-id="6df00-159">`EventCallback<T>`olarak <xref:blazor/event-handling#eventcallback>açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="6df00-159">`EventCallback<T>` is explained in <xref:blazor/event-handling#eventcallback>.</span></span>

<span data-ttu-id="6df00-160">Aşağıdaki üst bileşen kullanır:</span><span class="sxs-lookup"><span data-stu-id="6df00-160">The following parent component uses:</span></span>

* <span data-ttu-id="6df00-161">`ChildComponent`ve parametreyi `ParentYear` alt bileşendeki `Year` üst öğeden parametreye bağlar.</span><span class="sxs-lookup"><span data-stu-id="6df00-161">`ChildComponent` and binds the `ParentYear` parameter from the parent to the `Year` parameter on the child component.</span></span>
* <span data-ttu-id="6df00-162">Olay `onclick` `ChangeTheYear` yöntemi tetiklemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="6df00-162">The `onclick` event is used to trigger the `ChangeTheYear` method.</span></span> <span data-ttu-id="6df00-163">Daha fazla bilgi için bkz. <xref:blazor/event-handling>.</span><span class="sxs-lookup"><span data-stu-id="6df00-163">For more information, see <xref:blazor/event-handling>.</span></span>

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

<span data-ttu-id="6df00-164">Yükleme `ParentComponent` aşağıdaki biçimlendirme üretir:</span><span class="sxs-lookup"><span data-stu-id="6df00-164">Loading the `ParentComponent` produces the following markup:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

<span data-ttu-id="6df00-165">Özelliğin `ParentYear` değeri `ParentComponent`değiştirilirse, düğme seçilerek , `Year` özelliği `ChildComponent` güncelleştirilir.</span><span class="sxs-lookup"><span data-stu-id="6df00-165">If the value of the `ParentYear` property is changed by selecting the button in the `ParentComponent`, the `Year` property of the `ChildComponent` is updated.</span></span> <span data-ttu-id="6df00-166">Yeni değer, `Year` yeniden işlendiğinde UI'de `ParentComponent` işlenir:</span><span class="sxs-lookup"><span data-stu-id="6df00-166">The new value of `Year` is rendered in the UI when the `ParentComponent` is rerendered:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

<span data-ttu-id="6df00-167">`Year` Parametre, `Year` parametrenin türüyle eşleşen bir eşlik `YearChanged` eden olayı olduğundan bağlanabilir.</span><span class="sxs-lookup"><span data-stu-id="6df00-167">The `Year` parameter is bindable because it has a companion `YearChanged` event that matches the type of the `Year` parameter.</span></span>

<span data-ttu-id="6df00-168">Sözleşmeye `<ChildComponent @bind-Year="ParentYear" />` göre, temelde yazmaya eşdeğerdir:</span><span class="sxs-lookup"><span data-stu-id="6df00-168">By convention, `<ChildComponent @bind-Year="ParentYear" />` is essentially equivalent to writing:</span></span>

```razor
<ChildComponent @bind-Year="ParentYear" @bind-Year:event="YearChanged" />
```

<span data-ttu-id="6df00-169">Genel olarak, bir özellik bir `@bind-{PROPRETY}:event` öznitelik ekleyerek ilgili olay işleyicisi bağlanabilir.</span><span class="sxs-lookup"><span data-stu-id="6df00-169">In general, a property can be bound to a corresponding event handler by including an `@bind-{PROPRETY}:event` attribute.</span></span> <span data-ttu-id="6df00-170">Örneğin, özellik `MyProp` aşağıdaki iki `MyEventHandler` öznitelikleri kullanarak bağlı olabilir:</span><span class="sxs-lookup"><span data-stu-id="6df00-170">For example, the property `MyProp` can be bound to `MyEventHandler` using the following two attributes:</span></span>

```razor
<MyComponent @bind-MyProp="MyValue" @bind-MyProp:event="MyEventHandler" />
```

## <a name="child-to-parent-binding-with-chained-bind"></a><span data-ttu-id="6df00-171">Zincirli bağlama ile alt-üst teneye bağlama</span><span class="sxs-lookup"><span data-stu-id="6df00-171">Child-to-parent binding with chained bind</span></span>

<span data-ttu-id="6df00-172">Yaygın bir senaryo, bileşenin çıktısındaki bir sayfa öğesine veriye bağlı bir parametreyi zincirlemektir.</span><span class="sxs-lookup"><span data-stu-id="6df00-172">A common scenario is chaining a data-bound parameter to a page element in the component's output.</span></span> <span data-ttu-id="6df00-173">Birden çok bağlama düzeyi aynı anda oluştuğundan, bu senaryoya *zincirleme bağlama* denir.</span><span class="sxs-lookup"><span data-stu-id="6df00-173">This scenario is called a *chained bind* because multiple levels of binding occur simultaneously.</span></span>

<span data-ttu-id="6df00-174">Zincirleme bağlama, sayfanın öğesindeki `@bind` sözdizimiyle uygulaılamaz.</span><span class="sxs-lookup"><span data-stu-id="6df00-174">A chained bind can't be implemented with `@bind` syntax in the page's element.</span></span> <span data-ttu-id="6df00-175">Olay işleyicisi ve değeri ayrı olarak belirtilmelidir.</span><span class="sxs-lookup"><span data-stu-id="6df00-175">The event handler and value must be specified separately.</span></span> <span data-ttu-id="6df00-176">Ancak bir üst bileşen, `@bind` bileşenin parametresi ile sözdizimini kullanabilir.</span><span class="sxs-lookup"><span data-stu-id="6df00-176">A parent component, however, can use `@bind` syntax with the component's parameter.</span></span>

<span data-ttu-id="6df00-177">Aşağıdaki `PasswordField` bileşen (*PasswordField.razor):*</span><span class="sxs-lookup"><span data-stu-id="6df00-177">The following `PasswordField` component (*PasswordField.razor*):</span></span>

* <span data-ttu-id="6df00-178">Bir `<input>` öğeiçin `Password` öğenin değerini ayarlar.</span><span class="sxs-lookup"><span data-stu-id="6df00-178">Sets an `<input>` element's value to a `Password` property.</span></span>
* <span data-ttu-id="6df00-179">Özellik değişikliklerini `Password` [EventCallback](xref:blazor/event-handling#eventcallback)ile bir üst bileşene maruz bırakır.</span><span class="sxs-lookup"><span data-stu-id="6df00-179">Exposes changes of the `Password` property to a parent component with an [EventCallback](xref:blazor/event-handling#eventcallback).</span></span>
* <span data-ttu-id="6df00-180">Yöntemi `onclick` tetiklemek için kullanılan olayı kullanır. `ToggleShowPassword`</span><span class="sxs-lookup"><span data-stu-id="6df00-180">Uses the `onclick` event is used to trigger the `ToggleShowPassword` method.</span></span> <span data-ttu-id="6df00-181">Daha fazla bilgi için bkz. <xref:blazor/event-handling>.</span><span class="sxs-lookup"><span data-stu-id="6df00-181">For more information, see <xref:blazor/event-handling>.</span></span>

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

<span data-ttu-id="6df00-182">Bileşen `PasswordField` başka bir bileşende kullanılır:</span><span class="sxs-lookup"><span data-stu-id="6df00-182">The `PasswordField` component is used in another component:</span></span>

```razor
@page "/ParentComponent"

<h1>Parent Component</h1>

<PasswordField @bind-Password="_password" />

@code {
    private string _password;
}
```

<span data-ttu-id="6df00-183">Önceki örnekte parola üzerinde denetimler veya bindirme hataları gerçekleştirmek için:</span><span class="sxs-lookup"><span data-stu-id="6df00-183">To perform checks or trap errors on the password in the preceding example:</span></span>

* <span data-ttu-id="6df00-184">(Aşağıdaki`_password` örnek `Password` kodda) için bir destek alanı oluşturun.</span><span class="sxs-lookup"><span data-stu-id="6df00-184">Create a backing field for `Password` (`_password` in the following example code).</span></span>
* <span data-ttu-id="6df00-185">Ayarlayıcıdaki denetimleri veya `Password` bindirme hatalarını gerçekleştirin.</span><span class="sxs-lookup"><span data-stu-id="6df00-185">Perform the checks or trap errors in the `Password` setter.</span></span>

<span data-ttu-id="6df00-186">Aşağıdaki örnek, parolanın değerinde bir alan kullanılırsa kullanıcıya anında geri bildirim sağlar:</span><span class="sxs-lookup"><span data-stu-id="6df00-186">The following example provides immediate feedback to the user if a space is used in the password's value:</span></span>

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

## <a name="radio-buttons"></a><span data-ttu-id="6df00-187">Radyo düğmeleri</span><span class="sxs-lookup"><span data-stu-id="6df00-187">Radio buttons</span></span>

<span data-ttu-id="6df00-188">Bir formdaki radyo düğmelerine bağlama hakkında <xref:blazor/forms-validation#work-with-radio-buttons>bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="6df00-188">For information on binding to radio buttons in a form, see <xref:blazor/forms-validation#work-with-radio-buttons>.</span></span>
