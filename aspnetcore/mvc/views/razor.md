---
title: ASP.NET Core için Razor söz dizimi başvurusu
author: rick-anderson
description: RazorSunucu tabanlı kodu Web sayfalarına eklemek için biçimlendirme sözdizimi hakkında bilgi edinin.
ms.author: riande
ms.date: 02/12/2020
no-loc:
- appsettings.json
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
uid: mvc/views/razor
ms.openlocfilehash: 91e35a7cbd97e2bd6e77566362f02409915de7d7
ms.sourcegitcommit: 3f0ad1e513296ede1bff39a05be6c278e879afed
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96035716"
---
# <a name="no-locrazor-syntax-reference-for-aspnet-core"></a><span data-ttu-id="78e80-103">Razor ASP.NET Core için sözdizimi başvurusu</span><span class="sxs-lookup"><span data-stu-id="78e80-103">Razor syntax reference for ASP.NET Core</span></span>

<span data-ttu-id="78e80-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Taylor Mullen](https://twitter.com/ntaylormullen)ve [dan vicarel](https://github.com/Rabadash8820)</span><span class="sxs-lookup"><span data-stu-id="78e80-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Taylor Mullen](https://twitter.com/ntaylormullen), and [Dan Vicarel](https://github.com/Rabadash8820)</span></span>

<span data-ttu-id="78e80-105">Razor , Web sayfalarına sunucu tabanlı kod eklemeye yönelik biçimlendirme sözdizimidir.</span><span class="sxs-lookup"><span data-stu-id="78e80-105">Razor is a markup syntax for embedding server-based code into webpages.</span></span> <span data-ttu-id="78e80-106">RazorSöz dizimi, Razor biçimlendirme, C# ve HTML 'den oluşur.</span><span class="sxs-lookup"><span data-stu-id="78e80-106">The Razor syntax consists of Razor markup, C#, and HTML.</span></span> <span data-ttu-id="78e80-107">Genellikle içeren dosyaların Razor *. cshtml* dosya uzantısı vardır.</span><span class="sxs-lookup"><span data-stu-id="78e80-107">Files containing Razor generally have a *.cshtml* file extension.</span></span> <span data-ttu-id="78e80-108">RazorAyrıca, [ Razor Bileşenler](xref:blazor/components/index) dosyalarında (*. Razor*) bulunur.</span><span class="sxs-lookup"><span data-stu-id="78e80-108">Razor is also found in [Razor components](xref:blazor/components/index) files (*.razor*).</span></span>

## <a name="rendering-html"></a><span data-ttu-id="78e80-109">HTML işleniyor</span><span class="sxs-lookup"><span data-stu-id="78e80-109">Rendering HTML</span></span>

<span data-ttu-id="78e80-110">Varsayılan Razor DIL HTML 'dir.</span><span class="sxs-lookup"><span data-stu-id="78e80-110">The default Razor language is HTML.</span></span> <span data-ttu-id="78e80-111">Biçimlendirmeden HTML işleme HTML Razor dosyasından HTML işlenenden farklı değildir.</span><span class="sxs-lookup"><span data-stu-id="78e80-111">Rendering HTML from Razor markup is no different than rendering HTML from an HTML file.</span></span> <span data-ttu-id="78e80-112">*. Cshtml* Razor dosyalarındaki HTML işaretlemesi sunucu tarafından değiştirilmeden işlenir.</span><span class="sxs-lookup"><span data-stu-id="78e80-112">HTML markup in *.cshtml* Razor files is rendered by the server unchanged.</span></span>

## <a name="no-locrazor-syntax"></a><span data-ttu-id="78e80-113">Razor sözdizimi</span><span class="sxs-lookup"><span data-stu-id="78e80-113">Razor syntax</span></span>

<span data-ttu-id="78e80-114">Razor C# ' i destekler ve `@` HTML 'Den C# ' ye geçiş yapmak için sembolünü kullanır.</span><span class="sxs-lookup"><span data-stu-id="78e80-114">Razor supports C# and uses the `@` symbol to transition from HTML to C#.</span></span> <span data-ttu-id="78e80-115">Razor C# ifadelerini değerlendirir ve bunları HTML çıktısında işler.</span><span class="sxs-lookup"><span data-stu-id="78e80-115">Razor evaluates C# expressions and renders them in the HTML output.</span></span>

<span data-ttu-id="78e80-116">Bir `@` simgenin arkasından [ Razor ayrılmış bir anahtar sözcük](#razor-reserved-keywords)geldiğinde, bu, belirli bir Razor biçimlendirmeyi geçirir.</span><span class="sxs-lookup"><span data-stu-id="78e80-116">When an `@` symbol is followed by a [Razor reserved keyword](#razor-reserved-keywords), it transitions into Razor-specific markup.</span></span> <span data-ttu-id="78e80-117">Aksi halde, düz C# ' ye geçiş yapar.</span><span class="sxs-lookup"><span data-stu-id="78e80-117">Otherwise, it transitions into plain C#.</span></span>

<span data-ttu-id="78e80-118">`@`İşaretlemede bir sembolün çıkmak için Razor ikinci bir sembol kullanın `@` :</span><span class="sxs-lookup"><span data-stu-id="78e80-118">To escape an `@` symbol in Razor markup, use a second `@` symbol:</span></span>

```cshtml
<p>@@Username</p>
```

<span data-ttu-id="78e80-119">Kod, HTML 'de tek bir sembol ile işlenir `@` :</span><span class="sxs-lookup"><span data-stu-id="78e80-119">The code is rendered in HTML with a single `@` symbol:</span></span>

```html
<p>@Username</p>
```

<span data-ttu-id="78e80-120">HTML öznitelikleri ve e-posta adreslerini içeren içerik `@` sembol bir geçiş karakteri olarak kabul değildir.</span><span class="sxs-lookup"><span data-stu-id="78e80-120">HTML attributes and content containing email addresses don't treat the `@` symbol as a transition character.</span></span> <span data-ttu-id="78e80-121">Aşağıdaki örnekteki e-posta adresleri Razor ayrıştırılmadan dokunulmaz:</span><span class="sxs-lookup"><span data-stu-id="78e80-121">The email addresses in the following example are untouched by Razor parsing:</span></span>

```cshtml
<a href="mailto:Support@contoso.com">Support@contoso.com</a>
```

## <a name="implicit-no-locrazor-expressions"></a><span data-ttu-id="78e80-122">Örtük Razor ifadeler</span><span class="sxs-lookup"><span data-stu-id="78e80-122">Implicit Razor expressions</span></span>

<span data-ttu-id="78e80-123">Örtük Razor ifadeler `@` sonrasında C# kodu ile başlar:</span><span class="sxs-lookup"><span data-stu-id="78e80-123">Implicit Razor expressions start with `@` followed by C# code:</span></span>

```cshtml
<p>@DateTime.Now</p>
<p>@DateTime.IsLeapYear(2016)</p>
```

<span data-ttu-id="78e80-124">C# `await` anahtar sözcüğünün dışında, örtük ifadeler boşluk içermemelidir.</span><span class="sxs-lookup"><span data-stu-id="78e80-124">With the exception of the C# `await` keyword, implicit expressions must not contain spaces.</span></span> <span data-ttu-id="78e80-125">C# ifadesinde Temizleme işlemi varsa, boşluklar ıntermingled olabilir:</span><span class="sxs-lookup"><span data-stu-id="78e80-125">If the C# statement has a clear ending, spaces can be intermingled:</span></span>

```cshtml
<p>@await DoSomething("hello", "world")</p>
```

<span data-ttu-id="78e80-126">Parantez ( **cannot** ) içindeki karakterler `<>` bir HTML etiketi olarak yorumlandığından örtük ifadeler C# generics içeremez.</span><span class="sxs-lookup"><span data-stu-id="78e80-126">Implicit expressions **cannot** contain C# generics, as the characters inside the brackets (`<>`) are interpreted as an HTML tag.</span></span> <span data-ttu-id="78e80-127">Aşağıdaki kod geçerli **değil** :</span><span class="sxs-lookup"><span data-stu-id="78e80-127">The following code is **not** valid:</span></span>

```cshtml
<p>@GenericMethod<int>()</p>
```

<span data-ttu-id="78e80-128">Yukarıdaki kod, aşağıdakilerden birine benzer bir derleyici hatası oluşturur:</span><span class="sxs-lookup"><span data-stu-id="78e80-128">The preceding code generates a compiler error similar to one of the following:</span></span>

* <span data-ttu-id="78e80-129">"İnt" öğesi kapatılmamış.</span><span class="sxs-lookup"><span data-stu-id="78e80-129">The "int" element wasn't closed.</span></span> <span data-ttu-id="78e80-130">Tüm öğeler kendi kendini kapatıyor ya da eşleşen bir bitiş etiketine sahip olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="78e80-130">All elements must be either self-closing or have a matching end tag.</span></span>
* <span data-ttu-id="78e80-131">' GenericMethod ' Yöntem grubu temsilci olmayan ' Object ' türüne dönüştürülemiyor.</span><span class="sxs-lookup"><span data-stu-id="78e80-131">Cannot convert method group 'GenericMethod' to non-delegate type 'object'.</span></span> <span data-ttu-id="78e80-132">Yöntemi çağırmak mı istiyordunuz? "</span><span class="sxs-lookup"><span data-stu-id="78e80-132">Did you intend to invoke the method?\`</span></span>

<span data-ttu-id="78e80-133">Genel metot çağrılarının [açık bir Razor ifadede](#explicit-razor-expressions) veya [ Razor kod bloğunda](#razor-code-blocks)sarmalanması gerekir.</span><span class="sxs-lookup"><span data-stu-id="78e80-133">Generic method calls must be wrapped in an [explicit Razor expression](#explicit-razor-expressions) or a [Razor code block](#razor-code-blocks).</span></span>

## <a name="explicit-no-locrazor-expressions"></a><span data-ttu-id="78e80-134">Açık Razor ifadeler</span><span class="sxs-lookup"><span data-stu-id="78e80-134">Explicit Razor expressions</span></span>

<span data-ttu-id="78e80-135">Açık Razor ifadeler, `@` dengeli parantez içeren bir sembolden oluşur.</span><span class="sxs-lookup"><span data-stu-id="78e80-135">Explicit Razor expressions consist of an `@` symbol with balanced parenthesis.</span></span> <span data-ttu-id="78e80-136">Son haftanın saatini işlemek için aşağıdaki Razor biçimlendirme kullanılır:</span><span class="sxs-lookup"><span data-stu-id="78e80-136">To render last week's time, the following Razor markup is used:</span></span>

```cshtml
<p>Last week this time: @(DateTime.Now - TimeSpan.FromDays(7))</p>
```

<span data-ttu-id="78e80-137">Parantez içindeki içerikler `@()` değerlendirilir ve çıkışa işlenir.</span><span class="sxs-lookup"><span data-stu-id="78e80-137">Any content within the `@()` parenthesis is evaluated and rendered to the output.</span></span>

<span data-ttu-id="78e80-138">Önceki bölümde açıklanan örtük ifadeler, genellikle boşluk içeremez.</span><span class="sxs-lookup"><span data-stu-id="78e80-138">Implicit expressions, described in the previous section, generally can't contain spaces.</span></span> <span data-ttu-id="78e80-139">Aşağıdaki kodda, bir hafta geçerli saatten çıkarılır:</span><span class="sxs-lookup"><span data-stu-id="78e80-139">In the following code, one week isn't subtracted from the current time:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact.cshtml?range=17)]

<span data-ttu-id="78e80-140">Kod, aşağıdaki HTML 'yi işler:</span><span class="sxs-lookup"><span data-stu-id="78e80-140">The code renders the following HTML:</span></span>

```html
<p>Last week: 7/7/2016 4:39:52 PM - TimeSpan.FromDays(7)</p>
```

<span data-ttu-id="78e80-141">Açık ifadeler, metni bir ifade sonucuyla birleştirmek için kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="78e80-141">Explicit expressions can be used to concatenate text with an expression result:</span></span>

```cshtml
@{
    var joe = new Person("Joe", 33);
}

<p>Age@(joe.Age)</p>
```

<span data-ttu-id="78e80-142">Açık ifade olmadan `<p>Age@joe.Age</p>` bir e-posta adresi olarak kabul edilir ve `<p>Age@joe.Age</p>` işlenir.</span><span class="sxs-lookup"><span data-stu-id="78e80-142">Without the explicit expression, `<p>Age@joe.Age</p>` is treated as an email address, and `<p>Age@joe.Age</p>` is rendered.</span></span> <span data-ttu-id="78e80-143">Açık bir ifade olarak yazıldığında `<p>Age33</p>` işlenir.</span><span class="sxs-lookup"><span data-stu-id="78e80-143">When written as an explicit expression, `<p>Age33</p>` is rendered.</span></span>

<span data-ttu-id="78e80-144">Açık ifadeler, *. cshtml* dosyalarındaki genel metotlardan çıkış oluşturmak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="78e80-144">Explicit expressions can be used to render output from generic methods in *.cshtml* files.</span></span> <span data-ttu-id="78e80-145">Aşağıdaki biçimlendirme, C# genel parantezinin neden olduğu daha önce gösterilen hatayı nasıl düzeltebileceğiniz gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="78e80-145">The following markup shows how to correct the error shown earlier caused by the brackets of a C# generic.</span></span> <span data-ttu-id="78e80-146">Kod açık bir ifade olarak yazılır:</span><span class="sxs-lookup"><span data-stu-id="78e80-146">The code is written as an explicit expression:</span></span>

```cshtml
<p>@(GenericMethod<int>())</p>
```

## <a name="expression-encoding"></a><span data-ttu-id="78e80-147">İfade kodlaması</span><span class="sxs-lookup"><span data-stu-id="78e80-147">Expression encoding</span></span>

<span data-ttu-id="78e80-148">Dizeyi değerlendiren C# ifadeleri HTML kodlandı.</span><span class="sxs-lookup"><span data-stu-id="78e80-148">C# expressions that evaluate to a string are HTML encoded.</span></span> <span data-ttu-id="78e80-149">Sonucunu veren C# ifadeleri `IHtmlContent` doğrudan aracılığıyla işlenir `IHtmlContent.WriteTo` .</span><span class="sxs-lookup"><span data-stu-id="78e80-149">C# expressions that evaluate to `IHtmlContent` are rendered directly through `IHtmlContent.WriteTo`.</span></span> <span data-ttu-id="78e80-150">Değerlendirilmeyen C# ifadeleri `IHtmlContent` , işlenmeden önce bir dizeye dönüştürülür `ToString` ve kodlanabilir.</span><span class="sxs-lookup"><span data-stu-id="78e80-150">C# expressions that don't evaluate to `IHtmlContent` are converted to a string by `ToString` and encoded before they're rendered.</span></span>

```cshtml
@("<span>Hello World</span>")
```

<span data-ttu-id="78e80-151">Yukarıdaki kod, aşağıdaki HTML 'yi işler:</span><span class="sxs-lookup"><span data-stu-id="78e80-151">The preceding code renders the following HTML:</span></span>

```html
&lt;span&gt;Hello World&lt;/span&gt;
```

<span data-ttu-id="78e80-152">HTML tarayıcıda düz metin olarak gösterilir:</span><span class="sxs-lookup"><span data-stu-id="78e80-152">The HTML is shown in the browser as plain text:</span></span>

<span data-ttu-id="78e80-153">&lt;Aralık &gt; Merhaba Dünya &lt; /span&gt;</span><span class="sxs-lookup"><span data-stu-id="78e80-153">&lt;span&gt;Hello World&lt;/span&gt;</span></span>

<span data-ttu-id="78e80-154">`HtmlHelper.Raw` çıktı kodlanmamış, ancak HTML işaretlemesi olarak işlendi.</span><span class="sxs-lookup"><span data-stu-id="78e80-154">`HtmlHelper.Raw` output isn't encoded but rendered as HTML markup.</span></span>

> [!WARNING]
> <span data-ttu-id="78e80-155">`HtmlHelper.Raw`Ayıklanmış Kullanıcı girişinde kullanmak bir güvenlik riskidir.</span><span class="sxs-lookup"><span data-stu-id="78e80-155">Using `HtmlHelper.Raw` on unsanitized user input is a security risk.</span></span> <span data-ttu-id="78e80-156">Kullanıcı girişi kötü amaçlı JavaScript veya diğer kötüye kullanım içerebilir.</span><span class="sxs-lookup"><span data-stu-id="78e80-156">User input might contain malicious JavaScript or other exploits.</span></span> <span data-ttu-id="78e80-157">Kullanıcı girişinin Temizleme işlemi zordur.</span><span class="sxs-lookup"><span data-stu-id="78e80-157">Sanitizing user input is difficult.</span></span> <span data-ttu-id="78e80-158">`HtmlHelper.Raw`Kullanıcı girişiyle kullanmaktan kaçının.</span><span class="sxs-lookup"><span data-stu-id="78e80-158">Avoid using `HtmlHelper.Raw` with user input.</span></span>

```cshtml
@Html.Raw("<span>Hello World</span>")
```

<span data-ttu-id="78e80-159">Kod, aşağıdaki HTML 'yi işler:</span><span class="sxs-lookup"><span data-stu-id="78e80-159">The code renders the following HTML:</span></span>

```html
<span>Hello World</span>
```

## <a name="no-locrazor-code-blocks"></a><span data-ttu-id="78e80-160">Razor kod blokları</span><span class="sxs-lookup"><span data-stu-id="78e80-160">Razor code blocks</span></span>

<span data-ttu-id="78e80-161">Razor kod blokları ile başlar `@` ve tarafından alınmıştır `{}` .</span><span class="sxs-lookup"><span data-stu-id="78e80-161">Razor code blocks start with `@` and are enclosed by `{}`.</span></span> <span data-ttu-id="78e80-162">İfadelerin aksine, kod blokları içindeki C# kodu işlenmez.</span><span class="sxs-lookup"><span data-stu-id="78e80-162">Unlike expressions, C# code inside code blocks isn't rendered.</span></span> <span data-ttu-id="78e80-163">Bir görünümdeki kod blokları ve ifadeler aynı kapsamı paylaşır ve sırasıyla tanımlanmıştır:</span><span class="sxs-lookup"><span data-stu-id="78e80-163">Code blocks and expressions in a view share the same scope and are defined in order:</span></span>

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

<span data-ttu-id="78e80-164">Kod, aşağıdaki HTML 'yi işler:</span><span class="sxs-lookup"><span data-stu-id="78e80-164">The code renders the following HTML:</span></span>

```html
<p>The future depends on what you do today. - Mahatma Gandhi</p>
<p>Hate cannot drive out hate, only love can do that. - Martin Luther King, Jr.</p>
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="78e80-165">Kod blokları ' nda, şablon oluşturma yöntemleri olarak kullanılacak biçimlendirme ile [yerel işlevler](/dotnet/csharp/programming-guide/classes-and-structs/local-functions) bildirin:</span><span class="sxs-lookup"><span data-stu-id="78e80-165">In code blocks, declare [local functions](/dotnet/csharp/programming-guide/classes-and-structs/local-functions) with markup to serve as templating methods:</span></span>

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

<span data-ttu-id="78e80-166">Kod, aşağıdaki HTML 'yi işler:</span><span class="sxs-lookup"><span data-stu-id="78e80-166">The code renders the following HTML:</span></span>

```html
<p>Name: <strong>Mahatma Gandhi</strong></p>
<p>Name: <strong>Martin Luther King, Jr.</strong></p>
```

::: moniker-end

### <a name="implicit-transitions"></a><span data-ttu-id="78e80-167">Örtük geçişler</span><span class="sxs-lookup"><span data-stu-id="78e80-167">Implicit transitions</span></span>

<span data-ttu-id="78e80-168">Bir kod bloğundaki varsayılan dil C# ' dir, ancak Razor sayfa HTML 'ye geri dönebilir:</span><span class="sxs-lookup"><span data-stu-id="78e80-168">The default language in a code block is C#, but the Razor Page can transition back to HTML:</span></span>

```cshtml
@{
    var inCSharp = true;
    <p>Now in HTML, was in C# @inCSharp</p>
}
```

### <a name="explicit-delimited-transition"></a><span data-ttu-id="78e80-169">Açık sınırlı geçiş</span><span class="sxs-lookup"><span data-stu-id="78e80-169">Explicit delimited transition</span></span>

<span data-ttu-id="78e80-170">HTML oluşturması gereken bir kod bloğunun alt bölümünü tanımlamak için etiketi ile işleme için karakterleri çevreleyin Razor `<text>` :</span><span class="sxs-lookup"><span data-stu-id="78e80-170">To define a subsection of a code block that should render HTML, surround the characters for rendering with the Razor `<text>` tag:</span></span>

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    <text>Name: @person.Name</text>
}
```

<span data-ttu-id="78e80-171">HTML etiketiyle çevrelenmiş HTML 'yi işlemek için bu yaklaşımı kullanın.</span><span class="sxs-lookup"><span data-stu-id="78e80-171">Use this approach to render HTML that isn't surrounded by an HTML tag.</span></span> <span data-ttu-id="78e80-172">HTML veya etiket olmadan Razor bir Razor çalışma zamanı hatası oluşur.</span><span class="sxs-lookup"><span data-stu-id="78e80-172">Without an HTML or Razor tag, a Razor runtime error occurs.</span></span>

<span data-ttu-id="78e80-173">`<text>`Etiket, içerik işlerken boşluğu denetlemek için yararlıdır:</span><span class="sxs-lookup"><span data-stu-id="78e80-173">The `<text>` tag is useful to control whitespace when rendering content:</span></span>

* <span data-ttu-id="78e80-174">Yalnızca etiketi arasındaki içerik `<text>` işlenir.</span><span class="sxs-lookup"><span data-stu-id="78e80-174">Only the content between the `<text>` tag is rendered.</span></span>
* <span data-ttu-id="78e80-175">HTML çıktısında etiket görüntülenmeden önce veya sonra boşluk yok `<text>` .</span><span class="sxs-lookup"><span data-stu-id="78e80-175">No whitespace before or after the `<text>` tag appears in the HTML output.</span></span>

### <a name="explicit-line-transition"></a><span data-ttu-id="78e80-176">Açık satır geçişi</span><span class="sxs-lookup"><span data-stu-id="78e80-176">Explicit line transition</span></span>

<span data-ttu-id="78e80-177">Tüm satırın geri kalanını bir kod bloğu içinde HTML olarak işlemek için `@:` sözdizimi kullanın:</span><span class="sxs-lookup"><span data-stu-id="78e80-177">To render the rest of an entire line as HTML inside a code block, use `@:` syntax:</span></span>

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    @:Name: @person.Name
}
```

<span data-ttu-id="78e80-178">`@:`Kod içinde olmadan bir Razor çalışma zamanı hatası oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="78e80-178">Without the `@:` in the code, a Razor runtime error is generated.</span></span>

<span data-ttu-id="78e80-179">`@`Bir dosyadaki fazla karakter Razor , bloktaki daha sonra bulunan deyimlerde derleyici hatalarına neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="78e80-179">Extra `@` characters in a Razor file can cause compiler errors at statements later in the block.</span></span> <span data-ttu-id="78e80-180">Bu derleyici hatalarının anlaşılması zor olabilir çünkü asıl hata bildirilen hatadan önce oluşur.</span><span class="sxs-lookup"><span data-stu-id="78e80-180">These compiler errors can be difficult to understand because the actual error occurs before the reported error.</span></span> <span data-ttu-id="78e80-181">Bu hata, birden çok örtük/açık ifade tek bir kod bloğu içinde birleştirildikten sonra ortaktır.</span><span class="sxs-lookup"><span data-stu-id="78e80-181">This error is common after combining multiple implicit/explicit expressions into a single code block.</span></span>

## <a name="control-structures"></a><span data-ttu-id="78e80-182">Denetim yapıları</span><span class="sxs-lookup"><span data-stu-id="78e80-182">Control structures</span></span>

<span data-ttu-id="78e80-183">Denetim yapıları, kod bloklarının bir uzantısıdır.</span><span class="sxs-lookup"><span data-stu-id="78e80-183">Control structures are an extension of code blocks.</span></span> <span data-ttu-id="78e80-184">Kod bloklarının tüm yönleri (biçimlendirmeye geçme, satır içi C#) Ayrıca aşağıdaki yapılar için de geçerlidir:</span><span class="sxs-lookup"><span data-stu-id="78e80-184">All aspects of code blocks (transitioning to markup, inline C#) also apply to the following structures:</span></span>

### <a name="conditionals-if-else-if-else-and-switch"></a><span data-ttu-id="78e80-185">Koşullular `@if, else if, else, and @switch`</span><span class="sxs-lookup"><span data-stu-id="78e80-185">Conditionals `@if, else if, else, and @switch`</span></span>

<span data-ttu-id="78e80-186">`@if` kodun ne zaman çalışacağını denetler:</span><span class="sxs-lookup"><span data-stu-id="78e80-186">`@if` controls when code runs:</span></span>

```cshtml
@if (value % 2 == 0)
{
    <p>The value was even.</p>
}
```

<span data-ttu-id="78e80-187">`else` ve `else if` `@` sembol gerektirmez:</span><span class="sxs-lookup"><span data-stu-id="78e80-187">`else` and `else if` don't require the `@` symbol:</span></span>

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

<span data-ttu-id="78e80-188">Aşağıdaki biçimlendirme bir switch ifadesinin nasıl kullanılacağını göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="78e80-188">The following markup shows how to use a switch statement:</span></span>

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

### <a name="looping-for-foreach-while-and-do-while"></a><span data-ttu-id="78e80-189">Döngüye `@for, @foreach, @while, and @do while`</span><span class="sxs-lookup"><span data-stu-id="78e80-189">Looping `@for, @foreach, @while, and @do while`</span></span>

<span data-ttu-id="78e80-190">Şablonlu HTML, döngü denetim deyimleri ile oluşturulabilir.</span><span class="sxs-lookup"><span data-stu-id="78e80-190">Templated HTML can be rendered with looping control statements.</span></span> <span data-ttu-id="78e80-191">Bir kişi listesini işlemek için:</span><span class="sxs-lookup"><span data-stu-id="78e80-191">To render a list of people:</span></span>

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

<span data-ttu-id="78e80-192">Aşağıdaki döngü deyimleri desteklenir:</span><span class="sxs-lookup"><span data-stu-id="78e80-192">The following looping statements are supported:</span></span>

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

### <a name="compound-using"></a><span data-ttu-id="78e80-193">İstekteki `@using`</span><span class="sxs-lookup"><span data-stu-id="78e80-193">Compound `@using`</span></span>

<span data-ttu-id="78e80-194">C# ' de bir `using` nesne atılmış olduğundan emin olmak için bir ifade kullanılır.</span><span class="sxs-lookup"><span data-stu-id="78e80-194">In C#, a `using` statement is used to ensure an object is disposed.</span></span> <span data-ttu-id="78e80-195">Razor' De, ek içerik IÇEREN HTML Yardımcıları oluşturmak için aynı mekanizma kullanılır.</span><span class="sxs-lookup"><span data-stu-id="78e80-195">In Razor, the same mechanism is used to create HTML Helpers that contain additional content.</span></span> <span data-ttu-id="78e80-196">Aşağıdaki kodda, HTML Yardımcıları ifadesiyle bir etiketi işlerler `<form>` `@using` :</span><span class="sxs-lookup"><span data-stu-id="78e80-196">In the following code, HTML Helpers render a `<form>` tag with the `@using` statement:</span></span>

```cshtml
@using (Html.BeginForm())
{
    <div>
        Email: <input type="email" id="Email" value="">
        <button>Register</button>
    </div>
}
```

### `@try, catch, finally`

<span data-ttu-id="78e80-197">Özel durum işleme C# ile benzerdir:</span><span class="sxs-lookup"><span data-stu-id="78e80-197">Exception handling is similar to C#:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact7.cshtml)]

### `@lock`

<span data-ttu-id="78e80-198">Razor kilitleme deyimleriyle kritik bölümleri koruma özelliğine sahiptir:</span><span class="sxs-lookup"><span data-stu-id="78e80-198">Razor has the capability to protect critical sections with lock statements:</span></span>

```cshtml
@lock (SomeLock)
{
    // Do critical section work
}
```

### <a name="comments"></a><span data-ttu-id="78e80-199">Yorumlar</span><span class="sxs-lookup"><span data-stu-id="78e80-199">Comments</span></span>

<span data-ttu-id="78e80-200">Razor C# ve HTML açıklamalarını destekler:</span><span class="sxs-lookup"><span data-stu-id="78e80-200">Razor supports C# and HTML comments:</span></span>

```cshtml
@{
    /* C# comment */
    // Another C# comment
}
<!-- HTML comment -->
```

<span data-ttu-id="78e80-201">Kod, aşağıdaki HTML 'yi işler:</span><span class="sxs-lookup"><span data-stu-id="78e80-201">The code renders the following HTML:</span></span>

```html
<!-- HTML comment -->
```

<span data-ttu-id="78e80-202">Razor Web sayfası işlenmeden önce açıklamalar sunucu tarafından kaldırılır.</span><span class="sxs-lookup"><span data-stu-id="78e80-202">Razor comments are removed by the server before the webpage is rendered.</span></span> <span data-ttu-id="78e80-203">Razor`@*  *@`açıklamaları sınırlandırmak için kullanır.</span><span class="sxs-lookup"><span data-stu-id="78e80-203">Razor uses `@*  *@` to delimit comments.</span></span> <span data-ttu-id="78e80-204">Aşağıdaki kod açıklama olarak belirlenir, bu nedenle sunucu herhangi bir biçimlendirme oluşturmaz:</span><span class="sxs-lookup"><span data-stu-id="78e80-204">The following code is commented out, so the server doesn't render any markup:</span></span>

```cshtml
@*
    @{
        /* C# comment */
        // Another C# comment
    }
    <!-- HTML comment -->
