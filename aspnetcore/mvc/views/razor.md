---
title: ASP.NET Core için jilet sözdizimi başvurusu
author: rick-anderson
description: Sunucu tabanlı kodu web sayfalarına katıştırma için Razor biçimlendirme sözdizimi hakkında bilgi edinin.
ms.author: riande
ms.date: 02/12/2020
uid: mvc/views/razor
ms.openlocfilehash: dd5c73be56ed0dafb759df2f5ff2eac1a3b5b09e
ms.sourcegitcommit: d03905aadf5ceac39fff17706481af7f6c130411
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2020
ms.locfileid: "80381763"
---
# <a name="razor-syntax-reference-for-aspnet-core"></a><span data-ttu-id="84134-103">ASP.NET Core için jilet sözdizimi başvurusu</span><span class="sxs-lookup"><span data-stu-id="84134-103">Razor syntax reference for ASP.NET Core</span></span>

<span data-ttu-id="84134-104">Yazar: [Rick Anderson](https://twitter.com/RickAndMSFT), [Taylor Mullen](https://twitter.com/ntaylormullen), ve Dan [Vicarel](https://github.com/Rabadash8820)</span><span class="sxs-lookup"><span data-stu-id="84134-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Taylor Mullen](https://twitter.com/ntaylormullen), and [Dan Vicarel](https://github.com/Rabadash8820)</span></span>

<span data-ttu-id="84134-105">Razor, sunucu tabanlı kodu web sayfalarına katıştırma amaçlı bir biçimlendirme sözdizimidir.</span><span class="sxs-lookup"><span data-stu-id="84134-105">Razor is a markup syntax for embedding server-based code into webpages.</span></span> <span data-ttu-id="84134-106">Razor sözdizimi Razor biçimlendirme, C#ve HTML'den oluşur.</span><span class="sxs-lookup"><span data-stu-id="84134-106">The Razor syntax consists of Razor markup, C#, and HTML.</span></span> <span data-ttu-id="84134-107">Razor içeren dosyaların genellikle *.cshtml* dosya uzantısı vardır.</span><span class="sxs-lookup"><span data-stu-id="84134-107">Files containing Razor generally have a *.cshtml* file extension.</span></span> <span data-ttu-id="84134-108">Jilet de [Razor bileşenleri](xref:blazor/components) dosyaları bulunur (*.razor*).</span><span class="sxs-lookup"><span data-stu-id="84134-108">Razor is also found in [Razor components](xref:blazor/components) files (*.razor*).</span></span>

## <a name="rendering-html"></a><span data-ttu-id="84134-109">HTML oluşturma</span><span class="sxs-lookup"><span data-stu-id="84134-109">Rendering HTML</span></span>

<span data-ttu-id="84134-110">Varsayılan Razor dili HTML’dir.</span><span class="sxs-lookup"><span data-stu-id="84134-110">The default Razor language is HTML.</span></span> <span data-ttu-id="84134-111">Razor işaretlemesinden HTML işlemek, HTML dosyasından HTML işlemekten farklı değildir.</span><span class="sxs-lookup"><span data-stu-id="84134-111">Rendering HTML from Razor markup is no different than rendering HTML from an HTML file.</span></span> <span data-ttu-id="84134-112">*.cshtml* Razor dosyalarındaki HTML biçimlendirmesi sunucu tarafından değiştirilmeden işlenir.</span><span class="sxs-lookup"><span data-stu-id="84134-112">HTML markup in *.cshtml* Razor files is rendered by the server unchanged.</span></span>

## <a name="razor-syntax"></a><span data-ttu-id="84134-113">Razor söz dizimi</span><span class="sxs-lookup"><span data-stu-id="84134-113">Razor syntax</span></span>

<span data-ttu-id="84134-114">Razor C# destekler `@` ve HTML'den C#'a geçiş için sembolü kullanır.</span><span class="sxs-lookup"><span data-stu-id="84134-114">Razor supports C# and uses the `@` symbol to transition from HTML to C#.</span></span> <span data-ttu-id="84134-115">Razor C# ifadelerini değerlendirir ve HTML çıkışında işler.</span><span class="sxs-lookup"><span data-stu-id="84134-115">Razor evaluates C# expressions and renders them in the HTML output.</span></span>

<span data-ttu-id="84134-116">Bir `@` [sembol, Jilet ayrılmış](#razor-reserved-keywords)bir anahtar kelime tarafından takip edildiğinde, Razor'a özgü biçimlendirmeye dönüşür.</span><span class="sxs-lookup"><span data-stu-id="84134-116">When an `@` symbol is followed by a [Razor reserved keyword](#razor-reserved-keywords), it transitions into Razor-specific markup.</span></span> <span data-ttu-id="84134-117">Aksi takdirde, düz C# dönüşür.</span><span class="sxs-lookup"><span data-stu-id="84134-117">Otherwise, it transitions into plain C#.</span></span>

<span data-ttu-id="84134-118">Razor biçimlendirmesindeki bir `@` sembolden `@` kaçmak için ikinci bir sembol kullanın:</span><span class="sxs-lookup"><span data-stu-id="84134-118">To escape an `@` symbol in Razor markup, use a second `@` symbol:</span></span>

```cshtml
<p>@@Username</p>
```

<span data-ttu-id="84134-119">Kod HTML'de tek `@` bir sembolle işlenir:</span><span class="sxs-lookup"><span data-stu-id="84134-119">The code is rendered in HTML with a single `@` symbol:</span></span>

```html
<p>@Username</p>
```

<span data-ttu-id="84134-120">HTML öznitelikleri ve e-posta adresleri `@` içeren içerik, simgeyi bir geçiş karakteri olarak ele almaz.</span><span class="sxs-lookup"><span data-stu-id="84134-120">HTML attributes and content containing email addresses don't treat the `@` symbol as a transition character.</span></span> <span data-ttu-id="84134-121">Aşağıdaki örnekteki e-posta adreslerine Razor ayrıştma tarafından dokunulmaz:</span><span class="sxs-lookup"><span data-stu-id="84134-121">The email addresses in the following example are untouched by Razor parsing:</span></span>

```cshtml
<a href="mailto:Support@contoso.com">Support@contoso.com</a>
```

## <a name="implicit-razor-expressions"></a><span data-ttu-id="84134-122">Örtük Jilet ifadeleri</span><span class="sxs-lookup"><span data-stu-id="84134-122">Implicit Razor expressions</span></span>

<span data-ttu-id="84134-123">Örtük Jilet `@` ifadeleri C# kodu ile başlar:</span><span class="sxs-lookup"><span data-stu-id="84134-123">Implicit Razor expressions start with `@` followed by C# code:</span></span>

```cshtml
<p>@DateTime.Now</p>
<p>@DateTime.IsLeapYear(2016)</p>
```

<span data-ttu-id="84134-124">C# `await` anahtar sözcüğü dışında, örtük ifadeler boşluk içermemelidir.</span><span class="sxs-lookup"><span data-stu-id="84134-124">With the exception of the C# `await` keyword, implicit expressions must not contain spaces.</span></span> <span data-ttu-id="84134-125">C# deyiminin net bir sonu varsa, boşluklar iç içe olabilir:</span><span class="sxs-lookup"><span data-stu-id="84134-125">If the C# statement has a clear ending, spaces can be intermingled:</span></span>

```cshtml
<p>@await DoSomething("hello", "world")</p>
```

<span data-ttu-id="84134-126">Köşeli ayraçiçindeki karakterler (`<>`) HTML etiketi olarak yorumlandırılamadığı ndan, örtük ifadeler C# jenerikleri **içeremez.**</span><span class="sxs-lookup"><span data-stu-id="84134-126">Implicit expressions **cannot** contain C# generics, as the characters inside the brackets (`<>`) are interpreted as an HTML tag.</span></span> <span data-ttu-id="84134-127">Aşağıdaki kod geçerli **değildir:**</span><span class="sxs-lookup"><span data-stu-id="84134-127">The following code is **not** valid:</span></span>

```cshtml
<p>@GenericMethod<int>()</p>
```

<span data-ttu-id="84134-128">Önceki kod aşağıdakilerden birine benzer bir derleyici hatası oluşturur:</span><span class="sxs-lookup"><span data-stu-id="84134-128">The preceding code generates a compiler error similar to one of the following:</span></span>

* <span data-ttu-id="84134-129">"Int" öğesi kapalı değildi.</span><span class="sxs-lookup"><span data-stu-id="84134-129">The "int" element wasn't closed.</span></span> <span data-ttu-id="84134-130">Tüm öğeler kendi kendine kapanıyor olmalı veya eşleşen bir uç etiketine sahip olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="84134-130">All elements must be either self-closing or have a matching end tag.</span></span>
* <span data-ttu-id="84134-131">Yöntem grubu 'GenericMethod' 'object' olmayan temsilci türüne dönüştüremez.</span><span class="sxs-lookup"><span data-stu-id="84134-131">Cannot convert method group 'GenericMethod' to non-delegate type 'object'.</span></span> <span data-ttu-id="84134-132">Bu yöntemi uygulamak niyetinde miydiniz?'</span><span class="sxs-lookup"><span data-stu-id="84134-132">Did you intend to invoke the method?\`</span></span>

<span data-ttu-id="84134-133">Genel yöntem çağrıları açık bir [Razor ifadesine](#explicit-razor-expressions) veya [Razor kod bloğuna](#razor-code-blocks)sarılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="84134-133">Generic method calls must be wrapped in an [explicit Razor expression](#explicit-razor-expressions) or a [Razor code block](#razor-code-blocks).</span></span>

## <a name="explicit-razor-expressions"></a><span data-ttu-id="84134-134">Açık Jilet ifadeleri</span><span class="sxs-lookup"><span data-stu-id="84134-134">Explicit Razor expressions</span></span>

<span data-ttu-id="84134-135">Açık Razor ifadeler dengeli `@` parantez içeren bir sembolden oluşur.</span><span class="sxs-lookup"><span data-stu-id="84134-135">Explicit Razor expressions consist of an `@` symbol with balanced parenthesis.</span></span> <span data-ttu-id="84134-136">Geçen haftanın saatini işlemek için aşağıdaki Razor biçimlendirmesi kullanılır:</span><span class="sxs-lookup"><span data-stu-id="84134-136">To render last week's time, the following Razor markup is used:</span></span>

```cshtml
<p>Last week this time: @(DateTime.Now - TimeSpan.FromDays(7))</p>
```

<span data-ttu-id="84134-137">Parantez içindeki `@()` tüm içerik değerlendirilir ve çıktıya işlenir.</span><span class="sxs-lookup"><span data-stu-id="84134-137">Any content within the `@()` parenthesis is evaluated and rendered to the output.</span></span>

<span data-ttu-id="84134-138">Önceki bölümde açıklanan örtük ifadeler genellikle boşluk içeremez.</span><span class="sxs-lookup"><span data-stu-id="84134-138">Implicit expressions, described in the previous section, generally can't contain spaces.</span></span> <span data-ttu-id="84134-139">Aşağıdaki kodda, bir hafta geçerli zamandan çıkarılmıyor:</span><span class="sxs-lookup"><span data-stu-id="84134-139">In the following code, one week isn't subtracted from the current time:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact.cshtml?range=17)]

<span data-ttu-id="84134-140">Kod aşağıdaki HTML'yi işler:</span><span class="sxs-lookup"><span data-stu-id="84134-140">The code renders the following HTML:</span></span>

```html
<p>Last week: 7/7/2016 4:39:52 PM - TimeSpan.FromDays(7)</p>
```

<span data-ttu-id="84134-141">Açık ifadeler, metni bir ifade sonucuyla birleştirmek için kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="84134-141">Explicit expressions can be used to concatenate text with an expression result:</span></span>

```cshtml
@{
    var joe = new Person("Joe", 33);
}

<p>Age@(joe.Age)</p>
```

<span data-ttu-id="84134-142">Açık ifade olmadan, `<p>Age@joe.Age</p>` bir e-posta `<p>Age@joe.Age</p>` adresi olarak kabul edilir ve işlenir.</span><span class="sxs-lookup"><span data-stu-id="84134-142">Without the explicit expression, `<p>Age@joe.Age</p>` is treated as an email address, and `<p>Age@joe.Age</p>` is rendered.</span></span> <span data-ttu-id="84134-143">Açık bir ifade olarak `<p>Age33</p>` yazıldığında, işlenir.</span><span class="sxs-lookup"><span data-stu-id="84134-143">When written as an explicit expression, `<p>Age33</p>` is rendered.</span></span>

<span data-ttu-id="84134-144">Açık ifadeler *.cshtml* dosyalarındaki genel yöntemlerden çıktı işlemek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="84134-144">Explicit expressions can be used to render output from generic methods in *.cshtml* files.</span></span> <span data-ttu-id="84134-145">Aşağıdaki biçimlendirme, c# genel in parantezinin neden olduğu daha önce gösterilen hatanın nasıl düzeltilebildiğini gösterir.</span><span class="sxs-lookup"><span data-stu-id="84134-145">The following markup shows how to correct the error shown earlier caused by the brackets of a C# generic.</span></span> <span data-ttu-id="84134-146">Kod açık bir ifade olarak yazılır:</span><span class="sxs-lookup"><span data-stu-id="84134-146">The code is written as an explicit expression:</span></span>

```cshtml
<p>@(GenericMethod<int>())</p>
```

## <a name="expression-encoding"></a><span data-ttu-id="84134-147">İfade kodlama</span><span class="sxs-lookup"><span data-stu-id="84134-147">Expression encoding</span></span>

<span data-ttu-id="84134-148">Bir dize yi değerlendiren C# ifadeleri HTML kodlanır.</span><span class="sxs-lookup"><span data-stu-id="84134-148">C# expressions that evaluate to a string are HTML encoded.</span></span> <span data-ttu-id="84134-149">Değerlendirmek için `IHtmlContent` değerlendiren C# ifadeleri doğrudan `IHtmlContent.WriteTo`.</span><span class="sxs-lookup"><span data-stu-id="84134-149">C# expressions that evaluate to `IHtmlContent` are rendered directly through `IHtmlContent.WriteTo`.</span></span> <span data-ttu-id="84134-150">Değerlendirmeyen `IHtmlContent` C# ifadeleri, işlenmeden önce bir `ToString` dize dönüştürülür ve kodlanır.</span><span class="sxs-lookup"><span data-stu-id="84134-150">C# expressions that don't evaluate to `IHtmlContent` are converted to a string by `ToString` and encoded before they're rendered.</span></span>

```cshtml
@("<span>Hello World</span>")
```

<span data-ttu-id="84134-151">Kod aşağıdaki HTML'yi işler:</span><span class="sxs-lookup"><span data-stu-id="84134-151">The code renders the following HTML:</span></span>

```html
&lt;span&gt;Hello World&lt;/span&gt;
```

<span data-ttu-id="84134-152">HTML tarayıcıda aşağıdaki gibi gösterilir:</span><span class="sxs-lookup"><span data-stu-id="84134-152">The HTML is shown in the browser as:</span></span>

```html
<span>Hello World</span>
```

<span data-ttu-id="84134-153">`HtmlHelper.Raw`çıktı kodlanmış değil, HTML biçimlendirmesi olarak işlenir.</span><span class="sxs-lookup"><span data-stu-id="84134-153">`HtmlHelper.Raw` output isn't encoded but rendered as HTML markup.</span></span>

> [!WARNING]
> <span data-ttu-id="84134-154">Sanitize edilmemiş kullanıcı girişi `HtmlHelper.Raw` kullanmak bir güvenlik riskidir.</span><span class="sxs-lookup"><span data-stu-id="84134-154">Using `HtmlHelper.Raw` on unsanitized user input is a security risk.</span></span> <span data-ttu-id="84134-155">Kullanıcı girişi kötü amaçlı JavaScript veya diğer açıkları içerebilir.</span><span class="sxs-lookup"><span data-stu-id="84134-155">User input might contain malicious JavaScript or other exploits.</span></span> <span data-ttu-id="84134-156">Kullanıcı girişini dezenfekte etmek zordur.</span><span class="sxs-lookup"><span data-stu-id="84134-156">Sanitizing user input is difficult.</span></span> <span data-ttu-id="84134-157">Kullanıcı `HtmlHelper.Raw` girişi ile kullanmaktan kaçının.</span><span class="sxs-lookup"><span data-stu-id="84134-157">Avoid using `HtmlHelper.Raw` with user input.</span></span>

```cshtml
@Html.Raw("<span>Hello World</span>")
```

<span data-ttu-id="84134-158">Kod aşağıdaki HTML'yi işler:</span><span class="sxs-lookup"><span data-stu-id="84134-158">The code renders the following HTML:</span></span>

```html
<span>Hello World</span>
```

## <a name="razor-code-blocks"></a><span data-ttu-id="84134-159">Jilet kod blokları</span><span class="sxs-lookup"><span data-stu-id="84134-159">Razor code blocks</span></span>

<span data-ttu-id="84134-160">Jilet kod `@` blokları ile başlar `{}`ve ile çevrilidir.</span><span class="sxs-lookup"><span data-stu-id="84134-160">Razor code blocks start with `@` and are enclosed by `{}`.</span></span> <span data-ttu-id="84134-161">İfadelerin aksine, kod bloklarının içindeki C# kodu işlenmez.</span><span class="sxs-lookup"><span data-stu-id="84134-161">Unlike expressions, C# code inside code blocks isn't rendered.</span></span> <span data-ttu-id="84134-162">Görünümdeki kod blokları ve ifadeler aynı kapsamı paylaşır ve sırayla tanımlanır:</span><span class="sxs-lookup"><span data-stu-id="84134-162">Code blocks and expressions in a view share the same scope and are defined in order:</span></span>

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

<span data-ttu-id="84134-163">Kod aşağıdaki HTML'yi işler:</span><span class="sxs-lookup"><span data-stu-id="84134-163">The code renders the following HTML:</span></span>

```html
<p>The future depends on what you do today. - Mahatma Gandhi</p>
<p>Hate cannot drive out hate, only love can do that. - Martin Luther King, Jr.</p>
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="84134-164">Kod bloklarında, yerel [işlevleri](/dotnet/csharp/programming-guide/classes-and-structs/local-functions) biçimlendirme yöntemleri olarak hizmet etmek üzere biçimlendirmeile bildirin:</span><span class="sxs-lookup"><span data-stu-id="84134-164">In code blocks, declare [local functions](/dotnet/csharp/programming-guide/classes-and-structs/local-functions) with markup to serve as templating methods:</span></span>

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

<span data-ttu-id="84134-165">Kod aşağıdaki HTML'yi işler:</span><span class="sxs-lookup"><span data-stu-id="84134-165">The code renders the following HTML:</span></span>

```html
<p>Name: <strong>Mahatma Gandhi</strong></p>
<p>Name: <strong>Martin Luther King, Jr.</strong></p>
```

::: moniker-end

### <a name="implicit-transitions"></a><span data-ttu-id="84134-166">Örtük geçişler</span><span class="sxs-lookup"><span data-stu-id="84134-166">Implicit transitions</span></span>

<span data-ttu-id="84134-167">Kod bloğundaki varsayılan dil C#'dır, ancak Razor Page HTML'ye geri dönebilir:</span><span class="sxs-lookup"><span data-stu-id="84134-167">The default language in a code block is C#, but the Razor Page can transition back to HTML:</span></span>

```cshtml
@{
    var inCSharp = true;
    <p>Now in HTML, was in C# @inCSharp</p>
}
```

### <a name="explicit-delimited-transition"></a><span data-ttu-id="84134-168">Açık sınırlı geçiş</span><span class="sxs-lookup"><span data-stu-id="84134-168">Explicit delimited transition</span></span>

<span data-ttu-id="84134-169">HTML oluşturması gereken bir kod bloğunun alt bölümlerini tanımlamak için, `<text>` oluşturma için karakterleri Razor etiketiyle çevrelayın:</span><span class="sxs-lookup"><span data-stu-id="84134-169">To define a subsection of a code block that should render HTML, surround the characters for rendering with the Razor `<text>` tag:</span></span>

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    <text>Name: @person.Name</text>
}
```

<span data-ttu-id="84134-170">Html etiketiyle çevrili olmayan HTML'yi işlemek için bu yaklaşımı kullanın.</span><span class="sxs-lookup"><span data-stu-id="84134-170">Use this approach to render HTML that isn't surrounded by an HTML tag.</span></span> <span data-ttu-id="84134-171">HTML veya Razor etiketi olmadan, Razor çalışma zamanı hatası oluşur.</span><span class="sxs-lookup"><span data-stu-id="84134-171">Without an HTML or Razor tag, a Razor runtime error occurs.</span></span>

<span data-ttu-id="84134-172">Etiket, `<text>` içeriği işlerken beyaz alanı denetlemek için yararlıdır:</span><span class="sxs-lookup"><span data-stu-id="84134-172">The `<text>` tag is useful to control whitespace when rendering content:</span></span>

* <span data-ttu-id="84134-173">Yalnızca `<text>` etiket arasındaki içerik işlenir.</span><span class="sxs-lookup"><span data-stu-id="84134-173">Only the content between the `<text>` tag is rendered.</span></span>
* <span data-ttu-id="84134-174">ETIKETten önce veya `<text>` sonra HTML çıkışında boşluk bulunmaz.</span><span class="sxs-lookup"><span data-stu-id="84134-174">No whitespace before or after the `<text>` tag appears in the HTML output.</span></span>

### <a name="explicit-line-transition"></a><span data-ttu-id="84134-175">Açık satır geçişi</span><span class="sxs-lookup"><span data-stu-id="84134-175">Explicit line transition</span></span>

<span data-ttu-id="84134-176">Bir kod bloğu içinde tüm satırın geri kalanını `@:` HTML olarak işlemek için sözdizimini kullanın:</span><span class="sxs-lookup"><span data-stu-id="84134-176">To render the rest of an entire line as HTML inside a code block, use `@:` syntax:</span></span>

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    @:Name: @person.Name
}
```

<span data-ttu-id="84134-177">Kodda `@:` olmadan, bir Razor çalışma zamanı hatası oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="84134-177">Without the `@:` in the code, a Razor runtime error is generated.</span></span>

<span data-ttu-id="84134-178">Razor `@` dosyasındaki ek karakterler, daha sonra bloktaki ifadelerde derleyici hatalarına neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="84134-178">Extra `@` characters in a Razor file can cause compiler errors at statements later in the block.</span></span> <span data-ttu-id="84134-179">Gerçek hata bildirilen hatadan önce oluştuğundan, bu derleyici hatalarını anlamak zor olabilir.</span><span class="sxs-lookup"><span data-stu-id="84134-179">These compiler errors can be difficult to understand because the actual error occurs before the reported error.</span></span> <span data-ttu-id="84134-180">Bu hata, birden çok örtük/açık ifadeyi tek bir kod bloğunda birleştirdikten sonra sık görülür.</span><span class="sxs-lookup"><span data-stu-id="84134-180">This error is common after combining multiple implicit/explicit expressions into a single code block.</span></span>

## <a name="control-structures"></a><span data-ttu-id="84134-181">Kontrol yapıları</span><span class="sxs-lookup"><span data-stu-id="84134-181">Control structures</span></span>

<span data-ttu-id="84134-182">Denetim yapıları kod bloklarının bir uzantısıdır.</span><span class="sxs-lookup"><span data-stu-id="84134-182">Control structures are an extension of code blocks.</span></span> <span data-ttu-id="84134-183">Kod bloklarının tüm yönleri (biçimlendirmeye geçiş, satır altı C#) aşağıdaki yapılar için de geçerlidir:</span><span class="sxs-lookup"><span data-stu-id="84134-183">All aspects of code blocks (transitioning to markup, inline C#) also apply to the following structures:</span></span>

### <a name="conditionals-if-else-if-else-and-switch"></a><span data-ttu-id="84134-184">Koşullu \@lar eğer, başka if, else ve \@anahtar</span><span class="sxs-lookup"><span data-stu-id="84134-184">Conditionals \@if, else if, else, and \@switch</span></span>

<span data-ttu-id="84134-185">`@if`kod çalıştığında denetimleri:</span><span class="sxs-lookup"><span data-stu-id="84134-185">`@if` controls when code runs:</span></span>

```cshtml
@if (value % 2 == 0)
{
    <p>The value was even.</p>
}
```

<span data-ttu-id="84134-186">`else`ve `else if` `@` sembol gerekmez:</span><span class="sxs-lookup"><span data-stu-id="84134-186">`else` and `else if` don't require the `@` symbol:</span></span>

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

<span data-ttu-id="84134-187">Aşağıdaki biçimlendirme, anahtar deyiminin nasıl kullanılacağını gösterir:</span><span class="sxs-lookup"><span data-stu-id="84134-187">The following markup shows how to use a switch statement:</span></span>

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

### <a name="looping-for-foreach-while-and-do-while"></a><span data-ttu-id="84134-188">\@Döngü için, \@foreach, \@süre, ve \@süre yapmak</span><span class="sxs-lookup"><span data-stu-id="84134-188">Looping \@for, \@foreach, \@while, and \@do while</span></span>

<span data-ttu-id="84134-189">Şablonlu HTML döngü denetim ifadeleri ile oluşturulabilir.</span><span class="sxs-lookup"><span data-stu-id="84134-189">Templated HTML can be rendered with looping control statements.</span></span> <span data-ttu-id="84134-190">Kişilerin listesini işlemek için:</span><span class="sxs-lookup"><span data-stu-id="84134-190">To render a list of people:</span></span>

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

<span data-ttu-id="84134-191">Aşağıdaki döngü deyimleri desteklenir:</span><span class="sxs-lookup"><span data-stu-id="84134-191">The following looping statements are supported:</span></span>

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

### <a name="compound-using"></a><span data-ttu-id="84134-192">Bileşik \@kullanarak</span><span class="sxs-lookup"><span data-stu-id="84134-192">Compound \@using</span></span>

<span data-ttu-id="84134-193">C#'da, `using` bir nesnenin atıldığından emin olmak için bir deyim kullanılır.</span><span class="sxs-lookup"><span data-stu-id="84134-193">In C#, a `using` statement is used to ensure an object is disposed.</span></span> <span data-ttu-id="84134-194">Razor'da, aynı mekanizma ek içerik içeren HTML Yardımcıları oluşturmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="84134-194">In Razor, the same mechanism is used to create HTML Helpers that contain additional content.</span></span> <span data-ttu-id="84134-195">Aşağıdaki kodda, HTML Yardımcıları `<form>` `@using` deyimle birlikte bir etiket işler:</span><span class="sxs-lookup"><span data-stu-id="84134-195">In the following code, HTML Helpers render a `<form>` tag with the `@using` statement:</span></span>

```cshtml
@using (Html.BeginForm())
{
    <div>
        Email: <input type="email" id="Email" value="">
        <button>Register</button>
    </div>
}
```

### <a name="try-catch-finally"></a><span data-ttu-id="84134-196">\@deneyin, yakalamak, nihayet</span><span class="sxs-lookup"><span data-stu-id="84134-196">\@try, catch, finally</span></span>

<span data-ttu-id="84134-197">Özel durum işleme C#'a benzer:</span><span class="sxs-lookup"><span data-stu-id="84134-197">Exception handling is similar to C#:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact7.cshtml)]

### <a name="lock"></a><span data-ttu-id="84134-198">\@Kilit</span><span class="sxs-lookup"><span data-stu-id="84134-198">\@lock</span></span>

<span data-ttu-id="84134-199">Razor kilit ifadeleri ile kritik bölümleri korumak için yeteneğine sahiptir:</span><span class="sxs-lookup"><span data-stu-id="84134-199">Razor has the capability to protect critical sections with lock statements:</span></span>

```cshtml
@lock (SomeLock)
{
    // Do critical section work
}
```

### <a name="comments"></a><span data-ttu-id="84134-200">Yorumlar</span><span class="sxs-lookup"><span data-stu-id="84134-200">Comments</span></span>

<span data-ttu-id="84134-201">Razor C# ve HTML yorumlarını destekler:</span><span class="sxs-lookup"><span data-stu-id="84134-201">Razor supports C# and HTML comments:</span></span>

```cshtml
@{
    /* C# comment */
    // Another C# comment
}
<!-- HTML comment -->
```

<span data-ttu-id="84134-202">Kod aşağıdaki HTML'yi işler:</span><span class="sxs-lookup"><span data-stu-id="84134-202">The code renders the following HTML:</span></span>

```html
<!-- HTML comment -->
```

<span data-ttu-id="84134-203">Web sayfası oluşturulmadan önce razor yorumları sunucu tarafından kaldırılır.</span><span class="sxs-lookup"><span data-stu-id="84134-203">Razor comments are removed by the server before the webpage is rendered.</span></span> <span data-ttu-id="84134-204">Razor `@*  *@` yorumları sınırlamak için kullanır.</span><span class="sxs-lookup"><span data-stu-id="84134-204">Razor uses `@*  *@` to delimit comments.</span></span> <span data-ttu-id="84134-205">Aşağıdaki kod yorumlanır, böylece sunucu herhangi bir biçimlendirme oluşturmaz:</span><span class="sxs-lookup"><span data-stu-id="84134-205">The following code is commented out, so the server doesn't render any markup:</span></span>

```cshtml
@*
    @{
        /* C# comment */
        // Another C# comment
    }
    <!-- HTML comment -->
