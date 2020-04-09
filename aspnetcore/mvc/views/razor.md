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
# <a name="razor-syntax-reference-for-aspnet-core"></a>ASP.NET Core için jilet sözdizimi başvurusu

Yazar: [Rick Anderson](https://twitter.com/RickAndMSFT), [Taylor Mullen](https://twitter.com/ntaylormullen), ve Dan [Vicarel](https://github.com/Rabadash8820)

Razor, sunucu tabanlı kodu web sayfalarına katıştırma amaçlı bir biçimlendirme sözdizimidir. Razor sözdizimi Razor biçimlendirme, C#ve HTML'den oluşur. Razor içeren dosyaların genellikle *.cshtml* dosya uzantısı vardır. Jilet de [Razor bileşenleri](xref:blazor/components) dosyaları bulunur (*.razor*).

## <a name="rendering-html"></a>HTML oluşturma

Varsayılan Razor dili HTML’dir. Razor işaretlemesinden HTML işlemek, HTML dosyasından HTML işlemekten farklı değildir. *.cshtml* Razor dosyalarındaki HTML biçimlendirmesi sunucu tarafından değiştirilmeden işlenir.

## <a name="razor-syntax"></a>Razor söz dizimi

Razor C# destekler `@` ve HTML'den C#'a geçiş için sembolü kullanır. Razor C# ifadelerini değerlendirir ve HTML çıkışında işler.

Bir `@` [sembol, Jilet ayrılmış](#razor-reserved-keywords)bir anahtar kelime tarafından takip edildiğinde, Razor'a özgü biçimlendirmeye dönüşür. Aksi takdirde, düz C# dönüşür.

Razor biçimlendirmesindeki bir `@` sembolden `@` kaçmak için ikinci bir sembol kullanın:

```cshtml
<p>@@Username</p>
```

Kod HTML'de tek `@` bir sembolle işlenir:

```html
<p>@Username</p>
```

HTML öznitelikleri ve e-posta adresleri `@` içeren içerik, simgeyi bir geçiş karakteri olarak ele almaz. Aşağıdaki örnekteki e-posta adreslerine Razor ayrıştma tarafından dokunulmaz:

```cshtml
<a href="mailto:Support@contoso.com">Support@contoso.com</a>
```

## <a name="implicit-razor-expressions"></a>Örtük Jilet ifadeleri

Örtük Jilet `@` ifadeleri C# kodu ile başlar:

```cshtml
<p>@DateTime.Now</p>
<p>@DateTime.IsLeapYear(2016)</p>
```

C# `await` anahtar sözcüğü dışında, örtük ifadeler boşluk içermemelidir. C# deyiminin net bir sonu varsa, boşluklar iç içe olabilir:

```cshtml
<p>@await DoSomething("hello", "world")</p>
```

Köşeli ayraçiçindeki karakterler (`<>`) HTML etiketi olarak yorumlandırılamadığı ndan, örtük ifadeler C# jenerikleri **içeremez.** Aşağıdaki kod geçerli **değildir:**

```cshtml
<p>@GenericMethod<int>()</p>
```

Önceki kod aşağıdakilerden birine benzer bir derleyici hatası oluşturur:

* "Int" öğesi kapalı değildi. Tüm öğeler kendi kendine kapanıyor olmalı veya eşleşen bir uç etiketine sahip olmalıdır.
* Yöntem grubu 'GenericMethod' 'object' olmayan temsilci türüne dönüştüremez. Bu yöntemi uygulamak niyetinde miydiniz?'

Genel yöntem çağrıları açık bir [Razor ifadesine](#explicit-razor-expressions) veya [Razor kod bloğuna](#razor-code-blocks)sarılmalıdır.

## <a name="explicit-razor-expressions"></a>Açık Jilet ifadeleri

Açık Razor ifadeler dengeli `@` parantez içeren bir sembolden oluşur. Geçen haftanın saatini işlemek için aşağıdaki Razor biçimlendirmesi kullanılır:

```cshtml
<p>Last week this time: @(DateTime.Now - TimeSpan.FromDays(7))</p>
```

Parantez içindeki `@()` tüm içerik değerlendirilir ve çıktıya işlenir.

Önceki bölümde açıklanan örtük ifadeler genellikle boşluk içeremez. Aşağıdaki kodda, bir hafta geçerli zamandan çıkarılmıyor:

[!code-cshtml[](razor/sample/Views/Home/Contact.cshtml?range=17)]

Kod aşağıdaki HTML'yi işler:

```html
<p>Last week: 7/7/2016 4:39:52 PM - TimeSpan.FromDays(7)</p>
```

Açık ifadeler, metni bir ifade sonucuyla birleştirmek için kullanılabilir:

```cshtml
@{
    var joe = new Person("Joe", 33);
}

<p>Age@(joe.Age)</p>
```

Açık ifade olmadan, `<p>Age@joe.Age</p>` bir e-posta `<p>Age@joe.Age</p>` adresi olarak kabul edilir ve işlenir. Açık bir ifade olarak `<p>Age33</p>` yazıldığında, işlenir.

Açık ifadeler *.cshtml* dosyalarındaki genel yöntemlerden çıktı işlemek için kullanılabilir. Aşağıdaki biçimlendirme, c# genel in parantezinin neden olduğu daha önce gösterilen hatanın nasıl düzeltilebildiğini gösterir. Kod açık bir ifade olarak yazılır:

```cshtml
<p>@(GenericMethod<int>())</p>
```

## <a name="expression-encoding"></a>İfade kodlama

Bir dize yi değerlendiren C# ifadeleri HTML kodlanır. Değerlendirmek için `IHtmlContent` değerlendiren C# ifadeleri doğrudan `IHtmlContent.WriteTo`. Değerlendirmeyen `IHtmlContent` C# ifadeleri, işlenmeden önce bir `ToString` dize dönüştürülür ve kodlanır.

```cshtml
@("<span>Hello World</span>")
```

Kod aşağıdaki HTML'yi işler:

```html
&lt;span&gt;Hello World&lt;/span&gt;
```

HTML tarayıcıda aşağıdaki gibi gösterilir:

```html
<span>Hello World</span>
```

`HtmlHelper.Raw`çıktı kodlanmış değil, HTML biçimlendirmesi olarak işlenir.

> [!WARNING]
> Sanitize edilmemiş kullanıcı girişi `HtmlHelper.Raw` kullanmak bir güvenlik riskidir. Kullanıcı girişi kötü amaçlı JavaScript veya diğer açıkları içerebilir. Kullanıcı girişini dezenfekte etmek zordur. Kullanıcı `HtmlHelper.Raw` girişi ile kullanmaktan kaçının.

```cshtml
@Html.Raw("<span>Hello World</span>")
```

Kod aşağıdaki HTML'yi işler:

```html
<span>Hello World</span>
```

## <a name="razor-code-blocks"></a>Jilet kod blokları

Jilet kod `@` blokları ile başlar `{}`ve ile çevrilidir. İfadelerin aksine, kod bloklarının içindeki C# kodu işlenmez. Görünümdeki kod blokları ve ifadeler aynı kapsamı paylaşır ve sırayla tanımlanır:

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

Kod aşağıdaki HTML'yi işler:

```html
<p>The future depends on what you do today. - Mahatma Gandhi</p>
<p>Hate cannot drive out hate, only love can do that. - Martin Luther King, Jr.</p>
```

::: moniker range=">= aspnetcore-3.0"

Kod bloklarında, yerel [işlevleri](/dotnet/csharp/programming-guide/classes-and-structs/local-functions) biçimlendirme yöntemleri olarak hizmet etmek üzere biçimlendirmeile bildirin:

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

Kod aşağıdaki HTML'yi işler:

```html
<p>Name: <strong>Mahatma Gandhi</strong></p>
<p>Name: <strong>Martin Luther King, Jr.</strong></p>
```

::: moniker-end

### <a name="implicit-transitions"></a>Örtük geçişler

Kod bloğundaki varsayılan dil C#'dır, ancak Razor Page HTML'ye geri dönebilir:

```cshtml
@{
    var inCSharp = true;
    <p>Now in HTML, was in C# @inCSharp</p>
}
```

### <a name="explicit-delimited-transition"></a>Açık sınırlı geçiş

HTML oluşturması gereken bir kod bloğunun alt bölümlerini tanımlamak için, `<text>` oluşturma için karakterleri Razor etiketiyle çevrelayın:

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    <text>Name: @person.Name</text>
}
```

Html etiketiyle çevrili olmayan HTML'yi işlemek için bu yaklaşımı kullanın. HTML veya Razor etiketi olmadan, Razor çalışma zamanı hatası oluşur.

Etiket, `<text>` içeriği işlerken beyaz alanı denetlemek için yararlıdır:

* Yalnızca `<text>` etiket arasındaki içerik işlenir.
* ETIKETten önce veya `<text>` sonra HTML çıkışında boşluk bulunmaz.

### <a name="explicit-line-transition"></a>Açık satır geçişi

Bir kod bloğu içinde tüm satırın geri kalanını `@:` HTML olarak işlemek için sözdizimini kullanın:

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    @:Name: @person.Name
}
```

Kodda `@:` olmadan, bir Razor çalışma zamanı hatası oluşturulur.

Razor `@` dosyasındaki ek karakterler, daha sonra bloktaki ifadelerde derleyici hatalarına neden olabilir. Gerçek hata bildirilen hatadan önce oluştuğundan, bu derleyici hatalarını anlamak zor olabilir. Bu hata, birden çok örtük/açık ifadeyi tek bir kod bloğunda birleştirdikten sonra sık görülür.

## <a name="control-structures"></a>Kontrol yapıları

Denetim yapıları kod bloklarının bir uzantısıdır. Kod bloklarının tüm yönleri (biçimlendirmeye geçiş, satır altı C#) aşağıdaki yapılar için de geçerlidir:

### <a name="conditionals-if-else-if-else-and-switch"></a>Koşullu \@lar eğer, başka if, else ve \@anahtar

`@if`kod çalıştığında denetimleri:

```cshtml
@if (value % 2 == 0)
{
    <p>The value was even.</p>
}
```

`else`ve `else if` `@` sembol gerekmez:

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

Aşağıdaki biçimlendirme, anahtar deyiminin nasıl kullanılacağını gösterir:

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

### <a name="looping-for-foreach-while-and-do-while"></a>\@Döngü için, \@foreach, \@süre, ve \@süre yapmak

Şablonlu HTML döngü denetim ifadeleri ile oluşturulabilir. Kişilerin listesini işlemek için:

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

Aşağıdaki döngü deyimleri desteklenir:

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

### <a name="compound-using"></a>Bileşik \@kullanarak

C#'da, `using` bir nesnenin atıldığından emin olmak için bir deyim kullanılır. Razor'da, aynı mekanizma ek içerik içeren HTML Yardımcıları oluşturmak için kullanılır. Aşağıdaki kodda, HTML Yardımcıları `<form>` `@using` deyimle birlikte bir etiket işler:

```cshtml
@using (Html.BeginForm())
{
    <div>
        Email: <input type="email" id="Email" value="">
        <button>Register</button>
    </div>
}
```

### <a name="try-catch-finally"></a>\@deneyin, yakalamak, nihayet

Özel durum işleme C#'a benzer:

[!code-cshtml[](razor/sample/Views/Home/Contact7.cshtml)]

### <a name="lock"></a>\@Kilit

Razor kilit ifadeleri ile kritik bölümleri korumak için yeteneğine sahiptir:

```cshtml
@lock (SomeLock)
{
    // Do critical section work
}
```

### <a name="comments"></a>Yorumlar

Razor C# ve HTML yorumlarını destekler:

```cshtml
@{
    /* C# comment */
    // Another C# comment
}
<!-- HTML comment -->
```

Kod aşağıdaki HTML'yi işler:

```html
<!-- HTML comment -->
```

Web sayfası oluşturulmadan önce razor yorumları sunucu tarafından kaldırılır. Razor `@*  *@` yorumları sınırlamak için kullanır. Aşağıdaki kod yorumlanır, böylece sunucu herhangi bir biçimlendirme oluşturmaz:

```cshtml
@*
    @{
        /* C# comment */
        // Another C# comment
    }
    <!-- HTML comment -->
*@
```

## <a name="directives"></a>Yönergeler

Razor `@` yönergeleri, sembolü izleyen ayrılmış anahtar kelimelerle örtük ifadelerle temsil edilir. Yönerge genellikle görünümün ayrıştırılmanını değiştirir veya farklı işlevsellik sağlar.

Razor'Un bir görünüm için kod nasıl oluşturduğunu anlamak, yönergelerin nasıl çalıştığını anlamayı kolaylaştırır.

[!code-cshtml[](razor/sample/Views/Home/Contact8.cshtml)]

Kod aşağıdakilere benzer bir sınıf oluşturur:

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

Bu makalenin ilerleyen bölümlerinde, [görünüm için oluşturulan Razor C# sınıfını incele](#inspect-the-razor-c-class-generated-for-a-view) bölümü, oluşturulan bu sınıfın nasıl görüntülenilecek olarak görüntülenilecek olduğunu açıklar.

### <a name="attribute"></a>\@Öznitelik

Yönerge, `@attribute` verilen özniteliği oluşturulan sayfa veya görünüm sınıfına ekler. Aşağıdaki örnek öznitelik `[Authorize]` ekler:

```cshtml
@attribute [Authorize]
```

::: moniker range=">= aspnetcore-3.0"

### <a name="code"></a>\@Kod

*Bu senaryo yalnızca Razor bileşenleri (.razor) için geçerlidir.*

Blok, `@code` Bir [Razor bileşeninin](xref:blazor/components) bir bileşene C# üyeleri (alanlar, özellikler ve yöntemler) eklemesini sağlar:

```razor
@code {
    // C# members (fields, properties, and methods)
}
```

Razor bileşenleri `@code` için, bir [`@functions`](#functions) takma ad `@functions`ve üzerinde önerilir. Birden `@code` fazla blokca izin verilir.

::: moniker-end

### <a name="functions"></a>\@Işlev

Yönerge, `@functions` oluşturulan sınıfa C# üyeleri (alanlar, özellikler ve yöntemler) eklenmesini sağlar:

```cshtml
@functions {
    // C# members (fields, properties, and methods)
}
```

::: moniker range=">= aspnetcore-3.0"

[Razor bileşenlerinde,](xref:blazor/components) `@functions` C# üyeleri eklemek için üzerinde kullanın. `@code`

::: moniker-end

Örneğin:

[!code-cshtml[](razor/sample/Views/Home/Contact6.cshtml)]

Kod aşağıdaki HTML biçimlendirmesini oluşturur:

```html
<div>From method: Hello</div>
```

Aşağıdaki kod oluşturulan Razor C# sınıfıdır:

[!code-csharp[](razor/sample/Classes/Views_Home_Test_cshtml.cs?range=1-19)]

::: moniker range=">= aspnetcore-3.0"

`@functions`yöntemleri biçimlendirme olduğunda cazip yöntemler olarak hizmet vermektedir:

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

Kod aşağıdaki HTML'yi işler:

```html
<p>Name: <strong>Mahatma Gandhi</strong></p>
<p>Name: <strong>Martin Luther King, Jr.</strong></p>
```

### <a name="implements"></a>\@Uygulayan

Yönerge, `@implements` oluşturulan sınıf için bir arabirim uygular.

Yöntemin çağrılması <xref:System.IDisposable?displayProperty=fullName> için <xref:System.IDisposable.Dispose*> aşağıdaki örnek uygular:

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

### <a name="inherits"></a>\@Devralır

Yönerge, `@inherits` görünümün devraldığı sınıfın tam denetimini sağlar:

```cshtml
@inherits TypeNameOfClassToInheritFrom
```

Aşağıdaki kod özel bir Razor sayfa türüdür:

[!code-csharp[](razor/sample/Classes/CustomRazorPage.cs)]

Görünümde `CustomText` görüntülenir:

[!code-cshtml[](razor/sample/Views/Home/Contact10.cshtml)]

Kod aşağıdaki HTML'yi işler:

```html
<div>
    Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping
    a slop bucket on the street below.
</div>
```

 `@model`ve `@inherits` aynı görünümde kullanılabilir. `@inherits`görünümü niçin içe aktardığı *bir _ViewImports.cshtml* dosyasında olabilir:

[!code-cshtml[](razor/sample/Views/_ViewImportsModel.cshtml)]

Aşağıdaki kod, güçlü bir şekilde yazılan bir görünüme örnektir:

[!code-cshtml[](razor/sample/Views/Home/Login1.cshtml)]

Modelderick@contoso.com" " " geçirilirse, görünüm aşağıdaki HTML biçimlendirmesini oluşturur:

```html
<div>The Login Email: rick@contoso.com</div>
<div>
    Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping
    a slop bucket on the street below.
</div>
```

### <a name="inject"></a>\@Enjekte

Yönerge, `@inject` Razor Page'in [servis kabından](xref:fundamentals/dependency-injection) bir hizmeti görünüme enjekte etmesini sağlar. Daha fazla bilgi [için, görünümlere Bağımlılık enjeksiyonu](xref:mvc/views/dependency-injection)bakın.

::: moniker range=">= aspnetcore-3.0"

### <a name="layout"></a>\@Düzen

*Bu senaryo yalnızca Razor bileşenleri (.razor) için geçerlidir.*

Yönerge, `@layout` bir Razor bileşeni için bir düzen belirtir. Düzen bileşenleri kod yinelemesini ve tutarsızlığı önlemek için kullanılır. Daha fazla bilgi için bkz. <xref:blazor/layouts>.

::: moniker-end

### <a name="model"></a>\@Modeli

*Bu senaryo yalnızca MVC görünümleri ve Razor Pages (.cshtml) için geçerlidir.*

Yönerge, `@model` bir görünüme veya sayfaya geçirilen modeltürünü belirtir:

```cshtml
@model TypeNameOfModel
```

Tek tek kullanıcı hesaplarıyla oluşturulan ASP.NET Core MVC veya Razor Pages *uygulamasında, Views/Account/Login.cshtml* aşağıdaki model bildirimini içerir:

```cshtml
@model LoginViewModel
```

Oluşturulan sınıf `RazorPage<dynamic>`devralır:

```csharp
public class _Views_Account_Login_cshtml : RazorPage<LoginViewModel>
```

Razor, görünüme geçirilen modele erişmek için bir `Model` özelliği ortaya çıkarır:

```cshtml
<div>The Login Email: @Model.Email</div>
```

Yönerge özelliğin `@model` `Model` türünü belirtir. Yönerge, görünümün `T` `RazorPage<T>` türetildiği oluşturulan sınıfa göre belirtir. `@model` Yönerge belirtilmemişse, `Model` özellik türündedir. `dynamic` Daha fazla bilgi için, [Güçlü olarak @model yazılan modellere ve anahtar kelimeye](xref:tutorials/first-mvc-app/adding-model#strongly-typed-models-and-the--keyword)bakın.

### <a name="namespace"></a>\@ad alanı

Yönerge: `@namespace`

* Oluşturulan Razor sayfasının, MVC görünümünün veya Razor bileşeninin sınıfının ad alanını ayarlar.
* Dizin ağacındaki, *_ViewImports.cshtml* (görünümler veya sayfalar) veya *_Imports.razor* (Jilet bileşenleri) en yakın içe alma dosyasından bir sayfa, görünüm veya bileşen sınıflarının kök türetilmiş ad alanlarını ayarlar.

```cshtml
@namespace Your.Namespace.Here
```

Aşağıdaki tabloda gösterilen Jilet Sayfaları örneği için:

* Her sayfa *Sayfa/_ViewImports.cshtml aktarım.*
* *Sayfalar/_ViewImports.cshtml* `@namespace Hello.World`içerir.
* Her sayfanın kökü ad alanı dır. `Hello.World`

| Sayfa                                        | Ad Alanı                             |
| ------------------------------------------- | ------------------------------------- |
| *Sayfalar/Index.cshtml*                        | `Hello.World`                         |
| *Sayfalar/MorePages/Page.cshtml*               | `Hello.World.MorePages`               |
| *Sayfalar/MorePages/EvenMorePages/Page.cshtml* | `Hello.World.MorePages.EvenMorePages` |

Önceki ilişkiler, MVC görünümleri ve Razor bileşenleriyle kullanılan içe aktarma dosyaları için geçerlidir.

Birden çok içe `@namespace` aktarma dosyasında bir yönerge varsa, dizin ağacındaki sayfaya, görünüme veya bileşene en yakın dosya kök ad alanını ayarlamak için kullanılır.

Önceki örnekteki *EvenMorePages* klasöründe `@namespace Another.Planet` (veya *Pages/MorePages/EvenMorePages/Page.cshtml* dosyasıiçeren) `@namespace Another.Planet`bir aktarım dosyası varsa, sonuç aşağıdaki tabloda gösterilir.

| Sayfa                                        | Ad Alanı               |
| ------------------------------------------- | ----------------------- |
| *Sayfalar/Index.cshtml*                        | `Hello.World`           |
| *Sayfalar/MorePages/Page.cshtml*               | `Hello.World.MorePages` |
| *Sayfalar/MorePages/EvenMorePages/Page.cshtml* | `Another.Planet`        |

### <a name="page"></a>\@Sayfası

::: moniker range=">= aspnetcore-3.0"

Yönerge, `@page` göründüğü dosyanın türüne bağlı olarak farklı etkilere sahiptir. Yönerge:

* *.cshtml* dosyasında dosyanın Bir Jilet Sayfası olduğunu gösterir. Daha fazla bilgi için <xref:razor-pages/index> [bkz.](xref:razor-pages/index#custom-routes)
* Bir Razor bileşeninin istekleri doğrudan işlemesi gerektiğini belirtir. Daha fazla bilgi için bkz. <xref:blazor/routing>.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

`@page` *.cshtml* dosyasının ilk satırındaki yönerge, dosyanın bir Jilet Sayfası olduğunu gösterir. Daha fazla bilgi için bkz. <xref:razor-pages/index>.

::: moniker-end

### <a name="section"></a>\@Bölüm

*Bu senaryo yalnızca MVC görünümleri ve Razor Pages (.cshtml) için geçerlidir.*

Yönerge, `@section` HTML sayfasının farklı bölümlerinde içerik işlemek için görünümleri veya sayfaları etkinleştirmek için [MVC ve Jilet Sayfaları düzenleri](xref:mvc/views/layout) ile birlikte kullanılır. Daha fazla bilgi için bkz. <xref:mvc/views/layout>.

### <a name="using"></a>\@kullanma

Yönerge, `@using` oluşturulan `using` görünüme C# yönergesi ekler:

[!code-cshtml[](razor/sample/Views/Home/Contact9.cshtml)]

::: moniker range=">= aspnetcore-3.0"

[Razor bileşenlerinde,](xref:blazor/components) `@using` hangi bileşenlerin kapsamda olduğunu da denetler.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="directive-attributes"></a>Yönerge öznitelikleri

### <a name="attributes"></a>\@Öznitelik

*Bu senaryo yalnızca Razor bileşenleri (.razor) için geçerlidir.*

`@attributes`bir bileşenin beyan edilmeyen öznitelikleri işlemesine izin verir. Daha fazla bilgi için bkz. <xref:blazor/components#attribute-splatting-and-arbitrary-parameters>.

### <a name="bind"></a>\@Bağlamak

*Bu senaryo yalnızca Razor bileşenleri (.razor) için geçerlidir.*

Bileşenlerde veri bağlama `@bind` öznitelik ile gerçekleştirilir. Daha fazla bilgi için bkz. <xref:blazor/data-binding>.

### <a name="onevent"></a>\@on{EVENT}

*Bu senaryo yalnızca Razor bileşenleri (.razor) için geçerlidir.*

Razor bileşenler için olay işleme özellikleri sağlar. Daha fazla bilgi için bkz. <xref:blazor/event-handling>.

::: moniker-end

::: moniker range=">= aspnetcore-3.1"

### <a name="oneventpreventdefault"></a>\@on{EVENT}:preventVarsayılan

*Bu senaryo yalnızca Razor bileşenleri (.razor) için geçerlidir.*

Olay için varsayılan eylemi önler.

### <a name="oneventstoppropagation"></a>\@on{EVENT}:stopPropagation

*Bu senaryo yalnızca Razor bileşenleri (.razor) için geçerlidir.*

Olay için olay yayılmasını durdurur.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="key"></a>\@anahtar

*Bu senaryo yalnızca Razor bileşenleri (.razor) için geçerlidir.*

Yönerge özniteliği, `@key` anahtar değerine dayalı öğelerin veya bileşenlerin korunmasını garanti etmek için bileşenlerin dağılarak algoritmaya neden olur. Daha fazla bilgi için bkz. <xref:blazor/components#use-key-to-control-the-preservation-of-elements-and-components>.

### <a name="ref"></a>\@ref

*Bu senaryo yalnızca Razor bileşenleri (.razor) için geçerlidir.*

Bileşen başvuruları`@ref`( ) bir bileşen örneğine başvurmanın bir yolunu sağlar, böylece bu örne komutlar verebilirsiniz. Daha fazla bilgi için bkz. <xref:blazor/components#capture-references-to-components>.

### <a name="typeparam"></a>\@daktison

*Bu senaryo yalnızca Razor bileşenleri (.razor) için geçerlidir.*

Yönerge, `@typeparam` oluşturulan bileşen sınıfı için genel bir tür parametresi bildirir. Daha fazla bilgi için bkz. <xref:blazor/templated-components#generic-typed-components>.

::: moniker-end

## <a name="templated-razor-delegates"></a>Şablonlanmış Razor delegeleri

Jilet şablonları, aşağıdaki biçime sahip bir UI snippet tanımlamanızı sağlar:

```cshtml
@<tag>...</tag>
```

Aşağıdaki örnekte, şablonlanmış bir Razor temsilcisinin <xref:System.Func%602>nasıl bir . olarak belirtilmeniz gösteriş [Dinamik tür,](/dotnet/csharp/programming-guide/types/using-type-dynamic) temsilcinin kapsülletiyi kapsayan yöntemin parametresi için belirtilir. Nesne [türü,](/dotnet/csharp/language-reference/keywords/object) temsilcinin geri dönüş değeri olarak belirtilir. Şablon, bir özelliği <xref:System.Collections.Generic.List%601> `Pet` olan bir `Name` ile kullanılır.

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

Şablon bir `foreach` deyim `pets` le sağlanır:

```cshtml
@foreach (var pet in pets)
{
    @petTemplate(pet)
}
```

İşlenen çıktı:

```html
<p>You have a pet named <strong>Rin Tin Tin</strong>.</p>
<p>You have a pet named <strong>Mr. Bigglesworth</strong>.</p>
<p>You have a pet named <strong>K-9</strong>.</p>
```

Bir yönteme bağımsız değişken olarak satır içinde Jilet şablonu da sağlayabilirsiniz. Aşağıdaki örnekte, `Repeat` yöntem bir Razor şablonu alır. Yöntem, bir listeden sağlanan öğelerin tekrarı ile HTML içeriği oluşturmak için şablonu kullanır:

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

Önceki örnekteki evcil hayvanların listesini `Repeat` kullanarak, yöntem şu şekilde çağrılır:

* <xref:System.Collections.Generic.List%601>ve `Pet`.
* Her evcil hayvanı tekrarlamak için kaç kez.
* Sıralanmamış bir listenin liste öğeleri için kullanılacak satır çizgisi şablonu.

```cshtml
<ul>
    @Repeat(pets, 3, @<li>@item.Name</li>)
</ul>
```

İşlenen çıktı:

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

## <a name="tag-helpers"></a>Etiket Yardımcıları

*Bu senaryo yalnızca MVC görünümleri ve Razor Pages (.cshtml) için geçerlidir.*

[Tag Helpers](xref:mvc/views/tag-helpers/intro)ile ilgili üç yönerge vardır.

| Yönergesi | İşlev |
| --------- | -------- |
| [`@addTagHelper`](xref:mvc/views/tag-helpers/intro#add-helper-label) | Tag Helpers'ı görünümde kullanılabilir hale getirir. |
| [`@removeTagHelper`](xref:mvc/views/tag-helpers/intro#remove-razor-directives-label) | Daha önce görünümden eklenen Etiket Yardımcıları kaldırır. |
| [`@tagHelperPrefix`](xref:mvc/views/tag-helpers/intro#prefix-razor-directives-label) | Tag Helper desteğini etkinleştirmek ve Tag Helper kullanımını açık hale getirmek için bir etiket öneki belirtir. |

## <a name="razor-reserved-keywords"></a>Jilet rezerve anahtar kelimeler

### <a name="razor-keywords"></a>Jilet anahtar kelimeler

* sayfa (Core 2.1 veya sonraki ASP.NET gerektirir)
* ad alanı
*  işlevleri
* Devralır
* model
* section
* yardımcı (Şu anda ASP.NET Core tarafından desteklenmiyor)

Jilet anahtar `@(Razor Keyword)` kelimeler (örneğin, `@(functions)`) ile kaçılır.

### <a name="c-razor-keywords"></a>C# Jilet anahtar kelimeleri

* büyük/küçük harf
* do
* default
* for
* foreach
* if
* else
* lock
* switch
* Deneme
* Yakalamak
* finally
* kullanma
* while

C# Razor anahtar kelimeleri ile `@(@C# Razor Keyword)` çift kaçtı `@(@case)`olmalıdır (örneğin, ). İlki `@` Razor parser'dan kaçar. İkincisi `@` C# parser'dan kaçar.

### <a name="reserved-keywords-not-used-by-razor"></a>Razor tarafından kullanılmayan ayrılmış anahtar kelimeler

* sınıf

## <a name="inspect-the-razor-c-class-generated-for-a-view"></a>Görünüm için oluşturulan Razor C# sınıfını inceleyin

::: moniker range=">= aspnetcore-2.1"

.NET Core SDK 2.1 veya sonraki ile [Razor SDK,](xref:razor-pages/sdk) Razor dosyalarının derlemesini işler. Bir proje oluştururken, Razor SDK proje kökünde *obj/<build_configuration>/<target_framework_moniker>/Razor* dizini oluşturur. *Razor* dizinindeki dizin yapısı projenin dizin yapısını yansıtıyor.

.NET Core 2.1'i hedefleyen ASP.NET Core 2.1 Razor Pages projesinde aşağıdaki dizin yapısını göz önünde bulundurun:

* **Alanlar/**
  * **Yönetici/**
    * **Sayfalar/**
      * *Index.cshtml*
      * *Index.cshtml.cs*
* **Sayfalar/**
  * **Paylaşılan/**
    * *_Layout.cshtml*
  * *_ViewImports.cshtml*
  * *_ViewStart.cshtml*
  * *Index.cshtml*
  * *Index.cshtml.cs*

*Projeyi Hata Ayıklama* yapılandırmasında oluşturmak aşağıdaki *obj* dizinini verir:

* **obj/**
  * **Hata Ayıklama/**
    * **netcoreapp2.1/**
      * **Jilet/**
        * **Alanlar/**
          * **Yönetici/**
            * **Sayfalar/**
              * *Index.g.cshtml.cs*
        * **Sayfalar/**
          * **Paylaşılan/**
            * *_Layout.g.cshtml.cs*
          * *_ViewImports.g.cshtml.cs*
          * *_ViewStart.g.cshtml.cs*
          * *Index.g.cshtml.cs*

*Pages/Index.cshtml*için oluşturulan sınıfı görüntülemek için, açık *obj/Debug/netcoreapp2.1/Razor/Pages/Index.g.cshtml.cs*.

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

ASP.NET Core MVC projesine aşağıdaki sınıfı ekleyin:

[!code-csharp[](razor/sample/Utilities/CustomTemplateEngine.cs)]

In `Startup.ConfigureServices`, `CustomTemplateEngine` sınıf `RazorTemplateEngine` ile MVC tarafından eklenen geçersiz kılmak:

[!code-csharp[](razor/sample/Startup.cs?highlight=4&range=10-14)]

'nin `CustomTemplateEngine`ifadesi üzerinde `return csharpDocument;` bir kesme noktası ayarlayın. Program yürütme kesme noktasında durduğunda, değerini `generatedCode`görüntüleyin.

![Oluşturulan Kodun Metin Görselleştirici görünümü](razor/_static/tvr.png)

::: moniker-end

## <a name="view-lookups-and-case-sensitivity"></a>Aramaları ve servis talebi hassasiyetini görüntüleme

Razor görünüm motoru, görünümler için büyük/küçük harf duyarlı aramalar gerçekleştirir. Ancak, gerçek arama temel dosya sistemi tarafından belirlenir:

* Dosya tabanlı kaynak:
  * Büyük/küçük harf duyarsız dosya sistemlerine (örneğin, Windows) sahip işletim sistemlerinde, fiziksel dosya sağlayıcısı aramaları büyük/küçük harf duyarsızdır. Örneğin, `return View("Test")` */Views/Home/Test.cshtml*, */Views/home/test.cshtml*ve diğer kasa varyantları için eşleşmeler elde edilir.
  * Büyük/küçük harf duyarlı dosya sistemlerinde (örneğin, Linux, OSX ve ile), `EmbeddedFileProvider`aramalar büyük/küçük harf duyarlıdır. Örneğin, `return View("Test")` özellikle */Views/Home/Test.cshtml ile*eşleşir.
* Önceden derlenmiş görünümler: ASP.NET Core 2.0 ve daha sonra, önceden derlenmiş görünümleri yukarı bakarak tüm işletim sistemlerinde duyarsız durumda. Davranış, fiziksel dosya sağlayıcısının Windows'daki davranışıyla aynıdır. Önceden derlenmiş iki görünüm yalnızca farklıysa, aramanın sonucu belirleyici değildir.

Geliştiricilerin dosya ve dizin adlarının kasasını kasayla eşleştirmeleri tavsiye edilir:

* Alan, denetleyici ve eylem adları.
* Jilet Sayfaları.

Eşleşen servis talebi, dağıtımların temel dosya sisteminden bağımsız olarak görünümlerini bulmasını sağlar.

## <a name="additional-resources"></a>Ek kaynaklar

[ASP.NET Web Programlamaya Giriş Jilet Sözdizimini Kullanarak](/aspnet/web-pages/overview/getting-started/introducing-razor-syntax-c) Razor sözdizimi ile programlama birçok örnek sağlar.