*@
```

## <a name="directives"></a><span data-ttu-id="78e80-205">Yönergeler</span><span class="sxs-lookup"><span data-stu-id="78e80-205">Directives</span></span>

<span data-ttu-id="78e80-206">Razor yönergeler, simgeyi izleyen ayrılmış anahtar sözcüklerle örtük ifadelerle temsil edilir `@` .</span><span class="sxs-lookup"><span data-stu-id="78e80-206">Razor directives are represented by implicit expressions with reserved keywords following the `@` symbol.</span></span> <span data-ttu-id="78e80-207">Yönerge genellikle görünümün ayrıştırılma şeklini değiştirir veya farklı işlevleri sunar.</span><span class="sxs-lookup"><span data-stu-id="78e80-207">A directive typically changes the way a view is parsed or enables different functionality.</span></span>

<span data-ttu-id="78e80-208">RazorBir görünüm için kod üretme şeklini anlamak, yönergelerin nasıl çalıştığını anlamayı kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="78e80-208">Understanding how Razor generates code for a view makes it easier to understand how directives work.</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact8.cshtml)]

<span data-ttu-id="78e80-209">Kod, aşağıdakine benzer bir sınıf oluşturur:</span><span class="sxs-lookup"><span data-stu-id="78e80-209">The code generates a class similar to the following:</span></span>

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

<span data-ttu-id="78e80-210">Bu makalenin ilerleyen kısımlarında, [ Razor bir görünüm için oluşturulan C# sınıfını İnceleme](#inspect-the-razor-c-class-generated-for-a-view) bölümünde bu oluşturulan sınıfın nasıl görüntüleneceği açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="78e80-210">Later in this article, the section [Inspect the Razor C# class generated for a view](#inspect-the-razor-c-class-generated-for-a-view) explains how to view this generated class.</span></span>

### `@attribute`

<span data-ttu-id="78e80-211">`@attribute`Yönergesi verilen özniteliği oluşturulan sayfanın veya görünümün sınıfına ekler.</span><span class="sxs-lookup"><span data-stu-id="78e80-211">The `@attribute` directive adds the given attribute to the class of the generated page or view.</span></span> <span data-ttu-id="78e80-212">Aşağıdaki örnek `[Authorize]` özniteliğini ekler:</span><span class="sxs-lookup"><span data-stu-id="78e80-212">The following example adds the `[Authorize]` attribute:</span></span>

```cshtml
@attribute [Authorize]
```

::: moniker range=">= aspnetcore-3.0"

### `@code`

<span data-ttu-id="78e80-213">*Bu senaryo yalnızca Razor Bileşenler (. Razor) için geçerlidir.*</span><span class="sxs-lookup"><span data-stu-id="78e80-213">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="78e80-214">`@code`Blok, bir bileşenin [ Razor ](xref:blazor/components/index) bir bileşene C# üyeleri (alanlar, Özellikler ve Yöntemler) eklemesini sağlar:</span><span class="sxs-lookup"><span data-stu-id="78e80-214">The `@code` block enables a [Razor component](xref:blazor/components/index) to add C# members (fields, properties, and methods) to a component:</span></span>

```razor
@code {
    // C# members (fields, properties, and methods)
}
```

<span data-ttu-id="78e80-215">RazorBileşenler için `@code` bir diğer addır [`@functions`](#functions) ve önerilir `@functions` .</span><span class="sxs-lookup"><span data-stu-id="78e80-215">For Razor components, `@code` is an alias of [`@functions`](#functions) and recommended over `@functions`.</span></span> <span data-ttu-id="78e80-216">Birden çok `@code` blok izin verilir.</span><span class="sxs-lookup"><span data-stu-id="78e80-216">More than one `@code` block is permissible.</span></span>

::: moniker-end

### `@functions`

<span data-ttu-id="78e80-217">`@functions`Yönergesi, oluşturulan sınıfa C# üyeleri (alanlar, Özellikler ve Yöntemler) eklemeyi sağlar:</span><span class="sxs-lookup"><span data-stu-id="78e80-217">The `@functions` directive enables adding C# members (fields, properties, and methods) to the generated class:</span></span>

```cshtml
@functions {
    // C# members (fields, properties, and methods)
}
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="78e80-218">[ Razor Bileşenler](xref:blazor/components/index)' de `@code` `@functions` C# üyelerini eklemek için ' i kullanın.</span><span class="sxs-lookup"><span data-stu-id="78e80-218">In [Razor components](xref:blazor/components/index), use `@code` over `@functions` to add C# members.</span></span>