*@
```

## <a name="directives"></a><span data-ttu-id="84134-206">Yönergeler</span><span class="sxs-lookup"><span data-stu-id="84134-206">Directives</span></span>

<span data-ttu-id="84134-207">Razor `@` yönergeleri, sembolü izleyen ayrılmış anahtar kelimelerle örtük ifadelerle temsil edilir.</span><span class="sxs-lookup"><span data-stu-id="84134-207">Razor directives are represented by implicit expressions with reserved keywords following the `@` symbol.</span></span> <span data-ttu-id="84134-208">Yönerge genellikle görünümün ayrıştırılmanını değiştirir veya farklı işlevsellik sağlar.</span><span class="sxs-lookup"><span data-stu-id="84134-208">A directive typically changes the way a view is parsed or enables different functionality.</span></span>

<span data-ttu-id="84134-209">Razor'Un bir görünüm için kod nasıl oluşturduğunu anlamak, yönergelerin nasıl çalıştığını anlamayı kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="84134-209">Understanding how Razor generates code for a view makes it easier to understand how directives work.</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact8.cshtml)]

<span data-ttu-id="84134-210">Kod aşağıdakilere benzer bir sınıf oluşturur:</span><span class="sxs-lookup"><span data-stu-id="84134-210">The code generates a class similar to the following:</span></span>

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

<span data-ttu-id="84134-211">Bu makalenin ilerleyen bölümlerinde, [görünüm için oluşturulan Razor C# sınıfını incele](#inspect-the-razor-c-class-generated-for-a-view) bölümü, oluşturulan bu sınıfın nasıl görüntülenilecek olarak görüntülenilecek olduğunu açıklar.</span><span class="sxs-lookup"><span data-stu-id="84134-211">Later in this article, the section [Inspect the Razor C# class generated for a view](#inspect-the-razor-c-class-generated-for-a-view) explains how to view this generated class.</span></span>

### <a name="attribute"></a><span data-ttu-id="84134-212">\@Öznitelik</span><span class="sxs-lookup"><span data-stu-id="84134-212">\@attribute</span></span>

<span data-ttu-id="84134-213">Yönerge, `@attribute` verilen özniteliği oluşturulan sayfa veya görünüm sınıfına ekler.</span><span class="sxs-lookup"><span data-stu-id="84134-213">The `@attribute` directive adds the given attribute to the class of the generated page or view.</span></span> <span data-ttu-id="84134-214">Aşağıdaki örnek öznitelik `[Authorize]` ekler:</span><span class="sxs-lookup"><span data-stu-id="84134-214">The following example adds the `[Authorize]` attribute:</span></span>

```cshtml
@attribute [Authorize]
```

::: moniker range=">= aspnetcore-3.0"

### <a name="code"></a><span data-ttu-id="84134-215">\@Kod</span><span class="sxs-lookup"><span data-stu-id="84134-215">\@code</span></span>

<span data-ttu-id="84134-216">*Bu senaryo yalnızca Razor bileşenleri (.razor) için geçerlidir.*</span><span class="sxs-lookup"><span data-stu-id="84134-216">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="84134-217">Blok, `@code` Bir [Razor bileşeninin](xref:blazor/components) bir bileşene C# üyeleri (alanlar, özellikler ve yöntemler) eklemesini sağlar:</span><span class="sxs-lookup"><span data-stu-id="84134-217">The `@code` block enables a [Razor component](xref:blazor/components) to add C# members (fields, properties, and methods) to a component:</span></span>

```razor
@code {
    // C# members (fields, properties, and methods)
}
```

<span data-ttu-id="84134-218">Razor bileşenleri `@code` için, bir [`@functions`](#functions) takma ad `@functions`ve üzerinde önerilir.</span><span class="sxs-lookup"><span data-stu-id="84134-218">For Razor components, `@code` is an alias of [`@functions`](#functions) and recommended over `@functions`.</span></span> <span data-ttu-id="84134-219">Birden `@code` fazla blokca izin verilir.</span><span class="sxs-lookup"><span data-stu-id="84134-219">More than one `@code` block is permissible.</span></span>

::: moniker-end

### <a name="functions"></a><span data-ttu-id="84134-220">\@Işlev</span><span class="sxs-lookup"><span data-stu-id="84134-220">\@functions</span></span>

<span data-ttu-id="84134-221">Yönerge, `@functions` oluşturulan sınıfa C# üyeleri (alanlar, özellikler ve yöntemler) eklenmesini sağlar:</span><span class="sxs-lookup"><span data-stu-id="84134-221">The `@functions` directive enables adding C# members (fields, properties, and methods) to the generated class:</span></span>

```cshtml
@functions {
    // C# members (fields, properties, and methods)
}
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="84134-222">[Razor bileşenlerinde,](xref:blazor/components) `@functions` C# üyeleri eklemek için üzerinde kullanın. `@code`</span><span class="sxs-lookup"><span data-stu-id="84134-222">In [Razor components](xref:blazor/components), use `@code` over `@functions` to add C# members.</span></span>

