---
title: ASP.NET Core için Razor söz dizimi başvurusu
author: rick-anderson
description: RazorSunucu tabanlı kodu Web sayfalarına eklemek için biçimlendirme sözdizimi hakkında bilgi edinin.
ms.author: riande
ms.date: 02/12/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/razor
ms.openlocfilehash: 2831fd2edd029043e9457cd213e32f1a82c2872e
ms.sourcegitcommit: 69e1a79a572b0af17d08e81af12c594b7316f2e1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/15/2020
ms.locfileid: "83424418"
---
# <a name="razor-syntax-reference-for-aspnet-core"></a><span data-ttu-id="c2857-103">ASP.NET Core için Razor söz dizimi başvurusu</span><span class="sxs-lookup"><span data-stu-id="c2857-103">Razor syntax reference for ASP.NET Core</span></span>

<span data-ttu-id="c2857-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Taylor Mullen](https://twitter.com/ntaylormullen)ve [dan vicarel](https://github.com/Rabadash8820)</span><span class="sxs-lookup"><span data-stu-id="c2857-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Taylor Mullen](https://twitter.com/ntaylormullen), and [Dan Vicarel](https://github.com/Rabadash8820)</span></span>

<span data-ttu-id="c2857-105">Razor, Web sayfalarına sunucu tabanlı kod eklemeye yönelik bir biçimlendirme sözdizimidir.</span><span class="sxs-lookup"><span data-stu-id="c2857-105">Razor is a markup syntax for embedding server-based code into webpages.</span></span> <span data-ttu-id="c2857-106">Razor söz dizimi Razor Markup, C# ve HTML 'den oluşur.</span><span class="sxs-lookup"><span data-stu-id="c2857-106">The Razor syntax consists of Razor markup, C#, and HTML.</span></span> <span data-ttu-id="c2857-107">Razor içeren dosyalar genellikle *. cshtml* dosya uzantısına sahiptir.</span><span class="sxs-lookup"><span data-stu-id="c2857-107">Files containing Razor generally have a *.cshtml* file extension.</span></span> <span data-ttu-id="c2857-108">Razor, [Razor bileşenleri](xref:blazor/components) dosyalarında (*. Razor*) de bulunur.</span><span class="sxs-lookup"><span data-stu-id="c2857-108">Razor is also found in [Razor components](xref:blazor/components) files (*.razor*).</span></span>

## <a name="rendering-html"></a><span data-ttu-id="c2857-109">HTML işleniyor</span><span class="sxs-lookup"><span data-stu-id="c2857-109">Rendering HTML</span></span>

<span data-ttu-id="c2857-110">Varsayılan Razor dili HTML’dir.</span><span class="sxs-lookup"><span data-stu-id="c2857-110">The default Razor language is HTML.</span></span> <span data-ttu-id="c2857-111">Razor işaretlemesinden HTML işlemek, HTML dosyasından HTML işlemekten farklı değildir.</span><span class="sxs-lookup"><span data-stu-id="c2857-111">Rendering HTML from Razor markup is no different than rendering HTML from an HTML file.</span></span> <span data-ttu-id="c2857-112">*. Cshtml* Razor dosyalarındaki HTML işaretlemesi sunucu tarafından değiştirilmeden işlenir.</span><span class="sxs-lookup"><span data-stu-id="c2857-112">HTML markup in *.cshtml* Razor files is rendered by the server unchanged.</span></span>

## <a name="razor-syntax"></a><span data-ttu-id="c2857-113">Razor söz dizimi</span><span class="sxs-lookup"><span data-stu-id="c2857-113">Razor syntax</span></span>

<span data-ttu-id="c2857-114">Razor, C# ' i destekler ve `@` HTML 'Den c# ' ye geçiş yapmak için sembolünü kullanır.</span><span class="sxs-lookup"><span data-stu-id="c2857-114">Razor supports C# and uses the `@` symbol to transition from HTML to C#.</span></span> <span data-ttu-id="c2857-115">Razor, C# ifadelerini değerlendirir ve bunları HTML çıktısında oluşturur.</span><span class="sxs-lookup"><span data-stu-id="c2857-115">Razor evaluates C# expressions and renders them in the HTML output.</span></span>

<span data-ttu-id="c2857-116">Bir `@` sembol sonrasında [Razor ayrılmış anahtar sözcüğü](#razor-reserved-keywords)olduğunda, bu, Razor 'e özgü işaretlere geçer.</span><span class="sxs-lookup"><span data-stu-id="c2857-116">When an `@` symbol is followed by a [Razor reserved keyword](#razor-reserved-keywords), it transitions into Razor-specific markup.</span></span> <span data-ttu-id="c2857-117">Aksi halde, düz C# ' ye geçiş yapar.</span><span class="sxs-lookup"><span data-stu-id="c2857-117">Otherwise, it transitions into plain C#.</span></span>

<span data-ttu-id="c2857-118">`@`Razor biçimlendirmesinde bir sembolün çıkmak için ikinci bir `@` sembol kullanın:</span><span class="sxs-lookup"><span data-stu-id="c2857-118">To escape an `@` symbol in Razor markup, use a second `@` symbol:</span></span>

```cshtml
<p>@@Username</p>
```

<span data-ttu-id="c2857-119">Kod, HTML 'de tek bir sembol ile işlenir `@` :</span><span class="sxs-lookup"><span data-stu-id="c2857-119">The code is rendered in HTML with a single `@` symbol:</span></span>

```html
<p>@Username</p>
```

<span data-ttu-id="c2857-120">HTML öznitelikleri ve e-posta adreslerini içeren içerik `@` sembol bir geçiş karakteri olarak kabul değildir.</span><span class="sxs-lookup"><span data-stu-id="c2857-120">HTML attributes and content containing email addresses don't treat the `@` symbol as a transition character.</span></span> <span data-ttu-id="c2857-121">Aşağıdaki örnekteki e-posta adresleri Razor ayrıştırma tarafından dokunmaz:</span><span class="sxs-lookup"><span data-stu-id="c2857-121">The email addresses in the following example are untouched by Razor parsing:</span></span>

```cshtml
<a href="mailto:Support@contoso.com">Support@contoso.com</a>
```

## <a name="implicit-razor-expressions"></a><span data-ttu-id="c2857-122">Örtük Razor ifadeleri</span><span class="sxs-lookup"><span data-stu-id="c2857-122">Implicit Razor expressions</span></span>

<span data-ttu-id="c2857-123">Örtük Razor ifadeleri, `@` sonrasında C# kodu ile başlar:</span><span class="sxs-lookup"><span data-stu-id="c2857-123">Implicit Razor expressions start with `@` followed by C# code:</span></span>

```cshtml
<p>@DateTime.Now</p>
<p>@DateTime.IsLeapYear(2016)</p>
```

<span data-ttu-id="c2857-124">C# `await` anahtar sözcüğünün dışında, örtük ifadeler boşluk içermemelidir.</span><span class="sxs-lookup"><span data-stu-id="c2857-124">With the exception of the C# `await` keyword, implicit expressions must not contain spaces.</span></span> <span data-ttu-id="c2857-125">C# ifadesinde Temizleme işlemi varsa, boşluklar ıntermingled olabilir:</span><span class="sxs-lookup"><span data-stu-id="c2857-125">If the C# statement has a clear ending, spaces can be intermingled:</span></span>

```cshtml
<p>@await DoSomething("hello", "world")</p>
```

<span data-ttu-id="c2857-126">Parantez ( **cannot** ) içindeki karakterler `<>` bir HTML etiketi olarak yorumlandığından örtük ifadeler C# generics içeremez.</span><span class="sxs-lookup"><span data-stu-id="c2857-126">Implicit expressions **cannot** contain C# generics, as the characters inside the brackets (`<>`) are interpreted as an HTML tag.</span></span> <span data-ttu-id="c2857-127">Aşağıdaki kod geçerli **değil** :</span><span class="sxs-lookup"><span data-stu-id="c2857-127">The following code is **not** valid:</span></span>

```cshtml
<p>@GenericMethod<int>()</p>
```

<span data-ttu-id="c2857-128">Yukarıdaki kod, aşağıdakilerden birine benzer bir derleyici hatası oluşturur:</span><span class="sxs-lookup"><span data-stu-id="c2857-128">The preceding code generates a compiler error similar to one of the following:</span></span>

* <span data-ttu-id="c2857-129">"İnt" öğesi kapatılmamış.</span><span class="sxs-lookup"><span data-stu-id="c2857-129">The "int" element wasn't closed.</span></span> <span data-ttu-id="c2857-130">Tüm öğeler kendi kendini kapatıyor ya da eşleşen bir bitiş etiketine sahip olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="c2857-130">All elements must be either self-closing or have a matching end tag.</span></span>
* <span data-ttu-id="c2857-131">' GenericMethod ' Yöntem grubu temsilci olmayan ' Object ' türüne dönüştürülemiyor.</span><span class="sxs-lookup"><span data-stu-id="c2857-131">Cannot convert method group 'GenericMethod' to non-delegate type 'object'.</span></span> <span data-ttu-id="c2857-132">Yöntemi çağırmak mı istiyordunuz? "</span><span class="sxs-lookup"><span data-stu-id="c2857-132">Did you intend to invoke the method?\`</span></span>

<span data-ttu-id="c2857-133">Genel metot çağrılarının [açık bir Razor ifadesinde](#explicit-razor-expressions) veya [Razor kodu bloğunda](#razor-code-blocks)sarmalanması gerekir.</span><span class="sxs-lookup"><span data-stu-id="c2857-133">Generic method calls must be wrapped in an [explicit Razor expression](#explicit-razor-expressions) or a [Razor code block](#razor-code-blocks).</span></span>

## <a name="explicit-razor-expressions"></a><span data-ttu-id="c2857-134">Açık Razor ifadeleri</span><span class="sxs-lookup"><span data-stu-id="c2857-134">Explicit Razor expressions</span></span>

<span data-ttu-id="c2857-135">Açık Razor ifadeleri, `@` dengeli parantez içeren bir sembolden oluşur.</span><span class="sxs-lookup"><span data-stu-id="c2857-135">Explicit Razor expressions consist of an `@` symbol with balanced parenthesis.</span></span> <span data-ttu-id="c2857-136">Son haftanın saatini işlemek için aşağıdaki Razor işaretlemesi kullanılır:</span><span class="sxs-lookup"><span data-stu-id="c2857-136">To render last week's time, the following Razor markup is used:</span></span>

```cshtml
<p>Last week this time: @(DateTime.Now - TimeSpan.FromDays(7))</p>
```

<span data-ttu-id="c2857-137">Parantez içindeki içerikler `@()` değerlendirilir ve çıkışa işlenir.</span><span class="sxs-lookup"><span data-stu-id="c2857-137">Any content within the `@()` parenthesis is evaluated and rendered to the output.</span></span>

<span data-ttu-id="c2857-138">Önceki bölümde açıklanan örtük ifadeler, genellikle boşluk içeremez.</span><span class="sxs-lookup"><span data-stu-id="c2857-138">Implicit expressions, described in the previous section, generally can't contain spaces.</span></span> <span data-ttu-id="c2857-139">Aşağıdaki kodda, bir hafta geçerli saatten çıkarılır:</span><span class="sxs-lookup"><span data-stu-id="c2857-139">In the following code, one week isn't subtracted from the current time:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact.cshtml?range=17)]

<span data-ttu-id="c2857-140">Kod, aşağıdaki HTML 'yi işler:</span><span class="sxs-lookup"><span data-stu-id="c2857-140">The code renders the following HTML:</span></span>

```html
<p>Last week: 7/7/2016 4:39:52 PM - TimeSpan.FromDays(7)</p>
```

<span data-ttu-id="c2857-141">Açık ifadeler, metni bir ifade sonucuyla birleştirmek için kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="c2857-141">Explicit expressions can be used to concatenate text with an expression result:</span></span>

```cshtml
@{
    var joe = new Person("Joe", 33);
}

<p>Age@(joe.Age)</p>
```

<span data-ttu-id="c2857-142">Açık ifade olmadan `<p>Age@joe.Age</p>` bir e-posta adresi olarak kabul edilir ve `<p>Age@joe.Age</p>` işlenir.</span><span class="sxs-lookup"><span data-stu-id="c2857-142">Without the explicit expression, `<p>Age@joe.Age</p>` is treated as an email address, and `<p>Age@joe.Age</p>` is rendered.</span></span> <span data-ttu-id="c2857-143">Açık bir ifade olarak yazıldığında `<p>Age33</p>` işlenir.</span><span class="sxs-lookup"><span data-stu-id="c2857-143">When written as an explicit expression, `<p>Age33</p>` is rendered.</span></span>

<span data-ttu-id="c2857-144">Açık ifadeler, *. cshtml* dosyalarındaki genel metotlardan çıkış oluşturmak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="c2857-144">Explicit expressions can be used to render output from generic methods in *.cshtml* files.</span></span> <span data-ttu-id="c2857-145">Aşağıdaki biçimlendirme, C# genel parantezinin neden olduğu daha önce gösterilen hatayı nasıl düzeltebileceğiniz gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="c2857-145">The following markup shows how to correct the error shown earlier caused by the brackets of a C# generic.</span></span> <span data-ttu-id="c2857-146">Kod açık bir ifade olarak yazılır:</span><span class="sxs-lookup"><span data-stu-id="c2857-146">The code is written as an explicit expression:</span></span>

```cshtml
<p>@(GenericMethod<int>())</p>
```

## <a name="expression-encoding"></a><span data-ttu-id="c2857-147">İfade kodlaması</span><span class="sxs-lookup"><span data-stu-id="c2857-147">Expression encoding</span></span>

<span data-ttu-id="c2857-148">Dizeyi değerlendiren C# ifadeleri HTML kodlandı.</span><span class="sxs-lookup"><span data-stu-id="c2857-148">C# expressions that evaluate to a string are HTML encoded.</span></span> <span data-ttu-id="c2857-149">Sonucunu veren C# ifadeleri `IHtmlContent` doğrudan aracılığıyla işlenir `IHtmlContent.WriteTo` .</span><span class="sxs-lookup"><span data-stu-id="c2857-149">C# expressions that evaluate to `IHtmlContent` are rendered directly through `IHtmlContent.WriteTo`.</span></span> <span data-ttu-id="c2857-150">Değerlendirilmeyen C# ifadeleri `IHtmlContent` , işlenmeden önce bir dizeye dönüştürülür `ToString` ve kodlanabilir.</span><span class="sxs-lookup"><span data-stu-id="c2857-150">C# expressions that don't evaluate to `IHtmlContent` are converted to a string by `ToString` and encoded before they're rendered.</span></span>

```cshtml
@("<span>Hello World</span>")
```

<span data-ttu-id="c2857-151">Kod, aşağıdaki HTML 'yi işler:</span><span class="sxs-lookup"><span data-stu-id="c2857-151">The code renders the following HTML:</span></span>

```html
&lt;span&gt;Hello World&lt;/span&gt;
```

<span data-ttu-id="c2857-152">HTML tarayıcıda şu şekilde gösterilir:</span><span class="sxs-lookup"><span data-stu-id="c2857-152">The HTML is shown in the browser as:</span></span>

```html
<span>Hello World</span>
```

<span data-ttu-id="c2857-153">`HtmlHelper.Raw`çıktı kodlanmamış, ancak HTML işaretlemesi olarak işlendi.</span><span class="sxs-lookup"><span data-stu-id="c2857-153">`HtmlHelper.Raw` output isn't encoded but rendered as HTML markup.</span></span>

> [!WARNING]
> <span data-ttu-id="c2857-154">`HtmlHelper.Raw`Ayıklanmış Kullanıcı girişinde kullanmak bir güvenlik riskidir.</span><span class="sxs-lookup"><span data-stu-id="c2857-154">Using `HtmlHelper.Raw` on unsanitized user input is a security risk.</span></span> <span data-ttu-id="c2857-155">Kullanıcı girişi kötü amaçlı JavaScript veya diğer kötüye kullanım içerebilir.</span><span class="sxs-lookup"><span data-stu-id="c2857-155">User input might contain malicious JavaScript or other exploits.</span></span> <span data-ttu-id="c2857-156">Kullanıcı girişinin Temizleme işlemi zordur.</span><span class="sxs-lookup"><span data-stu-id="c2857-156">Sanitizing user input is difficult.</span></span> <span data-ttu-id="c2857-157">`HtmlHelper.Raw`Kullanıcı girişiyle kullanmaktan kaçının.</span><span class="sxs-lookup"><span data-stu-id="c2857-157">Avoid using `HtmlHelper.Raw` with user input.</span></span>

```cshtml
@Html.Raw("<span>Hello World</span>")
```

<span data-ttu-id="c2857-158">Kod, aşağıdaki HTML 'yi işler:</span><span class="sxs-lookup"><span data-stu-id="c2857-158">The code renders the following HTML:</span></span>

```html
<span>Hello World</span>
```

## <a name="razor-code-blocks"></a><span data-ttu-id="c2857-159">Razor kod blokları</span><span class="sxs-lookup"><span data-stu-id="c2857-159">Razor code blocks</span></span>

<span data-ttu-id="c2857-160">Razor kod blokları ile başlar `@` ve tarafından alınmıştır `{}` .</span><span class="sxs-lookup"><span data-stu-id="c2857-160">Razor code blocks start with `@` and are enclosed by `{}`.</span></span> <span data-ttu-id="c2857-161">İfadelerin aksine, kod blokları içindeki C# kodu işlenmez.</span><span class="sxs-lookup"><span data-stu-id="c2857-161">Unlike expressions, C# code inside code blocks isn't rendered.</span></span> <span data-ttu-id="c2857-162">Bir görünümdeki kod blokları ve ifadeler aynı kapsamı paylaşır ve sırasıyla tanımlanmıştır:</span><span class="sxs-lookup"><span data-stu-id="c2857-162">Code blocks and expressions in a view share the same scope and are defined in order:</span></span>

```cshtml
@{
    var quote = "The future depends on what you do today. - Mahatma Gandhi";
}

<p>@quote</p>

@{
    quote = "Hate cannot drive out hate, only love can do that. - Martin Luther King, Jr.";
}

<p>@quote</p>
```

<span data-ttu-id="c2857-163">Kod, aşağıdaki HTML 'yi işler:</span><span class="sxs-lookup"><span data-stu-id="c2857-163">The code renders the following HTML:</span></span>

```html
<p>The future depends on what you do today. - Mahatma Gandhi</p>
<p>Hate cannot drive out hate, only love can do that. - Martin Luther King, Jr.</p>
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c2857-164">Kod blokları ' nda, şablon oluşturma yöntemleri olarak kullanılacak biçimlendirme ile [yerel işlevler](/dotnet/csharp/programming-guide/classes-and-structs/local-functions) bildirin:</span><span class="sxs-lookup"><span data-stu-id="c2857-164">In code blocks, declare [local functions](/dotnet/csharp/programming-guide/classes-and-structs/local-functions) with markup to serve as templating methods:</span></span>

```cshtml
@{
    void RenderName(string name)
    {
        <p>Name: <strong>@name</strong></p>
    }

    RenderName("Mahatma Gandhi");
    RenderName("Martin Luther King, Jr.");
}
```

<span data-ttu-id="c2857-165">Kod, aşağıdaki HTML 'yi işler:</span><span class="sxs-lookup"><span data-stu-id="c2857-165">The code renders the following HTML:</span></span>

```html
<p>Name: <strong>Mahatma Gandhi</strong></p>
<p>Name: <strong>Martin Luther King, Jr.</strong></p>
```

::: moniker-end

### <a name="implicit-transitions"></a><span data-ttu-id="c2857-166">Örtük geçişler</span><span class="sxs-lookup"><span data-stu-id="c2857-166">Implicit transitions</span></span>

<span data-ttu-id="c2857-167">Bir kod bloğundaki varsayılan dil C# ' dir, ancak Razor sayfası HTML 'ye geri dönebilir:</span><span class="sxs-lookup"><span data-stu-id="c2857-167">The default language in a code block is C#, but the Razor Page can transition back to HTML:</span></span>

```cshtml
@{
    var inCSharp = true;
    <p>Now in HTML, was in C# @inCSharp</p>
}
```

### <a name="explicit-delimited-transition"></a><span data-ttu-id="c2857-168">Açık sınırlı geçiş</span><span class="sxs-lookup"><span data-stu-id="c2857-168">Explicit delimited transition</span></span>

<span data-ttu-id="c2857-169">HTML oluşturması gereken bir kod bloğunun alt bölümünü tanımlamak için, göstermek için karakterleri Razor etiketiyle çevreleyin `<text>` :</span><span class="sxs-lookup"><span data-stu-id="c2857-169">To define a subsection of a code block that should render HTML, surround the characters for rendering with the Razor `<text>` tag:</span></span>

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    <text>Name: @person.Name</text>
}
```

<span data-ttu-id="c2857-170">HTML etiketiyle çevrelenmiş HTML 'yi işlemek için bu yaklaşımı kullanın.</span><span class="sxs-lookup"><span data-stu-id="c2857-170">Use this approach to render HTML that isn't surrounded by an HTML tag.</span></span> <span data-ttu-id="c2857-171">HTML veya Razor etiketi olmadan Razor çalışma zamanı hatası oluşur.</span><span class="sxs-lookup"><span data-stu-id="c2857-171">Without an HTML or Razor tag, a Razor runtime error occurs.</span></span>

<span data-ttu-id="c2857-172">`<text>`Etiket, içerik işlerken boşluğu denetlemek için yararlıdır:</span><span class="sxs-lookup"><span data-stu-id="c2857-172">The `<text>` tag is useful to control whitespace when rendering content:</span></span>

* <span data-ttu-id="c2857-173">Yalnızca etiketi arasındaki içerik `<text>` işlenir.</span><span class="sxs-lookup"><span data-stu-id="c2857-173">Only the content between the `<text>` tag is rendered.</span></span>
* <span data-ttu-id="c2857-174">HTML çıktısında etiket görüntülenmeden önce veya sonra boşluk yok `<text>` .</span><span class="sxs-lookup"><span data-stu-id="c2857-174">No whitespace before or after the `<text>` tag appears in the HTML output.</span></span>

### <a name="explicit-line-transition"></a><span data-ttu-id="c2857-175">Açık satır geçişi</span><span class="sxs-lookup"><span data-stu-id="c2857-175">Explicit line transition</span></span>

<span data-ttu-id="c2857-176">Tüm satırın geri kalanını bir kod bloğu içinde HTML olarak işlemek için `@:` sözdizimi kullanın:</span><span class="sxs-lookup"><span data-stu-id="c2857-176">To render the rest of an entire line as HTML inside a code block, use `@:` syntax:</span></span>

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    @:Name: @person.Name
}
```

<span data-ttu-id="c2857-177">Kodda olmadan `@:` bir Razor çalışma zamanı hatası oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="c2857-177">Without the `@:` in the code, a Razor runtime error is generated.</span></span>

<span data-ttu-id="c2857-178">`@`Razor dosyasındaki fazla karakter, bloktaki daha sonra bulunan deyimlerde derleyici hatalarına neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="c2857-178">Extra `@` characters in a Razor file can cause compiler errors at statements later in the block.</span></span> <span data-ttu-id="c2857-179">Bu derleyici hatalarının anlaşılması zor olabilir çünkü asıl hata bildirilen hatadan önce oluşur.</span><span class="sxs-lookup"><span data-stu-id="c2857-179">These compiler errors can be difficult to understand because the actual error occurs before the reported error.</span></span> <span data-ttu-id="c2857-180">Bu hata, birden çok örtük/açık ifade tek bir kod bloğu içinde birleştirildikten sonra ortaktır.</span><span class="sxs-lookup"><span data-stu-id="c2857-180">This error is common after combining multiple implicit/explicit expressions into a single code block.</span></span>

## <a name="control-structures"></a><span data-ttu-id="c2857-181">Denetim yapıları</span><span class="sxs-lookup"><span data-stu-id="c2857-181">Control structures</span></span>

<span data-ttu-id="c2857-182">Denetim yapıları, kod bloklarının bir uzantısıdır.</span><span class="sxs-lookup"><span data-stu-id="c2857-182">Control structures are an extension of code blocks.</span></span> <span data-ttu-id="c2857-183">Kod bloklarının tüm yönleri (biçimlendirmeye geçme, satır içi C#) Ayrıca aşağıdaki yapılar için de geçerlidir:</span><span class="sxs-lookup"><span data-stu-id="c2857-183">All aspects of code blocks (transitioning to markup, inline C#) also apply to the following structures:</span></span>

### <a name="conditionals-if-else-if-else-and-switch"></a><span data-ttu-id="c2857-184">Koşul \@ varsa, Else, Else ve \@ anahtar</span><span class="sxs-lookup"><span data-stu-id="c2857-184">Conditionals \@if, else if, else, and \@switch</span></span>

<span data-ttu-id="c2857-185">`@if`kodun ne zaman çalışacağını denetler:</span><span class="sxs-lookup"><span data-stu-id="c2857-185">`@if` controls when code runs:</span></span>

```cshtml
@if (value % 2 == 0)
{
    <p>The value was even.</p>
}
```

<span data-ttu-id="c2857-186">`else`ve `else if` `@` sembol gerektirmez:</span><span class="sxs-lookup"><span data-stu-id="c2857-186">`else` and `else if` don't require the `@` symbol:</span></span>

```cshtml
@if (value % 2 == 0)
{
    <p>The value was even.</p>
}
else if (value >= 1337)
{
    <p>The value is large.</p>
}
else
{
    <p>The value is odd and small.</p>
}
```

<span data-ttu-id="c2857-187">Aşağıdaki biçimlendirme bir switch ifadesinin nasıl kullanılacağını göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="c2857-187">The following markup shows how to use a switch statement:</span></span>

```cshtml
@switch (value)
{
    case 1:
        <p>The value is 1!</p>
        break;
    case 1337:
        <p>Your number is 1337!</p>
        break;
    default:
        <p>Your number wasn't 1 or 1337.</p>
        break;
}
```

### <a name="looping-for-foreach-while-and-do-while"></a><span data-ttu-id="c2857-188">\@For döngüsü, \@ foreach, \@ while ve \@ Do</span><span class="sxs-lookup"><span data-stu-id="c2857-188">Looping \@for, \@foreach, \@while, and \@do while</span></span>

<span data-ttu-id="c2857-189">Şablonlu HTML, döngü denetim deyimleri ile oluşturulabilir.</span><span class="sxs-lookup"><span data-stu-id="c2857-189">Templated HTML can be rendered with looping control statements.</span></span> <span data-ttu-id="c2857-190">Bir kişi listesini işlemek için:</span><span class="sxs-lookup"><span data-stu-id="c2857-190">To render a list of people:</span></span>

```cshtml
@{
    var people = new Person[]
    {
          new Person("Weston", 33),
          new Person("Johnathon", 41),
          ...
    };
}
```

<span data-ttu-id="c2857-191">Aşağıdaki döngü deyimleri desteklenir:</span><span class="sxs-lookup"><span data-stu-id="c2857-191">The following looping statements are supported:</span></span>

`@for`

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>
}
```