::: moniker-end

<span data-ttu-id="78e80-219">Örnek:</span><span class="sxs-lookup"><span data-stu-id="78e80-219">For example:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact6.cshtml)]

<span data-ttu-id="78e80-220">Kod, aşağıdaki HTML işaretlemesini oluşturur:</span><span class="sxs-lookup"><span data-stu-id="78e80-220">The code generates the following HTML markup:</span></span>

```html
<div>From method: Hello</div>
```

<span data-ttu-id="78e80-221">Aşağıdaki kod, oluşturulan Razor C# sınıfıdır:</span><span class="sxs-lookup"><span data-stu-id="78e80-221">The following code is the generated Razor C# class:</span></span>

[!code-csharp[](razor/sample/Classes/Views_Home_Test_cshtml.cs?range=1-19)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="78e80-222">`@functions` Yöntemler, işaretlemelerdeki şablon oluşturma yöntemleri olarak görev yapar:</span><span class="sxs-lookup"><span data-stu-id="78e80-222">`@functions` methods serve as templating methods when they have markup:</span></span>

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

<span data-ttu-id="78e80-223">Kod, aşağıdaki HTML 'yi işler:</span><span class="sxs-lookup"><span data-stu-id="78e80-223">The code renders the following HTML:</span></span>

```html
<p>Name: <strong>Mahatma Gandhi</strong></p>
<p>Name: <strong>Martin Luther King, Jr.</strong></p>
```

### `@implements`

<span data-ttu-id="78e80-224">`@implements`Yönergesi, oluşturulan sınıf için bir arabirim uygular.</span><span class="sxs-lookup"><span data-stu-id="78e80-224">The `@implements` directive implements an interface for the generated class.</span></span>

<span data-ttu-id="78e80-225">Aşağıdaki örnek, <xref:System.IDisposable?displayProperty=fullName> <xref:System.IDisposable.Dispose*> yönteminin çağrılabilmesi için uygular:</span><span class="sxs-lookup"><span data-stu-id="78e80-225">The following example implements <xref:System.IDisposable?displayProperty=fullName> so that the <xref:System.IDisposable.Dispose*> method can be called:</span></span>

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

### `@inherits`

<span data-ttu-id="78e80-226">`@inherits`Yönergesi, görünümün devraldığı sınıfın tam denetimini sağlar:</span><span class="sxs-lookup"><span data-stu-id="78e80-226">The `@inherits` directive provides full control of the class the view inherits:</span></span>

```cshtml
@inherits TypeNameOfClassToInheritFrom
```

<span data-ttu-id="78e80-227">Aşağıdaki kod özel bir Razor sayfa türüdür:</span><span class="sxs-lookup"><span data-stu-id="78e80-227">The following code is a custom Razor page type:</span></span>

[!code-csharp[](razor/sample/Classes/CustomRazorPage.cs)]

<span data-ttu-id="78e80-228">, `CustomText` Bir görünümde görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="78e80-228">The `CustomText` is displayed in a view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact10.cshtml)]