::: moniker-end

<span data-ttu-id="84134-223">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="84134-223">For example:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact6.cshtml)]

<span data-ttu-id="84134-224">Kod aşağıdaki HTML biçimlendirmesini oluşturur:</span><span class="sxs-lookup"><span data-stu-id="84134-224">The code generates the following HTML markup:</span></span>

```html
<div>From method: Hello</div>
```

<span data-ttu-id="84134-225">Aşağıdaki kod oluşturulan Razor C# sınıfıdır:</span><span class="sxs-lookup"><span data-stu-id="84134-225">The following code is the generated Razor C# class:</span></span>

[!code-csharp[](razor/sample/Classes/Views_Home_Test_cshtml.cs?range=1-19)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="84134-226">`@functions`yöntemleri biçimlendirme olduğunda cazip yöntemler olarak hizmet vermektedir:</span><span class="sxs-lookup"><span data-stu-id="84134-226">`@functions` methods serve as templating methods when they have markup:</span></span>

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

<span data-ttu-id="84134-227">Kod aşağıdaki HTML'yi işler:</span><span class="sxs-lookup"><span data-stu-id="84134-227">The code renders the following HTML:</span></span>

```html
<p>Name: <strong>Mahatma Gandhi</strong></p>
<p>Name: <strong>Martin Luther King, Jr.</strong></p>
```

### <a name="implements"></a><span data-ttu-id="84134-228">\@Uygulayan</span><span class="sxs-lookup"><span data-stu-id="84134-228">\@implements</span></span>

<span data-ttu-id="84134-229">Yönerge, `@implements` oluşturulan sınıf için bir arabirim uygular.</span><span class="sxs-lookup"><span data-stu-id="84134-229">The `@implements` directive implements an interface for the generated class.</span></span>

<span data-ttu-id="84134-230">Yöntemin çağrılması <xref:System.IDisposable?displayProperty=fullName> için <xref:System.IDisposable.Dispose*> aşağıdaki örnek uygular:</span><span class="sxs-lookup"><span data-stu-id="84134-230">The following example implements <xref:System.IDisposable?displayProperty=fullName> so that the <xref:System.IDisposable.Dispose*> method can be called:</span></span>

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

### <a name="inherits"></a><span data-ttu-id="84134-231">\@Devralır</span><span class="sxs-lookup"><span data-stu-id="84134-231">\@inherits</span></span>

<span data-ttu-id="84134-232">Yönerge, `@inherits` görünümün devraldığı sınıfın tam denetimini sağlar:</span><span class="sxs-lookup"><span data-stu-id="84134-232">The `@inherits` directive provides full control of the class the view inherits:</span></span>

```cshtml
@inherits TypeNameOfClassToInheritFrom
```

<span data-ttu-id="84134-233">Aşağıdaki kod özel bir Razor sayfa türüdür:</span><span class="sxs-lookup"><span data-stu-id="84134-233">The following code is a custom Razor page type:</span></span>

[!code-csharp[](razor/sample/Classes/CustomRazorPage.cs)]

<span data-ttu-id="84134-234">Görünümde `CustomText` görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="84134-234">The `CustomText` is displayed in a view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact10.cshtml)]

