---
title: ASP.NET Core için Razor söz dizimi başvurusu
author: rick-anderson
description: Sunucu tabanlı Razor kodu Web sayfalarına eklemek için biçimlendirme sözdizimi hakkında bilgi edinin.
ms.author: riande
ms.date: 02/12/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/razor
ms.openlocfilehash: 3e77b25e2660688d0040d47840e47dab8f260197
ms.sourcegitcommit: 6c7a149168d2c4d747c36de210bfab3abd60809a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/09/2020
ms.locfileid: "83003198"
---
# <a name="razor-syntax-reference-for-aspnet-core"></a>ASP.NET Core için Razor söz dizimi başvurusu

By [Rick Anderson](https://twitter.com/RickAndMSFT), [Taylor Mullen](https://twitter.com/ntaylormullen)ve [dan vicarel](https://github.com/Rabadash8820)

Razor, Web sayfalarına sunucu tabanlı kod eklemeye yönelik bir biçimlendirme sözdizimidir. Razor söz dizimi Razor Markup, C# ve HTML 'den oluşur. Razor içeren dosyalar genellikle *. cshtml* dosya uzantısına sahiptir. Razor, [Razor bileşenleri](xref:blazor/components) dosyalarında (*. Razor*) de bulunur.

## <a name="rendering-html"></a>HTML işleniyor

Varsayılan Razor dili HTML’dir. Razor işaretlemesinden HTML işlemek, HTML dosyasından HTML işlemekten farklı değildir. *. Cshtml* Razor dosyalarındaki HTML işaretlemesi sunucu tarafından değiştirilmeden işlenir.

## <a name="razor-syntax"></a>Razor söz dizimi

Razor, `@` c# ' i destekler ve HTML 'den c# ' ye geçiş yapmak için sembolünü kullanır. Razor, C# ifadelerini değerlendirir ve bunları HTML çıktısında oluşturur.

Bir `@` sembol sonrasında [Razor ayrılmış anahtar sözcüğü](#razor-reserved-keywords)olduğunda, bu, Razor 'e özgü işaretlere geçer. Aksi halde, düz C# ' ye geçiş yapar.

Razor biçimlendirmesinde bir `@` sembolün çıkmak için ikinci `@` bir sembol kullanın:

```cshtml
<p>@@Username</p>
```

Kod, HTML 'de tek `@` bir sembol ile işlenir:

```html
<p>@Username</p>
```

HTML öznitelikleri ve e-posta adreslerini içeren içerik `@` sembol bir geçiş karakteri olarak kabul değildir. Aşağıdaki örnekteki e-posta adresleri Razor ayrıştırma tarafından dokunmaz:

```cshtml
<a href="mailto:Support@contoso.com">Support@contoso.com</a>
```

## <a name="implicit-razor-expressions"></a>Örtük Razor ifadeleri

Örtük Razor ifadeleri, sonrasında `@` C# kodu ile başlar:

```cshtml
<p>@DateTime.Now</p>
<p>@DateTime.IsLeapYear(2016)</p>
```

C# `await` anahtar sözcüğünün dışında, örtük ifadeler boşluk içermemelidir. C# ifadesinde Temizleme işlemi varsa, boşluklar ıntermingled olabilir:

```cshtml
<p>@await DoSomething("hello", "world")</p>
```

Parantez (`<>`) içindeki karakterler bir HTML etiketi olarak yorumlandığından **örtük ifadeler C# generics içeremez.** Aşağıdaki kod geçerli **değil** :

```cshtml
<p>@GenericMethod<int>()</p>
```

Yukarıdaki kod, aşağıdakilerden birine benzer bir derleyici hatası oluşturur:

* "İnt" öğesi kapatılmamış. Tüm öğeler kendi kendini kapatıyor ya da eşleşen bir bitiş etiketine sahip olmalıdır.
* ' GenericMethod ' Yöntem grubu temsilci olmayan ' Object ' türüne dönüştürülemiyor. Yöntemi çağırmak mı istiyordunuz? "

Genel metot çağrılarının [açık bir Razor ifadesinde](#explicit-razor-expressions) veya [Razor kodu bloğunda](#razor-code-blocks)sarmalanması gerekir.

## <a name="explicit-razor-expressions"></a>Açık Razor ifadeleri

Açık Razor ifadeleri, dengeli parantez `@` içeren bir sembolden oluşur. Son haftanın saatini işlemek için aşağıdaki Razor işaretlemesi kullanılır:

```cshtml
<p>Last week this time: @(DateTime.Now - TimeSpan.FromDays(7))</p>
```

`@()` Parantez içindeki içerikler değerlendirilir ve çıkışa işlenir.

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

Açık ifade `<p>Age@joe.Age</p>` olmadan bir e-posta adresi olarak kabul edilir ve `<p>Age@joe.Age</p>` işlenir. Açık bir ifade `<p>Age33</p>` olarak yazıldığında işlenir.

Açık ifadeler, *. cshtml* dosyalarındaki genel metotlardan çıkış oluşturmak için kullanılabilir. Aşağıdaki biçimlendirme, C# genel parantezinin neden olduğu daha önce gösterilen hatayı nasıl düzeltebileceğiniz gösterilmektedir. Kod açık bir ifade olarak yazılır:

```cshtml
<p>@(GenericMethod<int>())</p>
```

## <a name="expression-encoding"></a>İfade kodlaması

Dizeyi değerlendiren C# ifadeleri HTML kodlandı. Sonucunu `IHtmlContent` veren C# ifadeleri doğrudan aracılığıyla `IHtmlContent.WriteTo`işlenir. Değerlendirilmeyen C# ifadeleri `IHtmlContent` , işlenmeden önce bir dizeye dönüştürülür `ToString` ve kodlanabilir.

```cshtml
@("<span>Hello World</span>")
```

Kod, aşağıdaki HTML 'yi işler:

```html
&lt;span&gt;Hello World&lt;/span&gt;
```

HTML tarayıcıda şu şekilde gösterilir:

```html
<span>Hello World</span>
```

`HtmlHelper.Raw`çıktı kodlanmamış, ancak HTML işaretlemesi olarak işlendi.

> [!WARNING]
> Ayıklanmış `HtmlHelper.Raw` Kullanıcı girişinde kullanmak bir güvenlik riskidir. Kullanıcı girişi kötü amaçlı JavaScript veya diğer kötüye kullanım içerebilir. Kullanıcı girişinin Temizleme işlemi zordur. `HtmlHelper.Raw` Kullanıcı girişiyle kullanmaktan kaçının.

```cshtml
@Html.Raw("<span>Hello World</span>")
```

Kod, aşağıdaki HTML 'yi işler:

```html
<span>Hello World</span>
```

## <a name="razor-code-blocks"></a>Razor kod blokları

Razor kod blokları ile `@` başlar ve tarafından `{}`alınmıştır. İfadelerin aksine, kod blokları içindeki C# kodu işlenmez. Bir görünümdeki kod blokları ve ifadeler aynı kapsamı paylaşır ve sırasıyla tanımlanmıştır:

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

Bir kod bloğundaki varsayılan dil C# ' dir, ancak Razor sayfası HTML 'ye geri dönebilir:

```cshtml
@{
    var inCSharp = true;
    <p>Now in HTML, was in C# @inCSharp</p>
}
```

### <a name="explicit-delimited-transition"></a>Açık sınırlı geçiş

HTML oluşturması gereken bir kod bloğunun alt bölümünü tanımlamak için, göstermek için karakterleri Razor `<text>` etiketiyle çevreleyin:

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    <text>Name: @person.Name</text>
}
```

HTML etiketiyle çevrelenmiş HTML 'yi işlemek için bu yaklaşımı kullanın. HTML veya Razor etiketi olmadan Razor çalışma zamanı hatası oluşur.

Etiket `<text>` , içerik işlerken boşluğu denetlemek için yararlıdır:

* Yalnızca `<text>` etiketi arasındaki içerik işlenir.
* HTML çıktısında `<text>` etiket görüntülenmeden önce veya sonra boşluk yok.

### <a name="explicit-line-transition"></a>Açık satır geçişi

Tüm satırın geri kalanını bir kod bloğu içinde HTML olarak işlemek için sözdizimi kullanın `@:` :

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    @:Name: @person.Name
}
```

`@:` Kodda olmadan bir Razor çalışma zamanı hatası oluşturulur.

Razor `@` dosyasındaki fazla karakter, bloktaki daha sonra bulunan deyimlerde derleyici hatalarına neden olabilir. Bu derleyici hatalarının anlaşılması zor olabilir çünkü asıl hata bildirilen hatadan önce oluşur. Bu hata, birden çok örtük/açık ifade tek bir kod bloğu içinde birleştirildikten sonra ortaktır.

## <a name="control-structures"></a>Denetim yapıları

Denetim yapıları, kod bloklarının bir uzantısıdır. Kod bloklarının tüm yönleri (biçimlendirmeye geçme, satır içi C#) Ayrıca aşağıdaki yapılar için de geçerlidir:

### <a name="conditionals-if-else-if-else-and-switch"></a>Koşul \@varsa, Else, Else ve \@anahtar

`@if`kodun ne zaman çalışacağını denetler:

```cshtml
@if (value % 2 == 0)
{
    <p>The value was even.</p>
}
```

`else`ve `else if` `@` sembol gerektirmez:

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

### <a name="looping-for-foreach-while-and-do-while"></a>For \@döngüsü, \@foreach, \@while ve \@do

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

### <a name="compound-using"></a>Kullanarak \@bileşik

C# ' de bir `using` nesne atılmış olduğundan emin olmak için bir ifade kullanılır. Razor 'de, ek içerik içeren HTML Yardımcıları oluşturmak için aynı mekanizma kullanılır. Aşağıdaki kodda, HTML Yardımcıları `<form>` `@using` ifadesiyle bir etiketi işlerler:

```cshtml
@using (Html.BeginForm())
{
    <div>
        Email: <input type="email" id="Email" value="">
        <button>Register</button>
    </div>
}
```

### <a name="try-catch-finally"></a>\@TRY, catch, finally

Özel durum işleme C# ile benzerdir:

[!code-cshtml[](razor/sample/Views/Home/Contact7.cshtml)]

### <a name="lock"></a>\@ine

Razor, kilit deyimleriyle önemli bölümleri koruma özelliğine sahiptir:

```cshtml
@lock (SomeLock)
{
    // Do critical section work
}
```

### <a name="comments"></a>Açıklamalar

Razor, C# ve HTML açıklamalarını destekler:

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

Razor açıklamaları, Web sayfası işlenmeden önce sunucu tarafından kaldırılır. Razor, `@*  *@` Yorumları sınırlandırmak için kullanır. Aşağıdaki kod açıklama olarak belirlenir, bu nedenle sunucu herhangi bir biçimlendirme oluşturmaz:

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

Razor yönergeleri, `@` simgeyi izleyen ayrılmış anahtar sözcüklerle örtük ifadelerle temsil edilir. Yönerge genellikle görünümün ayrıştırılma şeklini değiştirir veya farklı işlevleri sunar.

Razor 'nin bir görünüm için nasıl kod üretdiğini anlamak, yönergelerin nasıl çalıştığını anlamayı kolaylaştırır.

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

Bu makalenin ilerleyen kısımlarında, [bir görünüm için oluşturulan Razor C# sınıfını İnceleme](#inspect-the-razor-c-class-generated-for-a-view) bölümünde bu oluşturulan sınıfın nasıl görüntüleneceği açıklanmaktadır.

### <a name="attribute"></a>\@özniteliği

`@attribute` Yönergesi verilen özniteliği oluşturulan sayfanın veya görünümün sınıfına ekler. Aşağıdaki örnek `[Authorize]` özniteliğini ekler:

```cshtml
@attribute [Authorize]
```

::: moniker range=">= aspnetcore-3.0"

### <a name="code"></a>\@kodudur

*Bu senaryo yalnızca Razor bileşenleri (. Razor) için geçerlidir.*

Blok `@code` , bir [Razor bileşeninin](xref:blazor/components) bir bileşene C# üyeleri (alanlar, Özellikler ve Yöntemler) eklemesini sağlar:

```razor
@code {
    // C# members (fields, properties, and methods)
}
```

Razor bileşenleri için, `@code` öğesinin [`@functions`](#functions) diğer adı ve önerilir `@functions`. Birden çok `@code` blok izin verilir.

::: moniker-end

### <a name="functions"></a>\@lerdir

Yönergesi `@functions` , oluşturulan sınıfa C# üyeleri (alanlar, Özellikler ve Yöntemler) eklemeyi sağlar:

```cshtml
@functions {
    // C# members (fields, properties, and methods)
}
```

::: moniker range=">= aspnetcore-3.0"

[Razor bileşenlerinde](xref:blazor/components)C# üyelerini eklemek `@code` `@functions` için ' i kullanın.

::: moniker-end

Örneğin:

[!code-cshtml[](razor/sample/Views/Home/Contact6.cshtml)]

Kod, aşağıdaki HTML işaretlemesini oluşturur:

```html
<div>From method: Hello</div>
```

Aşağıdaki kod, üretilen Razor C# sınıfıdır:

[!code-csharp[](razor/sample/Classes/Views_Home_Test_cshtml.cs?range=1-19)]

::: moniker range=">= aspnetcore-3.0"

`@functions`Yöntemler, işaretlemelerdeki şablon oluşturma yöntemleri olarak görev yapar:

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

### <a name="implements"></a>\@uygulamalar

`@implements` Yönergesi, oluşturulan sınıf için bir arabirim uygular.

Aşağıdaki örnek, yönteminin <xref:System.IDisposable?displayProperty=fullName> çağrılabilmesi için <xref:System.IDisposable.Dispose*> uygular:

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

### <a name="inherits"></a>\@alıp

`@inherits` Yönergesi, görünümün devraldığı sınıfın tam denetimini sağlar:

```cshtml
@inherits TypeNameOfClassToInheritFrom
```

Aşağıdaki kod özel bir Razor sayfası türüdür:

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

 `@model``@inherits` aynı görünümde kullanılabilir. `@inherits`görünümün içeri aktardığı bir *_ViewImports. cshtml* dosyasında olabilir:

[!code-cshtml[](razor/sample/Views/_ViewImportsModel.cshtml)]

Aşağıdaki kod, türü kesin belirlenmiş bir görünümün örneğidir:

[!code-cshtml[](razor/sample/Views/Home/Login1.cshtml)]

"rick@contoso.com" Modelde geçirilirse, Görünüm aşağıdaki HTML işaretlemesini oluşturur:

```html
<div>The Login Email: rick@contoso.com</div>
<div>
    Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping
    a slop bucket on the street below.
</div>
```

### <a name="inject"></a>\@eklenecek

`@inject` Yönergesi, Razor sayfasının [hizmet kapsayıcısından](xref:fundamentals/dependency-injection) bir hizmeti bir görünüme eklemesine olanak sağlar. Daha fazla bilgi için bkz. [görünümlere bağımlılık ekleme](xref:mvc/views/dependency-injection).

::: moniker range=">= aspnetcore-3.0"

### <a name="layout"></a>\@Düzenle

*Bu senaryo yalnızca Razor bileşenleri (. Razor) için geçerlidir.*

Yönerge `@layout` , Razor bileşeni için bir düzen belirtir. Düzen bileşenleri kod yinelemeyi ve tutarsızlığın önüne geçmek için kullanılır. Daha fazla bilgi için bkz. <xref:blazor/layouts>.

::: moniker-end

### <a name="model"></a>\@modelinizi

*Bu senaryo yalnızca MVC görünümleri ve Razor Pages (. cshtml) için geçerlidir.*

`@model` Yönergesi bir görünüm veya sayfaya geçirilen modelin türünü belirtir:

```cshtml
@model TypeNameOfModel
```

Bir ASP.NET Core MVC veya bireysel kullanıcı hesaplarıyla oluşturulan Razor Pages uygulama, *Görünümler/Account/Login. cshtml* aşağıdaki model bildirimini içerir:

```cshtml
@model LoginViewModel
```

Oluşturulan sınıf şuradan `RazorPage<dynamic>`devralır:

```csharp
public class _Views_Account_Login_cshtml : RazorPage<LoginViewModel>
```

Razor, görünüme `Model` geçirilen modele erişim için bir özellik sunar:

```cshtml
<div>The Login Email: @Model.Email</div>
```

`@model` Yönerge, `Model` özelliğin türünü belirtir. Yönergesi, görünümün türettiği `RazorPage<T>` oluşturulan sınıfın öğesini belirtir `T` . `@model` Yönerge belirtilmemişse, `Model` özelliği türündedir `dynamic`. Daha fazla bilgi için bkz. [türü kesin belirlenmiş modeller @model ve anahtar sözcüğü](xref:tutorials/first-mvc-app/adding-model#strongly-typed-models-and-the--keyword).

### <a name="namespace"></a>\@ad alanı

`@namespace` Yönergesi:

* Oluşturulan Razor sayfası, MVC görünümü veya Razor bileşeni sınıfının ad alanını ayarlar.
* Bir sayfa, görünüm veya bileşen sınıfının kök türetilmiş ad alanlarını dizin ağacındaki en yakın içeri aktarmalar dosyasından, *_ViewImports. cshtml* (görünümler veya sayfalar) veya *_Imports. Razor* (Razor bileşenleri) olarak ayarlar.

```cshtml
@namespace Your.Namespace.Here
```

Aşağıdaki tabloda gösterilen Razor Pages örneği için:

* Her sayfa *sayfaları/_ViewImports. cshtml*'yi içeri aktarır.
* *Pages/_ViewImports. cshtml* içerir `@namespace Hello.World`.
* Her sayfa, `Hello.World` ad alanının kökü olarak bulunur.

| Sayfa                                        | Ad Alanı                             |
| ------------------------------------------- | ------------------------------------- |
| *Pages/Index. cshtml*                        | `Hello.World`                         |
| *Sayfa/fazla sayfa/sayfa. cshtml*               | `Hello.World.MorePages`               |
| *Pages/te Pages/Evente Pages/Page. cshtml* | `Hello.World.MorePages.EvenMorePages` |

Yukarıdaki ilişkiler, MVC görünümleri ve Razor bileşenleriyle kullanılan dosyaları içeri aktarmak için geçerlidir.

Birden çok içeri aktarma dosyasında bir `@namespace` yönerge olduğunda, kök ad alanını ayarlamak için dizin ağacındaki sayfaya, görünüme veya bileşene en yakın dosya kullanılır.

Yukarıdaki örnekteki *evente Pages* klasöründe bir içeri aktarmalar dosyası varsa `@namespace Another.Planet` (veya sayfa/değer sayfaları */evente Pages/Page. cshtml* dosyası içeriyorsa `@namespace Another.Planet`), sonuç aşağıdaki tabloda gösterilir.

| Sayfa                                        | Ad Alanı               |
| ------------------------------------------- | ----------------------- |
| *Pages/Index. cshtml*                        | `Hello.World`           |
| *Sayfa/fazla sayfa/sayfa. cshtml*               | `Hello.World.MorePages` |
| *Pages/te Pages/Evente Pages/Page. cshtml* | `Another.Planet`        |

### <a name="page"></a>\@sayfasında

::: moniker range=">= aspnetcore-3.0"

`@page` Yönergesinin, göründüğü dosyanın türüne bağlı olarak farklı etkileri vardır. Yönergesi:

* İçindeki bir *. cshtml* dosyasında, dosyanın bir Razor sayfası olduğunu gösterir. Daha fazla bilgi için bkz. [özel rotalar](xref:razor-pages/index#custom-routes) ve <xref:razor-pages/index>.
* Bir Razor bileşeninin istekleri doğrudan işlemesini belirtir. Daha fazla bilgi için bkz. <xref:blazor/routing>.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Bir `@page` *. cshtml* dosyasının ilk satırındaki yönergesi, dosyanın bir Razor sayfası olduğunu gösterir. Daha fazla bilgi için bkz. <xref:razor-pages/index>.

::: moniker-end

### <a name="section"></a>\@kısmı

*Bu senaryo yalnızca MVC görünümleri ve Razor Pages (. cshtml) için geçerlidir.*

`@section` Yönerge, HTML sayfasının farklı kısımlarında görünüm veya sayfaların içerik işlemesini sağlamak için [MVC ve Razor Pages düzenleriyle](xref:mvc/views/layout) birlikte kullanılır. Daha fazla bilgi için bkz. <xref:mvc/views/layout>.

### <a name="using"></a>\@kullanma

`@using` Yönergesi, oluşturulan görünüme C# `using` yönergesini ekler:

[!code-cshtml[](razor/sample/Views/Home/Contact9.cshtml)]

::: moniker range=">= aspnetcore-3.0"

[Razor bileşenlerinde](xref:blazor/components) `@using` Ayrıca hangi bileşenlerin kapsamda olduğunu da kontrol eder.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="directive-attributes"></a>Yönerge öznitelikleri

### <a name="attributes"></a>\@özelliklerine

*Bu senaryo yalnızca Razor bileşenleri (. Razor) için geçerlidir.*

`@attributes`bir bileşenin bildirilmeyen öznitelikleri işlemesini sağlar. Daha fazla bilgi için bkz. <xref:blazor/components#attribute-splatting-and-arbitrary-parameters>.

### <a name="bind"></a>\@bağladığınızda

*Bu senaryo yalnızca Razor bileşenleri (. Razor) için geçerlidir.*

Bileşenlerdeki veri bağlama, `@bind` özniteliğiyle gerçekleştirilir. Daha fazla bilgi için bkz. <xref:blazor/data-binding>.

### <a name="onevent"></a>\@{EVENT} üzerinde

*Bu senaryo yalnızca Razor bileşenleri (. Razor) için geçerlidir.*

Razor, bileşenler için olay işleme özellikleri sağlar. Daha fazla bilgi için bkz. <xref:blazor/event-handling>.

::: moniker-end

::: moniker range=">= aspnetcore-3.1"

### <a name="oneventpreventdefault"></a>\@{EVENT} üzerinde:p reventDefault

*Bu senaryo yalnızca Razor bileşenleri (. Razor) için geçerlidir.*

Olay için varsayılan eylemi engeller.

### <a name="oneventstoppropagation"></a>\@{EVENT}: Stopyayma

*Bu senaryo yalnızca Razor bileşenleri (. Razor) için geçerlidir.*

Olay için olay yaymayı sonlandırır.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="key"></a>\@anahtar

*Bu senaryo yalnızca Razor bileşenleri (. Razor) için geçerlidir.*

`@key` Directive özniteliği, anahtar değerine göre öğelerin veya bileşenlerin korunmasını güvence altına almak için bileşenlerin algoritma almasına neden olur. Daha fazla bilgi için bkz. <xref:blazor/components#use-key-to-control-the-preservation-of-elements-and-components>.

### <a name="ref"></a>\@ref

*Bu senaryo yalnızca Razor bileşenleri (. Razor) için geçerlidir.*

Bileşen başvuruları (`@ref`) bir bileşen örneğine başvurmak için bir yol sağlar, böylece bu örneğe komut verebilirsiniz. Daha fazla bilgi için bkz. <xref:blazor/components#capture-references-to-components>.

### <a name="typeparam"></a>\@typeparam

*Bu senaryo yalnızca Razor bileşenleri (. Razor) için geçerlidir.*

`@typeparam` Yönergesi, oluşturulan bileşen sınıfı için genel bir tür parametresi bildirir. Daha fazla bilgi için bkz. <xref:blazor/templated-components#generic-typed-components>.

::: moniker-end

## <a name="templated-razor-delegates"></a>Şablonlu Razor temsilcileri

Razor şablonları aşağıdaki biçimde bir UI parçacığı tanımlamanızı sağlar:

```cshtml
@<tag>...</tag>
```

Aşağıdaki örnekte, bir şablonlu Razor temsilcisinin bir <xref:System.Func%602>olarak nasıl belirtildiği gösterilmektedir. [Dinamik tür](/dotnet/csharp/programming-guide/types/using-type-dynamic) , temsilcinin sarmalayan yönteminin parametresi için belirtilir. Bir [nesne türü](/dotnet/csharp/language-reference/keywords/object) , temsilcinin dönüş değeri olarak belirtilir. Şablon, bir <xref:System.Collections.Generic.List%601> `Name` özelliğine sahip olan bir `Pet` ile kullanılır.

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

Şablon, bir `foreach` ifade tarafından `pets` sağlanan ile işlenir:

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

Ayrıca bir yönteme bağımsız değişken olarak bir satır içi Razor şablonu da sağlayabilirsiniz. Aşağıdaki örnekte, `Repeat` yöntemi Razor şablonu alır. Yöntemi, bir listeden sağlanan öğelerin tekrarlandığı HTML içeriğini oluşturmak için şablonunu kullanır:

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

*Bu senaryo yalnızca MVC görünümleri ve Razor Pages (. cshtml) için geçerlidir.*

[Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro)ile ilgili üç yönergeler vardır.

| Deki | İşlev |
| --------- | -------- |
| [`@addTagHelper`](xref:mvc/views/tag-helpers/intro#add-helper-label) | Etiket yardımcılarını bir görünüm için kullanılabilir hale getirir. |
| [`@removeTagHelper`](xref:mvc/views/tag-helpers/intro#remove-razor-directives-label) | Daha önce bir görünümden eklenen etiket yardımcıları kaldırır. |
| [`@tagHelperPrefix`](xref:mvc/views/tag-helpers/intro#prefix-razor-directives-label) | Etiket Yardımcısı desteğini etkinleştirmek ve etiket Yardımcısı kullanımını açık hale getirmek için bir etiket ön eki belirtir. |

## <a name="razor-reserved-keywords"></a>Razorayrılmış anahtar sözcükler

### <a name="razor-keywords"></a>Razorlerimi

* sayfa (ASP.NET Core 2,1 veya üzeri bir sürüm gerektirir)
* ad alanı
*  işlevleri
* alıp
* model
* section
* yardımcı (Şu anda ASP.NET Core tarafından desteklenmiyor)

Razoranahtar kelimelerden kaçışın `@(Razor Keyword)` (örneğin `@(functions)`,).

### <a name="c-razor-keywords"></a>C# Razor anahtar sözcükleri

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
* yakalaya
* finally
* kullanma
* while

C# Razor anahtar sözcükleri ile `@(@C# Razor Keyword)` çift kaçış olmalıdır (örneğin, `@(@case)`). İlki `@` Razor Ayrıştırıcıdan çıkar. İkincisi `@` , C# ayrıştırıcısının çıkar.

### <a name="reserved-keywords-not-used-by-razor"></a>Ayrılmış anahtar sözcükler tarafından kullanılmıyorRazor

* sınıf

## <a name="inspect-the-razor-c-class-generated-for-a-view"></a>Bir görünüm Razor için oluşturulan C# sınıfını inceleyin

::: moniker range=">= aspnetcore-2.1"

.NET Core SDK 2,1 veya sonraki bir sürümü ile [ Razor SDK](xref:razor-pages/sdk) , Razor dosyaların derlemesini işler. Bir proje oluştururken, Razor SDK proje kökünde bir *obj/<build_configuration>/<target_framework_moniker>/Razor * dizin oluşturur. *Razor* Dizin içindeki dizin yapısı, projenin dizin yapısını yansıtır.

ASP.NET Core 2,1 Razor sayfaları projesinde .net Core 2,1 ' i hedefleyen aşağıdaki dizin yapısını göz önünde bulundurun:

* **Alanları**
  * **Yöneticileri**
    * **Sayfaları**
      * *Index.cshtml*
      * *Index.cshtml.cs*
* **Sayfaları**
  * **Paylaşılan**
    * *_Layout.cshtml*
  * *_ViewImports. cshtml*
  * *_ViewStart. cshtml*
  * *Index.cshtml*
  * *Index.cshtml.cs*

Projenin *hata ayıklama* yapılandırmasında oluşturulması aşağıdaki *obj* dizinini verir:

* **nesnesi**
  * **H**
    * **netcoreapp 2.1/**
      * **Razor/**
        * **Alanları**
          * **Yöneticileri**
            * **Sayfaları**
              * *Index.g.cshtml.cs*
        * **Sayfaları**
          * **Paylaşılan**
            * *_Layout. g. cshtml. cs*
          * *_ViewImports. g. cshtml. cs*
          * *_ViewStart. g. cshtml. cs*
          * *Index.g.cshtml.cs*

*Pages/Index. cshtml*için oluşturulan sınıfı görüntülemek için *obj/Debug/netcoreapp 2.1/Razor/Pages/Index.g.cshtml.cs*öğesini açın.

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

Aşağıdaki Sınıfı ASP.NET Core MVC projesine ekleyin:

[!code-csharp[](razor/sample/Utilities/CustomTemplateEngine.cs)]

' `Startup.ConfigureServices`De, ile `RazorTemplateEngine` MVC tarafından eklenen ' i `CustomTemplateEngine` şu sınıfla geçersiz kılın:

[!code-csharp[](razor/sample/Startup.cs?highlight=4&range=10-14)]

`return csharpDocument;` Bildiriminde bir kesme noktası ayarlayın `CustomTemplateEngine`. Kesme noktasında program yürütmesi durdurulduğunda değerini görüntüleyin `generatedCode`.

![GeneratedCode 'ın metin görselleştiricisi görünümü](razor/_static/tvr.png)

::: moniker-end

## <a name="view-lookups-and-case-sensitivity"></a>Aramaları ve büyük/küçük harf duyarlılığını görüntüle

Görünüm Razor altyapısı, görünümler için büyük/küçük harfe duyarlı aramalar gerçekleştirir. Ancak gerçek arama, temel alınan dosya sistemine göre belirlenir:

* Dosya tabanlı kaynak:
  * Büyük/küçük harf duyarsız dosya sistemlerine (örneğin, Windows) sahip işletim sistemlerinde, fiziksel dosya sağlayıcısı aramaları büyük/küçük harfe duyarsızdır. Örneğin, `return View("Test")` */views/Home/test.exe*, */views/Home/test.exe*ve diğer tüm büyük/küçük harf çeşitlerüyle sonuçlanır.
  * Büyük/küçük harf duyarlı dosya sistemlerinde (örneğin, Linux, OSX ve ile `EmbeddedFileProvider`), aramalar büyük/küçük harfe duyarlıdır. Örneğin, `return View("Test")` özellikle */views/Home/test.exe. cshtml*ile eşleşir.
* Önceden derlenmiş görünümler: ASP.NET Core 2,0 ve üzeri sürümlerde, önceden derlenmiş görünümleri aramak tüm işletim sistemlerinde büyük/küçük harfe duyarlıdır. Davranış, fiziksel dosya sağlayıcısının Windows 'daki davranışlarıyla aynıdır. Ön derlenmiş iki görünüm yalnızca bir durumda farklıysa, arama sonucu belirleyici değildir.

Geliştiricilerin dosya ve dizin adlarını büyük küçük harf olarak eşleşmesi önerilir:

* Alan, denetleyici ve eylem adları.
* RazorSayfaları.

Eşleşen durum, dağıtımların görünümlerini temel alınan dosya sisteminden bağımsız olarak bulmasını sağlar.

## <a name="additional-resources"></a>Ek kaynaklar

[Sözdizimi kullanılarak ASP.NET Web programlamaya giriş, Razor ](/aspnet/web-pages/overview/getting-started/introducing-razor-syntax-c) sözdizimi ile Razor programlama için birçok örnek sağlar.