`@foreach`

```cshtml
@foreach (var person in people)
{
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>
}
```

`@while`

```cshtml
@{ var i = 0; }
@while (i < people.Length)
{
    var person = people[i];
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>

    i++;
}
```

`@do while`

```cshtml
@{ var i = 0; }
@do
{
    var person = people[i];
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>

    i++;
} while (i < people.Length);
```

### <a name="compound-using"></a><span data-ttu-id="c2857-192">\@Kullanarak bileşik</span><span class="sxs-lookup"><span data-stu-id="c2857-192">Compound \@using</span></span>

<span data-ttu-id="c2857-193">C# ' de bir `using` nesne atılmış olduğundan emin olmak için bir ifade kullanılır.</span><span class="sxs-lookup"><span data-stu-id="c2857-193">In C#, a `using` statement is used to ensure an object is disposed.</span></span> <span data-ttu-id="c2857-194">Razor 'de, ek içerik içeren HTML Yardımcıları oluşturmak için aynı mekanizma kullanılır.</span><span class="sxs-lookup"><span data-stu-id="c2857-194">In Razor, the same mechanism is used to create HTML Helpers that contain additional content.</span></span> <span data-ttu-id="c2857-195">Aşağıdaki kodda, HTML Yardımcıları ifadesiyle bir etiketi işlerler `<form>` `@using` :</span><span class="sxs-lookup"><span data-stu-id="c2857-195">In the following code, HTML Helpers render a `<form>` tag with the `@using` statement:</span></span>