<span data-ttu-id="84134-235">Kod aşağıdaki HTML'yi işler:</span><span class="sxs-lookup"><span data-stu-id="84134-235">The code renders the following HTML:</span></span>

```html
<div>
    Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping
    a slop bucket on the street below.
</div>
```

 <span data-ttu-id="84134-236">`@model`ve `@inherits` aynı görünümde kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="84134-236">`@model` and `@inherits` can be used in the same view.</span></span> <span data-ttu-id="84134-237">`@inherits`görünümü niçin içe aktardığı *bir _ViewImports.cshtml* dosyasında olabilir:</span><span class="sxs-lookup"><span data-stu-id="84134-237">`@inherits` can be in a *_ViewImports.cshtml* file that the view imports:</span></span>

[!code-cshtml[](razor/sample/Views/_ViewImportsModel.cshtml)]

<span data-ttu-id="84134-238">Aşağıdaki kod, güçlü bir şekilde yazılan bir görünüme örnektir:</span><span class="sxs-lookup"><span data-stu-id="84134-238">The following code is an example of a strongly-typed view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Login1.cshtml)]

<span data-ttu-id="84134-239">Modelderick@contoso.com" " " geçirilirse, görünüm aşağıdaki HTML biçimlendirmesini oluşturur:</span><span class="sxs-lookup"><span data-stu-id="84134-239">If "rick@contoso.com" is passed in the model, the view generates the following HTML markup:</span></span>

```html
<div>The Login Email: rick@contoso.com</div>
<div>
    Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping
    a slop bucket on the street below.
</div>
```

### <a name="inject"></a><span data-ttu-id="84134-240">\@Enjekte</span><span class="sxs-lookup"><span data-stu-id="84134-240">\@inject</span></span>

<span data-ttu-id="84134-241">Yönerge, `@inject` Razor Page'in [servis kabından](xref:fundamentals/dependency-injection) bir hizmeti görünüme enjekte etmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="84134-241">The `@inject` directive enables the Razor Page to inject a service from the [service container](xref:fundamentals/dependency-injection) into a view.</span></span> <span data-ttu-id="84134-242">Daha fazla bilgi [için, görünümlere Bağımlılık enjeksiyonu](xref:mvc/views/dependency-injection)bakın.</span><span class="sxs-lookup"><span data-stu-id="84134-242">For more information, see [Dependency injection into views](xref:mvc/views/dependency-injection).</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="layout"></a><span data-ttu-id="84134-243">\@Düzen</span><span class="sxs-lookup"><span data-stu-id="84134-243">\@layout</span></span>

<span data-ttu-id="84134-244">*Bu senaryo yalnızca Razor bileşenleri (.razor) için geçerlidir.*</span><span class="sxs-lookup"><span data-stu-id="84134-244">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="84134-245">Yönerge, `@layout` bir Razor bileşeni için bir düzen belirtir.</span><span class="sxs-lookup"><span data-stu-id="84134-245">The `@layout` directive specifies a layout for a Razor component.</span></span> <span data-ttu-id="84134-246">Düzen bileşenleri kod yinelemesini ve tutarsızlığı önlemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="84134-246">Layout components are used to avoid code duplication and inconsistency.</span></span> <span data-ttu-id="84134-247">Daha fazla bilgi için bkz. <xref:blazor/layouts>.</span><span class="sxs-lookup"><span data-stu-id="84134-247">For more information, see <xref:blazor/layouts>.</span></span>

::: moniker-end

### <a name="model"></a><span data-ttu-id="84134-248">\@Modeli</span><span class="sxs-lookup"><span data-stu-id="84134-248">\@model</span></span>

<span data-ttu-id="84134-249">*Bu senaryo yalnızca MVC görünümleri ve Razor Pages (.cshtml) için geçerlidir.*</span><span class="sxs-lookup"><span data-stu-id="84134-249">*This scenario only applies to MVC views and Razor Pages (.cshtml).*</span></span>

<span data-ttu-id="84134-250">Yönerge, `@model` bir görünüme veya sayfaya geçirilen modeltürünü belirtir:</span><span class="sxs-lookup"><span data-stu-id="84134-250">The `@model` directive specifies the type of the model passed to a view or page:</span></span>

```cshtml
@model TypeNameOfModel
```

<span data-ttu-id="84134-251">Tek tek kullanıcı hesaplarıyla oluşturulan ASP.NET Core MVC veya Razor Pages *uygulamasında, Views/Account/Login.cshtml* aşağıdaki model bildirimini içerir:</span><span class="sxs-lookup"><span data-stu-id="84134-251">In an ASP.NET Core MVC or Razor Pages app created with individual user accounts, *Views/Account/Login.cshtml* contains the following model declaration:</span></span>

```cshtml
@model LoginViewModel
```

<span data-ttu-id="84134-252">Oluşturulan sınıf `RazorPage<dynamic>`devralır:</span><span class="sxs-lookup"><span data-stu-id="84134-252">The class generated inherits from `RazorPage<dynamic>`:</span></span>

```csharp
public class _Views_Account_Login_cshtml : RazorPage<LoginViewModel>
```

<span data-ttu-id="84134-253">Razor, görünüme geçirilen modele erişmek için bir `Model` özelliği ortaya çıkarır:</span><span class="sxs-lookup"><span data-stu-id="84134-253">Razor exposes a `Model` property for accessing the model passed to the view:</span></span>