<span data-ttu-id="78e80-229">Kod, aşağıdaki HTML 'yi işler:</span><span class="sxs-lookup"><span data-stu-id="78e80-229">The code renders the following HTML:</span></span>

```html
<div>
    Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping
    a slop bucket on the street below.
</div>
```

 <span data-ttu-id="78e80-230">`@model``@inherits`aynı görünümde kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="78e80-230">`@model` and `@inherits` can be used in the same view.</span></span> <span data-ttu-id="78e80-231">`@inherits` görünümün içeri aktardığı bir *_ViewImports. cshtml* dosyasında olabilir:</span><span class="sxs-lookup"><span data-stu-id="78e80-231">`@inherits` can be in a *_ViewImports.cshtml* file that the view imports:</span></span>

[!code-cshtml[](razor/sample/Views/_ViewImportsModel.cshtml)]

<span data-ttu-id="78e80-232">Aşağıdaki kod, türü kesin belirlenmiş bir görünümün örneğidir:</span><span class="sxs-lookup"><span data-stu-id="78e80-232">The following code is an example of a strongly-typed view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Login1.cshtml)]

<span data-ttu-id="78e80-233">" rick@contoso.com " Modelde geçirilirse, görünüm AŞAĞıDAKI HTML işaretlemesini oluşturur:</span><span class="sxs-lookup"><span data-stu-id="78e80-233">If "rick@contoso.com" is passed in the model, the view generates the following HTML markup:</span></span>

```html
<div>The Login Email: rick@contoso.com</div>
<div>
    Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping
    a slop bucket on the street below.
</div>
```

### `@inject`