```cshtml
@using (Html.BeginForm())
{
    <div>
        Email: <input type="email" id="Email" value="">
        <button>Register</button>
    </div>
}
```

### <a name="try-catch-finally"></a><span data-ttu-id="c2857-196">\@TRY, catch, finally</span><span class="sxs-lookup"><span data-stu-id="c2857-196">\@try, catch, finally</span></span>

<span data-ttu-id="c2857-197">Özel durum işleme C# ile benzerdir:</span><span class="sxs-lookup"><span data-stu-id="c2857-197">Exception handling is similar to C#:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact7.cshtml)]

### <a name="lock"></a><span data-ttu-id="c2857-198">\@ine</span><span class="sxs-lookup"><span data-stu-id="c2857-198">\@lock</span></span>

<span data-ttu-id="c2857-199">Razor, kilit deyimleriyle önemli bölümleri koruma özelliğine sahiptir:</span><span class="sxs-lookup"><span data-stu-id="c2857-199">Razor has the capability to protect critical sections with lock statements:</span></span>

```cshtml
@lock (SomeLock)
{
    // Do critical section work
}
```

### <a name="comments"></a><span data-ttu-id="c2857-200">Yorumlar</span><span class="sxs-lookup"><span data-stu-id="c2857-200">Comments</span></span>

<span data-ttu-id="c2857-201">Razor, C# ve HTML açıklamalarını destekler:</span><span class="sxs-lookup"><span data-stu-id="c2857-201">Razor supports C# and HTML comments:</span></span>

```cshtml
@{
    /* C# comment */
    // Another C# comment
}
<!-- HTML comment -->
```

<span data-ttu-id="c2857-202">Kod, aşağıdaki HTML 'yi işler:</span><span class="sxs-lookup"><span data-stu-id="c2857-202">The code renders the following HTML:</span></span>

```html
<!-- HTML comment -->
```

<span data-ttu-id="c2857-203">Razor açıklamaları, Web sayfası işlenmeden önce sunucu tarafından kaldırılır.</span><span class="sxs-lookup"><span data-stu-id="c2857-203">Razor comments are removed by the server before the webpage is rendered.</span></span> <span data-ttu-id="c2857-204">Razor, `@*  *@` Yorumları sınırlandırmak için kullanır.</span><span class="sxs-lookup"><span data-stu-id="c2857-204">Razor uses `@*  *@` to delimit comments.</span></span> <span data-ttu-id="c2857-205">Aşağıdaki kod açıklama olarak belirlenir, bu nedenle sunucu herhangi bir biçimlendirme oluşturmaz:</span><span class="sxs-lookup"><span data-stu-id="c2857-205">The following code is commented out, so the server doesn't render any markup:</span></span>

```cshtml
@*
    @{
        /* C# comment */
        // Another C# comment
    }
    <!-- HTML comment -->
*@
```

## <a name="directives"></a><span data-ttu-id="c2857-206">Yönergeler</span><span class="sxs-lookup"><span data-stu-id="c2857-206">Directives</span></span>

<span data-ttu-id="c2857-207">Razor yönergeleri, simgeyi izleyen ayrılmış anahtar sözcüklerle örtük ifadelerle temsil edilir `@` .</span><span class="sxs-lookup"><span data-stu-id="c2857-207">Razor directives are represented by implicit expressions with reserved keywords following the `@` symbol.</span></span> <span data-ttu-id="c2857-208">Yönerge genellikle görünümün ayrıştırılma şeklini değiştirir veya farklı işlevleri sunar.</span><span class="sxs-lookup"><span data-stu-id="c2857-208">A directive typically changes the way a view is parsed or enables different functionality.</span></span>

<span data-ttu-id="c2857-209">Razor 'nin bir görünüm için nasıl kod üretdiğini anlamak, yönergelerin nasıl çalıştığını anlamayı kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="c2857-209">Understanding how Razor generates code for a view makes it easier to understand how directives work.</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact8.cshtml)]

<span data-ttu-id="c2857-210">Kod, aşağıdakine benzer bir sınıf oluşturur:</span><span class="sxs-lookup"><span data-stu-id="c2857-210">The code generates a class similar to the following:</span></span>

```csharp
public class _Views_Something_cshtml : RazorPage<dynamic>
{
    public override async Task ExecuteAsync()
    {
        var output = "Getting old ain't for wimps! - Anonymous";

        WriteLiteral("/r/n<div>Quote of the Day: ");
        Write(output);
        WriteLiteral("</div>");
    }
}
```

