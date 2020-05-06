---
title: ASP.NET Core formlardaki etiket yardımcıları
author: rick-anderson
description: Formlarla kullanılan yerleşik etiket yardımcılarını açıklar.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/working-with-forms
ms.openlocfilehash: ba523fba60153e2ae804f5a875cfaa1aa8fffedd
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82769108"
---
# <a name="tag-helpers-in-forms-in-aspnet-core"></a>ASP.NET Core formlardaki etiket yardımcıları

By [Rick Anderson](https://twitter.com/RickAndMSFT), [N. Taylor Mullen](https://github.com/NTaylorMullen), [Davve Patıı](https://twitter.com/Dave_Paquette)ve [Jerrie Pelser](https://github.com/jerriep)

Bu belge, formlarda ve genellikle form üzerinde kullanılan HTML öğeleriyle çalışmayı gösterir. HTML [form](https://www.w3.org/TR/html401/interact/forms.html) öğesi, Web uygulamalarının sunucuya veri geri göndermek için kullanacağı birincil mekanizmayı sağlar. Bu belgenin çoğunda [Etiket Yardımcıları](tag-helpers/intro.md) ve BUNLARıN güçlü HTML formları oluşturma konusunda nasıl yardımcı olabilecekleri açıklanmaktadır. Bu belgeyi okuyabilmeniz [Için yardımcıları etiketleyerek](tag-helpers/intro.md) okumanız önerilir.

Birçok durumda, HTML Yardımcıları belirli bir etiket Yardımcısı için alternatif bir yaklaşım sağlar, ancak bu etiket yardımcıların HTML yardımcılarını değiştirmez ve her HTML Yardımcısı için bir etiket Yardımcısı olmadığını bilmek önemlidir. Bir HTML Yardımcısı alternatifi varsa, bu, bahsedilir.

<a name="my-asp-route-param-ref-label"></a>

## <a name="the-form-tag-helper"></a>Form etiketi Yardımcısı

[Form](https://www.w3.org/TR/html401/interact/forms.html) etiketi Yardımcısı:

* MVC denetleyicisi eylemi veya adlandırılmış yol için HTML [ \<form>](https://www.w3.org/TR/html401/interact/forms.html) `action` öznitelik değeri oluşturur

* Siteler arası istek yasaklamasını engellemek için gizli bir [Istek doğrulama belirteci](/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages) ÜRETIR (http post eylem yönteminde `[ValidateAntiForgeryToken]` özniteliğiyle birlikte kullanıldığında)

* , `<Parameter Name>` Yol `asp-route-<Parameter Name>` değerlerine eklendiği özniteliği sağlar. Ve `routeValues` `Html.BeginRouteForm` için `Html.BeginForm` parametreler, benzer işlevlere sahiptir.

* Bir HTML Yardımcısı alternatifi `Html.BeginForm` ve`Html.BeginRouteForm`

Örnek:

[!code-HTML[](working-with-forms/sample/final/Views/Demo/RegisterFormOnly.cshtml)]

Yukarıdaki form etiketi Yardımcısı aşağıdaki HTML 'yi oluşturur:

```html
<form method="post" action="/Demo/Register">
    <!-- Input and Submit elements -->
    <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
</form>
```

MVC çalışma zamanı, form `action` etiketi yardımcı öznitelikleri `asp-controller` ve ' `asp-action`den öznitelik değeri oluşturur. Form etiketi Yardımcısı ayrıca siteler arası istek sahteciliği (HTTP POST eylem yönteminde `[ValidateAntiForgeryToken]` özniteliğiyle kullanıldığında) engellemek için gizli bir [istek doğrulama belirteci](/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages) oluşturur. Bir saf HTML formunun siteler arası istek sahteciliğini önleme 'den korunması zordur, form etiketi Yardımcısı bu hizmeti sizin için sağlar.

### <a name="using-a-named-route"></a>Adlandırılmış yol kullanma

`asp-route` Etiket Yardımcısı ÖZNITELIĞI, HTML `action` özniteliği için de biçimlendirme oluşturabilir. Adlı `register` [yolu](../../fundamentals/routing.md) içeren bir uygulama, kayıt sayfası için aşağıdaki biçimlendirmeyi kullanabilir:

[!code-HTML[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterRoute.cshtml)]

*Görünümler/hesap* klasöründeki görünümlerin birçoğu ( *bireysel kullanıcı hesaplarıyla*yeni bir Web uygulaması oluşturduğunuzda oluşturulur), [ASP-Route-ReturnUrl](xref:mvc/views/working-with-forms) özniteliğini içerir:

```cshtml
<form asp-controller="Account" asp-action="Login"
     asp-route-returnurl="@ViewData["ReturnUrl"]"
     method="post" class="form-horizontal" role="form">
```

>[!NOTE]
>Yerleşik şablonlarla, `returnUrl` yetkili bir kaynağa erişmeye çalıştığınızda ancak kimliği doğrulanmamış veya yetkilendirilmeyen otomatik olarak doldurulur. Yetkisiz erişim yapmaya çalıştığınızda güvenlik ara yazılımı sizi, `returnUrl` küme ile oturum açma sayfasına yönlendirir.

## <a name="the-form-action-tag-helper"></a>Form eylemi etiketi Yardımcısı

Form eylemi etiketi Yardımcısı, oluşturulan `formaction` `<button ...>` veya `<input type="image" ...>` etiketteki özniteliği oluşturur. `formaction` Özniteliği bir formun verilerini nereden gönderdiğini denetler. Tür `image` [ve \<düğme>](https://www.w3.org/wiki/HTML/Elements/button) öğeleri [ \<giriş>](https://www.w3.org/wiki/HTML/Elements/input) öğelerine bağlanır. Form eylemi etiketi Yardımcısı, ilgili öğe için hangi `formaction` bağlantının oluşturulduğunu denetlemek için çeşitli [AnchorTagHelper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) `asp-` özniteliklerinin kullanılmasını sağlar.

Değerini denetlemek için desteklenen [AnchorTagHelper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) öznitelikleri `formaction`:

|Öznitelik|Açıklama|
|---|---|
|[ASP-Controller](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-controller)|Denetleyicinin adı.|
|[ASP-eylem](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-action)|Eylem yönteminin adı.|
|[ASP-alanı](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-area)|Alanın adı.|
|[asp-sayfa](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-page)|Razor sayfasının adı.|
|[ASP-Page-Handler](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-page-handler)|Razor sayfası işleyicisinin adı.|
|[ASP-Route](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-route)|Yolun adı.|
|[ASP-Route-{Value}](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-route-value)|Tek bir URL yol değeri. Örneğin, `asp-route-id="1234"`.|
|[ASP-All-Route-Data](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-all-route-data)|Tüm rota değerleri.|
|[ASP-Fragment](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-fragment)|URL parçası.|

### <a name="submit-to-controller-example"></a>Denetleyiciye gönder örneği

Aşağıdaki biçimlendirme, formu giriş veya düğme seçildiğinde `Index` eyleme `HomeController` gönderir:

```cshtml
<form method="post">
    <button asp-controller="Home" asp-action="Index">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" asp-controller="Home" 
                                asp-action="Index">
</form>
```

Önceki biçimlendirme, aşağıdaki HTML 'yi oluşturur:

```html
<form method="post">
    <button formaction="/Home">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" formaction="/Home">
</form>
```

### <a name="submit-to-page-example"></a>Sayfa örneğine gönder

Aşağıdaki biçimlendirme formu `About` Razor sayfasına gönderir:

```cshtml
<form method="post">
    <button asp-page="About">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" asp-page="About">
</form>
```

Önceki biçimlendirme, aşağıdaki HTML 'yi oluşturur:

```html
<form method="post">
    <button formaction="/About">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" formaction="/About">
</form>
```

### <a name="submit-to-route-example"></a>Yönlendirme örneğine gönder

`/Home/Test` Uç noktayı göz önünde bulundurun:

```csharp
public class HomeController : Controller
{
    [Route("/Home/Test", Name = "Custom")]
    public string Test()
    {
        return "This is the test page";
    }
}
```

Aşağıdaki biçimlendirme formu `/Home/Test` uç noktaya gönderir.

```cshtml
<form method="post">
    <button asp-route="Custom">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" asp-route="Custom">
</form>
```

Önceki biçimlendirme, aşağıdaki HTML 'yi oluşturur:

```html
<form method="post">
    <button formaction="/Home/Test">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" formaction="/Home/Test">
</form>
```

## <a name="the-input-tag-helper"></a>Giriş etiketi Yardımcısı

Giriş etiketi Yardımcısı, bir HTML [ \<giriş>](https://www.w3.org/wiki/HTML/Elements/input) öğesini Razor görünüminizdeki bir model ifadesine bağlar.

Söz dizimi:

```cshtml
<input asp-for="<Expression Name>">
```

Giriş etiketi Yardımcısı:

* Özniteliğinde belirtilen `id` ifade `name` adı için ve HTML özniteliklerini üretir. `asp-for` `asp-for="Property1.Property2"`değerine `m => m.Property1.Property2`eşdeğerdir. İfadenin adı, `asp-for` öznitelik değeri için kullanılan şeydir. Ek bilgi için [ifade adları](#expression-names) bölümüne bakın.

* Model özelliğine uygulanan `type` model türüne ve [veri ek AÇıKLAMASı](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter) özniteliklerine göre html öznitelik değerini ayarlar

* Belirtildiğinde HTML `type` öznitelik değerinin üzerine yazılmaz

* Model özelliklerine uygulanan [veri ek açıklama](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter) özniteliklerinden [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5) doğrulama öznitelikleri oluşturur

* , Ve `Html.TextBoxFor` `Html.EditorFor`Ile çakışan bir HTML yardımcı özelliğine sahiptir. Ayrıntılar için bkz. **giriş etiketi Yardımcısı Için HTML Yardımcısı alternatifleri** .

* Güçlü yazma sağlar. Özelliğin adı değişirse ve etiket yardımcısını güncelleştirmezseniz aşağıdakine benzer bir hata alırsınız:

```
An error occurred during the compilation of a resource required to process
this request. Please review the following specific error details and modify
your source code appropriately.

Type expected
 'RegisterViewModel' does not contain a definition for 'Email' and no
 extension method 'Email' accepting a first argument of type 'RegisterViewModel'
 could be found (are you missing a using directive or an assembly reference?)
```

`Input` Etiket Yardımcısı, HTML `type` özniteliğini .net türüne göre ayarlar. Aşağıdaki tabloda bazı ortak .NET türleri ve oluşturulan HTML türü listelenmekte (her .NET türü listelenmemiştir).

|.NET türü|Giriş Türü|
|---|---|
|Bool|Type = "onay kutusu"|
|Dize|Type = "metin"|
|DateTime|Type =["TarihSaat-yerel"](https://developer.mozilla.org/docs/Web/HTML/Element/input/datetime-local)|
|Bayt|Type = "Number"|
|int|Type = "Number"|
|Tek, Çift|Type = "Number"|

Aşağıdaki tabloda, giriş etiketi Yardımcısı 'nın belirli giriş türleriyle eşleşecağı bazı ortak [veri ek açıklamaları](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter) (her doğrulama özniteliği listelenmez) gösterilmektedir:

|Öznitelik|Giriş Türü|
|---|---|
|EmailAddress|Type = "e-posta"|
|'Deki|Type = "URL"|
|[Hiddenınput]|Type = "Hidden"|
|Numarası|Type = "tel"|
|[DataType (DataType. Password)]|Type = "Password"|
|[DataType (DataType. Date)]|Type = "Date"|
|[DataType (DataType. Time)]|yazın = "Time"|

Örnek:

[!code-csharp[](working-with-forms/sample/final/ViewModels/RegisterViewModel.cs)]

[!code-HTML[](working-with-forms/sample/final/Views/Demo/RegisterInput.cshtml)]

Yukarıdaki kod, aşağıdaki HTML 'yi oluşturur:

```html
  <form method="post" action="/Demo/RegisterInput">
      Email:
      <input type="email" data-val="true"
             data-val-email="The Email Address field is not a valid email address."
             data-val-required="The Email Address field is required."
             id="Email" name="Email" value=""><br>
      Password:
      <input type="password" data-val="true"
             data-val-required="The Password field is required."
             id="Password" name="Password"><br>
      <button type="submit">Register</button>
      <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
   </form>
```

`Email` Ve `Password` özelliklerine uygulanan veri ek açıklamaları modelde meta veriler oluşturur. Giriş etiketi Yardımcısı, model meta verilerini kullanır ve [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5) `data-val-*` öznitelikleri üretir (bkz. [model doğrulama](../models/validation.md)). Bu öznitelikler, giriş alanlarına iliştirilecek Doğrulayıcıları anlatmaktadır. Bu unobtrusive HTML5 ve [jQuery](https://jquery.com/) doğrulaması sağlar. `data-val-rule="Error Message"`Unobtrusive öznitelikleri biçimindedir, burada kural doğrulama kuralının adıdır ( `data-val-required`örneğin `data-val-email` `data-val-maxlength`,, vb.) Öznitelikte bir hata iletisi sağlanırsa, `data-val-rule` özniteliği için değer olarak görüntülenir. Ayrıca, kural hakkında ek ayrıntılar sağlayan `data-val-ruleName-argumentName="argumentValue"` formun öznitelikleri de vardır, örneğin `data-val-maxlength-max="1024"` .

### <a name="html-helper-alternatives-to-input-tag-helper"></a>Giriş etiketi Yardımcısı için HTML Yardımcısı alternatifleri

`Html.TextBox`, `Html.TextBoxFor`ve `Html.Editor` `Html.EditorFor` , giriş etiketi Yardımcısı ile çakışan özelliklere sahiptir. Giriş etiketi Yardımcısı `type` özniteliği otomatik olarak ayarlar; `Html.TextBox` ve `Html.TextBoxFor` değildir. `Html.Editor`ve `Html.EditorFor` idare koleksiyonları, karmaşık nesneler ve şablonlar; Giriş etiketi Yardımcısı yok. Giriş etiketi Yardımcısı ve `Html.EditorFor` `Html.TextBoxFor` kesin olarak türlidir (lambda ifadeleri kullanır); `Html.TextBox` değil (ifade adları kullanır `Html.Editor` ).

### <a name="htmlattributes"></a>HtmlAttributes

`@Html.Editor()`ve `@Html.EditorFor()` varsayılan şablonlarını yürütürken `ViewDataDictionary` adlı `htmlAttributes` özel bir giriş kullanın. Bu davranış, isteğe bağlı olarak parametreler `additionalViewData` kullanılarak genişletilmiş şekilde belirlenir. "HtmlAttributes" anahtarı büyük/küçük harfe duyarlıdır. "HtmlAttributes" anahtarı, gibi `htmlAttributes` `@Html.TextBox()`giriş yardımcılarını geçirilmiş nesneye benzer şekilde işlenir.

```cshtml
@Html.EditorFor(model => model.YourProperty, 
  new { htmlAttributes = new { @class="myCssClass", style="Width:100px" } })
```

### <a name="expression-names"></a>İfade adları

`asp-for` Öznitelik değeri bir lambda ifadesinin `ModelExpression` bir ve sağ tarafıdır. Bu nedenle `asp-for="Property1"` , `m => m.Property1` ile `Model`öneki gerekmez, oluşturulan kodda olur. "\@" Karakterini kullanarak bir satır içi ifade başlatabilir ve öğesinden önce taşıyabilirsiniz `m.`:

```cshtml
@{
  var joe = "Joe";
}

<input asp-for="@joe">
```

Şunları üretir:

```html
<input type="text" id="joe" name="joe" value="Joe">
```

`asp-for="CollectionProperty[23].Member"` Koleksiyon özellikleriyle, `asp-for="CollectionProperty[i].Member"` değeri `i` `23`olduğu gibi aynı adı oluşturur.

ASP.NET Core MVC değeri `ModelExpression`hesapladığında, dahil olmak üzere `ModelState`çeşitli kaynakları inceler. Göz `<input type="text" asp-for="@Name">`önünde bulundurun. Hesaplanan `value` öznitelik, öğesinden gelen ilk null olmayan değerdir:

* `ModelState`"Name" anahtarına sahip giriş.
* İfadenin `Model.Name`sonucu.

### <a name="navigating-child-properties"></a>Alt özelliklerde gezinme

Ayrıca, görünüm modelinin özellik yolunu kullanarak alt Özellikler ' e gidebilirsiniz. Alt `Address` özellik içeren daha karmaşık bir model sınıfı düşünün.

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/AddressViewModel.cs?highlight=1,2,3,4&range=5-8)]

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/RegisterAddressViewModel.cs?highlight=8&range=5-13)]

Görünümünde, şu şekilde `Address.AddressLine1`bağlandık:

[!code-HTML[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterAddress.cshtml?highlight=6)]

İçin `Address.AddressLine1`aşağıdaki HTML oluşturulur:

```html
<input type="text" id="Address_AddressLine1" name="Address.AddressLine1" value="">
```

### <a name="expression-names-and-collections"></a>İfade adları ve koleksiyonlar

Örnek, bir dizisi içeren bir model `Colors`:

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/Person.cs?highlight=3&range=5-10)]

Eylem yöntemi:

```csharp
public IActionResult Edit(int id, int colorIndex)
{
    ViewData["Index"] = colorIndex;
    return View(GetPerson(id));
}
```

Aşağıdaki Razor, belirli `Color` bir öğeye nasıl erişistediğinizi göstermektedir:

[!code-HTML[](working-with-forms/sample/final/Views/Demo/EditColor.cshtml)]

*Views/Shared/EditorTemplates/String. cshtml* şablonu:

[!code-HTML[](working-with-forms/sample/final/Views/Shared/EditorTemplates/String.cshtml)]

Örnek kullanarak `List<T>`:

[!code-csharp[](working-with-forms/sample/final/ViewModels/ToDoItem.cs?range=3-8)]

Aşağıdaki Razor, bir koleksiyonun üzerinde nasıl yineleme yapılacağını göstermektedir:

[!code-HTML[](working-with-forms/sample/final/Views/Demo/Edit.cshtml)]

*Views/Shared/EditorTemplates/TodoItem. cshtml* şablonu:

[!code-HTML[](working-with-forms/sample/final/Views/Shared/EditorTemplates/ToDoItem.cshtml)]

`foreach`değer bir `asp-for` veya `Html.DisplayFor` eşdeğer bağlamda kullanılacaksa, mümkünse kullanılması gerekir. Genel olarak, `for` bir Numaralandırıcı ayırması `foreach` gerekmiyorsa (senaryo buna izin veriyorsa) daha iyidir; Ancak, bir LINQ ifadesinde bir dizin oluşturucuyu değerlendirmek pahalı olabilir ve simge durumuna küçültülmüş olmalıdır.

&nbsp;

>[!NOTE]
>Yukarıdaki açıklamalı örnek kod, listedeki her birine `@` `ToDoItem` erişmek için lambda ifadesinin işleçle nasıl değiştirileceğini gösterir.

## <a name="the-textarea-tag-helper"></a>TextArea etiketi Yardımcısı

`Textarea Tag Helper` Etiket Yardımcısı giriş etiketi Yardımcısı ile benzerdir.

* , `id` Ve `name` özniteliklerini ve bir [ \<TextArea>](https://www.w3.org/wiki/HTML/Elements/textarea) öğesi için modelden veri doğrulama özniteliklerini üretir.

* Güçlü yazma sağlar.

* HTML Yardımcısı alternatifi:`Html.TextAreaFor`

Örnek:

[!code-csharp[](working-with-forms/sample/final/ViewModels/DescriptionViewModel.cs)]

[!code-HTML[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterTextArea.cshtml?highlight=4)]

Aşağıdaki HTML oluşturulur:

```html
<form method="post" action="/Demo/RegisterTextArea">
  <textarea data-val="true"
   data-val-maxlength="The field Description must be a string or array type with a maximum length of &#x27;1024&#x27;."
   data-val-maxlength-max="1024"
   data-val-minlength="The field Description must be a string or array type with a minimum length of &#x27;5&#x27;."
   data-val-minlength-min="5"
   id="Description" name="Description">
  </textarea>
  <button type="submit">Test</button>
  <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
</form>
```

## <a name="the-label-tag-helper"></a>Etiket etiketi Yardımcısı

* Bir ifade adı için `for` [ \<etiket>](https://www.w3.org/wiki/HTML/Elements/label) öğesinde etiket başlık yazısını ve özniteliği oluşturur

* HTML Yardımcısı alternatifi: `Html.LabelFor`.

, `Label Tag Helper` Saf HTML etiket öğesi üzerinde aşağıdaki avantajları sağlar:

* `Display` Öznitelikten açıklayıcı etiket değerini otomatik olarak alırsınız. İstenen görünen ad zaman içinde değişebilir ve öznitelik ve etiket etiketi Yardımcısı 'nın `Display` birleşimi, `Display` kullanıldığı her yere uygulanır.

* Kaynak kodunda daha az biçimlendirme

* Model özelliğiyle güçlü yazma.

Örnek:

[!code-csharp[](working-with-forms/sample/final/ViewModels/SimpleViewModel.cs)]

[!code-HTML[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterLabel.cshtml?highlight=4)]

`<label>` Öğesi IÇIN aşağıdaki HTML oluşturulur:

```html
<label for="Email">Email Address</label>
```

Etiket etiketi Yardımcısı, `for` `<input>` öğesiyle Ilişkili kimlik olan "e-posta" öznitelik değerini oluşturdu. Etiket Yardımcıları, doğru şekilde `id` ilişkilendirilebilen tutarlı ve `for` öğeleri oluşturur. Bu örnekteki başlık, `Display` özniteliğinden gelir. Modelde bir `Display` öznitelik yoksa, başlık ifadenin Özellik adı olacaktır.

## <a name="the-validation-tag-helpers"></a>Doğrulama etiketi yardımcıları

İki doğrulama etiketi yardımcıları vardır. `Validation Message Tag Helper` (Bu, modelinizde tek bir özellik için bir doğrulama iletisi gösterir) ve `Validation Summary Tag Helper` (doğrulama hatalarının özetini görüntüler). , `Input Tag Helper` Model sınıflarınızda bulunan veri ek açıklaması özniteliklerini temel alan GIRIŞ öğelerine HTML5 istemci tarafı doğrulama öznitelikleri ekler. Doğrulama de sunucuda gerçekleştirilir. Doğrulama etiketi Yardımcısı, bir doğrulama hatası oluştuğunda bu hata iletilerini görüntüler.

### <a name="the-validation-message-tag-helper"></a>Doğrulama Iletisi etiketi Yardımcısı

* Belirtilen model özelliğinin giriş alanındaki doğrulama hatası mesajlarını bağlayan [span](https://developer.mozilla.org/docs/Web/HTML/Element/span) öğesine [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5) `data-valmsg-for="property"` özniteliğini ekler.   İstemci tarafı doğrulama hatası oluştuğunda [jQuery](https://jquery.com/) , `<span>` öğesinde hata iletisini görüntüler.

* Doğrulama de sunucuda gerçekleşir. İstemciler JavaScript devre dışı bırakılmış olabilir ve bazı doğrulamalar yalnızca sunucu tarafında yapılabilir.

* HTML Yardımcısı alternatifi:`Html.ValidationMessageFor`

, `Validation Message Tag Helper` Bir HTML [span](https://developer.mozilla.org/docs/Web/HTML/Element/span) öğesinde `asp-validation-for` özniteliğiyle kullanılır.

```cshtml
<span asp-validation-for="Email"></span>
```

Doğrulama Iletisi etiketi Yardımcısı aşağıdaki HTML 'yi oluşturur:

```html
<span class="field-validation-valid"
  data-valmsg-for="Email"
  data-valmsg-replace="true"></span>
```

Genellikle aynı özellik için `Validation Message Tag Helper` bir `Input` etiket Yardımcısı ' nı kullanırsınız. Bunun yapılması, hataya neden olan girişin yakınında herhangi bir doğrulama hata iletisi görüntüler.

> [!NOTE]
> İstemci tarafı doğrulaması için doğru JavaScript ve [jQuery](https://jquery.com/) betik başvurularını içeren bir görünümsiniz olmalıdır. Daha fazla bilgi için bkz. [model doğrulaması](../models/validation.md) .

Sunucu tarafı doğrulama hatası oluştuğunda (örneğin, özel sunucu tarafı doğrulama veya istemci tarafı doğrulaması devre dışı bırakılmışsa), MVC bu hata iletisini `<span>` öğenin gövdesi olarak koyar.

```html
<span class="field-validation-error" data-valmsg-for="Email"
            data-valmsg-replace="true">
   The Email Address field is required.
</span>
```

### <a name="the-validation-summary-tag-helper"></a>Doğrulama Özeti etiketi Yardımcısı

* `asp-validation-summary` Özniteliği olan öğeleri hedefler `<div>`

* HTML Yardımcısı alternatifi:`@Html.ValidationSummary`

, `Validation Summary Tag Helper` Doğrulama iletilerinin özetini göstermek için kullanılır. `asp-validation-summary` Öznitelik değeri, aşağıdakilerden herhangi biri olabilir:

|ASP-doğrulama-Özet|Görünen doğrulama iletileri|
|--- |--- |
|ValidationSummary. All|Özellik ve model düzeyi|
|Yalnızca ValidationSummary. model|Model|
|ValidationSummary. None|Hiçbiri|

### <a name="sample"></a>Örnek

Aşağıdaki örnekte, veri modelinde, `DataAnnotation` `<input>` öğesinde doğrulama hatası iletileri üreten öznitelikler vardır.  Doğrulama hatası oluştuğunda, doğrulama etiketi Yardımcısı şu hata iletisini görüntüler:

[!code-csharp[](working-with-forms/sample/final/ViewModels/RegisterViewModel.cs)]

[!code-HTML[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterValidation.cshtml?highlight=4,6,8&range=1-10)]

Oluşturulan HTML (model geçerli olduğunda):

```html
<form action="/DemoReg/Register" method="post">
  <div class="validation-summary-valid" data-valmsg-summary="true">
  <ul><li style="display:none"></li></ul></div>
  Email:  <input name="Email" id="Email" type="email" value=""
   data-val-required="The Email field is required."
   data-val-email="The Email field is not a valid email address."
   data-val="true"><br>
  <span class="field-validation-valid" data-valmsg-replace="true"
   data-valmsg-for="Email"></span><br>
  Password: <input name="Password" id="Password" type="password"
   data-val-required="The Password field is required." data-val="true"><br>
  <span class="field-validation-valid" data-valmsg-replace="true"
   data-valmsg-for="Password"></span><br>
  <button type="submit">Register</button>
  <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
</form>
```

## <a name="the-select-tag-helper"></a>Etiket Seç Yardımcısı

* Modelinizin özellikleri için [Select](https://www.w3.org/wiki/HTML/Elements/select) ve ilişkili [seçenek](https://www.w3.org/wiki/HTML/Elements/option) öğeleri oluşturur.

* Bir HTML Yardımcısı alternatifi `Html.DropDownListFor` ve`Html.ListBoxFor`

, `Select Tag Helper` `asp-for` [Select](https://www.w3.org/wiki/HTML/Elements/select) öğesi için model özelliği adını belirtir ve `asp-items` [seçenek](https://www.w3.org/wiki/HTML/Elements/option) öğelerini belirtir.  Örneğin:

[!code-HTML[](working-with-forms/sample/final/Views/Home/Index.cshtml?range=4)]

Örnek:

[!code-csharp[](working-with-forms/sample/final/ViewModels/CountryViewModel.cs)]

Yöntemi öğesini başlatır, seçilen ülkeyi ayarlar ve `Index` görünüme geçirir. `CountryViewModel` `Index`

[!code-csharp[](working-with-forms/sample/final/Controllers/HomeController.cs?range=8-13)]

HTTP POST `Index` yöntemi seçimi görüntüler:

[!code-csharp[](working-with-forms/sample/final/Controllers/HomeController.cs?range=15-27)]

`Index` Görünüm:

[!code-cshtml[](working-with-forms/sample/final/Views/Home/Index.cshtml?highlight=4)]

Aşağıdaki HTML 'yi üreten ("CA" seçiliyken):

```html
<form method="post" action="/">
     <select id="Country" name="Country">
       <option value="MX">Mexico</option>
       <option selected="selected" value="CA">Canada</option>
       <option value="US">USA</option>
     </select>
       <br /><button type="submit">Register</button>
     <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
   </form>
```

> [!NOTE]
> Etiket Seç Yardımcısı ile `ViewBag` veya `ViewData` kullanmayı önermiyoruz. Bir görünüm modeli, MVC meta verileri sağlamaya ve genellikle daha az soruna neden olacak daha sağlamdır.

`asp-for` Öznitelik değeri özel bir durumdur ve bir `Model` ön ek gerektirmez, diğer etiket Yardımcısı öznitelikleri olur (gibi `asp-items`)

[!code-HTML[](working-with-forms/sample/final/Views/Home/Index.cshtml?range=4)]

### <a name="enum-binding"></a>Sabit Listesi bağlama

`<select>` Genellikle bir `enum` özellikle kullanılması ve `SelectListItem` `enum` değerlerden öğeleri oluşturmak kullanışlıdır.

Örnek:

[!code-csharp[](working-with-forms/sample/final/ViewModels/CountryEnumViewModel.cs?range=3-7)]

[!code-csharp[](working-with-forms/sample/final/ViewModels/CountryEnum.cs)]

Yöntemi `GetEnumSelectList` , bir numaralandırma `SelectList` için bir nesne oluşturur.

[!code-HTML[](../../mvc/views/working-with-forms/sample/final/Views/Home/IndexEnum.cshtml?highlight=5)]

Daha zengin bir kullanıcı arabirimi almak için, `Display` Numaralandırıcı listenizi özniteliğiyle işaretleyebilirsiniz:

[!code-csharp[](working-with-forms/sample/final/ViewModels/CountryEnum.cs?highlight=5,7)]

Aşağıdaki HTML oluşturulur:

```html
  <form method="post" action="/Home/IndexEnum">
         <select data-val="true" data-val-required="The EnumCountry field is required."
                 id="EnumCountry" name="EnumCountry">
             <option value="0">United Mexican States</option>
             <option value="1">United States of America</option>
             <option value="2">Canada</option>
             <option value="3">France</option>
             <option value="4">Germany</option>
             <option selected="selected" value="5">Spain</option>
         </select>
         <br /><button type="submit">Register</button>
         <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
    </form>
```

### <a name="option-group"></a>Seçenek grubu

HTML [ \<SeçenekGrubu>](https://www.w3.org/wiki/HTML/Elements/optgroup) öğesi, görünüm modelinde bir veya daha fazla `SelectListGroup` nesne içerdiğinde oluşturulur.

Öğeleri "Kuzey Amerika" ve "Avrupa" gruplarında `CountryViewModelGroup` gruplandırır: `SelectListItem`

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/CountryViewModelGroup.cs?highlight=5,6,14,20,26,32,38,44&range=6-56)]

İki grup aşağıda gösterilmiştir:

![seçenek grubu örneği](working-with-forms/_static/grp.png)

Oluşturulan HTML:

```html
 <form method="post" action="/Home/IndexGroup">
      <select id="Country" name="Country">
          <optgroup label="North America">
              <option value="MEX">Mexico</option>
              <option value="CAN">Canada</option>
              <option value="US">USA</option>
          </optgroup>
          <optgroup label="Europe">
              <option value="FR">France</option>
              <option value="ES">Spain</option>
              <option value="DE">Germany</option>
          </optgroup>
      </select>
      <br /><button type="submit">Register</button>
      <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
 </form>
```

### <a name="multiple-select"></a>Çoklu seçim

Öznitelikte belirtilen özellik bir `IEnumerable`Ise, select etiketi Yardımcısı otomatik olarak [birden çok = "çoklu"](https://w3c.github.io/html-reference/select.html) özniteliği oluşturur. `asp-for` Örneğin, aşağıdaki model verildiğinde:

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/CountryViewModelIEnumerable.cs?highlight=6)]

Aşağıdaki görünümle:

[!code-HTML[](../../mvc/views/working-with-forms/sample/final/Views/Home/IndexMultiSelect.cshtml?highlight=4)]

Aşağıdaki HTML 'yi oluşturur:

```html
<form method="post" action="/Home/IndexMultiSelect">
    <select id="CountryCodes"
    multiple="multiple"
    name="CountryCodes"><option value="MX">Mexico</option>
<option value="CA">Canada</option>
<option value="US">USA</option>
<option value="FR">France</option>
<option value="ES">Spain</option>
<option value="DE">Germany</option>
</select>
    <br /><button type="submit">Register</button>
  <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
</form>
```

### <a name="no-selection"></a>Seçim yok

Birden çok sayfada "belirtilmemiş" seçeneğini kullanarak kendinizi bulursanız, HTML 'yi yinelemeyi ortadan kaldırmak için bir şablon oluşturabilirsiniz:

[!code-HTML[](../../mvc/views/working-with-forms/sample/final/Views/Home/IndexEmptyTemplate.cshtml?highlight=5)]

*Views/Shared/EditorTemplates/CountryViewModel. cshtml* şablonu:

[!code-HTML[](working-with-forms/sample/final/Views/Shared/EditorTemplates/CountryViewModel.cshtml)]

HTML [ \<seçeneği>](https://www.w3.org/wiki/HTML/Elements/option) öğesi ekleme *hiçbir seçim* durumuyla sınırlı değildir. Örneğin, aşağıdaki görünüm ve eylem yöntemi yukarıdaki koda benzer HTML oluşturur:

[!code-csharp[](working-with-forms/sample/final/Controllers/HomeController.cs?name=snippetNone)]

[!code-HTML[](working-with-forms/sample/final/Views/Home/IndexOption.cshtml)]

Geçerli `Country` değere `<option>` bağlı olarak doğru öğe seçilir ( `selected="selected"` özniteliği içerir).

[!code-csharp[](working-with-forms/sample/final/Controllers/HomeController.cs?range=114-119)]

```html
 <form method="post" action="/Home/IndexEmpty">
      <select id="Country" name="Country">
          <option value="">&lt;none&gt;</option>
          <option value="MX">Mexico</option>
          <option value="CA" selected="selected">Canada</option>
          <option value="US">USA</option>
      </select>
      <br /><button type="submit">Register</button>
   <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
 </form>
 ```

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:mvc/views/tag-helpers/intro>
* [HTML form öğesi](https://www.w3.org/TR/html401/interact/forms.html)
* [İstek doğrulama belirteci](/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages)
* <xref:mvc/models/model-binding>
* <xref:mvc/models/validation>
* [Iattributeadapter arabirimi](/dotnet/api/Microsoft.AspNetCore.Mvc.DataAnnotations.IAttributeAdapter)
* [Bu belge için kod parçacıkları](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/working-with-forms/sample/final)