<span data-ttu-id="78e80-234">`@inject`Yönergesi, Razor sayfanın [hizmet kapsayıcısından](xref:fundamentals/dependency-injection) bir hizmeti bir görünüme eklemesine olanak sağlar.</span><span class="sxs-lookup"><span data-stu-id="78e80-234">The `@inject` directive enables the Razor Page to inject a service from the [service container](xref:fundamentals/dependency-injection) into a view.</span></span> <span data-ttu-id="78e80-235">Daha fazla bilgi için bkz. [görünümlere bağımlılık ekleme](xref:mvc/views/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="78e80-235">For more information, see [Dependency injection into views](xref:mvc/views/dependency-injection).</span></span>

::: moniker range=">= aspnetcore-3.0"

### `@layout`

<span data-ttu-id="78e80-236">*Bu senaryo yalnızca Razor Bileşenler (. Razor) için geçerlidir.*</span><span class="sxs-lookup"><span data-stu-id="78e80-236">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="78e80-237">`@layout`Yönergesi bir bileşenin yerleşimini belirtir Razor .</span><span class="sxs-lookup"><span data-stu-id="78e80-237">The `@layout` directive specifies a layout for a Razor component.</span></span> <span data-ttu-id="78e80-238">Düzen bileşenleri kod yinelemeyi ve tutarsızlığın önüne geçmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="78e80-238">Layout components are used to avoid code duplication and inconsistency.</span></span> <span data-ttu-id="78e80-239">Daha fazla bilgi için bkz. <xref:blazor/layouts>.</span><span class="sxs-lookup"><span data-stu-id="78e80-239">For more information, see <xref:blazor/layouts>.</span></span>

::: moniker-end

### `@model`

<span data-ttu-id="78e80-240">*Bu senaryo yalnızca MVC görünümleri ve Razor sayfaları (. cshtml) için geçerlidir.*</span><span class="sxs-lookup"><span data-stu-id="78e80-240">*This scenario only applies to MVC views and Razor Pages (.cshtml).*</span></span>

<span data-ttu-id="78e80-241">`@model`Yönergesi bir görünüm veya sayfaya geçirilen modelin türünü belirtir:</span><span class="sxs-lookup"><span data-stu-id="78e80-241">The `@model` directive specifies the type of the model passed to a view or page:</span></span>

```cshtml
@model TypeNameOfModel
```

<span data-ttu-id="78e80-242">RazorBireysel kullanıcı hesaplarıyla oluşturulan bir ASP.NET Core MVC veya sayfaları uygulamasında, *views/Account/Login. cshtml* aşağıdaki model bildirimini içerir:</span><span class="sxs-lookup"><span data-stu-id="78e80-242">In an ASP.NET Core MVC or Razor Pages app created with individual user accounts, *Views/Account/Login.cshtml* contains the following model declaration:</span></span>

```cshtml
@model LoginViewModel
```

<span data-ttu-id="78e80-243">Oluşturulan sınıf şuradan devralır `RazorPage<dynamic>` :</span><span class="sxs-lookup"><span data-stu-id="78e80-243">The class generated inherits from `RazorPage<dynamic>`:</span></span>

```csharp
public class _Views_Account_Login_cshtml : RazorPage<LoginViewModel>
```

<span data-ttu-id="78e80-244">Razor`Model`görünüme geçirilen modele erişim için bir özellik sunar:</span><span class="sxs-lookup"><span data-stu-id="78e80-244">Razor exposes a `Model` property for accessing the model passed to the view:</span></span>

```cshtml
<div>The Login Email: @Model.Email</div>
```

<span data-ttu-id="78e80-245">`@model`Yönerge, özelliğin türünü belirtir `Model` .</span><span class="sxs-lookup"><span data-stu-id="78e80-245">The `@model` directive specifies the type of the `Model` property.</span></span> <span data-ttu-id="78e80-246">Yönergesi, `T` `RazorPage<T>` görünümün türettiği oluşturulan sınıfın öğesini belirtir.</span><span class="sxs-lookup"><span data-stu-id="78e80-246">The directive specifies the `T` in `RazorPage<T>` that the generated class that the view derives from.</span></span> <span data-ttu-id="78e80-247">`@model`Yönerge belirtilmemişse, `Model` özelliği türündedir `dynamic` .</span><span class="sxs-lookup"><span data-stu-id="78e80-247">If the `@model` directive isn't specified, the `Model` property is of type `dynamic`.</span></span> <span data-ttu-id="78e80-248">Daha fazla bilgi için bkz. [türü kesin belirlenmiş modeller ve @model anahtar sözcüğü](xref:tutorials/first-mvc-app/adding-model#strongly-typed-models-and-the--keyword).</span><span class="sxs-lookup"><span data-stu-id="78e80-248">For more information, see [Strongly typed models and the @model keyword](xref:tutorials/first-mvc-app/adding-model#strongly-typed-models-and-the--keyword).</span></span>

### `@namespace`

<span data-ttu-id="78e80-249">`@namespace`Yönergesi:</span><span class="sxs-lookup"><span data-stu-id="78e80-249">The `@namespace` directive:</span></span>

* <span data-ttu-id="78e80-250">Oluşturulan Razor sayfa, MVC görünümü veya bileşen sınıfının ad alanını ayarlar Razor .</span><span class="sxs-lookup"><span data-stu-id="78e80-250">Sets the namespace of the class of the generated Razor page, MVC view, or Razor component.</span></span>
* <span data-ttu-id="78e80-251">Bir sayfa, görünüm veya bileşen sınıfının kök türetilmiş ad alanlarını dizin ağacındaki en yakın içeri aktarmalar dosyasından, *_ViewImports. cshtml* (görünümler veya sayfalar) veya *_Imports. Razor* ( Razor bileşenler) olarak ayarlar.</span><span class="sxs-lookup"><span data-stu-id="78e80-251">Sets the root derived namespaces of a pages, views, or components classes from the closest imports file in the directory tree, *_ViewImports.cshtml* (views or pages) or *_Imports.razor* (Razor components).</span></span>

```cshtml
@namespace Your.Namespace.Here
```

<span data-ttu-id="78e80-252">RazorAşağıdaki tabloda gösterilen sayfalar örneği için:</span><span class="sxs-lookup"><span data-stu-id="78e80-252">For the Razor Pages example shown in the following table:</span></span>

* <span data-ttu-id="78e80-253">Her sayfa *sayfaları/_ViewImports. cshtml*'yi içeri aktarır.</span><span class="sxs-lookup"><span data-stu-id="78e80-253">Each page imports *Pages/_ViewImports.cshtml*.</span></span>
* <span data-ttu-id="78e80-254">*Pages/_ViewImports. cshtml* içerir `@namespace Hello.World` .</span><span class="sxs-lookup"><span data-stu-id="78e80-254">*Pages/_ViewImports.cshtml* contains `@namespace Hello.World`.</span></span>
* <span data-ttu-id="78e80-255">Her sayfa `Hello.World` , ad alanının kökü olarak bulunur.</span><span class="sxs-lookup"><span data-stu-id="78e80-255">Each page has `Hello.World` as the root of it's namespace.</span></span>

| <span data-ttu-id="78e80-256">Sayfa</span><span class="sxs-lookup"><span data-stu-id="78e80-256">Page</span></span>                                        | <span data-ttu-id="78e80-257">Ad Alanı</span><span class="sxs-lookup"><span data-stu-id="78e80-257">Namespace</span></span>                             |
| ------------------------------------------- | ------------------------------------- |
| <span data-ttu-id="78e80-258">*Pages/Index. cshtml*</span><span class="sxs-lookup"><span data-stu-id="78e80-258">*Pages/Index.cshtml*</span></span>                        | `Hello.World`                         |
| <span data-ttu-id="78e80-259">*Sayfa/fazla sayfa/sayfa. cshtml*</span><span class="sxs-lookup"><span data-stu-id="78e80-259">*Pages/MorePages/Page.cshtml*</span></span>               | `Hello.World.MorePages`               |
| <span data-ttu-id="78e80-260">*Pages/te Pages/Evente Pages/Page. cshtml*</span><span class="sxs-lookup"><span data-stu-id="78e80-260">*Pages/MorePages/EvenMorePages/Page.cshtml*</span></span> | `Hello.World.MorePages.EvenMorePages` |

<span data-ttu-id="78e80-261">Yukarıdaki ilişkiler, MVC görünümleri ve bileşenleriyle kullanılan dosyaları içeri aktarmak için geçerlidir Razor .</span><span class="sxs-lookup"><span data-stu-id="78e80-261">The preceding relationships apply to import files used with MVC views and Razor components.</span></span>

<span data-ttu-id="78e80-262">Birden çok içeri aktarma dosyasında bir `@namespace` yönerge olduğunda, kök ad alanını ayarlamak için dizin ağacındaki sayfaya, görünüme veya bileşene en yakın dosya kullanılır.</span><span class="sxs-lookup"><span data-stu-id="78e80-262">When multiple import files have a `@namespace` directive, the file closest to the page, view, or component in the directory tree is used to set the root namespace.</span></span>

<span data-ttu-id="78e80-263">Yukarıdaki örnekteki *evente Pages* klasöründe bir içeri aktarmalar dosyası varsa `@namespace Another.Planet` (veya *sayfa/değer sayfaları/Evente Pages/Page. cshtml* dosyası içeriyorsa `@namespace Another.Planet` ), sonuç aşağıdaki tabloda gösterilir.</span><span class="sxs-lookup"><span data-stu-id="78e80-263">If the *EvenMorePages* folder in the preceding example has an imports file with `@namespace Another.Planet` (or the *Pages/MorePages/EvenMorePages/Page.cshtml* file contains `@namespace Another.Planet`), the result is shown in the following table.</span></span>

| <span data-ttu-id="78e80-264">Sayfa</span><span class="sxs-lookup"><span data-stu-id="78e80-264">Page</span></span>                                        | <span data-ttu-id="78e80-265">Ad Alanı</span><span class="sxs-lookup"><span data-stu-id="78e80-265">Namespace</span></span>               |
| ------------------------------------------- | ----------------------- |
| <span data-ttu-id="78e80-266">*Pages/Index. cshtml*</span><span class="sxs-lookup"><span data-stu-id="78e80-266">*Pages/Index.cshtml*</span></span>                        | `Hello.World`           |
| <span data-ttu-id="78e80-267">*Sayfa/fazla sayfa/sayfa. cshtml*</span><span class="sxs-lookup"><span data-stu-id="78e80-267">*Pages/MorePages/Page.cshtml*</span></span>               | `Hello.World.MorePages` |
| <span data-ttu-id="78e80-268">*Pages/te Pages/Evente Pages/Page. cshtml*</span><span class="sxs-lookup"><span data-stu-id="78e80-268">*Pages/MorePages/EvenMorePages/Page.cshtml*</span></span> | `Another.Planet`        |

### `@page`

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="78e80-269">`@page`Yönergesinin, göründüğü dosyanın türüne bağlı olarak farklı etkileri vardır.</span><span class="sxs-lookup"><span data-stu-id="78e80-269">The `@page` directive has different effects depending on the type of the file where it appears.</span></span> <span data-ttu-id="78e80-270">Yönergesi:</span><span class="sxs-lookup"><span data-stu-id="78e80-270">The directive:</span></span>

* <span data-ttu-id="78e80-271">Bir *. cshtml* dosyasında, dosyanın bir sayfa olduğunu gösterir Razor .</span><span class="sxs-lookup"><span data-stu-id="78e80-271">In a *.cshtml* file indicates that the file is a Razor Page.</span></span> <span data-ttu-id="78e80-272">Daha fazla bilgi için bkz. [özel rotalar](xref:razor-pages/index#custom-routes) ve <xref:razor-pages/index> .</span><span class="sxs-lookup"><span data-stu-id="78e80-272">For more information, see [Custom routes](xref:razor-pages/index#custom-routes) and <xref:razor-pages/index>.</span></span>
* <span data-ttu-id="78e80-273">Bir Razor bileşenin istekleri doğrudan işlemesi gerektiğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="78e80-273">Specifies that a Razor component should handle requests directly.</span></span> <span data-ttu-id="78e80-274">Daha fazla bilgi için bkz. <xref:blazor/fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="78e80-274">For more information, see <xref:blazor/fundamentals/routing>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="78e80-275">`@page`Bir *. cshtml* dosyasının ilk satırındaki yönerge, dosyanın bir sayfa olduğunu gösterir Razor .</span><span class="sxs-lookup"><span data-stu-id="78e80-275">The `@page` directive on the first line of a *.cshtml* file indicates that the file is a Razor Page.</span></span> <span data-ttu-id="78e80-276">Daha fazla bilgi için bkz. <xref:razor-pages/index>.</span><span class="sxs-lookup"><span data-stu-id="78e80-276">For more information, see <xref:razor-pages/index>.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

### `@preservewhitespace`

<span data-ttu-id="78e80-277">*Bu senaryo yalnızca bileşenler için geçerlidir Razor ( `.razor` ).*</span><span class="sxs-lookup"><span data-stu-id="78e80-277">*This scenario only applies to Razor components (`.razor`).*</span></span>

<span data-ttu-id="78e80-278">`false`(Varsayılan) olarak ayarlandığında, bileşenlerden () oluşturulan İşaretlemede boşluk Razor `.razor` Şu durumlarda kaldırılır:</span><span class="sxs-lookup"><span data-stu-id="78e80-278">When set to `false` (default), whitespace in the rendered markup from Razor components (`.razor`) is removed if:</span></span>

* <span data-ttu-id="78e80-279">Bir öğe içinde baştaki veya sondaki.</span><span class="sxs-lookup"><span data-stu-id="78e80-279">Leading or trailing within an element.</span></span>
* <span data-ttu-id="78e80-280">Bir parametre içinde baştaki veya sondaki `RenderFragment` .</span><span class="sxs-lookup"><span data-stu-id="78e80-280">Leading or trailing within a `RenderFragment` parameter.</span></span> <span data-ttu-id="78e80-281">Örneğin, alt içerik başka bir bileşene geçirildi.</span><span class="sxs-lookup"><span data-stu-id="78e80-281">For example, child content passed to another component.</span></span>
* <span data-ttu-id="78e80-282">Ya da gibi bir C# kod bloğunun önünde veya sonrasında gelir `@if` `@foreach` .</span><span class="sxs-lookup"><span data-stu-id="78e80-282">It precedes or follows a C# code block, such as `@if` or `@foreach`.</span></span>

::: moniker-end

### `@section`

<span data-ttu-id="78e80-283">*Bu senaryo yalnızca MVC görünümleri ve Razor sayfaları (. cshtml) için geçerlidir.*</span><span class="sxs-lookup"><span data-stu-id="78e80-283">*This scenario only applies to MVC views and Razor Pages (.cshtml).*</span></span>

<span data-ttu-id="78e80-284">`@section`Yönerge, görünümler veya SAYFALARıN HTML sayfasının farklı bölümlerinde içerik işlemesini sağlamak Için [MVC ve Razor sayfa düzenleri](xref:mvc/views/layout) ile birlikte kullanılır.</span><span class="sxs-lookup"><span data-stu-id="78e80-284">The `@section` directive is used in conjunction with [MVC and Razor Pages layouts](xref:mvc/views/layout) to enable views or pages to render content in different parts of the HTML page.</span></span> <span data-ttu-id="78e80-285">Daha fazla bilgi için bkz. <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="78e80-285">For more information, see <xref:mvc/views/layout>.</span></span>

### `@using`

<span data-ttu-id="78e80-286">`@using`Yönergesi, `using` oluşturulan görünüme C# yönergesini ekler:</span><span class="sxs-lookup"><span data-stu-id="78e80-286">The `@using` directive adds the C# `using` directive to the generated view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact9.cshtml)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="78e80-287">[ Razor Bileşenlerinde](xref:blazor/components/index), `@using` hangi bileşenlerin kapsamda olduğunu da denetler.</span><span class="sxs-lookup"><span data-stu-id="78e80-287">In [Razor components](xref:blazor/components/index), `@using` also controls which components are in scope.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="directive-attributes"></a><span data-ttu-id="78e80-288">Yönerge öznitelikleri</span><span class="sxs-lookup"><span data-stu-id="78e80-288">Directive attributes</span></span>

<span data-ttu-id="78e80-289">Razor yönerge öznitelikleri, simgeyi izleyen ayrılmış anahtar sözcüklerle örtük ifadelerle temsil edilir `@` .</span><span class="sxs-lookup"><span data-stu-id="78e80-289">Razor directive attributes are represented by implicit expressions with reserved keywords following the `@` symbol.</span></span> <span data-ttu-id="78e80-290">Bir Directive özniteliği genellikle bir öğenin ayrıştırılma şeklini değiştirir veya farklı işlevlere izin vermez.</span><span class="sxs-lookup"><span data-stu-id="78e80-290">A directive attribute typically changes the way an element is parsed or enables different functionality.</span></span>

### `@attributes`

<span data-ttu-id="78e80-291">*Bu senaryo yalnızca Razor Bileşenler (. Razor) için geçerlidir.*</span><span class="sxs-lookup"><span data-stu-id="78e80-291">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="78e80-292">`@attributes` bir bileşenin bildirilmeyen öznitelikleri işlemesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="78e80-292">`@attributes` allows a component to render non-declared attributes.</span></span> <span data-ttu-id="78e80-293">Daha fazla bilgi için bkz. <xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters>.</span><span class="sxs-lookup"><span data-stu-id="78e80-293">For more information, see <xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters>.</span></span>

### `@bind`

<span data-ttu-id="78e80-294">*Bu senaryo yalnızca Razor Bileşenler (. Razor) için geçerlidir.*</span><span class="sxs-lookup"><span data-stu-id="78e80-294">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="78e80-295">Bileşenlerdeki veri bağlama, `@bind` özniteliğiyle gerçekleştirilir.</span><span class="sxs-lookup"><span data-stu-id="78e80-295">Data binding in components is accomplished with the `@bind` attribute.</span></span> <span data-ttu-id="78e80-296">Daha fazla bilgi için bkz. <xref:blazor/components/data-binding>.</span><span class="sxs-lookup"><span data-stu-id="78e80-296">For more information, see <xref:blazor/components/data-binding>.</span></span>

### `@on{EVENT}`

<span data-ttu-id="78e80-297">*Bu senaryo yalnızca Razor Bileşenler (. Razor) için geçerlidir.*</span><span class="sxs-lookup"><span data-stu-id="78e80-297">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="78e80-298">Razor bileşenler için olay işleme özellikleri sağlar.</span><span class="sxs-lookup"><span data-stu-id="78e80-298">Razor provides event handling features for components.</span></span> <span data-ttu-id="78e80-299">Daha fazla bilgi için bkz. <xref:blazor/components/event-handling>.</span><span class="sxs-lookup"><span data-stu-id="78e80-299">For more information, see <xref:blazor/components/event-handling>.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.1"

### `@on{EVENT}:preventDefault`

<span data-ttu-id="78e80-300">*Bu senaryo yalnızca Razor Bileşenler (. Razor) için geçerlidir.*</span><span class="sxs-lookup"><span data-stu-id="78e80-300">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="78e80-301">Olay için varsayılan eylemi engeller.</span><span class="sxs-lookup"><span data-stu-id="78e80-301">Prevents the default action for the event.</span></span>

### `@on{EVENT}:stopPropagation`

<span data-ttu-id="78e80-302">*Bu senaryo yalnızca Razor Bileşenler (. Razor) için geçerlidir.*</span><span class="sxs-lookup"><span data-stu-id="78e80-302">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="78e80-303">Olay için olay yaymayı sonlandırır.</span><span class="sxs-lookup"><span data-stu-id="78e80-303">Stops event propagation for the event.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### `@key`

<span data-ttu-id="78e80-304">*Bu senaryo yalnızca Razor Bileşenler (. Razor) için geçerlidir.*</span><span class="sxs-lookup"><span data-stu-id="78e80-304">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="78e80-305">`@key`Directive özniteliği, anahtar değerine göre öğelerin veya bileşenlerin korunmasını güvence altına almak için bileşenlerin algoritma almasına neden olur.</span><span class="sxs-lookup"><span data-stu-id="78e80-305">The `@key` directive attribute causes the components diffing algorithm to guarantee preservation of elements or components based on the key's value.</span></span> <span data-ttu-id="78e80-306">Daha fazla bilgi için bkz. <xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components>.</span><span class="sxs-lookup"><span data-stu-id="78e80-306">For more information, see <xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components>.</span></span>

### `@ref`

<span data-ttu-id="78e80-307">*Bu senaryo yalnızca Razor Bileşenler (. Razor) için geçerlidir.*</span><span class="sxs-lookup"><span data-stu-id="78e80-307">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="78e80-308">Bileşen başvuruları ( `@ref` ) bir bileşen örneğine başvurmak için bir yol sağlar, böylece bu örneğe komut verebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="78e80-308">Component references (`@ref`) provide a way to reference a component instance so that you can issue commands to that instance.</span></span> <span data-ttu-id="78e80-309">Daha fazla bilgi için bkz. <xref:blazor/components/index#capture-references-to-components>.</span><span class="sxs-lookup"><span data-stu-id="78e80-309">For more information, see <xref:blazor/components/index#capture-references-to-components>.</span></span>

### `@typeparam`

<span data-ttu-id="78e80-310">*Bu senaryo yalnızca Razor Bileşenler (. Razor) için geçerlidir.*</span><span class="sxs-lookup"><span data-stu-id="78e80-310">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="78e80-311">`@typeparam`Yönergesi, oluşturulan bileşen sınıfı için genel bir tür parametresi bildirir.</span><span class="sxs-lookup"><span data-stu-id="78e80-311">The `@typeparam` directive declares a generic type parameter for the generated component class.</span></span> <span data-ttu-id="78e80-312">Daha fazla bilgi için bkz. <xref:blazor/components/templated-components#generic-typed-components>.</span><span class="sxs-lookup"><span data-stu-id="78e80-312">For more information, see <xref:blazor/components/templated-components#generic-typed-components>.</span></span>

::: moniker-end

## <a name="templated-no-locrazor-delegates"></a><span data-ttu-id="78e80-313">Şablonlu Razor Temsilciler</span><span class="sxs-lookup"><span data-stu-id="78e80-313">Templated Razor delegates</span></span>

<span data-ttu-id="78e80-314">Razor Şablonlar, aşağıdaki biçimde bir UI parçacığı tanımlamanızı sağlar:</span><span class="sxs-lookup"><span data-stu-id="78e80-314">Razor templates allow you to define a UI snippet with the following format:</span></span>

```cshtml
@<tag>...</tag>
```

<span data-ttu-id="78e80-315">Aşağıdaki örnek, olarak şablonlu bir temsilcinin nasıl ekleneceğini gösterir Razor <xref:System.Func%602> .</span><span class="sxs-lookup"><span data-stu-id="78e80-315">The following example illustrates how to specify a templated Razor delegate as a <xref:System.Func%602>.</span></span> <span data-ttu-id="78e80-316">[Dinamik tür](/dotnet/csharp/programming-guide/types/using-type-dynamic) , temsilcinin sarmalayan yönteminin parametresi için belirtilir.</span><span class="sxs-lookup"><span data-stu-id="78e80-316">The [dynamic type](/dotnet/csharp/programming-guide/types/using-type-dynamic) is specified for the parameter of the method that the delegate encapsulates.</span></span> <span data-ttu-id="78e80-317">Bir [nesne türü](/dotnet/csharp/language-reference/keywords/object) , temsilcinin dönüş değeri olarak belirtilir.</span><span class="sxs-lookup"><span data-stu-id="78e80-317">An [object type](/dotnet/csharp/language-reference/keywords/object) is specified as the return value of the delegate.</span></span> <span data-ttu-id="78e80-318">Şablon, bir özelliğine sahip olan bir ile kullanılır <xref:System.Collections.Generic.List%601> `Pet` `Name` .</span><span class="sxs-lookup"><span data-stu-id="78e80-318">The template is used with a <xref:System.Collections.Generic.List%601> of `Pet` that has a `Name` property.</span></span>

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

<span data-ttu-id="78e80-319">Şablon, `pets` bir ifade tarafından sağlanan ile işlenir `foreach` :</span><span class="sxs-lookup"><span data-stu-id="78e80-319">The template is rendered with `pets` supplied by a `foreach` statement:</span></span>

```cshtml
@foreach (var pet in pets)
{
    @petTemplate(pet)
}
```

<span data-ttu-id="78e80-320">İşlenmiş çıkış:</span><span class="sxs-lookup"><span data-stu-id="78e80-320">Rendered output:</span></span>

```html
<p>You have a pet named <strong>Rin Tin Tin</strong>.</p>
<p>You have a pet named <strong>Mr. Bigglesworth</strong>.</p>
<p>You have a pet named <strong>K-9</strong>.</p>
```

<span data-ttu-id="78e80-321">RazorBir yönteme bağımsız değişken olarak bir satır içi şablon da sağlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="78e80-321">You can also supply an inline Razor template as an argument to a method.</span></span> <span data-ttu-id="78e80-322">Aşağıdaki örnekte, `Repeat` yöntemi bir Razor şablon alır.</span><span class="sxs-lookup"><span data-stu-id="78e80-322">In the following example, the `Repeat` method receives a Razor template.</span></span> <span data-ttu-id="78e80-323">Yöntemi, bir listeden sağlanan öğelerin tekrarlandığı HTML içeriğini oluşturmak için şablonunu kullanır:</span><span class="sxs-lookup"><span data-stu-id="78e80-323">The method uses the template to produce HTML content with repeats of items supplied from a list:</span></span>

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

<span data-ttu-id="78e80-324">Önceki örnekteki Evcil hayvanlar 'ların listesini kullanarak, `Repeat` yöntemi ile çağrılır:</span><span class="sxs-lookup"><span data-stu-id="78e80-324">Using the list of pets from the prior example, the `Repeat` method is called with:</span></span>

* <span data-ttu-id="78e80-325"><xref:System.Collections.Generic.List%601>`Pet`.</span><span class="sxs-lookup"><span data-stu-id="78e80-325"><xref:System.Collections.Generic.List%601> of `Pet`.</span></span>
* <span data-ttu-id="78e80-326">Her evcil hayvan kaç kez tekrarlanacak.</span><span class="sxs-lookup"><span data-stu-id="78e80-326">Number of times to repeat each pet.</span></span>
* <span data-ttu-id="78e80-327">Sıralanmamış bir listenin liste öğeleri için kullanılacak satır içi şablon.</span><span class="sxs-lookup"><span data-stu-id="78e80-327">Inline template to use for the list items of an unordered list.</span></span>

```cshtml
<ul>
    @Repeat(pets, 3, @<li>@item.Name</li>)
</ul>
```

<span data-ttu-id="78e80-328">İşlenmiş çıkış:</span><span class="sxs-lookup"><span data-stu-id="78e80-328">Rendered output:</span></span>

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

## <a name="tag-helpers"></a><span data-ttu-id="78e80-329">Etiket Yardımcıları</span><span class="sxs-lookup"><span data-stu-id="78e80-329">Tag Helpers</span></span>

<span data-ttu-id="78e80-330">*Bu senaryo yalnızca MVC görünümleri ve Razor sayfaları (. cshtml) için geçerlidir.*</span><span class="sxs-lookup"><span data-stu-id="78e80-330">*This scenario only applies to MVC views and Razor Pages (.cshtml).*</span></span>

<span data-ttu-id="78e80-331">[Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro)ile ilgili üç yönergeler vardır.</span><span class="sxs-lookup"><span data-stu-id="78e80-331">There are three directives that pertain to [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span>

| <span data-ttu-id="78e80-332">Deki</span><span class="sxs-lookup"><span data-stu-id="78e80-332">Directive</span></span> | <span data-ttu-id="78e80-333">İşlev</span><span class="sxs-lookup"><span data-stu-id="78e80-333">Function</span></span> |
| --------- | -------- |
| [`@addTagHelper`](xref:mvc/views/tag-helpers/intro#add-helper-label) | <span data-ttu-id="78e80-334">Etiket yardımcılarını bir görünüm için kullanılabilir hale getirir.</span><span class="sxs-lookup"><span data-stu-id="78e80-334">Makes Tag Helpers available to a view.</span></span> |
| [`@removeTagHelper`](xref:mvc/views/tag-helpers/intro#remove-razor-directives-label) | <span data-ttu-id="78e80-335">Daha önce bir görünümden eklenen etiket yardımcıları kaldırır.</span><span class="sxs-lookup"><span data-stu-id="78e80-335">Removes Tag Helpers previously added from a view.</span></span> |
| [`@tagHelperPrefix`](xref:mvc/views/tag-helpers/intro#prefix-razor-directives-label) | <span data-ttu-id="78e80-336">Etiket Yardımcısı desteğini etkinleştirmek ve etiket Yardımcısı kullanımını açık hale getirmek için bir etiket ön eki belirtir.</span><span class="sxs-lookup"><span data-stu-id="78e80-336">Specifies a tag prefix to enable Tag Helper support and to make Tag Helper usage explicit.</span></span> |

## <a name="no-locrazor-reserved-keywords"></a><span data-ttu-id="78e80-337">Razor ayrılmış anahtar sözcükler</span><span class="sxs-lookup"><span data-stu-id="78e80-337">Razor reserved keywords</span></span>

### <a name="no-locrazor-keywords"></a><span data-ttu-id="78e80-338">Razor lerimi</span><span class="sxs-lookup"><span data-stu-id="78e80-338">Razor keywords</span></span>

* <span data-ttu-id="78e80-339">`page` (ASP.NET Core 2,1 veya üzeri gerekir)</span><span class="sxs-lookup"><span data-stu-id="78e80-339">`page` (Requires ASP.NET Core 2.1 or later)</span></span>
* `namespace`
* `functions`
* `inherits`
* `model`
* `section`
* <span data-ttu-id="78e80-340">`helper` (Şu anda ASP.NET Core tarafından desteklenmiyor)</span><span class="sxs-lookup"><span data-stu-id="78e80-340">`helper` (Not currently supported by ASP.NET Core)</span></span>

<span data-ttu-id="78e80-341">Razor anahtar kelimelerden kaçışın `@(Razor Keyword)` (örneğin, `@(functions)` ).</span><span class="sxs-lookup"><span data-stu-id="78e80-341">Razor keywords are escaped with `@(Razor Keyword)` (for example, `@(functions)`).</span></span>

### <a name="c-no-locrazor-keywords"></a><span data-ttu-id="78e80-342">C# Razor anahtar sözcükleri</span><span class="sxs-lookup"><span data-stu-id="78e80-342">C# Razor keywords</span></span>

* `case`
* `do`
* `default`
* `for`
* `foreach`
* `if`
* `else`
* `lock`
* `switch`
* `try`
* `catch`
* `finally`
* `using`
* `while`

<span data-ttu-id="78e80-343">C# Razor anahtar sözcükleri ile çift kaçış olmalıdır `@(@C# Razor Keyword)` (örneğin, `@(@case)` ).</span><span class="sxs-lookup"><span data-stu-id="78e80-343">C# Razor keywords must be double-escaped with `@(@C# Razor Keyword)` (for example, `@(@case)`).</span></span> <span data-ttu-id="78e80-344">İlki `@` Razor Ayrıştırıcıdan çıkar.</span><span class="sxs-lookup"><span data-stu-id="78e80-344">The first `@` escapes the Razor parser.</span></span> <span data-ttu-id="78e80-345">İkincisi, `@` C# ayrıştırıcısının çıkar.</span><span class="sxs-lookup"><span data-stu-id="78e80-345">The second `@` escapes the C# parser.</span></span>

### <a name="reserved-keywords-not-used-by-no-locrazor"></a><span data-ttu-id="78e80-346">Ayrılmış anahtar sözcükler tarafından kullanılmıyor Razor</span><span class="sxs-lookup"><span data-stu-id="78e80-346">Reserved keywords not used by Razor</span></span>

* `class`

## <a name="inspect-the-no-locrazor-c-class-generated-for-a-view"></a><span data-ttu-id="78e80-347">RazorBir görünüm için oluşturulan C# sınıfını inceleyin</span><span class="sxs-lookup"><span data-stu-id="78e80-347">Inspect the Razor C# class generated for a view</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="78e80-348">.NET Core SDK 2,1 veya sonraki bir sürümü ile [ Razor SDK](xref:razor-pages/sdk) , dosyaların derlemesini işler Razor .</span><span class="sxs-lookup"><span data-stu-id="78e80-348">With .NET Core SDK 2.1 or later, the [Razor SDK](xref:razor-pages/sdk) handles compilation of Razor files.</span></span> <span data-ttu-id="78e80-349">Bir proje oluştururken, Razor SDK proje kökünde bir *obj/<build_configuration>/<target_framework_moniker>/ Razor* dizin oluşturur.</span><span class="sxs-lookup"><span data-stu-id="78e80-349">When building a project, the Razor SDK generates an *obj/<build_configuration>/<target_framework_moniker>/Razor* directory in the project root.</span></span> <span data-ttu-id="78e80-350">Dizin içindeki dizin yapısı *Razor* , projenin dizin yapısını yansıtır.</span><span class="sxs-lookup"><span data-stu-id="78e80-350">The directory structure within the *Razor* directory mirrors the project's directory structure.</span></span>

<span data-ttu-id="78e80-351">ASP.NET Core 2,1 Razor sayfaları projesinde .NET Core 2,1 ' i hedefleyen aşağıdaki dizin yapısını göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="78e80-351">Consider the following directory structure in an ASP.NET Core 2.1 Razor Pages project targeting .NET Core 2.1:</span></span>

```
 Areas/
   Admin/
     Pages/
       Index.cshtml
       Index.cshtml.cs
 Pages/
   Shared/
     _Layout.cshtml
   _ViewImports.cshtml
   _ViewStart.cshtml
   Index.cshtml
   Index.cshtml.cs
  ```

<span data-ttu-id="78e80-352">Projenin *hata ayıklama* yapılandırmasında oluşturulması aşağıdaki *obj* dizinini verir:</span><span class="sxs-lookup"><span data-stu-id="78e80-352">Building the project in *Debug* configuration yields the following *obj* directory:</span></span>

```
 obj/
   Debug/
     netcoreapp2.1/
       Razor/
         Areas/
           Admin/
             Pages/
               Index.g.cshtml.cs
         Pages/
           Shared/
             _Layout.g.cshtml.cs
           _ViewImports.g.cshtml.cs
           _ViewStart.g.cshtml.cs
           Index.g.cshtml.cs
```

<span data-ttu-id="78e80-353">*Pages/Index. cshtml* için oluşturulan sınıfı görüntülemek için *obj/Debug/netcoreapp 2.1/ Razor /Pages/Index.g.cshtml.cs* öğesini açın.</span><span class="sxs-lookup"><span data-stu-id="78e80-353">To view the generated class for *Pages/Index.cshtml*, open *obj/Debug/netcoreapp2.1/Razor/Pages/Index.g.cshtml.cs*.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="78e80-354">Aşağıdaki Sınıfı ASP.NET Core MVC projesine ekleyin:</span><span class="sxs-lookup"><span data-stu-id="78e80-354">Add the following class to the ASP.NET Core MVC project:</span></span>

[!code-csharp[](razor/sample/Utilities/CustomTemplateEngine.cs)]

<span data-ttu-id="78e80-355">' De `Startup.ConfigureServices` , `RazorTemplateEngine` ile MVC tarafından eklenen ' i şu sınıfla geçersiz kılın `CustomTemplateEngine` :</span><span class="sxs-lookup"><span data-stu-id="78e80-355">In `Startup.ConfigureServices`, override the `RazorTemplateEngine` added by MVC with the `CustomTemplateEngine` class:</span></span>

[!code-csharp[](razor/sample/Startup.cs?highlight=4&range=10-14)]

<span data-ttu-id="78e80-356">Bildiriminde bir kesme noktası ayarlayın `return csharpDocument;` `CustomTemplateEngine` .</span><span class="sxs-lookup"><span data-stu-id="78e80-356">Set a breakpoint on the `return csharpDocument;` statement of `CustomTemplateEngine`.</span></span> <span data-ttu-id="78e80-357">Kesme noktasında program yürütmesi durdurulduğunda değerini görüntüleyin `generatedCode` .</span><span class="sxs-lookup"><span data-stu-id="78e80-357">When program execution stops at the breakpoint, view the value of `generatedCode`.</span></span>

![GeneratedCode 'ın metin görselleştiricisi görünümü](razor/_static/tvr.png)

::: moniker-end

## <a name="view-lookups-and-case-sensitivity"></a><span data-ttu-id="78e80-359">Aramaları ve büyük/küçük harf duyarlılığını görüntüle</span><span class="sxs-lookup"><span data-stu-id="78e80-359">View lookups and case sensitivity</span></span>

<span data-ttu-id="78e80-360">RazorGörünüm altyapısı, görünümler için büyük/küçük harfe duyarlı aramalar gerçekleştirir.</span><span class="sxs-lookup"><span data-stu-id="78e80-360">The Razor view engine performs case-sensitive lookups for views.</span></span> <span data-ttu-id="78e80-361">Ancak gerçek arama, temel alınan dosya sistemine göre belirlenir:</span><span class="sxs-lookup"><span data-stu-id="78e80-361">However, the actual lookup is determined by the underlying file system:</span></span>

* <span data-ttu-id="78e80-362">Dosya tabanlı kaynak:</span><span class="sxs-lookup"><span data-stu-id="78e80-362">File based source:</span></span>
  * <span data-ttu-id="78e80-363">Büyük/küçük harf duyarsız dosya sistemlerine (örneğin, Windows) sahip işletim sistemlerinde, fiziksel dosya sağlayıcısı aramaları büyük/küçük harfe duyarsızdır.</span><span class="sxs-lookup"><span data-stu-id="78e80-363">On operating systems with case insensitive file systems (for example, Windows), physical file provider lookups are case insensitive.</span></span> <span data-ttu-id="78e80-364">Örneğin, `return View("Test")` */views/Home/test.exe*, */views/Home/test.exe* ve diğer tüm büyük/küçük harf çeşitlerüyle sonuçlanır.</span><span class="sxs-lookup"><span data-stu-id="78e80-364">For example, `return View("Test")` results in matches for */Views/Home/Test.cshtml*, */Views/home/test.cshtml*, and any other casing variant.</span></span>
  * <span data-ttu-id="78e80-365">Büyük/küçük harf duyarlı dosya sistemlerinde (örneğin, Linux, OSX ve ile `EmbeddedFileProvider` ), aramalar büyük/küçük harfe duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="78e80-365">On case-sensitive file systems (for example, Linux, OSX, and with `EmbeddedFileProvider`), lookups are case-sensitive.</span></span> <span data-ttu-id="78e80-366">Örneğin, `return View("Test")` özellikle */views/Home/test.exe. cshtml* ile eşleşir.</span><span class="sxs-lookup"><span data-stu-id="78e80-366">For example, `return View("Test")` specifically matches */Views/Home/Test.cshtml*.</span></span>
* <span data-ttu-id="78e80-367">Önceden derlenmiş görünümler: ASP.NET Core 2,0 ve üzeri sürümlerde, önceden derlenmiş görünümleri aramak tüm işletim sistemlerinde büyük/küçük harfe duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="78e80-367">Precompiled views: With ASP.NET Core 2.0 and later, looking up precompiled views is case insensitive on all operating systems.</span></span> <span data-ttu-id="78e80-368">Davranış, fiziksel dosya sağlayıcısının Windows 'daki davranışlarıyla aynıdır.</span><span class="sxs-lookup"><span data-stu-id="78e80-368">The behavior is identical to physical file provider's behavior on Windows.</span></span> <span data-ttu-id="78e80-369">Ön derlenmiş iki görünüm yalnızca bir durumda farklıysa, arama sonucu belirleyici değildir.</span><span class="sxs-lookup"><span data-stu-id="78e80-369">If two precompiled views differ only in case, the result of lookup is non-deterministic.</span></span>

<span data-ttu-id="78e80-370">Geliştiricilerin dosya ve dizin adlarını büyük küçük harf olarak eşleşmesi önerilir:</span><span class="sxs-lookup"><span data-stu-id="78e80-370">Developers are encouraged to match the casing of file and directory names to the casing of:</span></span>

* <span data-ttu-id="78e80-371">Alan, denetleyici ve eylem adları.</span><span class="sxs-lookup"><span data-stu-id="78e80-371">Area, controller, and action names.</span></span>
* <span data-ttu-id="78e80-372">Razor Sayfaları.</span><span class="sxs-lookup"><span data-stu-id="78e80-372">Razor Pages.</span></span>

<span data-ttu-id="78e80-373">Eşleşen durum, dağıtımların görünümlerini temel alınan dosya sisteminden bağımsız olarak bulmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="78e80-373">Matching case ensures the deployments find their views regardless of the underlying file system.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="78e80-374">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="78e80-374">Additional resources</span></span>

<span data-ttu-id="78e80-375">Şunu [kullanarak Razor ASP.NET Web programlamaya giriş Sözdizimi](/aspnet/web-pages/overview/getting-started/introducing-razor-syntax-c) , sözdizimi ile programlama için birçok örnek sağlar Razor .</span><span class="sxs-lookup"><span data-stu-id="78e80-375">[Introduction to ASP.NET Web Programming Using the Razor Syntax](/aspnet/web-pages/overview/getting-started/introducing-razor-syntax-c) provides many samples of programming with Razor syntax.</span></span>
