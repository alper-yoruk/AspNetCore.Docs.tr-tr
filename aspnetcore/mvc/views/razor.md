---
title: ASP.NET Core için Razor söz dizimi başvurusu
author: rick-anderson
description: RazorSunucu tabanlı kodu Web sayfalarına eklemek için biçimlendirme sözdizimi hakkında bilgi edinin.
ms.author: riande
ms.date: 02/12/2020
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
uid: mvc/views/razor
ms.openlocfilehash: 9c2bbd2d463af8a2ea7db716d01bf1436338ea77
ms.sourcegitcommit: cd861463faf44956855e3c4b3669483bbc4a7463
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/29/2020
ms.locfileid: "89101367"
---
# <a name="no-locrazor-syntax-reference-for-aspnet-core"></a>Razor ASP.NET Core için sözdizimi başvurusu

By [Rick Anderson](https://twitter.com/RickAndMSFT), [Taylor Mullen](https://twitter.com/ntaylormullen)ve [dan vicarel](https://github.com/Rabadash8820)

Razor , Web sayfalarına sunucu tabanlı kod eklemeye yönelik biçimlendirme sözdizimidir. RazorSöz dizimi, Razor biçimlendirme, C# ve HTML 'den oluşur. Genellikle içeren dosyaların Razor *. cshtml* dosya uzantısı vardır. RazorAyrıca, [ Razor Bileşenler](xref:blazor/components/index) dosyalarında (*. Razor*) bulunur.

## <a name="rendering-html"></a>HTML işleniyor

Varsayılan Razor DIL HTML 'dir. Biçimlendirmeden HTML işleme HTML Razor dosyasından HTML işlenenden farklı değildir. *. Cshtml* Razor dosyalarındaki HTML işaretlemesi sunucu tarafından değiştirilmeden işlenir.

## <a name="no-locrazor-syntax"></a>Razor sözdizimi

Razor C# ' i destekler ve `@` HTML 'Den C# ' ye geçiş yapmak için sembolünü kullanır. Razor C# ifadelerini değerlendirir ve bunları HTML çıktısında işler.

Bir `@` simgenin arkasından [ Razor ayrılmış bir anahtar sözcük](#razor-reserved-keywords)geldiğinde, bu, belirli bir Razor biçimlendirmeyi geçirir. Aksi halde, düz C# ' ye geçiş yapar.

`@`İşaretlemede bir sembolün çıkmak için Razor ikinci bir sembol kullanın `@` :

```cshtml
<p>@@Username</p>
```

Kod, HTML 'de tek bir sembol ile işlenir `@` :

```html
<p>@Username</p>
```

HTML öznitelikleri ve e-posta adreslerini içeren içerik `@` sembol bir geçiş karakteri olarak kabul değildir. Aşağıdaki örnekteki e-posta adresleri Razor ayrıştırılmadan dokunulmaz:

```cshtml
<a href="mailto:Support@contoso.com">Support@contoso.com</a>
```

## <a name="implicit-no-locrazor-expressions"></a>Örtük Razor ifadeler

Örtük Razor ifadeler `@` sonrasında C# kodu ile başlar:

```cshtml
<p>@DateTime.Now</p>
<p>@DateTime.IsLeapYear(2016)</p>
```

C# `await` anahtar sözcüğünün dışında, örtük ifadeler boşluk içermemelidir. C# ifadesinde Temizleme işlemi varsa, boşluklar ıntermingled olabilir:

```cshtml
<p>@await DoSomething("hello", "world")</p>
```

Parantez ( **cannot** ) içindeki karakterler `<>` bir HTML etiketi olarak yorumlandığından örtük ifadeler C# generics içeremez. Aşağıdaki kod geçerli **değil** :

```cshtml
<p>@GenericMethod<int>()</p>
```

Yukarıdaki kod, aşağıdakilerden birine benzer bir derleyici hatası oluşturur:

* "İnt" öğesi kapatılmamış. Tüm öğeler kendi kendini kapatıyor ya da eşleşen bir bitiş etiketine sahip olmalıdır.
* ' GenericMethod ' Yöntem grubu temsilci olmayan ' Object ' türüne dönüştürülemiyor. Yöntemi çağırmak mı istiyordunuz? "

Genel metot çağrılarının [açık bir Razor ifadede](#explicit-razor-expressions) veya [ Razor kod bloğunda](#razor-code-blocks)sarmalanması gerekir.

## <a name="explicit-no-locrazor-expressions"></a>Açık Razor ifadeler

Açık Razor ifadeler, `@` dengeli parantez içeren bir sembolden oluşur. Son haftanın saatini işlemek için aşağıdaki Razor biçimlendirme kullanılır:

```cshtml
<p>Last week this time: @(DateTime.Now - TimeSpan.FromDays(7))</p>
```

Parantez içindeki içerikler `@()` değerlendirilir ve çıkışa işlenir.

Önceki bölümde açıklanan örtük ifadeler, genellikle boşluk içeremez. Aşağıdaki kodda, bir hafta geçerli saatten çıkarılır:

[!code-cshtml[](razor/sample/Views/Home/Contact.cshtml?range=17)]

Kod, aşağıdaki HTML 'yi işler:

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

Açık ifade olmadan `<p>Age@joe.Age</p>` bir e-posta adresi olarak kabul edilir ve `<p>Age@joe.Age</p>` işlenir. Açık bir ifade olarak yazıldığında `<p>Age33</p>` işlenir.

Açık ifadeler, *. cshtml* dosyalarındaki genel metotlardan çıkış oluşturmak için kullanılabilir. Aşağıdaki biçimlendirme, C# genel parantezinin neden olduğu daha önce gösterilen hatayı nasıl düzeltebileceğiniz gösterilmektedir. Kod açık bir ifade olarak yazılır:

```cshtml
<p>@(GenericMethod<int>())</p>
```

## <a name="expression-encoding"></a>İfade kodlaması

Dizeyi değerlendiren C# ifadeleri HTML kodlandı. Sonucunu veren C# ifadeleri `IHtmlContent` doğrudan aracılığıyla işlenir `IHtmlContent.WriteTo` . Değerlendirilmeyen C# ifadeleri `IHtmlContent` , işlenmeden önce bir dizeye dönüştürülür `ToString` ve kodlanabilir.

```cshtml
@("<span>Hello World</span>")
```

Yukarıdaki kod, aşağıdaki HTML 'yi işler:

```html
&lt;span&gt;Hello World&lt;/span&gt;
```

HTML tarayıcıda düz metin olarak gösterilir:

&lt;Aralık &gt; Merhaba Dünya &lt; /span&gt;

`HtmlHelper.Raw` çıktı kodlanmamış, ancak HTML işaretlemesi olarak işlendi.

> [!WARNING]
> `HtmlHelper.Raw`Ayıklanmış Kullanıcı girişinde kullanmak bir güvenlik riskidir. Kullanıcı girişi kötü amaçlı JavaScript veya diğer kötüye kullanım içerebilir. Kullanıcı girişinin Temizleme işlemi zordur. `HtmlHelper.Raw`Kullanıcı girişiyle kullanmaktan kaçının.

```cshtml
@Html.Raw("<span>Hello World</span>")
```

Kod, aşağıdaki HTML 'yi işler:

```html
<span>Hello World</span>
```

## <a name="no-locrazor-code-blocks"></a>Razor kod blokları

Razor kod blokları ile başlar `@` ve tarafından alınmıştır `{}` . İfadelerin aksine, kod blokları içindeki C# kodu işlenmez. Bir görünümdeki kod blokları ve ifadeler aynı kapsamı paylaşır ve sırasıyla tanımlanmıştır:

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

Kod, aşağıdaki HTML 'yi işler:

```html
<p>The future depends on what you do today. - Mahatma Gandhi</p>
<p>Hate cannot drive out hate, only love can do that. - Martin Luther King, Jr.</p>
```

::: moniker range=">= aspnetcore-3.0"

Kod blokları ' nda, şablon oluşturma yöntemleri olarak kullanılacak biçimlendirme ile [yerel işlevler](/dotnet/csharp/programming-guide/classes-and-structs/local-functions) bildirin:

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

Kod, aşağıdaki HTML 'yi işler:

```html
<p>Name: <strong>Mahatma Gandhi</strong></p>
<p>Name: <strong>Martin Luther King, Jr.</strong></p>
```

::: moniker-end

### <a name="implicit-transitions"></a>Örtük geçişler

Bir kod bloğundaki varsayılan dil C# ' dir, ancak Razor sayfa HTML 'ye geri dönebilir:

```cshtml
@{
    var inCSharp = true;
    <p>Now in HTML, was in C# @inCSharp</p>
}
```

### <a name="explicit-delimited-transition"></a>Açık sınırlı geçiş

HTML oluşturması gereken bir kod bloğunun alt bölümünü tanımlamak için etiketi ile işleme için karakterleri çevreleyin Razor `<text>` :

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    <text>Name: @person.Name</text>
}
```

HTML etiketiyle çevrelenmiş HTML 'yi işlemek için bu yaklaşımı kullanın. HTML veya etiket olmadan Razor bir Razor çalışma zamanı hatası oluşur.

`<text>`Etiket, içerik işlerken boşluğu denetlemek için yararlıdır:

* Yalnızca etiketi arasındaki içerik `<text>` işlenir.
* HTML çıktısında etiket görüntülenmeden önce veya sonra boşluk yok `<text>` .

### <a name="explicit-line-transition"></a>Açık satır geçişi

Tüm satırın geri kalanını bir kod bloğu içinde HTML olarak işlemek için `@:` sözdizimi kullanın:

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    @:Name: @person.Name
}
```

`@:`Kod içinde olmadan bir Razor çalışma zamanı hatası oluşturulur.

`@`Bir dosyadaki fazla karakter Razor , bloktaki daha sonra bulunan deyimlerde derleyici hatalarına neden olabilir. Bu derleyici hatalarının anlaşılması zor olabilir çünkü asıl hata bildirilen hatadan önce oluşur. Bu hata, birden çok örtük/açık ifade tek bir kod bloğu içinde birleştirildikten sonra ortaktır.

## <a name="control-structures"></a>Denetim yapıları

Denetim yapıları, kod bloklarının bir uzantısıdır. Kod bloklarının tüm yönleri (biçimlendirmeye geçme, satır içi C#) Ayrıca aşağıdaki yapılar için de geçerlidir:

### <a name="conditionals-if-else-if-else-and-switch"></a>Koşullular `@if, else if, else, and @switch`

`@if` kodun ne zaman çalışacağını denetler:

```cshtml
@if (value % 2 == 0)
{
    <p>The value was even.</p>
}
```

`else` ve `else if` `@` sembol gerektirmez:

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

Aşağıdaki biçimlendirme bir switch ifadesinin nasıl kullanılacağını göstermektedir:

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

### <a name="looping-for-foreach-while-and-do-while"></a>Döngüye `@for, @foreach, @while, and @do while`

Şablonlu HTML, döngü denetim deyimleri ile oluşturulabilir. Bir kişi listesini işlemek için:

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

### <a name="compound-using"></a>İstekteki `@using`

C# ' de bir `using` nesne atılmış olduğundan emin olmak için bir ifade kullanılır. Razor' De, ek içerik IÇEREN HTML Yardımcıları oluşturmak için aynı mekanizma kullanılır. Aşağıdaki kodda, HTML Yardımcıları ifadesiyle bir etiketi işlerler `<form>` `@using` :

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

Özel durum işleme C# ile benzerdir:

[!code-cshtml[](razor/sample/Views/Home/Contact7.cshtml)]

### `@lock`

Razor kilitleme deyimleriyle kritik bölümleri koruma özelliğine sahiptir:

```cshtml
@lock (SomeLock)
{
    // Do critical section work
}
```

### <a name="comments"></a>Yorumlar

Razor C# ve HTML açıklamalarını destekler:

```cshtml
@{
    /* C# comment */
    // Another C# comment
}
<!-- HTML comment -->
```

Kod, aşağıdaki HTML 'yi işler:

```html
<!-- HTML comment -->
```

Razor Web sayfası işlenmeden önce açıklamalar sunucu tarafından kaldırılır. Razor`@*  *@`açıklamaları sınırlandırmak için kullanır. Aşağıdaki kod açıklama olarak belirlenir, bu nedenle sunucu herhangi bir biçimlendirme oluşturmaz:

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

Razor yönergeler, simgeyi izleyen ayrılmış anahtar sözcüklerle örtük ifadelerle temsil edilir `@` . Yönerge genellikle görünümün ayrıştırılma şeklini değiştirir veya farklı işlevleri sunar.

RazorBir görünüm için kod üretme şeklini anlamak, yönergelerin nasıl çalıştığını anlamayı kolaylaştırır.

[!code-cshtml[](razor/sample/Views/Home/Contact8.cshtml)]

Kod, aşağıdakine benzer bir sınıf oluşturur:

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

Bu makalenin ilerleyen kısımlarında, [ Razor bir görünüm için oluşturulan C# sınıfını İnceleme](#inspect-the-razor-c-class-generated-for-a-view) bölümünde bu oluşturulan sınıfın nasıl görüntüleneceği açıklanmaktadır.

### `@attribute`

`@attribute`Yönergesi verilen özniteliği oluşturulan sayfanın veya görünümün sınıfına ekler. Aşağıdaki örnek `[Authorize]` özniteliğini ekler:

```cshtml
@attribute [Authorize]
```

::: moniker range=">= aspnetcore-3.0"

### `@code`

*Bu senaryo yalnızca Razor Bileşenler (. Razor) için geçerlidir.*

`@code`Blok, bir bileşenin [ Razor ](xref:blazor/components/index) bir bileşene C# üyeleri (alanlar, Özellikler ve Yöntemler) eklemesini sağlar:

```razor
@code {
    // C# members (fields, properties, and methods)
}
```

RazorBileşenler için `@code` bir diğer addır [`@functions`](#functions) ve önerilir `@functions` . Birden çok `@code` blok izin verilir.

::: moniker-end

### `@functions`

`@functions`Yönergesi, oluşturulan sınıfa C# üyeleri (alanlar, Özellikler ve Yöntemler) eklemeyi sağlar:

```cshtml
@functions {
    // C# members (fields, properties, and methods)
}
```

::: moniker range=">= aspnetcore-3.0"

[ Razor Bileşenler](xref:blazor/components/index)' de `@code` `@functions` C# üyelerini eklemek için ' i kullanın.

::: moniker-end

Örneğin:

[!code-cshtml[](razor/sample/Views/Home/Contact6.cshtml)]

Kod, aşağıdaki HTML işaretlemesini oluşturur:

```html
<div>From method: Hello</div>
```

Aşağıdaki kod, oluşturulan Razor C# sınıfıdır:

[!code-csharp[](razor/sample/Classes/Views_Home_Test_cshtml.cs?range=1-19)]

::: moniker range=">= aspnetcore-3.0"

`@functions` Yöntemler, işaretlemelerdeki şablon oluşturma yöntemleri olarak görev yapar:

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

Kod, aşağıdaki HTML 'yi işler:

```html
<p>Name: <strong>Mahatma Gandhi</strong></p>
<p>Name: <strong>Martin Luther King, Jr.</strong></p>
```

### `@implements`

`@implements`Yönergesi, oluşturulan sınıf için bir arabirim uygular.

Aşağıdaki örnek, <xref:System.IDisposable?displayProperty=fullName> <xref:System.IDisposable.Dispose*> yönteminin çağrılabilmesi için uygular:

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

`@inherits`Yönergesi, görünümün devraldığı sınıfın tam denetimini sağlar:

```cshtml
@inherits TypeNameOfClassToInheritFrom
```

Aşağıdaki kod özel bir Razor sayfa türüdür:

[!code-csharp[](razor/sample/Classes/CustomRazorPage.cs)]

, `CustomText` Bir görünümde görüntülenir:

[!code-cshtml[](razor/sample/Views/Home/Contact10.cshtml)]

Kod, aşağıdaki HTML 'yi işler:

```html
<div>
    Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping
    a slop bucket on the street below.
</div>
```

 `@model``@inherits`aynı görünümde kullanılabilir. `@inherits` görünümün içeri aktardığı bir *_ViewImports. cshtml* dosyasında olabilir:

[!code-cshtml[](razor/sample/Views/_ViewImportsModel.cshtml)]

Aşağıdaki kod, türü kesin belirlenmiş bir görünümün örneğidir:

[!code-cshtml[](razor/sample/Views/Home/Login1.cshtml)]

" rick@contoso.com " Modelde geçirilirse, görünüm AŞAĞıDAKI HTML işaretlemesini oluşturur:

```html
<div>The Login Email: rick@contoso.com</div>
<div>
    Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping
    a slop bucket on the street below.
</div>
```

### `@inject`

`@inject`Yönergesi, Razor sayfanın [hizmet kapsayıcısından](xref:fundamentals/dependency-injection) bir hizmeti bir görünüme eklemesine olanak sağlar. Daha fazla bilgi için bkz. [görünümlere bağımlılık ekleme](xref:mvc/views/dependency-injection).

::: moniker range=">= aspnetcore-3.0"

### `@layout`

*Bu senaryo yalnızca Razor Bileşenler (. Razor) için geçerlidir.*

`@layout`Yönergesi bir bileşenin yerleşimini belirtir Razor . Düzen bileşenleri kod yinelemeyi ve tutarsızlığın önüne geçmek için kullanılır. Daha fazla bilgi için bkz. <xref:blazor/layouts>.

::: moniker-end

### `@model`

*Bu senaryo yalnızca MVC görünümleri ve Razor sayfaları (. cshtml) için geçerlidir.*

`@model`Yönergesi bir görünüm veya sayfaya geçirilen modelin türünü belirtir:

```cshtml
@model TypeNameOfModel
```

RazorBireysel kullanıcı hesaplarıyla oluşturulan bir ASP.NET Core MVC veya sayfaları uygulamasında, *views/Account/Login. cshtml* aşağıdaki model bildirimini içerir:

```cshtml
@model LoginViewModel
```

Oluşturulan sınıf şuradan devralır `RazorPage<dynamic>` :

```csharp
public class _Views_Account_Login_cshtml : RazorPage<LoginViewModel>
```

Razor`Model`görünüme geçirilen modele erişim için bir özellik sunar:

```cshtml
<div>The Login Email: @Model.Email</div>
```

`@model`Yönerge, özelliğin türünü belirtir `Model` . Yönergesi, `T` `RazorPage<T>` görünümün türettiği oluşturulan sınıfın öğesini belirtir. `@model`Yönerge belirtilmemişse, `Model` özelliği türündedir `dynamic` . Daha fazla bilgi için bkz. [türü kesin belirlenmiş modeller ve @model anahtar sözcüğü](xref:tutorials/first-mvc-app/adding-model#strongly-typed-models-and-the--keyword).

### `@namespace`

`@namespace`Yönergesi:

* Oluşturulan Razor sayfa, MVC görünümü veya bileşen sınıfının ad alanını ayarlar Razor .
* Bir sayfa, görünüm veya bileşen sınıfının kök türetilmiş ad alanlarını dizin ağacındaki en yakın içeri aktarmalar dosyasından, *_ViewImports. cshtml* (görünümler veya sayfalar) veya *_Imports. Razor* ( Razor bileşenler) olarak ayarlar.

```cshtml
@namespace Your.Namespace.Here
```

RazorAşağıdaki tabloda gösterilen sayfalar örneği için:

* Her sayfa *sayfaları/_ViewImports. cshtml*'yi içeri aktarır.
* *Pages/_ViewImports. cshtml* içerir `@namespace Hello.World` .
* Her sayfa `Hello.World` , ad alanının kökü olarak bulunur.

| Sayfa                                        | Ad Alanı                             |
| ------------------------------------------- | ------------------------------------- |
| *Pages/Index. cshtml*                        | `Hello.World`                         |
| *Sayfa/fazla sayfa/sayfa. cshtml*               | `Hello.World.MorePages`               |
| *Pages/te Pages/Evente Pages/Page. cshtml* | `Hello.World.MorePages.EvenMorePages` |

Yukarıdaki ilişkiler, MVC görünümleri ve bileşenleriyle kullanılan dosyaları içeri aktarmak için geçerlidir Razor .

Birden çok içeri aktarma dosyasında bir `@namespace` yönerge olduğunda, kök ad alanını ayarlamak için dizin ağacındaki sayfaya, görünüme veya bileşene en yakın dosya kullanılır.

Yukarıdaki örnekteki *evente Pages* klasöründe bir içeri aktarmalar dosyası varsa `@namespace Another.Planet` (veya *sayfa/değer sayfaları/Evente Pages/Page. cshtml* dosyası içeriyorsa `@namespace Another.Planet` ), sonuç aşağıdaki tabloda gösterilir.

| Sayfa                                        | Ad Alanı               |
| ------------------------------------------- | ----------------------- |
| *Pages/Index. cshtml*                        | `Hello.World`           |
| *Sayfa/fazla sayfa/sayfa. cshtml*               | `Hello.World.MorePages` |
| *Pages/te Pages/Evente Pages/Page. cshtml* | `Another.Planet`        |

### `@page`

::: moniker range=">= aspnetcore-3.0"

`@page`Yönergesinin, göründüğü dosyanın türüne bağlı olarak farklı etkileri vardır. Yönergesi:

* Bir *. cshtml* dosyasında, dosyanın bir sayfa olduğunu gösterir Razor . Daha fazla bilgi için bkz. [özel rotalar](xref:razor-pages/index#custom-routes) ve <xref:razor-pages/index> .
* Bir Razor bileşenin istekleri doğrudan işlemesi gerektiğini belirtir. Daha fazla bilgi için bkz. <xref:blazor/fundamentals/routing>.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

`@page`Bir *. cshtml* dosyasının ilk satırındaki yönerge, dosyanın bir sayfa olduğunu gösterir Razor . Daha fazla bilgi için bkz. <xref:razor-pages/index>.

::: moniker-end

### `@section`

*Bu senaryo yalnızca MVC görünümleri ve Razor sayfaları (. cshtml) için geçerlidir.*

`@section`Yönerge, görünümler veya SAYFALARıN HTML sayfasının farklı bölümlerinde içerik işlemesini sağlamak Için [MVC ve Razor sayfa düzenleri](xref:mvc/views/layout) ile birlikte kullanılır. Daha fazla bilgi için bkz. <xref:mvc/views/layout>.

### `@using`

`@using`Yönergesi, `using` oluşturulan görünüme C# yönergesini ekler:

[!code-cshtml[](razor/sample/Views/Home/Contact9.cshtml)]

::: moniker range=">= aspnetcore-3.0"

[ Razor Bileşenlerinde](xref:blazor/components/index), `@using` hangi bileşenlerin kapsamda olduğunu da denetler.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="directive-attributes"></a>Yönerge öznitelikleri

Razor yönerge öznitelikleri, simgeyi izleyen ayrılmış anahtar sözcüklerle örtük ifadelerle temsil edilir `@` . Bir Directive özniteliği genellikle bir öğenin ayrıştırılma şeklini değiştirir veya farklı işlevlere izin vermez.

### `@attributes`

*Bu senaryo yalnızca Razor Bileşenler (. Razor) için geçerlidir.*

`@attributes` bir bileşenin bildirilmeyen öznitelikleri işlemesini sağlar. Daha fazla bilgi için bkz. <xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters>.

### `@bind`

*Bu senaryo yalnızca Razor Bileşenler (. Razor) için geçerlidir.*

Bileşenlerdeki veri bağlama, `@bind` özniteliğiyle gerçekleştirilir. Daha fazla bilgi için bkz. <xref:blazor/components/data-binding>.

### `@on{EVENT}`

*Bu senaryo yalnızca Razor Bileşenler (. Razor) için geçerlidir.*

Razor bileşenler için olay işleme özellikleri sağlar. Daha fazla bilgi için bkz. <xref:blazor/components/event-handling>.

::: moniker-end

::: moniker range=">= aspnetcore-3.1"

### `@on{EVENT}:preventDefault`

*Bu senaryo yalnızca Razor Bileşenler (. Razor) için geçerlidir.*

Olay için varsayılan eylemi engeller.

### `@on{EVENT}:stopPropagation`

*Bu senaryo yalnızca Razor Bileşenler (. Razor) için geçerlidir.*

Olay için olay yaymayı sonlandırır.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### `@key`

*Bu senaryo yalnızca Razor Bileşenler (. Razor) için geçerlidir.*

`@key`Directive özniteliği, anahtar değerine göre öğelerin veya bileşenlerin korunmasını güvence altına almak için bileşenlerin algoritma almasına neden olur. Daha fazla bilgi için bkz. <xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components>.

### `@ref`

*Bu senaryo yalnızca Razor Bileşenler (. Razor) için geçerlidir.*

Bileşen başvuruları ( `@ref` ) bir bileşen örneğine başvurmak için bir yol sağlar, böylece bu örneğe komut verebilirsiniz. Daha fazla bilgi için bkz. <xref:blazor/components/index#capture-references-to-components>.

### `@typeparam`

*Bu senaryo yalnızca Razor Bileşenler (. Razor) için geçerlidir.*

`@typeparam`Yönergesi, oluşturulan bileşen sınıfı için genel bir tür parametresi bildirir. Daha fazla bilgi için bkz. <xref:blazor/components/templated-components#generic-typed-components>.

::: moniker-end

## <a name="templated-no-locrazor-delegates"></a>Şablonlu Razor Temsilciler

Razor Şablonlar, aşağıdaki biçimde bir UI parçacığı tanımlamanızı sağlar:

```cshtml
@<tag>...</tag>
```

Aşağıdaki örnek, olarak şablonlu bir temsilcinin nasıl ekleneceğini gösterir Razor <xref:System.Func%602> . [Dinamik tür](/dotnet/csharp/programming-guide/types/using-type-dynamic) , temsilcinin sarmalayan yönteminin parametresi için belirtilir. Bir [nesne türü](/dotnet/csharp/language-reference/keywords/object) , temsilcinin dönüş değeri olarak belirtilir. Şablon, bir özelliğine sahip olan bir ile kullanılır <xref:System.Collections.Generic.List%601> `Pet` `Name` .

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

Şablon, `pets` bir ifade tarafından sağlanan ile işlenir `foreach` :

```cshtml
@foreach (var pet in pets)
{
    @petTemplate(pet)
}
```

İşlenmiş çıkış:

```html
<p>You have a pet named <strong>Rin Tin Tin</strong>.</p>
<p>You have a pet named <strong>Mr. Bigglesworth</strong>.</p>
<p>You have a pet named <strong>K-9</strong>.</p>
```

RazorBir yönteme bağımsız değişken olarak bir satır içi şablon da sağlayabilirsiniz. Aşağıdaki örnekte, `Repeat` yöntemi bir Razor şablon alır. Yöntemi, bir listeden sağlanan öğelerin tekrarlandığı HTML içeriğini oluşturmak için şablonunu kullanır:

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

Önceki örnekteki Evcil hayvanlar 'ların listesini kullanarak, `Repeat` yöntemi ile çağrılır:

* <xref:System.Collections.Generic.List%601>`Pet`.
* Her evcil hayvan kaç kez tekrarlanacak.
* Sıralanmamış bir listenin liste öğeleri için kullanılacak satır içi şablon.

```cshtml
<ul>
    @Repeat(pets, 3, @<li>@item.Name</li>)
</ul>
```

İşlenmiş çıkış:

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

*Bu senaryo yalnızca MVC görünümleri ve Razor sayfaları (. cshtml) için geçerlidir.*

[Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro)ile ilgili üç yönergeler vardır.

| Deki | İşlev |
| --------- | -------- |
| [`@addTagHelper`](xref:mvc/views/tag-helpers/intro#add-helper-label) | Etiket yardımcılarını bir görünüm için kullanılabilir hale getirir. |
| [`@removeTagHelper`](xref:mvc/views/tag-helpers/intro#remove-razor-directives-label) | Daha önce bir görünümden eklenen etiket yardımcıları kaldırır. |
| [`@tagHelperPrefix`](xref:mvc/views/tag-helpers/intro#prefix-razor-directives-label) | Etiket Yardımcısı desteğini etkinleştirmek ve etiket Yardımcısı kullanımını açık hale getirmek için bir etiket ön eki belirtir. |

## <a name="no-locrazor-reserved-keywords"></a>Razor ayrılmış anahtar sözcükler

### <a name="no-locrazor-keywords"></a>Razor lerimi

* `page` (ASP.NET Core 2,1 veya üzeri gerekir)
* `namespace`
* `functions`
* `inherits`
* `model`
* `section`
* `helper` (Şu anda ASP.NET Core tarafından desteklenmiyor)

Razor anahtar kelimelerden kaçışın `@(Razor Keyword)` (örneğin, `@(functions)` ).

### <a name="c-no-locrazor-keywords"></a>C# Razor anahtar sözcükleri

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

C# Razor anahtar sözcükleri ile çift kaçış olmalıdır `@(@C# Razor Keyword)` (örneğin, `@(@case)` ). İlki `@` Razor Ayrıştırıcıdan çıkar. İkincisi, `@` C# ayrıştırıcısının çıkar.

### <a name="reserved-keywords-not-used-by-no-locrazor"></a>Ayrılmış anahtar sözcükler tarafından kullanılmıyor Razor

* `class`

## <a name="inspect-the-no-locrazor-c-class-generated-for-a-view"></a>RazorBir görünüm için oluşturulan C# sınıfını inceleyin

::: moniker range=">= aspnetcore-2.1"

.NET Core SDK 2,1 veya sonraki bir sürümü ile [ Razor SDK](xref:razor-pages/sdk) , dosyaların derlemesini işler Razor . Bir proje oluştururken, Razor SDK proje kökünde bir *obj/<build_configuration>/<target_framework_moniker>/ Razor * dizin oluşturur. Dizin içindeki dizin yapısı *Razor* , projenin dizin yapısını yansıtır.

ASP.NET Core 2,1 Razor sayfaları projesinde .NET Core 2,1 ' i hedefleyen aşağıdaki dizin yapısını göz önünde bulundurun:

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

Projenin *hata ayıklama* yapılandırmasında oluşturulması aşağıdaki *obj* dizinini verir:

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

*Pages/Index. cshtml*için oluşturulan sınıfı görüntülemek için *obj/Debug/netcoreapp 2.1/ Razor /Pages/Index.g.cshtml.cs*öğesini açın.

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

Aşağıdaki Sınıfı ASP.NET Core MVC projesine ekleyin:

[!code-csharp[](razor/sample/Utilities/CustomTemplateEngine.cs)]

' De `Startup.ConfigureServices` , `RazorTemplateEngine` ile MVC tarafından eklenen ' i şu sınıfla geçersiz kılın `CustomTemplateEngine` :

[!code-csharp[](razor/sample/Startup.cs?highlight=4&range=10-14)]

Bildiriminde bir kesme noktası ayarlayın `return csharpDocument;` `CustomTemplateEngine` . Kesme noktasında program yürütmesi durdurulduğunda değerini görüntüleyin `generatedCode` .

![GeneratedCode 'ın metin görselleştiricisi görünümü](razor/_static/tvr.png)

::: moniker-end

## <a name="view-lookups-and-case-sensitivity"></a>Aramaları ve büyük/küçük harf duyarlılığını görüntüle

RazorGörünüm altyapısı, görünümler için büyük/küçük harfe duyarlı aramalar gerçekleştirir. Ancak gerçek arama, temel alınan dosya sistemine göre belirlenir:

* Dosya tabanlı kaynak:
  * Büyük/küçük harf duyarsız dosya sistemlerine (örneğin, Windows) sahip işletim sistemlerinde, fiziksel dosya sağlayıcısı aramaları büyük/küçük harfe duyarsızdır. Örneğin, `return View("Test")` */views/Home/test.exe*, */views/Home/test.exe*ve diğer tüm büyük/küçük harf çeşitlerüyle sonuçlanır.
  * Büyük/küçük harf duyarlı dosya sistemlerinde (örneğin, Linux, OSX ve ile `EmbeddedFileProvider` ), aramalar büyük/küçük harfe duyarlıdır. Örneğin, `return View("Test")` özellikle */views/Home/test.exe. cshtml*ile eşleşir.
* Önceden derlenmiş görünümler: ASP.NET Core 2,0 ve üzeri sürümlerde, önceden derlenmiş görünümleri aramak tüm işletim sistemlerinde büyük/küçük harfe duyarlıdır. Davranış, fiziksel dosya sağlayıcısının Windows 'daki davranışlarıyla aynıdır. Ön derlenmiş iki görünüm yalnızca bir durumda farklıysa, arama sonucu belirleyici değildir.

Geliştiricilerin dosya ve dizin adlarını büyük küçük harf olarak eşleşmesi önerilir:

* Alan, denetleyici ve eylem adları.
* Razor Sayfaları.

Eşleşen durum, dağıtımların görünümlerini temel alınan dosya sisteminden bağımsız olarak bulmasını sağlar.

## <a name="additional-resources"></a>Ek kaynaklar

Şunu [kullanarak Razor ASP.NET Web programlamaya giriş Sözdizimi](/aspnet/web-pages/overview/getting-started/introducing-razor-syntax-c) , sözdizimi ile programlama için birçok örnek sağlar Razor .