<span data-ttu-id="c2857-211">Bu makalenin ilerleyen kısımlarında, [bir görünüm için oluşturulan Razor C# sınıfını İnceleme](#inspect-the-razor-c-class-generated-for-a-view) bölümünde bu oluşturulan sınıfın nasıl görüntüleneceği açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="c2857-211">Later in this article, the section [Inspect the Razor C# class generated for a view](#inspect-the-razor-c-class-generated-for-a-view) explains how to view this generated class.</span></span>

### <a name="attribute"></a><span data-ttu-id="c2857-212">\@özniteliğe</span><span class="sxs-lookup"><span data-stu-id="c2857-212">\@attribute</span></span>

<span data-ttu-id="c2857-213">`@attribute`Yönergesi verilen özniteliği oluşturulan sayfanın veya görünümün sınıfına ekler.</span><span class="sxs-lookup"><span data-stu-id="c2857-213">The `@attribute` directive adds the given attribute to the class of the generated page or view.</span></span> <span data-ttu-id="c2857-214">Aşağıdaki örnek `[Authorize]` özniteliğini ekler:</span><span class="sxs-lookup"><span data-stu-id="c2857-214">The following example adds the `[Authorize]` attribute:</span></span>

```cshtml
@attribute [Authorize]
```

::: moniker range=">= aspnetcore-3.0"

### <a name="code"></a><span data-ttu-id="c2857-215">\@kodudur</span><span class="sxs-lookup"><span data-stu-id="c2857-215">\@code</span></span>

<span data-ttu-id="c2857-216">*Bu senaryo yalnızca Razor bileşenleri (. Razor) için geçerlidir.*</span><span class="sxs-lookup"><span data-stu-id="c2857-216">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="c2857-217">Blok, bir `@code` [Razor bileşeninin](xref:blazor/components) bir bileşene C# üyeleri (alanlar, Özellikler ve Yöntemler) eklemesini sağlar:</span><span class="sxs-lookup"><span data-stu-id="c2857-217">The `@code` block enables a [Razor component](xref:blazor/components) to add C# members (fields, properties, and methods) to a component:</span></span>

```razor
@code {
    // C# members (fields, properties, and methods)
}
```

<span data-ttu-id="c2857-218">Razor bileşenleri için, `@code` öğesinin diğer adı [`@functions`](#functions) ve önerilir `@functions` .</span><span class="sxs-lookup"><span data-stu-id="c2857-218">For Razor components, `@code` is an alias of [`@functions`](#functions) and recommended over `@functions`.</span></span> <span data-ttu-id="c2857-219">Birden çok `@code` blok izin verilir.</span><span class="sxs-lookup"><span data-stu-id="c2857-219">More than one `@code` block is permissible.</span></span>

::: moniker-end

### <a name="functions"></a><span data-ttu-id="c2857-220">\@lerdir</span><span class="sxs-lookup"><span data-stu-id="c2857-220">\@functions</span></span>

<span data-ttu-id="c2857-221">`@functions`Yönergesi, oluşturulan sınıfa C# üyeleri (alanlar, Özellikler ve Yöntemler) eklemeyi sağlar:</span><span class="sxs-lookup"><span data-stu-id="c2857-221">The `@functions` directive enables adding C# members (fields, properties, and methods) to the generated class:</span></span>

```cshtml
@functions {
    // C# members (fields, properties, and methods)
}
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c2857-222">[Razor bileşenlerinde](xref:blazor/components) `@code` `@functions` C# üyelerini eklemek için ' i kullanın.</span><span class="sxs-lookup"><span data-stu-id="c2857-222">In [Razor components](xref:blazor/components), use `@code` over `@functions` to add C# members.</span></span>

::: moniker-end

<span data-ttu-id="c2857-223">Örnek:</span><span class="sxs-lookup"><span data-stu-id="c2857-223">For example:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact6.cshtml)]

<span data-ttu-id="c2857-224">Kod, aşağıdaki HTML işaretlemesini oluşturur:</span><span class="sxs-lookup"><span data-stu-id="c2857-224">The code generates the following HTML markup:</span></span>

```html
<div>From method: Hello</div>
```

<span data-ttu-id="c2857-225">Aşağıdaki kod, üretilen Razor C# sınıfıdır:</span><span class="sxs-lookup"><span data-stu-id="c2857-225">The following code is the generated Razor C# class:</span></span>

[!code-csharp[](razor/sample/Classes/Views_Home_Test_cshtml.cs?range=1-19)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c2857-226">`@functions`Yöntemler, işaretlemelerdeki şablon oluşturma yöntemleri olarak görev yapar:</span><span class="sxs-lookup"><span data-stu-id="c2857-226">`@functions` methods serve as templating methods when they have markup:</span></span>

```cshtml
@{
    RenderName("Mahatma Gandhi");
    RenderName("Martin Luther King, Jr.");
}

@functions {
    private void RenderName(string name)
    {
        <p>Name: <strong>@name</strong></p>
    }
}
```

<span data-ttu-id="c2857-227">Kod, aşağıdaki HTML 'yi işler:</span><span class="sxs-lookup"><span data-stu-id="c2857-227">The code renders the following HTML:</span></span>

```html
<p>Name: <strong>Mahatma Gandhi</strong></p>
<p>Name: <strong>Martin Luther King, Jr.</strong></p>
```

### <a name="implements"></a><span data-ttu-id="c2857-228">\@uygular</span><span class="sxs-lookup"><span data-stu-id="c2857-228">\@implements</span></span>

<span data-ttu-id="c2857-229">`@implements`Yönergesi, oluşturulan sınıf için bir arabirim uygular.</span><span class="sxs-lookup"><span data-stu-id="c2857-229">The `@implements` directive implements an interface for the generated class.</span></span>

<span data-ttu-id="c2857-230">Aşağıdaki örnek, <xref:System.IDisposable?displayProperty=fullName> <xref:System.IDisposable.Dispose*> yönteminin çağrılabilmesi için uygular:</span><span class="sxs-lookup"><span data-stu-id="c2857-230">The following example implements <xref:System.IDisposable?displayProperty=fullName> so that the <xref:System.IDisposable.Dispose*> method can be called:</span></span>

```cshtml
@implements IDisposable

<h1>Example</h1>

@functions {
    private bool _isDisposed;

    ...

    public void Dispose() => _isDisposed = true;
}
```

::: moniker-end

### <a name="inherits"></a><span data-ttu-id="c2857-231">\@alıp</span><span class="sxs-lookup"><span data-stu-id="c2857-231">\@inherits</span></span>

<span data-ttu-id="c2857-232">`@inherits`Yönergesi, görünümün devraldığı sınıfın tam denetimini sağlar:</span><span class="sxs-lookup"><span data-stu-id="c2857-232">The `@inherits` directive provides full control of the class the view inherits:</span></span>

```cshtml
@inherits TypeNameOfClassToInheritFrom
```

<span data-ttu-id="c2857-233">Aşağıdaki kod özel bir Razor sayfası türüdür:</span><span class="sxs-lookup"><span data-stu-id="c2857-233">The following code is a custom Razor page type:</span></span>

[!code-csharp[](razor/sample/Classes/CustomRazorPage.cs)]

<span data-ttu-id="c2857-234">, `CustomText` Bir görünümde görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="c2857-234">The `CustomText` is displayed in a view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact10.cshtml)]

<span data-ttu-id="c2857-235">Kod, aşağıdaki HTML 'yi işler:</span><span class="sxs-lookup"><span data-stu-id="c2857-235">The code renders the following HTML:</span></span>

```html
<div>
    Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping
    a slop bucket on the street below.
</div>
```

 <span data-ttu-id="c2857-236">`@model``@inherits`aynı görünümde kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="c2857-236">`@model` and `@inherits` can be used in the same view.</span></span> <span data-ttu-id="c2857-237">`@inherits`görünümün içeri aktardığı bir *_ViewImports. cshtml* dosyasında olabilir:</span><span class="sxs-lookup"><span data-stu-id="c2857-237">`@inherits` can be in a *_ViewImports.cshtml* file that the view imports:</span></span>

[!code-cshtml[](razor/sample/Views/_ViewImportsModel.cshtml)]

<span data-ttu-id="c2857-238">Aşağıdaki kod, türü kesin belirlenmiş bir görünümün örneğidir:</span><span class="sxs-lookup"><span data-stu-id="c2857-238">The following code is an example of a strongly-typed view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Login1.cshtml)]

<span data-ttu-id="c2857-239">" rick@contoso.com " Modelde geçirilirse, görünüm AŞAĞıDAKI HTML işaretlemesini oluşturur:</span><span class="sxs-lookup"><span data-stu-id="c2857-239">If "rick@contoso.com" is passed in the model, the view generates the following HTML markup:</span></span>

```html
<div>The Login Email: rick@contoso.com</div>
<div>
    Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping
    a slop bucket on the street below.
</div>
```

### <a name="inject"></a><span data-ttu-id="c2857-240">\@eklenecek</span><span class="sxs-lookup"><span data-stu-id="c2857-240">\@inject</span></span>

<span data-ttu-id="c2857-241">`@inject`Yönergesi, Razor sayfasının [hizmet kapsayıcısından](xref:fundamentals/dependency-injection) bir hizmeti bir görünüme eklemesine olanak sağlar.</span><span class="sxs-lookup"><span data-stu-id="c2857-241">The `@inject` directive enables the Razor Page to inject a service from the [service container](xref:fundamentals/dependency-injection) into a view.</span></span> <span data-ttu-id="c2857-242">Daha fazla bilgi için bkz. [görünümlere bağımlılık ekleme](xref:mvc/views/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="c2857-242">For more information, see [Dependency injection into views](xref:mvc/views/dependency-injection).</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="layout"></a><span data-ttu-id="c2857-243">\@Düzenle</span><span class="sxs-lookup"><span data-stu-id="c2857-243">\@layout</span></span>

<span data-ttu-id="c2857-244">*Bu senaryo yalnızca Razor bileşenleri (. Razor) için geçerlidir.*</span><span class="sxs-lookup"><span data-stu-id="c2857-244">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="c2857-245">`@layout`Yönerge, Razor bileşeni için bir düzen belirtir.</span><span class="sxs-lookup"><span data-stu-id="c2857-245">The `@layout` directive specifies a layout for a Razor component.</span></span> <span data-ttu-id="c2857-246">Düzen bileşenleri kod yinelemeyi ve tutarsızlığın önüne geçmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="c2857-246">Layout components are used to avoid code duplication and inconsistency.</span></span> <span data-ttu-id="c2857-247">Daha fazla bilgi için bkz. <xref:blazor/layouts>.</span><span class="sxs-lookup"><span data-stu-id="c2857-247">For more information, see <xref:blazor/layouts>.</span></span>

::: moniker-end

### <a name="model"></a><span data-ttu-id="c2857-248">\@modelinizi</span><span class="sxs-lookup"><span data-stu-id="c2857-248">\@model</span></span>

<span data-ttu-id="c2857-249">*Bu senaryo yalnızca MVC görünümleri ve Razor Pages (. cshtml) için geçerlidir.*</span><span class="sxs-lookup"><span data-stu-id="c2857-249">*This scenario only applies to MVC views and Razor Pages (.cshtml).*</span></span>

<span data-ttu-id="c2857-250">`@model`Yönergesi bir görünüm veya sayfaya geçirilen modelin türünü belirtir:</span><span class="sxs-lookup"><span data-stu-id="c2857-250">The `@model` directive specifies the type of the model passed to a view or page:</span></span>

```cshtml
@model TypeNameOfModel
```

<span data-ttu-id="c2857-251">Bir ASP.NET Core MVC veya bireysel kullanıcı hesaplarıyla oluşturulan Razor Pages uygulama, *Görünümler/Account/Login. cshtml* aşağıdaki model bildirimini içerir:</span><span class="sxs-lookup"><span data-stu-id="c2857-251">In an ASP.NET Core MVC or Razor Pages app created with individual user accounts, *Views/Account/Login.cshtml* contains the following model declaration:</span></span>

```cshtml
@model LoginViewModel
```

<span data-ttu-id="c2857-252">Oluşturulan sınıf şuradan devralır `RazorPage<dynamic>` :</span><span class="sxs-lookup"><span data-stu-id="c2857-252">The class generated inherits from `RazorPage<dynamic>`:</span></span>

```csharp
public class _Views_Account_Login_cshtml : RazorPage<LoginViewModel>
```

<span data-ttu-id="c2857-253">Razor `Model` , görünüme geçirilen modele erişim için bir özellik sunar:</span><span class="sxs-lookup"><span data-stu-id="c2857-253">Razor exposes a `Model` property for accessing the model passed to the view:</span></span>

```cshtml
<div>The Login Email: @Model.Email</div>
```

<span data-ttu-id="c2857-254">`@model`Yönerge, özelliğin türünü belirtir `Model` .</span><span class="sxs-lookup"><span data-stu-id="c2857-254">The `@model` directive specifies the type of the `Model` property.</span></span> <span data-ttu-id="c2857-255">Yönergesi, `T` `RazorPage<T>` görünümün türettiği oluşturulan sınıfın öğesini belirtir.</span><span class="sxs-lookup"><span data-stu-id="c2857-255">The directive specifies the `T` in `RazorPage<T>` that the generated class that the view derives from.</span></span> <span data-ttu-id="c2857-256">`@model`Yönerge belirtilmemişse, `Model` özelliği türündedir `dynamic` .</span><span class="sxs-lookup"><span data-stu-id="c2857-256">If the `@model` directive isn't specified, the `Model` property is of type `dynamic`.</span></span> <span data-ttu-id="c2857-257">Daha fazla bilgi için bkz. [türü kesin belirlenmiş modeller ve @model anahtar sözcüğü](xref:tutorials/first-mvc-app/adding-model#strongly-typed-models-and-the--keyword).</span><span class="sxs-lookup"><span data-stu-id="c2857-257">For more information, see [Strongly typed models and the @model keyword](xref:tutorials/first-mvc-app/adding-model#strongly-typed-models-and-the--keyword).</span></span>

### <a name="namespace"></a><span data-ttu-id="c2857-258">\@uzayına</span><span class="sxs-lookup"><span data-stu-id="c2857-258">\@namespace</span></span>

<span data-ttu-id="c2857-259">`@namespace`Yönergesi:</span><span class="sxs-lookup"><span data-stu-id="c2857-259">The `@namespace` directive:</span></span>

* <span data-ttu-id="c2857-260">Oluşturulan Razor sayfası, MVC görünümü veya Razor bileşeni sınıfının ad alanını ayarlar.</span><span class="sxs-lookup"><span data-stu-id="c2857-260">Sets the namespace of the class of the generated Razor page, MVC view, or Razor component.</span></span>
* <span data-ttu-id="c2857-261">Bir sayfa, görünüm veya bileşen sınıfının kök türetilmiş ad alanlarını dizin ağacındaki en yakın içeri aktarmalar dosyasından, *_ViewImports. cshtml* (görünümler veya sayfalar) veya *_Imports. Razor* (Razor bileşenleri) olarak ayarlar.</span><span class="sxs-lookup"><span data-stu-id="c2857-261">Sets the root derived namespaces of a pages, views, or components classes from the closest imports file in the directory tree, *_ViewImports.cshtml* (views or pages) or *_Imports.razor* (Razor components).</span></span>

```cshtml
@namespace Your.Namespace.Here
```

<span data-ttu-id="c2857-262">Aşağıdaki tabloda gösterilen Razor Pages örneği için:</span><span class="sxs-lookup"><span data-stu-id="c2857-262">For the Razor Pages example shown in the following table:</span></span>

* <span data-ttu-id="c2857-263">Her sayfa *sayfaları/_ViewImports. cshtml*'yi içeri aktarır.</span><span class="sxs-lookup"><span data-stu-id="c2857-263">Each page imports *Pages/_ViewImports.cshtml*.</span></span>
* <span data-ttu-id="c2857-264">*Pages/_ViewImports. cshtml* içerir `@namespace Hello.World` .</span><span class="sxs-lookup"><span data-stu-id="c2857-264">*Pages/_ViewImports.cshtml* contains `@namespace Hello.World`.</span></span>
* <span data-ttu-id="c2857-265">Her sayfa `Hello.World` , ad alanının kökü olarak bulunur.</span><span class="sxs-lookup"><span data-stu-id="c2857-265">Each page has `Hello.World` as the root of it's namespace.</span></span>

| <span data-ttu-id="c2857-266">Sayfa</span><span class="sxs-lookup"><span data-stu-id="c2857-266">Page</span></span>                                        | <span data-ttu-id="c2857-267">Ad Alanı</span><span class="sxs-lookup"><span data-stu-id="c2857-267">Namespace</span></span>                             |
| ------------------------------------------- | ------------------------------------- |
| <span data-ttu-id="c2857-268">*Pages/Index. cshtml*</span><span class="sxs-lookup"><span data-stu-id="c2857-268">*Pages/Index.cshtml*</span></span>                        | `Hello.World`                         |
| <span data-ttu-id="c2857-269">*Sayfa/fazla sayfa/sayfa. cshtml*</span><span class="sxs-lookup"><span data-stu-id="c2857-269">*Pages/MorePages/Page.cshtml*</span></span>               | `Hello.World.MorePages`               |
| <span data-ttu-id="c2857-270">*Pages/te Pages/Evente Pages/Page. cshtml*</span><span class="sxs-lookup"><span data-stu-id="c2857-270">*Pages/MorePages/EvenMorePages/Page.cshtml*</span></span> | `Hello.World.MorePages.EvenMorePages` |

<span data-ttu-id="c2857-271">Yukarıdaki ilişkiler, MVC görünümleri ve Razor bileşenleriyle kullanılan dosyaları içeri aktarmak için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="c2857-271">The preceding relationships apply to import files used with MVC views and Razor components.</span></span>

<span data-ttu-id="c2857-272">Birden çok içeri aktarma dosyasında bir `@namespace` yönerge olduğunda, kök ad alanını ayarlamak için dizin ağacındaki sayfaya, görünüme veya bileşene en yakın dosya kullanılır.</span><span class="sxs-lookup"><span data-stu-id="c2857-272">When multiple import files have a `@namespace` directive, the file closest to the page, view, or component in the directory tree is used to set the root namespace.</span></span>

<span data-ttu-id="c2857-273">Yukarıdaki örnekteki *evente Pages* klasöründe bir içeri aktarmalar dosyası varsa `@namespace Another.Planet` (veya *sayfa/değer sayfaları/Evente Pages/Page. cshtml* dosyası içeriyorsa `@namespace Another.Planet` ), sonuç aşağıdaki tabloda gösterilir.</span><span class="sxs-lookup"><span data-stu-id="c2857-273">If the *EvenMorePages* folder in the preceding example has an imports file with `@namespace Another.Planet` (or the *Pages/MorePages/EvenMorePages/Page.cshtml* file contains `@namespace Another.Planet`), the result is shown in the following table.</span></span>

| <span data-ttu-id="c2857-274">Sayfa</span><span class="sxs-lookup"><span data-stu-id="c2857-274">Page</span></span>                                        | <span data-ttu-id="c2857-275">Ad Alanı</span><span class="sxs-lookup"><span data-stu-id="c2857-275">Namespace</span></span>               |
| ------------------------------------------- | ----------------------- |
| <span data-ttu-id="c2857-276">*Pages/Index. cshtml*</span><span class="sxs-lookup"><span data-stu-id="c2857-276">*Pages/Index.cshtml*</span></span>                        | `Hello.World`           |
| <span data-ttu-id="c2857-277">*Sayfa/fazla sayfa/sayfa. cshtml*</span><span class="sxs-lookup"><span data-stu-id="c2857-277">*Pages/MorePages/Page.cshtml*</span></span>               | `Hello.World.MorePages` |
| <span data-ttu-id="c2857-278">*Pages/te Pages/Evente Pages/Page. cshtml*</span><span class="sxs-lookup"><span data-stu-id="c2857-278">*Pages/MorePages/EvenMorePages/Page.cshtml*</span></span> | `Another.Planet`        |

### <a name="page"></a><span data-ttu-id="c2857-279">\@sayfasında</span><span class="sxs-lookup"><span data-stu-id="c2857-279">\@page</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c2857-280">`@page`Yönergesinin, göründüğü dosyanın türüne bağlı olarak farklı etkileri vardır.</span><span class="sxs-lookup"><span data-stu-id="c2857-280">The `@page` directive has different effects depending on the type of the file where it appears.</span></span> <span data-ttu-id="c2857-281">Yönergesi:</span><span class="sxs-lookup"><span data-stu-id="c2857-281">The directive:</span></span>

* <span data-ttu-id="c2857-282">İçindeki bir *. cshtml* dosyasında, dosyanın bir Razor sayfası olduğunu gösterir.</span><span class="sxs-lookup"><span data-stu-id="c2857-282">In in a *.cshtml* file indicates that the file is a Razor Page.</span></span> <span data-ttu-id="c2857-283">Daha fazla bilgi için bkz. [özel rotalar](xref:razor-pages/index#custom-routes) ve <xref:razor-pages/index> .</span><span class="sxs-lookup"><span data-stu-id="c2857-283">For more information, see [Custom routes](xref:razor-pages/index#custom-routes) and <xref:razor-pages/index>.</span></span>
* <span data-ttu-id="c2857-284">Bir Razor bileşeninin istekleri doğrudan işlemesini belirtir.</span><span class="sxs-lookup"><span data-stu-id="c2857-284">Specifies that a Razor component should handle requests directly.</span></span> <span data-ttu-id="c2857-285">Daha fazla bilgi için bkz. <xref:blazor/routing>.</span><span class="sxs-lookup"><span data-stu-id="c2857-285">For more information, see <xref:blazor/routing>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="c2857-286">`@page`Bir *. cshtml* dosyasının ilk satırındaki yönergesi, dosyanın bir Razor sayfası olduğunu gösterir.</span><span class="sxs-lookup"><span data-stu-id="c2857-286">The `@page` directive on the first line of a *.cshtml* file indicates that the file is a Razor Page.</span></span> <span data-ttu-id="c2857-287">Daha fazla bilgi için bkz. <xref:razor-pages/index>.</span><span class="sxs-lookup"><span data-stu-id="c2857-287">For more information, see <xref:razor-pages/index>.</span></span>

::: moniker-end

### <a name="section"></a><span data-ttu-id="c2857-288">\@kısmı</span><span class="sxs-lookup"><span data-stu-id="c2857-288">\@section</span></span>

<span data-ttu-id="c2857-289">*Bu senaryo yalnızca MVC görünümleri ve Razor Pages (. cshtml) için geçerlidir.*</span><span class="sxs-lookup"><span data-stu-id="c2857-289">*This scenario only applies to MVC views and Razor Pages (.cshtml).*</span></span>

<span data-ttu-id="c2857-290">`@section`Yönerge, HTML sayfasının farklı kısımlarında görünüm veya sayfaların içerik işlemesini sağlamak Için [MVC ve Razor Pages düzenleriyle](xref:mvc/views/layout) birlikte kullanılır.</span><span class="sxs-lookup"><span data-stu-id="c2857-290">The `@section` directive is used in conjunction with [MVC and Razor Pages layouts](xref:mvc/views/layout) to enable views or pages to render content in different parts of the HTML page.</span></span> <span data-ttu-id="c2857-291">Daha fazla bilgi için bkz. <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="c2857-291">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="using"></a><span data-ttu-id="c2857-292">\@kullanma</span><span class="sxs-lookup"><span data-stu-id="c2857-292">\@using</span></span>

<span data-ttu-id="c2857-293">`@using`Yönergesi, `using` oluşturulan görünüme C# yönergesini ekler:</span><span class="sxs-lookup"><span data-stu-id="c2857-293">The `@using` directive adds the C# `using` directive to the generated view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact9.cshtml)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c2857-294">[Razor bileşenlerinde](xref:blazor/components) `@using` Ayrıca hangi bileşenlerin kapsamda olduğunu da kontrol eder.</span><span class="sxs-lookup"><span data-stu-id="c2857-294">In [Razor components](xref:blazor/components), `@using` also controls which components are in scope.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="directive-attributes"></a><span data-ttu-id="c2857-295">Yönerge öznitelikleri</span><span class="sxs-lookup"><span data-stu-id="c2857-295">Directive attributes</span></span>

<span data-ttu-id="c2857-296">Razor yönergesi öznitelikleri, simgeyi izleyen ayrılmış anahtar sözcüklerle örtük ifadelerle temsil edilir `@` .</span><span class="sxs-lookup"><span data-stu-id="c2857-296">Razor directive attributes are represented by implicit expressions with reserved keywords following the `@` symbol.</span></span> <span data-ttu-id="c2857-297">Bir Directive özniteliği genellikle bir öğenin ayrıştırılma şeklini değiştirir veya farklı işlevlere izin vermez.</span><span class="sxs-lookup"><span data-stu-id="c2857-297">A directive attribute typically changes the way an element is parsed or enables different functionality.</span></span>

### <a name="attributes"></a><span data-ttu-id="c2857-298">\@özelliklerine</span><span class="sxs-lookup"><span data-stu-id="c2857-298">\@attributes</span></span>

<span data-ttu-id="c2857-299">*Bu senaryo yalnızca Razor bileşenleri (. Razor) için geçerlidir.*</span><span class="sxs-lookup"><span data-stu-id="c2857-299">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="c2857-300">`@attributes`bir bileşenin bildirilmeyen öznitelikleri işlemesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="c2857-300">`@attributes` allows a component to render non-declared attributes.</span></span> <span data-ttu-id="c2857-301">Daha fazla bilgi için bkz. <xref:blazor/components#attribute-splatting-and-arbitrary-parameters>.</span><span class="sxs-lookup"><span data-stu-id="c2857-301">For more information, see <xref:blazor/components#attribute-splatting-and-arbitrary-parameters>.</span></span>

### <a name="bind"></a><span data-ttu-id="c2857-302">\@bağladığınızda</span><span class="sxs-lookup"><span data-stu-id="c2857-302">\@bind</span></span>

<span data-ttu-id="c2857-303">*Bu senaryo yalnızca Razor bileşenleri (. Razor) için geçerlidir.*</span><span class="sxs-lookup"><span data-stu-id="c2857-303">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="c2857-304">Bileşenlerdeki veri bağlama, `@bind` özniteliğiyle gerçekleştirilir.</span><span class="sxs-lookup"><span data-stu-id="c2857-304">Data binding in components is accomplished with the `@bind` attribute.</span></span> <span data-ttu-id="c2857-305">Daha fazla bilgi için bkz. <xref:blazor/data-binding>.</span><span class="sxs-lookup"><span data-stu-id="c2857-305">For more information, see <xref:blazor/data-binding>.</span></span>

### <a name="onevent"></a><span data-ttu-id="c2857-306">\@{EVENT} üzerinde</span><span class="sxs-lookup"><span data-stu-id="c2857-306">\@on{EVENT}</span></span>

<span data-ttu-id="c2857-307">*Bu senaryo yalnızca Razor bileşenleri (. Razor) için geçerlidir.*</span><span class="sxs-lookup"><span data-stu-id="c2857-307">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="c2857-308">Razor, bileşenler için olay işleme özellikleri sağlar.</span><span class="sxs-lookup"><span data-stu-id="c2857-308">Razor provides event handling features for components.</span></span> <span data-ttu-id="c2857-309">Daha fazla bilgi için bkz. <xref:blazor/event-handling>.</span><span class="sxs-lookup"><span data-stu-id="c2857-309">For more information, see <xref:blazor/event-handling>.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.1"

### <a name="oneventpreventdefault"></a><span data-ttu-id="c2857-310">\@{EVENT} üzerinde:p reventDefault</span><span class="sxs-lookup"><span data-stu-id="c2857-310">\@on{EVENT}:preventDefault</span></span>

<span data-ttu-id="c2857-311">*Bu senaryo yalnızca Razor bileşenleri (. Razor) için geçerlidir.*</span><span class="sxs-lookup"><span data-stu-id="c2857-311">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="c2857-312">Olay için varsayılan eylemi engeller.</span><span class="sxs-lookup"><span data-stu-id="c2857-312">Prevents the default action for the event.</span></span>

### <a name="oneventstoppropagation"></a><span data-ttu-id="c2857-313">\@{EVENT}: Stopyayma</span><span class="sxs-lookup"><span data-stu-id="c2857-313">\@on{EVENT}:stopPropagation</span></span>

<span data-ttu-id="c2857-314">*Bu senaryo yalnızca Razor bileşenleri (. Razor) için geçerlidir.*</span><span class="sxs-lookup"><span data-stu-id="c2857-314">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="c2857-315">Olay için olay yaymayı sonlandırır.</span><span class="sxs-lookup"><span data-stu-id="c2857-315">Stops event propagation for the event.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="key"></a><span data-ttu-id="c2857-316">\@anahtar</span><span class="sxs-lookup"><span data-stu-id="c2857-316">\@key</span></span>

<span data-ttu-id="c2857-317">*Bu senaryo yalnızca Razor bileşenleri (. Razor) için geçerlidir.*</span><span class="sxs-lookup"><span data-stu-id="c2857-317">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="c2857-318">`@key`Directive özniteliği, anahtar değerine göre öğelerin veya bileşenlerin korunmasını güvence altına almak için bileşenlerin algoritma almasına neden olur.</span><span class="sxs-lookup"><span data-stu-id="c2857-318">The `@key` directive attribute causes the components diffing algorithm to guarantee preservation of elements or components based on the key's value.</span></span> <span data-ttu-id="c2857-319">Daha fazla bilgi için bkz. <xref:blazor/components#use-key-to-control-the-preservation-of-elements-and-components>.</span><span class="sxs-lookup"><span data-stu-id="c2857-319">For more information, see <xref:blazor/components#use-key-to-control-the-preservation-of-elements-and-components>.</span></span>

### <a name="ref"></a><span data-ttu-id="c2857-320">\@ref</span><span class="sxs-lookup"><span data-stu-id="c2857-320">\@ref</span></span>

<span data-ttu-id="c2857-321">*Bu senaryo yalnızca Razor bileşenleri (. Razor) için geçerlidir.*</span><span class="sxs-lookup"><span data-stu-id="c2857-321">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="c2857-322">Bileşen başvuruları ( `@ref` ) bir bileşen örneğine başvurmak için bir yol sağlar, böylece bu örneğe komut verebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="c2857-322">Component references (`@ref`) provide a way to reference a component instance so that you can issue commands to that instance.</span></span> <span data-ttu-id="c2857-323">Daha fazla bilgi için bkz. <xref:blazor/components#capture-references-to-components>.</span><span class="sxs-lookup"><span data-stu-id="c2857-323">For more information, see <xref:blazor/components#capture-references-to-components>.</span></span>

### <a name="typeparam"></a><span data-ttu-id="c2857-324">\@typeparam</span><span class="sxs-lookup"><span data-stu-id="c2857-324">\@typeparam</span></span>

<span data-ttu-id="c2857-325">*Bu senaryo yalnızca Razor bileşenleri (. Razor) için geçerlidir.*</span><span class="sxs-lookup"><span data-stu-id="c2857-325">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="c2857-326">`@typeparam`Yönergesi, oluşturulan bileşen sınıfı için genel bir tür parametresi bildirir.</span><span class="sxs-lookup"><span data-stu-id="c2857-326">The `@typeparam` directive declares a generic type parameter for the generated component class.</span></span> <span data-ttu-id="c2857-327">Daha fazla bilgi için bkz. <xref:blazor/templated-components#generic-typed-components>.</span><span class="sxs-lookup"><span data-stu-id="c2857-327">For more information, see <xref:blazor/templated-components#generic-typed-components>.</span></span>

::: moniker-end

## <a name="templated-razor-delegates"></a><span data-ttu-id="c2857-328">Şablonlu Razor temsilcileri</span><span class="sxs-lookup"><span data-stu-id="c2857-328">Templated Razor delegates</span></span>

<span data-ttu-id="c2857-329">Razor şablonları aşağıdaki biçimde bir UI parçacığı tanımlamanızı sağlar:</span><span class="sxs-lookup"><span data-stu-id="c2857-329">Razor templates allow you to define a UI snippet with the following format:</span></span>

```cshtml
@<tag>...</tag>
```

<span data-ttu-id="c2857-330">Aşağıdaki örnekte, bir şablonlu Razor temsilcisinin bir olarak nasıl belirtildiği gösterilmektedir <xref:System.Func%602> .</span><span class="sxs-lookup"><span data-stu-id="c2857-330">The following example illustrates how to specify a templated Razor delegate as a <xref:System.Func%602>.</span></span> <span data-ttu-id="c2857-331">[Dinamik tür](/dotnet/csharp/programming-guide/types/using-type-dynamic) , temsilcinin sarmalayan yönteminin parametresi için belirtilir.</span><span class="sxs-lookup"><span data-stu-id="c2857-331">The [dynamic type](/dotnet/csharp/programming-guide/types/using-type-dynamic) is specified for the parameter of the method that the delegate encapsulates.</span></span> <span data-ttu-id="c2857-332">Bir [nesne türü](/dotnet/csharp/language-reference/keywords/object) , temsilcinin dönüş değeri olarak belirtilir.</span><span class="sxs-lookup"><span data-stu-id="c2857-332">An [object type](/dotnet/csharp/language-reference/keywords/object) is specified as the return value of the delegate.</span></span> <span data-ttu-id="c2857-333">Şablon, bir özelliğine sahip olan bir ile kullanılır <xref:System.Collections.Generic.List%601> `Pet` `Name` .</span><span class="sxs-lookup"><span data-stu-id="c2857-333">The template is used with a <xref:System.Collections.Generic.List%601> of `Pet` that has a `Name` property.</span></span>

```csharp
public class Pet
{
    public string Name { get; set; }
}
```

```cshtml
@{
    Func<dynamic, object> petTemplate = @<p>You have a pet named <strong>@item.Name</strong>.</p>;

    var pets = new List<Pet>
    {
        new Pet { Name = "Rin Tin Tin" },
        new Pet { Name = "Mr. Bigglesworth" },
        new Pet { Name = "K-9" }
    };
}
```

<span data-ttu-id="c2857-334">Şablon, `pets` bir ifade tarafından sağlanan ile işlenir `foreach` :</span><span class="sxs-lookup"><span data-stu-id="c2857-334">The template is rendered with `pets` supplied by a `foreach` statement:</span></span>

```cshtml
@foreach (var pet in pets)
{
    @petTemplate(pet)
}
```

<span data-ttu-id="c2857-335">İşlenmiş çıkış:</span><span class="sxs-lookup"><span data-stu-id="c2857-335">Rendered output:</span></span>

```html
<p>You have a pet named <strong>Rin Tin Tin</strong>.</p>
<p>You have a pet named <strong>Mr. Bigglesworth</strong>.</p>
<p>You have a pet named <strong>K-9</strong>.</p>
```

<span data-ttu-id="c2857-336">Ayrıca bir yönteme bağımsız değişken olarak bir satır içi Razor şablonu da sağlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="c2857-336">You can also supply an inline Razor template as an argument to a method.</span></span> <span data-ttu-id="c2857-337">Aşağıdaki örnekte, `Repeat` yöntemi Razor şablonu alır.</span><span class="sxs-lookup"><span data-stu-id="c2857-337">In the following example, the `Repeat` method receives a Razor template.</span></span> <span data-ttu-id="c2857-338">Yöntemi, bir listeden sağlanan öğelerin tekrarlandığı HTML içeriğini oluşturmak için şablonunu kullanır:</span><span class="sxs-lookup"><span data-stu-id="c2857-338">The method uses the template to produce HTML content with repeats of items supplied from a list:</span></span>

```cshtml
@using Microsoft.AspNetCore.Html

@functions {
    public static IHtmlContent Repeat(IEnumerable<dynamic> items, int times,
        Func<dynamic, IHtmlContent> template)
    {
        var html = new HtmlContentBuilder();

        foreach (var item in items)
        {
            for (var i = 0; i < times; i++)
            {
                html.AppendHtml(template(item));
            }
        }

        return html;
    }
}
```

<span data-ttu-id="c2857-339">Önceki örnekteki Evcil hayvanlar 'ların listesini kullanarak, `Repeat` yöntemi ile çağrılır:</span><span class="sxs-lookup"><span data-stu-id="c2857-339">Using the list of pets from the prior example, the `Repeat` method is called with:</span></span>

* <span data-ttu-id="c2857-340"><xref:System.Collections.Generic.List%601>`Pet`.</span><span class="sxs-lookup"><span data-stu-id="c2857-340"><xref:System.Collections.Generic.List%601> of `Pet`.</span></span>
* <span data-ttu-id="c2857-341">Her evcil hayvan kaç kez tekrarlanacak.</span><span class="sxs-lookup"><span data-stu-id="c2857-341">Number of times to repeat each pet.</span></span>
* <span data-ttu-id="c2857-342">Sıralanmamış bir listenin liste öğeleri için kullanılacak satır içi şablon.</span><span class="sxs-lookup"><span data-stu-id="c2857-342">Inline template to use for the list items of an unordered list.</span></span>

```cshtml
<ul>
    @Repeat(pets, 3, @<li>@item.Name</li>)
</ul>
```

<span data-ttu-id="c2857-343">İşlenmiş çıkış:</span><span class="sxs-lookup"><span data-stu-id="c2857-343">Rendered output:</span></span>

```html
<ul>
    <li>Rin Tin Tin</li>
    <li>Rin Tin Tin</li>
    <li>Rin Tin Tin</li>
    <li>Mr. Bigglesworth</li>
    <li>Mr. Bigglesworth</li>
    <li>Mr. Bigglesworth</li>
    <li>K-9</li>
    <li>K-9</li>
    <li>K-9</li>
</ul>
```

## <a name="tag-helpers"></a><span data-ttu-id="c2857-344">Etiket Yardımcıları</span><span class="sxs-lookup"><span data-stu-id="c2857-344">Tag Helpers</span></span>

<span data-ttu-id="c2857-345">*Bu senaryo yalnızca MVC görünümleri ve Razor Pages (. cshtml) için geçerlidir.*</span><span class="sxs-lookup"><span data-stu-id="c2857-345">*This scenario only applies to MVC views and Razor Pages (.cshtml).*</span></span>

<span data-ttu-id="c2857-346">[Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro)ile ilgili üç yönergeler vardır.</span><span class="sxs-lookup"><span data-stu-id="c2857-346">There are three directives that pertain to [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span>

| <span data-ttu-id="c2857-347">Deki</span><span class="sxs-lookup"><span data-stu-id="c2857-347">Directive</span></span> | <span data-ttu-id="c2857-348">İşlev</span><span class="sxs-lookup"><span data-stu-id="c2857-348">Function</span></span> |
| --------- | -------- |
| [`@addTagHelper`](xref:mvc/views/tag-helpers/intro#add-helper-label) | <span data-ttu-id="c2857-349">Etiket yardımcılarını bir görünüm için kullanılabilir hale getirir.</span><span class="sxs-lookup"><span data-stu-id="c2857-349">Makes Tag Helpers available to a view.</span></span> |
| [`@removeTagHelper`](xref:mvc/views/tag-helpers/intro#remove-razor-directives-label) | <span data-ttu-id="c2857-350">Daha önce bir görünümden eklenen etiket yardımcıları kaldırır.</span><span class="sxs-lookup"><span data-stu-id="c2857-350">Removes Tag Helpers previously added from a view.</span></span> |
| [`@tagHelperPrefix`](xref:mvc/views/tag-helpers/intro#prefix-razor-directives-label) | <span data-ttu-id="c2857-351">Etiket Yardımcısı desteğini etkinleştirmek ve etiket Yardımcısı kullanımını açık hale getirmek için bir etiket ön eki belirtir.</span><span class="sxs-lookup"><span data-stu-id="c2857-351">Specifies a tag prefix to enable Tag Helper support and to make Tag Helper usage explicit.</span></span> |

## <a name="razor-reserved-keywords"></a>Razor<span data-ttu-id="c2857-352">ayrılmış anahtar sözcükler</span><span class="sxs-lookup"><span data-stu-id="c2857-352"> reserved keywords</span></span>

### <a name="razor-keywords"></a>Razor<span data-ttu-id="c2857-353">lerimi</span><span class="sxs-lookup"><span data-stu-id="c2857-353"> keywords</span></span>

* <span data-ttu-id="c2857-354">sayfa (ASP.NET Core 2,1 veya üzeri bir sürüm gerektirir)</span><span class="sxs-lookup"><span data-stu-id="c2857-354">page (Requires ASP.NET Core 2.1 or later)</span></span>
* <span data-ttu-id="c2857-355">ad alanı</span><span class="sxs-lookup"><span data-stu-id="c2857-355">namespace</span></span>
* <span data-ttu-id="c2857-356"> işlevleri</span><span class="sxs-lookup"><span data-stu-id="c2857-356">functions</span></span>
* <span data-ttu-id="c2857-357">alıp</span><span class="sxs-lookup"><span data-stu-id="c2857-357">inherits</span></span>
* <span data-ttu-id="c2857-358">model</span><span class="sxs-lookup"><span data-stu-id="c2857-358">model</span></span>
* <span data-ttu-id="c2857-359">section</span><span class="sxs-lookup"><span data-stu-id="c2857-359">section</span></span>
* <span data-ttu-id="c2857-360">yardımcı (Şu anda ASP.NET Core tarafından desteklenmiyor)</span><span class="sxs-lookup"><span data-stu-id="c2857-360">helper (Not currently supported by ASP.NET Core)</span></span>

Razor<span data-ttu-id="c2857-361">anahtar kelimelerden kaçışın `@(Razor Keyword)` (örneğin, `@(functions)` ).</span><span class="sxs-lookup"><span data-stu-id="c2857-361"> keywords are escaped with `@(Razor Keyword)` (for example, `@(functions)`).</span></span>

### <a name="c-razor-keywords"></a><span data-ttu-id="c2857-362">C# Razor anahtar sözcükleri</span><span class="sxs-lookup"><span data-stu-id="c2857-362">C# Razor keywords</span></span>

* <span data-ttu-id="c2857-363">büyük/küçük harf</span><span class="sxs-lookup"><span data-stu-id="c2857-363">case</span></span>
* <span data-ttu-id="c2857-364">do</span><span class="sxs-lookup"><span data-stu-id="c2857-364">do</span></span>
* <span data-ttu-id="c2857-365">default</span><span class="sxs-lookup"><span data-stu-id="c2857-365">default</span></span>
* <span data-ttu-id="c2857-366">:</span><span class="sxs-lookup"><span data-stu-id="c2857-366">for</span></span>
* <span data-ttu-id="c2857-367">foreach</span><span class="sxs-lookup"><span data-stu-id="c2857-367">foreach</span></span>
* <span data-ttu-id="c2857-368">if</span><span class="sxs-lookup"><span data-stu-id="c2857-368">if</span></span>
* <span data-ttu-id="c2857-369">else</span><span class="sxs-lookup"><span data-stu-id="c2857-369">else</span></span>
* <span data-ttu-id="c2857-370">lock</span><span class="sxs-lookup"><span data-stu-id="c2857-370">lock</span></span>
* <span data-ttu-id="c2857-371">switch</span><span class="sxs-lookup"><span data-stu-id="c2857-371">switch</span></span>
* <span data-ttu-id="c2857-372">Deneme</span><span class="sxs-lookup"><span data-stu-id="c2857-372">try</span></span>
* <span data-ttu-id="c2857-373">yakalaya</span><span class="sxs-lookup"><span data-stu-id="c2857-373">catch</span></span>
* <span data-ttu-id="c2857-374">finally</span><span class="sxs-lookup"><span data-stu-id="c2857-374">finally</span></span>
* <span data-ttu-id="c2857-375">kullanma</span><span class="sxs-lookup"><span data-stu-id="c2857-375">using</span></span>
* <span data-ttu-id="c2857-376">while</span><span class="sxs-lookup"><span data-stu-id="c2857-376">while</span></span>

<span data-ttu-id="c2857-377">C# Razor anahtar sözcükleri ile çift kaçış olmalıdır `@(@C# Razor Keyword)` (örneğin, `@(@case)` ).</span><span class="sxs-lookup"><span data-stu-id="c2857-377">C# Razor keywords must be double-escaped with `@(@C# Razor Keyword)` (for example, `@(@case)`).</span></span> <span data-ttu-id="c2857-378">İlki `@` Razor Ayrıştırıcıdan çıkar.</span><span class="sxs-lookup"><span data-stu-id="c2857-378">The first `@` escapes the Razor parser.</span></span> <span data-ttu-id="c2857-379">İkincisi, `@` C# ayrıştırıcısının çıkar.</span><span class="sxs-lookup"><span data-stu-id="c2857-379">The second `@` escapes the C# parser.</span></span>

### <a name="reserved-keywords-not-used-by-razor"></a><span data-ttu-id="c2857-380">Ayrılmış anahtar sözcükler tarafından kullanılmıyorRazor</span><span class="sxs-lookup"><span data-stu-id="c2857-380">Reserved keywords not used by Razor</span></span>

* <span data-ttu-id="c2857-381">sınıf</span><span class="sxs-lookup"><span data-stu-id="c2857-381">class</span></span>

## <a name="inspect-the-razor-c-class-generated-for-a-view"></a><span data-ttu-id="c2857-382">RazorBir görünüm için oluşturulan C# sınıfını inceleyin</span><span class="sxs-lookup"><span data-stu-id="c2857-382">Inspect the Razor C# class generated for a view</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="c2857-383">.NET Core SDK 2,1 veya sonraki bir sürümü ile [ Razor SDK](xref:razor-pages/sdk) , dosyaların derlemesini işler Razor .</span><span class="sxs-lookup"><span data-stu-id="c2857-383">With .NET Core SDK 2.1 or later, the [Razor SDK](xref:razor-pages/sdk) handles compilation of Razor files.</span></span> <span data-ttu-id="c2857-384">Bir proje oluştururken, Razor SDK proje kökünde bir \*obj/<build_configuration>/<target_framework_moniker>/ Razor \* dizin oluşturur.</span><span class="sxs-lookup"><span data-stu-id="c2857-384">When building a project, the Razor SDK generates an *obj/<build_configuration>/<target_framework_moniker>/Razor* directory in the project root.</span></span> <span data-ttu-id="c2857-385">Dizin içindeki dizin yapısı *Razor* , projenin dizin yapısını yansıtır.</span><span class="sxs-lookup"><span data-stu-id="c2857-385">The directory structure within the *Razor* directory mirrors the project's directory structure.</span></span>

<span data-ttu-id="c2857-386">ASP.NET Core 2,1 Razor sayfaları projesinde .NET Core 2,1 ' i hedefleyen aşağıdaki dizin yapısını göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="c2857-386">Consider the following directory structure in an ASP.NET Core 2.1 Razor Pages project targeting .NET Core 2.1:</span></span>

* <span data-ttu-id="c2857-387">**Alanları**</span><span class="sxs-lookup"><span data-stu-id="c2857-387">**Areas/**</span></span>
  * <span data-ttu-id="c2857-388">**Yöneticileri**</span><span class="sxs-lookup"><span data-stu-id="c2857-388">**Admin/**</span></span>
    * <span data-ttu-id="c2857-389">**Sayfaları**</span><span class="sxs-lookup"><span data-stu-id="c2857-389">**Pages/**</span></span>
      * <span data-ttu-id="c2857-390">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="c2857-390">*Index.cshtml*</span></span>
      * <span data-ttu-id="c2857-391">*Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="c2857-391">*Index.cshtml.cs*</span></span>
* <span data-ttu-id="c2857-392">**Sayfaları**</span><span class="sxs-lookup"><span data-stu-id="c2857-392">**Pages/**</span></span>
  * <span data-ttu-id="c2857-393">**Paylaşılan**</span><span class="sxs-lookup"><span data-stu-id="c2857-393">**Shared/**</span></span>
    * <span data-ttu-id="c2857-394">*_Layout.cshtml*</span><span class="sxs-lookup"><span data-stu-id="c2857-394">*_Layout.cshtml*</span></span>
  * <span data-ttu-id="c2857-395">*_ViewImports. cshtml*</span><span class="sxs-lookup"><span data-stu-id="c2857-395">*_ViewImports.cshtml*</span></span>
  * <span data-ttu-id="c2857-396">*_ViewStart. cshtml*</span><span class="sxs-lookup"><span data-stu-id="c2857-396">*_ViewStart.cshtml*</span></span>
  * <span data-ttu-id="c2857-397">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="c2857-397">*Index.cshtml*</span></span>
  * <span data-ttu-id="c2857-398">*Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="c2857-398">*Index.cshtml.cs*</span></span>

<span data-ttu-id="c2857-399">Projenin *hata ayıklama* yapılandırmasında oluşturulması aşağıdaki *obj* dizinini verir:</span><span class="sxs-lookup"><span data-stu-id="c2857-399">Building the project in *Debug* configuration yields the following *obj* directory:</span></span>

* <span data-ttu-id="c2857-400">**nesnesi**</span><span class="sxs-lookup"><span data-stu-id="c2857-400">**obj/**</span></span>
  * <span data-ttu-id="c2857-401">**H**</span><span class="sxs-lookup"><span data-stu-id="c2857-401">**Debug/**</span></span>
    * <span data-ttu-id="c2857-402">**netcoreapp 2.1/**</span><span class="sxs-lookup"><span data-stu-id="c2857-402">**netcoreapp2.1/**</span></span>
      * **Razor/**
        * <span data-ttu-id="c2857-403">**Alanları**</span><span class="sxs-lookup"><span data-stu-id="c2857-403">**Areas/**</span></span>
          * <span data-ttu-id="c2857-404">**Yöneticileri**</span><span class="sxs-lookup"><span data-stu-id="c2857-404">**Admin/**</span></span>
            * <span data-ttu-id="c2857-405">**Sayfaları**</span><span class="sxs-lookup"><span data-stu-id="c2857-405">**Pages/**</span></span>
              * <span data-ttu-id="c2857-406">*Index.g.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="c2857-406">*Index.g.cshtml.cs*</span></span>
        * <span data-ttu-id="c2857-407">**Sayfaları**</span><span class="sxs-lookup"><span data-stu-id="c2857-407">**Pages/**</span></span>
          * <span data-ttu-id="c2857-408">**Paylaşılan**</span><span class="sxs-lookup"><span data-stu-id="c2857-408">**Shared/**</span></span>
            * <span data-ttu-id="c2857-409">*_Layout. g. cshtml. cs*</span><span class="sxs-lookup"><span data-stu-id="c2857-409">*_Layout.g.cshtml.cs*</span></span>
          * <span data-ttu-id="c2857-410">*_ViewImports. g. cshtml. cs*</span><span class="sxs-lookup"><span data-stu-id="c2857-410">*_ViewImports.g.cshtml.cs*</span></span>
          * <span data-ttu-id="c2857-411">*_ViewStart. g. cshtml. cs*</span><span class="sxs-lookup"><span data-stu-id="c2857-411">*_ViewStart.g.cshtml.cs*</span></span>
          * <span data-ttu-id="c2857-412">*Index.g.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="c2857-412">*Index.g.cshtml.cs*</span></span>

<span data-ttu-id="c2857-413">*Pages/Index. cshtml*için oluşturulan sınıfı görüntülemek için *obj/Debug/netcoreapp 2.1/ Razor /Pages/Index.g.cshtml.cs*öğesini açın.</span><span class="sxs-lookup"><span data-stu-id="c2857-413">To view the generated class for *Pages/Index.cshtml*, open *obj/Debug/netcoreapp2.1/Razor/Pages/Index.g.cshtml.cs*.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="c2857-414">Aşağıdaki Sınıfı ASP.NET Core MVC projesine ekleyin:</span><span class="sxs-lookup"><span data-stu-id="c2857-414">Add the following class to the ASP.NET Core MVC project:</span></span>

[!code-csharp[](razor/sample/Utilities/CustomTemplateEngine.cs)]

<span data-ttu-id="c2857-415">' De `Startup.ConfigureServices` , `RazorTemplateEngine` ile MVC tarafından eklenen ' i şu sınıfla geçersiz kılın `CustomTemplateEngine` :</span><span class="sxs-lookup"><span data-stu-id="c2857-415">In `Startup.ConfigureServices`, override the `RazorTemplateEngine` added by MVC with the `CustomTemplateEngine` class:</span></span>

[!code-csharp[](razor/sample/Startup.cs?highlight=4&range=10-14)]

<span data-ttu-id="c2857-416">Bildiriminde bir kesme noktası ayarlayın `return csharpDocument;` `CustomTemplateEngine` .</span><span class="sxs-lookup"><span data-stu-id="c2857-416">Set a breakpoint on the `return csharpDocument;` statement of `CustomTemplateEngine`.</span></span> <span data-ttu-id="c2857-417">Kesme noktasında program yürütmesi durdurulduğunda değerini görüntüleyin `generatedCode` .</span><span class="sxs-lookup"><span data-stu-id="c2857-417">When program execution stops at the breakpoint, view the value of `generatedCode`.</span></span>

![GeneratedCode 'ın metin görselleştiricisi görünümü](razor/_static/tvr.png)

::: moniker-end

## <a name="view-lookups-and-case-sensitivity"></a><span data-ttu-id="c2857-419">Aramaları ve büyük/küçük harf duyarlılığını görüntüle</span><span class="sxs-lookup"><span data-stu-id="c2857-419">View lookups and case sensitivity</span></span>

<span data-ttu-id="c2857-420">RazorGörünüm altyapısı, görünümler için büyük/küçük harfe duyarlı aramalar gerçekleştirir.</span><span class="sxs-lookup"><span data-stu-id="c2857-420">The Razor view engine performs case-sensitive lookups for views.</span></span> <span data-ttu-id="c2857-421">Ancak gerçek arama, temel alınan dosya sistemine göre belirlenir:</span><span class="sxs-lookup"><span data-stu-id="c2857-421">However, the actual lookup is determined by the underlying file system:</span></span>

* <span data-ttu-id="c2857-422">Dosya tabanlı kaynak:</span><span class="sxs-lookup"><span data-stu-id="c2857-422">File based source:</span></span>
  * <span data-ttu-id="c2857-423">Büyük/küçük harf duyarsız dosya sistemlerine (örneğin, Windows) sahip işletim sistemlerinde, fiziksel dosya sağlayıcısı aramaları büyük/küçük harfe duyarsızdır.</span><span class="sxs-lookup"><span data-stu-id="c2857-423">On operating systems with case insensitive file systems (for example, Windows), physical file provider lookups are case insensitive.</span></span> <span data-ttu-id="c2857-424">Örneğin, `return View("Test")` */views/Home/test.exe*, */views/Home/test.exe*ve diğer tüm büyük/küçük harf çeşitlerüyle sonuçlanır.</span><span class="sxs-lookup"><span data-stu-id="c2857-424">For example, `return View("Test")` results in matches for */Views/Home/Test.cshtml*, */Views/home/test.cshtml*, and any other casing variant.</span></span>
  * <span data-ttu-id="c2857-425">Büyük/küçük harf duyarlı dosya sistemlerinde (örneğin, Linux, OSX ve ile `EmbeddedFileProvider` ), aramalar büyük/küçük harfe duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="c2857-425">On case-sensitive file systems (for example, Linux, OSX, and with `EmbeddedFileProvider`), lookups are case-sensitive.</span></span> <span data-ttu-id="c2857-426">Örneğin, `return View("Test")` özellikle */views/Home/test.exe. cshtml*ile eşleşir.</span><span class="sxs-lookup"><span data-stu-id="c2857-426">For example, `return View("Test")` specifically matches */Views/Home/Test.cshtml*.</span></span>
* <span data-ttu-id="c2857-427">Önceden derlenmiş görünümler: ASP.NET Core 2,0 ve üzeri sürümlerde, önceden derlenmiş görünümleri aramak tüm işletim sistemlerinde büyük/küçük harfe duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="c2857-427">Precompiled views: With ASP.NET Core 2.0 and later, looking up precompiled views is case insensitive on all operating systems.</span></span> <span data-ttu-id="c2857-428">Davranış, fiziksel dosya sağlayıcısının Windows 'daki davranışlarıyla aynıdır.</span><span class="sxs-lookup"><span data-stu-id="c2857-428">The behavior is identical to physical file provider's behavior on Windows.</span></span> <span data-ttu-id="c2857-429">Ön derlenmiş iki görünüm yalnızca bir durumda farklıysa, arama sonucu belirleyici değildir.</span><span class="sxs-lookup"><span data-stu-id="c2857-429">If two precompiled views differ only in case, the result of lookup is non-deterministic.</span></span>

<span data-ttu-id="c2857-430">Geliştiricilerin dosya ve dizin adlarını büyük küçük harf olarak eşleşmesi önerilir:</span><span class="sxs-lookup"><span data-stu-id="c2857-430">Developers are encouraged to match the casing of file and directory names to the casing of:</span></span>

* <span data-ttu-id="c2857-431">Alan, denetleyici ve eylem adları.</span><span class="sxs-lookup"><span data-stu-id="c2857-431">Area, controller, and action names.</span></span>
* Razor<span data-ttu-id="c2857-432">Sayfaları.</span><span class="sxs-lookup"><span data-stu-id="c2857-432"> Pages.</span></span>

<span data-ttu-id="c2857-433">Eşleşen durum, dağıtımların görünümlerini temel alınan dosya sisteminden bağımsız olarak bulmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="c2857-433">Matching case ensures the deployments find their views regardless of the underlying file system.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c2857-434">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="c2857-434">Additional resources</span></span>

<span data-ttu-id="c2857-435">Şunu [kullanarak Razor ASP.NET Web programlamaya giriş Sözdizimi](/aspnet/web-pages/overview/getting-started/introducing-razor-syntax-c) , sözdizimi ile programlama için birçok örnek sağlar Razor .</span><span class="sxs-lookup"><span data-stu-id="c2857-435">[Introduction to ASP.NET Web Programming Using the Razor Syntax](/aspnet/web-pages/overview/getting-started/introducing-razor-syntax-c) provides many samples of programming with Razor syntax.</span></span>