```cshtml
<div>The Login Email: @Model.Email</div>
```

<span data-ttu-id="84134-254">Yönerge özelliğin `@model` `Model` türünü belirtir.</span><span class="sxs-lookup"><span data-stu-id="84134-254">The `@model` directive specifies the type of the `Model` property.</span></span> <span data-ttu-id="84134-255">Yönerge, görünümün `T` `RazorPage<T>` türetildiği oluşturulan sınıfa göre belirtir.</span><span class="sxs-lookup"><span data-stu-id="84134-255">The directive specifies the `T` in `RazorPage<T>` that the generated class that the view derives from.</span></span> <span data-ttu-id="84134-256">`@model` Yönerge belirtilmemişse, `Model` özellik türündedir. `dynamic`</span><span class="sxs-lookup"><span data-stu-id="84134-256">If the `@model` directive isn't specified, the `Model` property is of type `dynamic`.</span></span> <span data-ttu-id="84134-257">Daha fazla bilgi için, [Güçlü olarak @model yazılan modellere ve anahtar kelimeye](xref:tutorials/first-mvc-app/adding-model#strongly-typed-models-and-the--keyword)bakın.</span><span class="sxs-lookup"><span data-stu-id="84134-257">For more information, see [Strongly typed models and the @model keyword](xref:tutorials/first-mvc-app/adding-model#strongly-typed-models-and-the--keyword).</span></span>

### <a name="namespace"></a><span data-ttu-id="84134-258">\@ad alanı</span><span class="sxs-lookup"><span data-stu-id="84134-258">\@namespace</span></span>

<span data-ttu-id="84134-259">Yönerge: `@namespace`</span><span class="sxs-lookup"><span data-stu-id="84134-259">The `@namespace` directive:</span></span>

* <span data-ttu-id="84134-260">Oluşturulan Razor sayfasının, MVC görünümünün veya Razor bileşeninin sınıfının ad alanını ayarlar.</span><span class="sxs-lookup"><span data-stu-id="84134-260">Sets the namespace of the class of the generated Razor page, MVC view, or Razor component.</span></span>
* <span data-ttu-id="84134-261">Dizin ağacındaki, *_ViewImports.cshtml* (görünümler veya sayfalar) veya *_Imports.razor* (Jilet bileşenleri) en yakın içe alma dosyasından bir sayfa, görünüm veya bileşen sınıflarının kök türetilmiş ad alanlarını ayarlar.</span><span class="sxs-lookup"><span data-stu-id="84134-261">Sets the root derived namespaces of a pages, views, or components classes from the closest imports file in the directory tree, *_ViewImports.cshtml* (views or pages) or *_Imports.razor* (Razor components).</span></span>

```cshtml
@namespace Your.Namespace.Here
```

<span data-ttu-id="84134-262">Aşağıdaki tabloda gösterilen Jilet Sayfaları örneği için:</span><span class="sxs-lookup"><span data-stu-id="84134-262">For the Razor Pages example shown in the following table:</span></span>

* <span data-ttu-id="84134-263">Her sayfa *Sayfa/_ViewImports.cshtml aktarım.*</span><span class="sxs-lookup"><span data-stu-id="84134-263">Each page imports *Pages/_ViewImports.cshtml*.</span></span>
* <span data-ttu-id="84134-264">*Sayfalar/_ViewImports.cshtml* `@namespace Hello.World`içerir.</span><span class="sxs-lookup"><span data-stu-id="84134-264">*Pages/_ViewImports.cshtml* contains `@namespace Hello.World`.</span></span>
* <span data-ttu-id="84134-265">Her sayfanın kökü ad alanı dır. `Hello.World`</span><span class="sxs-lookup"><span data-stu-id="84134-265">Each page has `Hello.World` as the root of it's namespace.</span></span>

| <span data-ttu-id="84134-266">Sayfa</span><span class="sxs-lookup"><span data-stu-id="84134-266">Page</span></span>                                        | <span data-ttu-id="84134-267">Ad Alanı</span><span class="sxs-lookup"><span data-stu-id="84134-267">Namespace</span></span>                             |
| ------------------------------------------- | ------------------------------------- |
| <span data-ttu-id="84134-268">*Sayfalar/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="84134-268">*Pages/Index.cshtml*</span></span>                        | `Hello.World`                         |
| <span data-ttu-id="84134-269">*Sayfalar/MorePages/Page.cshtml*</span><span class="sxs-lookup"><span data-stu-id="84134-269">*Pages/MorePages/Page.cshtml*</span></span>               | `Hello.World.MorePages`               |
| <span data-ttu-id="84134-270">*Sayfalar/MorePages/EvenMorePages/Page.cshtml*</span><span class="sxs-lookup"><span data-stu-id="84134-270">*Pages/MorePages/EvenMorePages/Page.cshtml*</span></span> | `Hello.World.MorePages.EvenMorePages` |

<span data-ttu-id="84134-271">Önceki ilişkiler, MVC görünümleri ve Razor bileşenleriyle kullanılan içe aktarma dosyaları için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="84134-271">The preceding relationships apply to import files used with MVC views and Razor components.</span></span>

<span data-ttu-id="84134-272">Birden çok içe `@namespace` aktarma dosyasında bir yönerge varsa, dizin ağacındaki sayfaya, görünüme veya bileşene en yakın dosya kök ad alanını ayarlamak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="84134-272">When multiple import files have a `@namespace` directive, the file closest to the page, view, or component in the directory tree is used to set the root namespace.</span></span>

<span data-ttu-id="84134-273">Önceki örnekteki *EvenMorePages* klasöründe `@namespace Another.Planet` (veya *Pages/MorePages/EvenMorePages/Page.cshtml* dosyasıiçeren) `@namespace Another.Planet`bir aktarım dosyası varsa, sonuç aşağıdaki tabloda gösterilir.</span><span class="sxs-lookup"><span data-stu-id="84134-273">If the *EvenMorePages* folder in the preceding example has an imports file with `@namespace Another.Planet` (or the *Pages/MorePages/EvenMorePages/Page.cshtml* file contains `@namespace Another.Planet`), the result is shown in the following table.</span></span>

| <span data-ttu-id="84134-274">Sayfa</span><span class="sxs-lookup"><span data-stu-id="84134-274">Page</span></span>                                        | <span data-ttu-id="84134-275">Ad Alanı</span><span class="sxs-lookup"><span data-stu-id="84134-275">Namespace</span></span>               |
| ------------------------------------------- | ----------------------- |
| <span data-ttu-id="84134-276">*Sayfalar/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="84134-276">*Pages/Index.cshtml*</span></span>                        | `Hello.World`           |
| <span data-ttu-id="84134-277">*Sayfalar/MorePages/Page.cshtml*</span><span class="sxs-lookup"><span data-stu-id="84134-277">*Pages/MorePages/Page.cshtml*</span></span>               | `Hello.World.MorePages` |
| <span data-ttu-id="84134-278">*Sayfalar/MorePages/EvenMorePages/Page.cshtml*</span><span class="sxs-lookup"><span data-stu-id="84134-278">*Pages/MorePages/EvenMorePages/Page.cshtml*</span></span> | `Another.Planet`        |

### <a name="page"></a><span data-ttu-id="84134-279">\@Sayfası</span><span class="sxs-lookup"><span data-stu-id="84134-279">\@page</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="84134-280">Yönerge, `@page` göründüğü dosyanın türüne bağlı olarak farklı etkilere sahiptir.</span><span class="sxs-lookup"><span data-stu-id="84134-280">The `@page` directive has different effects depending on the type of the file where it appears.</span></span> <span data-ttu-id="84134-281">Yönerge:</span><span class="sxs-lookup"><span data-stu-id="84134-281">The directive:</span></span>

* <span data-ttu-id="84134-282">*.cshtml* dosyasında dosyanın Bir Jilet Sayfası olduğunu gösterir.</span><span class="sxs-lookup"><span data-stu-id="84134-282">In in a *.cshtml* file indicates that the file is a Razor Page.</span></span> <span data-ttu-id="84134-283">Daha fazla bilgi için <xref:razor-pages/index> [bkz.](xref:razor-pages/index#custom-routes)</span><span class="sxs-lookup"><span data-stu-id="84134-283">For more information, see [Custom routes](xref:razor-pages/index#custom-routes) and <xref:razor-pages/index>.</span></span>
* <span data-ttu-id="84134-284">Bir Razor bileşeninin istekleri doğrudan işlemesi gerektiğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="84134-284">Specifies that a Razor component should handle requests directly.</span></span> <span data-ttu-id="84134-285">Daha fazla bilgi için bkz. <xref:blazor/routing>.</span><span class="sxs-lookup"><span data-stu-id="84134-285">For more information, see <xref:blazor/routing>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="84134-286">`@page` *.cshtml* dosyasının ilk satırındaki yönerge, dosyanın bir Jilet Sayfası olduğunu gösterir.</span><span class="sxs-lookup"><span data-stu-id="84134-286">The `@page` directive on the first line of a *.cshtml* file indicates that the file is a Razor Page.</span></span> <span data-ttu-id="84134-287">Daha fazla bilgi için bkz. <xref:razor-pages/index>.</span><span class="sxs-lookup"><span data-stu-id="84134-287">For more information, see <xref:razor-pages/index>.</span></span>

::: moniker-end

### <a name="section"></a><span data-ttu-id="84134-288">\@Bölüm</span><span class="sxs-lookup"><span data-stu-id="84134-288">\@section</span></span>

<span data-ttu-id="84134-289">*Bu senaryo yalnızca MVC görünümleri ve Razor Pages (.cshtml) için geçerlidir.*</span><span class="sxs-lookup"><span data-stu-id="84134-289">*This scenario only applies to MVC views and Razor Pages (.cshtml).*</span></span>

<span data-ttu-id="84134-290">Yönerge, `@section` HTML sayfasının farklı bölümlerinde içerik işlemek için görünümleri veya sayfaları etkinleştirmek için [MVC ve Jilet Sayfaları düzenleri](xref:mvc/views/layout) ile birlikte kullanılır.</span><span class="sxs-lookup"><span data-stu-id="84134-290">The `@section` directive is used in conjunction with [MVC and Razor Pages layouts](xref:mvc/views/layout) to enable views or pages to render content in different parts of the HTML page.</span></span> <span data-ttu-id="84134-291">Daha fazla bilgi için bkz. <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="84134-291">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="using"></a><span data-ttu-id="84134-292">\@kullanma</span><span class="sxs-lookup"><span data-stu-id="84134-292">\@using</span></span>

<span data-ttu-id="84134-293">Yönerge, `@using` oluşturulan `using` görünüme C# yönergesi ekler:</span><span class="sxs-lookup"><span data-stu-id="84134-293">The `@using` directive adds the C# `using` directive to the generated view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact9.cshtml)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="84134-294">[Razor bileşenlerinde,](xref:blazor/components) `@using` hangi bileşenlerin kapsamda olduğunu da denetler.</span><span class="sxs-lookup"><span data-stu-id="84134-294">In [Razor components](xref:blazor/components), `@using` also controls which components are in scope.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="directive-attributes"></a><span data-ttu-id="84134-295">Yönerge öznitelikleri</span><span class="sxs-lookup"><span data-stu-id="84134-295">Directive attributes</span></span>

### <a name="attributes"></a><span data-ttu-id="84134-296">\@Öznitelik</span><span class="sxs-lookup"><span data-stu-id="84134-296">\@attributes</span></span>

<span data-ttu-id="84134-297">*Bu senaryo yalnızca Razor bileşenleri (.razor) için geçerlidir.*</span><span class="sxs-lookup"><span data-stu-id="84134-297">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="84134-298">`@attributes`bir bileşenin beyan edilmeyen öznitelikleri işlemesine izin verir.</span><span class="sxs-lookup"><span data-stu-id="84134-298">`@attributes` allows a component to render non-declared attributes.</span></span> <span data-ttu-id="84134-299">Daha fazla bilgi için bkz. <xref:blazor/components#attribute-splatting-and-arbitrary-parameters>.</span><span class="sxs-lookup"><span data-stu-id="84134-299">For more information, see <xref:blazor/components#attribute-splatting-and-arbitrary-parameters>.</span></span>

### <a name="bind"></a><span data-ttu-id="84134-300">\@Bağlamak</span><span class="sxs-lookup"><span data-stu-id="84134-300">\@bind</span></span>

<span data-ttu-id="84134-301">*Bu senaryo yalnızca Razor bileşenleri (.razor) için geçerlidir.*</span><span class="sxs-lookup"><span data-stu-id="84134-301">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="84134-302">Bileşenlerde veri bağlama `@bind` öznitelik ile gerçekleştirilir.</span><span class="sxs-lookup"><span data-stu-id="84134-302">Data binding in components is accomplished with the `@bind` attribute.</span></span> <span data-ttu-id="84134-303">Daha fazla bilgi için bkz. <xref:blazor/data-binding>.</span><span class="sxs-lookup"><span data-stu-id="84134-303">For more information, see <xref:blazor/data-binding>.</span></span>

### <a name="onevent"></a><span data-ttu-id="84134-304">\@on{EVENT}</span><span class="sxs-lookup"><span data-stu-id="84134-304">\@on{EVENT}</span></span>

<span data-ttu-id="84134-305">*Bu senaryo yalnızca Razor bileşenleri (.razor) için geçerlidir.*</span><span class="sxs-lookup"><span data-stu-id="84134-305">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="84134-306">Razor bileşenler için olay işleme özellikleri sağlar.</span><span class="sxs-lookup"><span data-stu-id="84134-306">Razor provides event handling features for components.</span></span> <span data-ttu-id="84134-307">Daha fazla bilgi için bkz. <xref:blazor/event-handling>.</span><span class="sxs-lookup"><span data-stu-id="84134-307">For more information, see <xref:blazor/event-handling>.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.1"

### <a name="oneventpreventdefault"></a><span data-ttu-id="84134-308">\@on{EVENT}:preventVarsayılan</span><span class="sxs-lookup"><span data-stu-id="84134-308">\@on{EVENT}:preventDefault</span></span>

<span data-ttu-id="84134-309">*Bu senaryo yalnızca Razor bileşenleri (.razor) için geçerlidir.*</span><span class="sxs-lookup"><span data-stu-id="84134-309">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="84134-310">Olay için varsayılan eylemi önler.</span><span class="sxs-lookup"><span data-stu-id="84134-310">Prevents the default action for the event.</span></span>

### <a name="oneventstoppropagation"></a><span data-ttu-id="84134-311">\@on{EVENT}:stopPropagation</span><span class="sxs-lookup"><span data-stu-id="84134-311">\@on{EVENT}:stopPropagation</span></span>

<span data-ttu-id="84134-312">*Bu senaryo yalnızca Razor bileşenleri (.razor) için geçerlidir.*</span><span class="sxs-lookup"><span data-stu-id="84134-312">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="84134-313">Olay için olay yayılmasını durdurur.</span><span class="sxs-lookup"><span data-stu-id="84134-313">Stops event propagation for the event.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="key"></a><span data-ttu-id="84134-314">\@anahtar</span><span class="sxs-lookup"><span data-stu-id="84134-314">\@key</span></span>

<span data-ttu-id="84134-315">*Bu senaryo yalnızca Razor bileşenleri (.razor) için geçerlidir.*</span><span class="sxs-lookup"><span data-stu-id="84134-315">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="84134-316">Yönerge özniteliği, `@key` anahtar değerine dayalı öğelerin veya bileşenlerin korunmasını garanti etmek için bileşenlerin dağılarak algoritmaya neden olur.</span><span class="sxs-lookup"><span data-stu-id="84134-316">The `@key` directive attribute causes the components diffing algorithm to guarantee preservation of elements or components based on the key's value.</span></span> <span data-ttu-id="84134-317">Daha fazla bilgi için bkz. <xref:blazor/components#use-key-to-control-the-preservation-of-elements-and-components>.</span><span class="sxs-lookup"><span data-stu-id="84134-317">For more information, see <xref:blazor/components#use-key-to-control-the-preservation-of-elements-and-components>.</span></span>

### <a name="ref"></a><span data-ttu-id="84134-318">\@ref</span><span class="sxs-lookup"><span data-stu-id="84134-318">\@ref</span></span>

<span data-ttu-id="84134-319">*Bu senaryo yalnızca Razor bileşenleri (.razor) için geçerlidir.*</span><span class="sxs-lookup"><span data-stu-id="84134-319">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="84134-320">Bileşen başvuruları`@ref`( ) bir bileşen örneğine başvurmanın bir yolunu sağlar, böylece bu örne komutlar verebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="84134-320">Component references (`@ref`) provide a way to reference a component instance so that you can issue commands to that instance.</span></span> <span data-ttu-id="84134-321">Daha fazla bilgi için bkz. <xref:blazor/components#capture-references-to-components>.</span><span class="sxs-lookup"><span data-stu-id="84134-321">For more information, see <xref:blazor/components#capture-references-to-components>.</span></span>

### <a name="typeparam"></a><span data-ttu-id="84134-322">\@daktison</span><span class="sxs-lookup"><span data-stu-id="84134-322">\@typeparam</span></span>

<span data-ttu-id="84134-323">*Bu senaryo yalnızca Razor bileşenleri (.razor) için geçerlidir.*</span><span class="sxs-lookup"><span data-stu-id="84134-323">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="84134-324">Yönerge, `@typeparam` oluşturulan bileşen sınıfı için genel bir tür parametresi bildirir.</span><span class="sxs-lookup"><span data-stu-id="84134-324">The `@typeparam` directive declares a generic type parameter for the generated component class.</span></span> <span data-ttu-id="84134-325">Daha fazla bilgi için bkz. <xref:blazor/templated-components#generic-typed-components>.</span><span class="sxs-lookup"><span data-stu-id="84134-325">For more information, see <xref:blazor/templated-components#generic-typed-components>.</span></span>

::: moniker-end

## <a name="templated-razor-delegates"></a><span data-ttu-id="84134-326">Şablonlanmış Razor delegeleri</span><span class="sxs-lookup"><span data-stu-id="84134-326">Templated Razor delegates</span></span>

<span data-ttu-id="84134-327">Jilet şablonları, aşağıdaki biçime sahip bir UI snippet tanımlamanızı sağlar:</span><span class="sxs-lookup"><span data-stu-id="84134-327">Razor templates allow you to define a UI snippet with the following format:</span></span>

```cshtml
@<tag>...</tag>
```

<span data-ttu-id="84134-328">Aşağıdaki örnekte, şablonlanmış bir Razor temsilcisinin <xref:System.Func%602>nasıl bir . olarak belirtilmeniz gösteriş</span><span class="sxs-lookup"><span data-stu-id="84134-328">The following example illustrates how to specify a templated Razor delegate as a <xref:System.Func%602>.</span></span> <span data-ttu-id="84134-329">[Dinamik tür,](/dotnet/csharp/programming-guide/types/using-type-dynamic) temsilcinin kapsülletiyi kapsayan yöntemin parametresi için belirtilir.</span><span class="sxs-lookup"><span data-stu-id="84134-329">The [dynamic type](/dotnet/csharp/programming-guide/types/using-type-dynamic) is specified for the parameter of the method that the delegate encapsulates.</span></span> <span data-ttu-id="84134-330">Nesne [türü,](/dotnet/csharp/language-reference/keywords/object) temsilcinin geri dönüş değeri olarak belirtilir.</span><span class="sxs-lookup"><span data-stu-id="84134-330">An [object type](/dotnet/csharp/language-reference/keywords/object) is specified as the return value of the delegate.</span></span> <span data-ttu-id="84134-331">Şablon, bir özelliği <xref:System.Collections.Generic.List%601> `Pet` olan bir `Name` ile kullanılır.</span><span class="sxs-lookup"><span data-stu-id="84134-331">The template is used with a <xref:System.Collections.Generic.List%601> of `Pet` that has a `Name` property.</span></span>

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

<span data-ttu-id="84134-332">Şablon bir `foreach` deyim `pets` le sağlanır:</span><span class="sxs-lookup"><span data-stu-id="84134-332">The template is rendered with `pets` supplied by a `foreach` statement:</span></span>

```cshtml
@foreach (var pet in pets)
{
    @petTemplate(pet)
}
```

<span data-ttu-id="84134-333">İşlenen çıktı:</span><span class="sxs-lookup"><span data-stu-id="84134-333">Rendered output:</span></span>

```html
<p>You have a pet named <strong>Rin Tin Tin</strong>.</p>
<p>You have a pet named <strong>Mr. Bigglesworth</strong>.</p>
<p>You have a pet named <strong>K-9</strong>.</p>
```

<span data-ttu-id="84134-334">Bir yönteme bağımsız değişken olarak satır içinde Jilet şablonu da sağlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="84134-334">You can also supply an inline Razor template as an argument to a method.</span></span> <span data-ttu-id="84134-335">Aşağıdaki örnekte, `Repeat` yöntem bir Razor şablonu alır.</span><span class="sxs-lookup"><span data-stu-id="84134-335">In the following example, the `Repeat` method receives a Razor template.</span></span> <span data-ttu-id="84134-336">Yöntem, bir listeden sağlanan öğelerin tekrarı ile HTML içeriği oluşturmak için şablonu kullanır:</span><span class="sxs-lookup"><span data-stu-id="84134-336">The method uses the template to produce HTML content with repeats of items supplied from a list:</span></span>

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

<span data-ttu-id="84134-337">Önceki örnekteki evcil hayvanların listesini `Repeat` kullanarak, yöntem şu şekilde çağrılır:</span><span class="sxs-lookup"><span data-stu-id="84134-337">Using the list of pets from the prior example, the `Repeat` method is called with:</span></span>

* <span data-ttu-id="84134-338"><xref:System.Collections.Generic.List%601>ve `Pet`.</span><span class="sxs-lookup"><span data-stu-id="84134-338"><xref:System.Collections.Generic.List%601> of `Pet`.</span></span>
* <span data-ttu-id="84134-339">Her evcil hayvanı tekrarlamak için kaç kez.</span><span class="sxs-lookup"><span data-stu-id="84134-339">Number of times to repeat each pet.</span></span>
* <span data-ttu-id="84134-340">Sıralanmamış bir listenin liste öğeleri için kullanılacak satır çizgisi şablonu.</span><span class="sxs-lookup"><span data-stu-id="84134-340">Inline template to use for the list items of an unordered list.</span></span>

```cshtml
<ul>
    @Repeat(pets, 3, @<li>@item.Name</li>)
</ul>
```

<span data-ttu-id="84134-341">İşlenen çıktı:</span><span class="sxs-lookup"><span data-stu-id="84134-341">Rendered output:</span></span>

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

## <a name="tag-helpers"></a><span data-ttu-id="84134-342">Etiket Yardımcıları</span><span class="sxs-lookup"><span data-stu-id="84134-342">Tag Helpers</span></span>

<span data-ttu-id="84134-343">*Bu senaryo yalnızca MVC görünümleri ve Razor Pages (.cshtml) için geçerlidir.*</span><span class="sxs-lookup"><span data-stu-id="84134-343">*This scenario only applies to MVC views and Razor Pages (.cshtml).*</span></span>

<span data-ttu-id="84134-344">[Tag Helpers](xref:mvc/views/tag-helpers/intro)ile ilgili üç yönerge vardır.</span><span class="sxs-lookup"><span data-stu-id="84134-344">There are three directives that pertain to [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span>

| <span data-ttu-id="84134-345">Yönergesi</span><span class="sxs-lookup"><span data-stu-id="84134-345">Directive</span></span> | <span data-ttu-id="84134-346">İşlev</span><span class="sxs-lookup"><span data-stu-id="84134-346">Function</span></span> |
| --------- | -------- |
| [`@addTagHelper`](xref:mvc/views/tag-helpers/intro#add-helper-label) | <span data-ttu-id="84134-347">Tag Helpers'ı görünümde kullanılabilir hale getirir.</span><span class="sxs-lookup"><span data-stu-id="84134-347">Makes Tag Helpers available to a view.</span></span> |
| [`@removeTagHelper`](xref:mvc/views/tag-helpers/intro#remove-razor-directives-label) | <span data-ttu-id="84134-348">Daha önce görünümden eklenen Etiket Yardımcıları kaldırır.</span><span class="sxs-lookup"><span data-stu-id="84134-348">Removes Tag Helpers previously added from a view.</span></span> |
| [`@tagHelperPrefix`](xref:mvc/views/tag-helpers/intro#prefix-razor-directives-label) | <span data-ttu-id="84134-349">Tag Helper desteğini etkinleştirmek ve Tag Helper kullanımını açık hale getirmek için bir etiket öneki belirtir.</span><span class="sxs-lookup"><span data-stu-id="84134-349">Specifies a tag prefix to enable Tag Helper support and to make Tag Helper usage explicit.</span></span> |

## <a name="razor-reserved-keywords"></a><span data-ttu-id="84134-350">Jilet rezerve anahtar kelimeler</span><span class="sxs-lookup"><span data-stu-id="84134-350">Razor reserved keywords</span></span>

### <a name="razor-keywords"></a><span data-ttu-id="84134-351">Jilet anahtar kelimeler</span><span class="sxs-lookup"><span data-stu-id="84134-351">Razor keywords</span></span>

* <span data-ttu-id="84134-352">sayfa (Core 2.1 veya sonraki ASP.NET gerektirir)</span><span class="sxs-lookup"><span data-stu-id="84134-352">page (Requires ASP.NET Core 2.1 or later)</span></span>
* <span data-ttu-id="84134-353">ad alanı</span><span class="sxs-lookup"><span data-stu-id="84134-353">namespace</span></span>
* <span data-ttu-id="84134-354"> işlevleri</span><span class="sxs-lookup"><span data-stu-id="84134-354">functions</span></span>
* <span data-ttu-id="84134-355">Devralır</span><span class="sxs-lookup"><span data-stu-id="84134-355">inherits</span></span>
* <span data-ttu-id="84134-356">model</span><span class="sxs-lookup"><span data-stu-id="84134-356">model</span></span>
* <span data-ttu-id="84134-357">section</span><span class="sxs-lookup"><span data-stu-id="84134-357">section</span></span>
* <span data-ttu-id="84134-358">yardımcı (Şu anda ASP.NET Core tarafından desteklenmiyor)</span><span class="sxs-lookup"><span data-stu-id="84134-358">helper (Not currently supported by ASP.NET Core)</span></span>

<span data-ttu-id="84134-359">Jilet anahtar `@(Razor Keyword)` kelimeler (örneğin, `@(functions)`) ile kaçılır.</span><span class="sxs-lookup"><span data-stu-id="84134-359">Razor keywords are escaped with `@(Razor Keyword)` (for example, `@(functions)`).</span></span>

### <a name="c-razor-keywords"></a><span data-ttu-id="84134-360">C# Jilet anahtar kelimeleri</span><span class="sxs-lookup"><span data-stu-id="84134-360">C# Razor keywords</span></span>

* <span data-ttu-id="84134-361">büyük/küçük harf</span><span class="sxs-lookup"><span data-stu-id="84134-361">case</span></span>
* <span data-ttu-id="84134-362">do</span><span class="sxs-lookup"><span data-stu-id="84134-362">do</span></span>
* <span data-ttu-id="84134-363">default</span><span class="sxs-lookup"><span data-stu-id="84134-363">default</span></span>
* <span data-ttu-id="84134-364">for</span><span class="sxs-lookup"><span data-stu-id="84134-364">for</span></span>
* <span data-ttu-id="84134-365">foreach</span><span class="sxs-lookup"><span data-stu-id="84134-365">foreach</span></span>
* <span data-ttu-id="84134-366">if</span><span class="sxs-lookup"><span data-stu-id="84134-366">if</span></span>
* <span data-ttu-id="84134-367">else</span><span class="sxs-lookup"><span data-stu-id="84134-367">else</span></span>
* <span data-ttu-id="84134-368">lock</span><span class="sxs-lookup"><span data-stu-id="84134-368">lock</span></span>
* <span data-ttu-id="84134-369">switch</span><span class="sxs-lookup"><span data-stu-id="84134-369">switch</span></span>
* <span data-ttu-id="84134-370">Deneme</span><span class="sxs-lookup"><span data-stu-id="84134-370">try</span></span>
* <span data-ttu-id="84134-371">Yakalamak</span><span class="sxs-lookup"><span data-stu-id="84134-371">catch</span></span>
* <span data-ttu-id="84134-372">finally</span><span class="sxs-lookup"><span data-stu-id="84134-372">finally</span></span>
* <span data-ttu-id="84134-373">kullanma</span><span class="sxs-lookup"><span data-stu-id="84134-373">using</span></span>
* <span data-ttu-id="84134-374">while</span><span class="sxs-lookup"><span data-stu-id="84134-374">while</span></span>

<span data-ttu-id="84134-375">C# Razor anahtar kelimeleri ile `@(@C# Razor Keyword)` çift kaçtı `@(@case)`olmalıdır (örneğin, ).</span><span class="sxs-lookup"><span data-stu-id="84134-375">C# Razor keywords must be double-escaped with `@(@C# Razor Keyword)` (for example, `@(@case)`).</span></span> <span data-ttu-id="84134-376">İlki `@` Razor parser'dan kaçar.</span><span class="sxs-lookup"><span data-stu-id="84134-376">The first `@` escapes the Razor parser.</span></span> <span data-ttu-id="84134-377">İkincisi `@` C# parser'dan kaçar.</span><span class="sxs-lookup"><span data-stu-id="84134-377">The second `@` escapes the C# parser.</span></span>

### <a name="reserved-keywords-not-used-by-razor"></a><span data-ttu-id="84134-378">Razor tarafından kullanılmayan ayrılmış anahtar kelimeler</span><span class="sxs-lookup"><span data-stu-id="84134-378">Reserved keywords not used by Razor</span></span>

* <span data-ttu-id="84134-379">sınıf</span><span class="sxs-lookup"><span data-stu-id="84134-379">class</span></span>

## <a name="inspect-the-razor-c-class-generated-for-a-view"></a><span data-ttu-id="84134-380">Görünüm için oluşturulan Razor C# sınıfını inceleyin</span><span class="sxs-lookup"><span data-stu-id="84134-380">Inspect the Razor C# class generated for a view</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="84134-381">.NET Core SDK 2.1 veya sonraki ile [Razor SDK,](xref:razor-pages/sdk) Razor dosyalarının derlemesini işler.</span><span class="sxs-lookup"><span data-stu-id="84134-381">With .NET Core SDK 2.1 or later, the [Razor SDK](xref:razor-pages/sdk) handles compilation of Razor files.</span></span> <span data-ttu-id="84134-382">Bir proje oluştururken, Razor SDK proje kökünde *obj/<build_configuration>/<target_framework_moniker>/Razor* dizini oluşturur.</span><span class="sxs-lookup"><span data-stu-id="84134-382">When building a project, the Razor SDK generates an *obj/<build_configuration>/<target_framework_moniker>/Razor* directory in the project root.</span></span> <span data-ttu-id="84134-383">*Razor* dizinindeki dizin yapısı projenin dizin yapısını yansıtıyor.</span><span class="sxs-lookup"><span data-stu-id="84134-383">The directory structure within the *Razor* directory mirrors the project's directory structure.</span></span>

<span data-ttu-id="84134-384">.NET Core 2.1'i hedefleyen ASP.NET Core 2.1 Razor Pages projesinde aşağıdaki dizin yapısını göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="84134-384">Consider the following directory structure in an ASP.NET Core 2.1 Razor Pages project targeting .NET Core 2.1:</span></span>

* <span data-ttu-id="84134-385">**Alanlar/**</span><span class="sxs-lookup"><span data-stu-id="84134-385">**Areas/**</span></span>
  * <span data-ttu-id="84134-386">**Yönetici/**</span><span class="sxs-lookup"><span data-stu-id="84134-386">**Admin/**</span></span>
    * <span data-ttu-id="84134-387">**Sayfalar/**</span><span class="sxs-lookup"><span data-stu-id="84134-387">**Pages/**</span></span>
      * <span data-ttu-id="84134-388">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="84134-388">*Index.cshtml*</span></span>
      * <span data-ttu-id="84134-389">*Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="84134-389">*Index.cshtml.cs*</span></span>
* <span data-ttu-id="84134-390">**Sayfalar/**</span><span class="sxs-lookup"><span data-stu-id="84134-390">**Pages/**</span></span>
  * <span data-ttu-id="84134-391">**Paylaşılan/**</span><span class="sxs-lookup"><span data-stu-id="84134-391">**Shared/**</span></span>
    * <span data-ttu-id="84134-392">*_Layout.cshtml*</span><span class="sxs-lookup"><span data-stu-id="84134-392">*_Layout.cshtml*</span></span>
  * <span data-ttu-id="84134-393">*_ViewImports.cshtml*</span><span class="sxs-lookup"><span data-stu-id="84134-393">*_ViewImports.cshtml*</span></span>
  * <span data-ttu-id="84134-394">*_ViewStart.cshtml*</span><span class="sxs-lookup"><span data-stu-id="84134-394">*_ViewStart.cshtml*</span></span>
  * <span data-ttu-id="84134-395">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="84134-395">*Index.cshtml*</span></span>
  * <span data-ttu-id="84134-396">*Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="84134-396">*Index.cshtml.cs*</span></span>

<span data-ttu-id="84134-397">*Projeyi Hata Ayıklama* yapılandırmasında oluşturmak aşağıdaki *obj* dizinini verir:</span><span class="sxs-lookup"><span data-stu-id="84134-397">Building the project in *Debug* configuration yields the following *obj* directory:</span></span>

* <span data-ttu-id="84134-398">**obj/**</span><span class="sxs-lookup"><span data-stu-id="84134-398">**obj/**</span></span>
  * <span data-ttu-id="84134-399">**Hata Ayıklama/**</span><span class="sxs-lookup"><span data-stu-id="84134-399">**Debug/**</span></span>
    * <span data-ttu-id="84134-400">**netcoreapp2.1/**</span><span class="sxs-lookup"><span data-stu-id="84134-400">**netcoreapp2.1/**</span></span>
      * <span data-ttu-id="84134-401">**Jilet/**</span><span class="sxs-lookup"><span data-stu-id="84134-401">**Razor/**</span></span>
        * <span data-ttu-id="84134-402">**Alanlar/**</span><span class="sxs-lookup"><span data-stu-id="84134-402">**Areas/**</span></span>
          * <span data-ttu-id="84134-403">**Yönetici/**</span><span class="sxs-lookup"><span data-stu-id="84134-403">**Admin/**</span></span>
            * <span data-ttu-id="84134-404">**Sayfalar/**</span><span class="sxs-lookup"><span data-stu-id="84134-404">**Pages/**</span></span>
              * <span data-ttu-id="84134-405">*Index.g.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="84134-405">*Index.g.cshtml.cs*</span></span>
        * <span data-ttu-id="84134-406">**Sayfalar/**</span><span class="sxs-lookup"><span data-stu-id="84134-406">**Pages/**</span></span>
          * <span data-ttu-id="84134-407">**Paylaşılan/**</span><span class="sxs-lookup"><span data-stu-id="84134-407">**Shared/**</span></span>
            * <span data-ttu-id="84134-408">*_Layout.g.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="84134-408">*_Layout.g.cshtml.cs*</span></span>
          * <span data-ttu-id="84134-409">*_ViewImports.g.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="84134-409">*_ViewImports.g.cshtml.cs*</span></span>
          * <span data-ttu-id="84134-410">*_ViewStart.g.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="84134-410">*_ViewStart.g.cshtml.cs*</span></span>
          * <span data-ttu-id="84134-411">*Index.g.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="84134-411">*Index.g.cshtml.cs*</span></span>

<span data-ttu-id="84134-412">*Pages/Index.cshtml*için oluşturulan sınıfı görüntülemek için, açık *obj/Debug/netcoreapp2.1/Razor/Pages/Index.g.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="84134-412">To view the generated class for *Pages/Index.cshtml*, open *obj/Debug/netcoreapp2.1/Razor/Pages/Index.g.cshtml.cs*.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="84134-413">ASP.NET Core MVC projesine aşağıdaki sınıfı ekleyin:</span><span class="sxs-lookup"><span data-stu-id="84134-413">Add the following class to the ASP.NET Core MVC project:</span></span>

[!code-csharp[](razor/sample/Utilities/CustomTemplateEngine.cs)]

<span data-ttu-id="84134-414">In `Startup.ConfigureServices`, `CustomTemplateEngine` sınıf `RazorTemplateEngine` ile MVC tarafından eklenen geçersiz kılmak:</span><span class="sxs-lookup"><span data-stu-id="84134-414">In `Startup.ConfigureServices`, override the `RazorTemplateEngine` added by MVC with the `CustomTemplateEngine` class:</span></span>

[!code-csharp[](razor/sample/Startup.cs?highlight=4&range=10-14)]

<span data-ttu-id="84134-415">'nin `CustomTemplateEngine`ifadesi üzerinde `return csharpDocument;` bir kesme noktası ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="84134-415">Set a breakpoint on the `return csharpDocument;` statement of `CustomTemplateEngine`.</span></span> <span data-ttu-id="84134-416">Program yürütme kesme noktasında durduğunda, değerini `generatedCode`görüntüleyin.</span><span class="sxs-lookup"><span data-stu-id="84134-416">When program execution stops at the breakpoint, view the value of `generatedCode`.</span></span>

![Oluşturulan Kodun Metin Görselleştirici görünümü](razor/_static/tvr.png)

::: moniker-end

## <a name="view-lookups-and-case-sensitivity"></a><span data-ttu-id="84134-418">Aramaları ve servis talebi hassasiyetini görüntüleme</span><span class="sxs-lookup"><span data-stu-id="84134-418">View lookups and case sensitivity</span></span>

<span data-ttu-id="84134-419">Razor görünüm motoru, görünümler için büyük/küçük harf duyarlı aramalar gerçekleştirir.</span><span class="sxs-lookup"><span data-stu-id="84134-419">The Razor view engine performs case-sensitive lookups for views.</span></span> <span data-ttu-id="84134-420">Ancak, gerçek arama temel dosya sistemi tarafından belirlenir:</span><span class="sxs-lookup"><span data-stu-id="84134-420">However, the actual lookup is determined by the underlying file system:</span></span>

* <span data-ttu-id="84134-421">Dosya tabanlı kaynak:</span><span class="sxs-lookup"><span data-stu-id="84134-421">File based source:</span></span>
  * <span data-ttu-id="84134-422">Büyük/küçük harf duyarsız dosya sistemlerine (örneğin, Windows) sahip işletim sistemlerinde, fiziksel dosya sağlayıcısı aramaları büyük/küçük harf duyarsızdır.</span><span class="sxs-lookup"><span data-stu-id="84134-422">On operating systems with case insensitive file systems (for example, Windows), physical file provider lookups are case insensitive.</span></span> <span data-ttu-id="84134-423">Örneğin, `return View("Test")` */Views/Home/Test.cshtml*, */Views/home/test.cshtml*ve diğer kasa varyantları için eşleşmeler elde edilir.</span><span class="sxs-lookup"><span data-stu-id="84134-423">For example, `return View("Test")` results in matches for */Views/Home/Test.cshtml*, */Views/home/test.cshtml*, and any other casing variant.</span></span>
  * <span data-ttu-id="84134-424">Büyük/küçük harf duyarlı dosya sistemlerinde (örneğin, Linux, OSX ve ile), `EmbeddedFileProvider`aramalar büyük/küçük harf duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="84134-424">On case-sensitive file systems (for example, Linux, OSX, and with `EmbeddedFileProvider`), lookups are case-sensitive.</span></span> <span data-ttu-id="84134-425">Örneğin, `return View("Test")` özellikle */Views/Home/Test.cshtml ile*eşleşir.</span><span class="sxs-lookup"><span data-stu-id="84134-425">For example, `return View("Test")` specifically matches */Views/Home/Test.cshtml*.</span></span>
* <span data-ttu-id="84134-426">Önceden derlenmiş görünümler: ASP.NET Core 2.0 ve daha sonra, önceden derlenmiş görünümleri yukarı bakarak tüm işletim sistemlerinde duyarsız durumda.</span><span class="sxs-lookup"><span data-stu-id="84134-426">Precompiled views: With ASP.NET Core 2.0 and later, looking up precompiled views is case insensitive on all operating systems.</span></span> <span data-ttu-id="84134-427">Davranış, fiziksel dosya sağlayıcısının Windows'daki davranışıyla aynıdır.</span><span class="sxs-lookup"><span data-stu-id="84134-427">The behavior is identical to physical file provider's behavior on Windows.</span></span> <span data-ttu-id="84134-428">Önceden derlenmiş iki görünüm yalnızca farklıysa, aramanın sonucu belirleyici değildir.</span><span class="sxs-lookup"><span data-stu-id="84134-428">If two precompiled views differ only in case, the result of lookup is non-deterministic.</span></span>

<span data-ttu-id="84134-429">Geliştiricilerin dosya ve dizin adlarının kasasını kasayla eşleştirmeleri tavsiye edilir:</span><span class="sxs-lookup"><span data-stu-id="84134-429">Developers are encouraged to match the casing of file and directory names to the casing of:</span></span>

* <span data-ttu-id="84134-430">Alan, denetleyici ve eylem adları.</span><span class="sxs-lookup"><span data-stu-id="84134-430">Area, controller, and action names.</span></span>
* <span data-ttu-id="84134-431">Jilet Sayfaları.</span><span class="sxs-lookup"><span data-stu-id="84134-431">Razor Pages.</span></span>

<span data-ttu-id="84134-432">Eşleşen servis talebi, dağıtımların temel dosya sisteminden bağımsız olarak görünümlerini bulmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="84134-432">Matching case ensures the deployments find their views regardless of the underlying file system.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="84134-433">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="84134-433">Additional resources</span></span>

<span data-ttu-id="84134-434">[ASP.NET Web Programlamaya Giriş Jilet Sözdizimini Kullanarak](/aspnet/web-pages/overview/getting-started/introducing-razor-syntax-c) Razor sözdizimi ile programlama birçok örnek sağlar.</span><span class="sxs-lookup"><span data-stu-id="84134-434">[Introduction to ASP.NET Web Programming Using the Razor Syntax](/aspnet/web-pages/overview/getting-started/introducing-razor-syntax-c) provides many samples of programming with Razor syntax.</span></span>
