---
title: ASP.NET Core 'de bağlayıcı etiketi Yardımcısı
author: pkellner
description: ASP.NET Core tutturucu etiketi yardımcı özniteliklerini ve her bir özniteliğin, HTML bağlantısı etiketinin genişletme davranışında oynadığı rolü bulur.
ms.author: scaddie
ms.custom: mvc
ms.date: 10/13/2019
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
uid: mvc/views/tag-helpers/builtin-th/anchor-tag-helper
ms.openlocfilehash: d39db59b0fc273fe4193a4864f302ecd3f4ad348
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060917"
---
# <a name="anchor-tag-helper-in-aspnet-core"></a>ASP.NET Core 'de bağlayıcı etiketi Yardımcısı

By [Peter Kellner](https://peterkellner.net) ve [Scott Ade](https://github.com/scottaddie)

[Tutturucu etiketi Yardımcısı](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper) , yeni öznitelikler ekleyerek standart HTML Tutturucu ( `<a ... ></a>` ) etiketini geliştirir. Kurala göre, öznitelik adları ön ekine sahiptir `asp-` . İşlenmiş tutturucu öğenin `href` öznitelik değeri, özniteliklerin değerlerine göre belirlenir `asp-` .

Etiket Yardımcıları hakkında genel bilgi için bkz <xref:mvc/views/tag-helpers/intro> ..

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/built-in/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

*Speakercontroller* bu belgenin tamamında örneklerde kullanılır:

[!code-csharp[](samples/TagHelpersBuiltIn/Controllers/SpeakerController.cs?name=snippet_SpeakerController)]

## <a name="anchor-tag-helper-attributes"></a>Tutturucu etiketi yardımcı öznitelikleri

### <a name="asp-controller"></a>ASP-Controller

[ASP-Controller](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Controller*) ÖZNITELIĞI, URL oluşturmak için kullanılan denetleyiciyi atar. Aşağıdaki biçimlendirme tüm hoparlörleri listeler:

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspController)]

Oluşturulan HTML:

```html
<a href="/Speaker">All Speakers</a>
```

`asp-controller`Özniteliği belirtilmişse ve `asp-action` yoksa, varsayılan `asp-action` değer şu anda yürütülmekte olan görünümle ilişkili denetleyici eylemi olur. `asp-action`Önceki biçimlendirmeden atlanırsa ve bağlayıcı etiketi Yardımcısı *HomeController* 'ın *Dizin* görünümünde ( */Home* ) kullanılırsa, oluşturulan HTML:

```html
<a href="/Home">All Speakers</a>
```

### <a name="asp-action"></a>ASP-eylem

[ASP-Action](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Action*) öznitelik değeri, oluşturulan özniteliğe dahil olan denetleyici eylemi adını temsil eder `href` . Aşağıdaki biçimlendirme oluşturulan `href` öznitelik değerini konuşmacı değerlendirmeleri sayfasına ayarlar:

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspAction)]

Oluşturulan HTML:

```html
<a href="/Speaker/Evaluations">Speaker Evaluations</a>
```

Hiçbir `asp-controller` öznitelik belirtilmemişse, geçerli görünümü yürüten görünümü çağıran varsayılan denetleyici kullanılır.

`asp-action`Öznitelik değeri ise `Index` , URL 'ye hiçbir eylem eklenmez ve bu, varsayılan eylemi çağırmaya önde gelen bir eylem değildir `Index` . Belirtilen eylem (veya varsayılan olarak), içinde başvurulan denetleyicide bulunmalıdır `asp-controller` .

### <a name="asp-route-value"></a>ASP-Route-{Value}

[ASP-Route-{Value}](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.RouteValues*) özniteliği bir joker karakter yolu öneki sunar. Yer tutucusunu kaplayan herhangi bir değer `{value}` olası bir yol parametresi olarak yorumlanır. Varsayılan bir yol bulunmazsa, bu rota öneki oluşturulan `href` özniteliğe bir istek parametresi ve değeri olarak eklenir. Aksi takdirde, yol şablonunda değiştirilir.

Aşağıdaki denetleyici eylemini göz önünde bulundurun:

[!code-csharp[](samples/TagHelpersBuiltIn/Controllers/BuiltInTagController.cs?name=snippet_AnchorTagHelperAction)]

*Startup.Configure* içinde tanımlanmış bir varsayılan yol şablonuyla:

[!code-csharp[](samples/TagHelpersBuiltIn/Startup.cs?name=snippet_UseMvc&highlight=8-10)]

MVC görünümü, aşağıdaki gibi eylem tarafından belirtilen modeli kullanır:

```cshtml
@model Speaker
<!DOCTYPE html>
<html>
<body>
    <a asp-controller="Speaker"
       asp-action="Detail" 
       asp-route-id="@Model.SpeakerId">SpeakerId: @Model.SpeakerId</a>
</body>
</html>
```

Varsayılan yolun `{id?}` yer tutucusu eşleşti. Oluşturulan HTML:

```html
<a href="/Speaker/Detail/12">SpeakerId: 12</a>
```

Aşağıdaki MVC görünümünde olduğu gibi, yol ön ekinin eşleşen yönlendirme şablonunun bir parçası olmadığını varsayın:

```cshtml
@model Speaker
<!DOCTYPE html>
<html>
<body>
    <a asp-controller="Speaker"
       asp-action="Detail"
       asp-route-speakerid="@Model.SpeakerId">SpeakerId: @Model.SpeakerId</a>
<body>
</html>
```

Eşleşen rotada bulunamadığı için aşağıdaki HTML oluşturuldu `speakerid` :

```html
<a href="/Speaker/Detail?speakerid=12">SpeakerId: 12</a>
```

Ya da `asp-controller` `asp-action` belirtilmemişse, aynı varsayılan işleme, özniteliğinde olduğu gibi olur `asp-route` .

### <a name="asp-route"></a>ASP-Route

[ASP-Route](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Route*) özniteliği, doğrudan adlandırılmış bir yola bağlanan bir URL oluşturmak için kullanılır. [Yönlendirme özniteliklerini](xref:mvc/controllers/routing#attribute-routing)kullanarak, bir yol, öğesinde gösterildiği gibi adlandırılabilir `SpeakerController` ve `Evaluations` eyleminde kullanılır:

[!code-csharp[](samples/TagHelpersBuiltIn/Controllers/SpeakerController.cs?range=22-24)]

Aşağıdaki biçimlendirmede `asp-route` öznitelik, adlandırılmış yola başvurur:

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspRoute)]

Tutturucu etiketi Yardımcısı, */Hoparlörker/değerlendirmeleri* URL 'sini kullanarak bu denetleyiciye doğrudan bir yol oluşturur. Oluşturulan HTML:

```html
<a href="/Speaker/Evaluations">Speaker Evaluations</a>
```

`asp-controller`Veya `asp-action` buna ek olarak belirtilmişse `asp-route` , oluşturulan yol beklediğiniz gibi olmayabilir. Bir yol çakışmasını önlemek için `asp-route` `asp-controller` ve `asp-action` öznitelikleriyle kullanılmamalıdır.

### <a name="asp-all-route-data"></a>ASP-All-Route-Data

[ASP-All-Route-Data](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.RouteValues*) özniteliği, anahtar-değer çiftleri sözlüğü oluşturmayı destekler. Anahtar parametre adıdır ve değer parametre değeridir.

Aşağıdaki örnekte, bir sözlük başlatılır ve bir Razor görünüme geçirilir. Alternatif olarak, veriler modelinize iletilebilir.

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspAllRouteData)]

Yukarıdaki kod, aşağıdaki HTML 'yi oluşturur:

```html
<a href="/Speaker/EvaluationsCurrent?speakerId=11&currentYear=true">Speaker Evaluations</a>
```

`asp-all-route-data`Sözlük, aşırı yüklenmiş eylemin gereksinimlerini karşılayan bir QueryString oluşturmak için düzleştirilir `Evaluations` :

[!code-csharp[](samples/TagHelpersBuiltIn/Controllers/SpeakerController.cs?range=26-30)]

Sözlükteki herhangi bir anahtar yol parametreleriyle eşleşiyorsa, bu değerler rotada uygun şekilde değiştirilir. Diğer eşleşme olmayan değerler istek parametreleri olarak oluşturulur.

### <a name="asp-fragment"></a>ASP-Fragment

[ASP-Fragment](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Fragment*) özniteliği URL 'ye eklenecek URL parçasını tanımlar. Tutturucu etiketi Yardımcısı, karma karakterini (#) ekler. Aşağıdaki biçimlendirmeyi göz önünde bulundurun:

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspFragment)]

Oluşturulan HTML:

```html
<a href="/Speaker/Evaluations#SpeakerEvaluations">Speaker Evaluations</a>
```

Karma Etiketler, istemci tarafı uygulamalar oluşturulurken faydalıdır. JavaScript 'te kolay işaretlemek ve aramak için kullanılabilirler.

### <a name="asp-area"></a>ASP-alanı

[ASP-Area](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Area*) özniteliği, uygun yolu ayarlamak için kullanılan alan adını ayarlar. Aşağıdaki örneklerde, `asp-area` özniteliğinin yolların yeniden eşleştirmesine neden olduğu gösterilmektedir.

#### <a name="usage-in-no-locrazor-pages"></a>Sayfalarda kullanım Razor

Razor Sayfa alanı ASP.NET Core 2,1 veya üzeri sürümlerde desteklenir.

Aşağıdaki dizin hiyerarşisini göz önünde bulundurun:

* **{Proje adı}**
  * **wwwroot**
  * **Alanlar**
    * **Oturumlar**
      * **Sayfalar**
        * *\_ViewStart. cshtml*
        * *Index.cshtml*
        * *Index.cshtml.cs*
  * **Sayfalar**

*Oturumlar* alanı *Dizin* sayfasına başvurmak için yapılan biçimlendirme şunlardır Razor :

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspAreaRazorPages)]

Oluşturulan HTML:

```html
<a href="/Sessions">View Sessions</a>
```

> [!TIP]
> Bir sayfalar uygulamasındaki bölgeleri desteklemek için aşağıdakilerden Razor birini yapın `Startup.ConfigureServices` :
>
> * [Uyumluluk sürümünü](xref:mvc/compatibility-version) 2,1 veya üzeri olarak ayarlayın.
> * [ Razor Pagesoptions. allowareas](xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.AllowAreas*) özelliğini şu şekilde ayarlayın `true` :
>
>   [!code-csharp[](samples/TagHelpersBuiltIn/Startup.cs?name=snippet_AllowAreas)]

#### <a name="usage-in-mvc"></a>MVC 'de kullanım

Aşağıdaki dizin hiyerarşisini göz önünde bulundurun:

* **{Proje adı}**
  * **wwwroot**
  * **Alanlar**
    * **Bloglar**
      * **Denetleyiciler**
        * *HomeController.cs*
      * **Görünümler**
        * **Giriş Ekranı**
          * *AboutBlog. cshtml*
          * *Index.cshtml*
        * *\_ViewStart. cshtml*
  * **Denetleyiciler**

`asp-area`"Bloglar" ayarı, Dizin *alanları/bloglarını* bu tutturucu etiketi için ilişkili denetleyicilerin ve görünümlerin yollarına ön ekler. *Aboutblog* görünümüne başvurmak için yapılan biçimlendirme şu şekilde yapılır:

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspArea)]

Oluşturulan HTML:

```html
<a href="/Blogs/Home/AboutBlog">About Blog</a>
```

> [!TIP]
> MVC uygulamasındaki bölgeleri desteklemek için, yol şablonu varsa alana bir başvuru içermelidir. Bu şablon `routes.MapRoute` *Startup.Configure* içindeki yöntem çağrısının ikinci parametresiyle temsil edilir:
>
> [!code-csharp[](samples/TagHelpersBuiltIn/Startup.cs?name=snippet_UseMvc&highlight=5)]

### <a name="asp-protocol"></a>ASP-protokol

[ASP-Protocol](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Protocol*) ÖZNITELIĞI, URL 'niz için bir protokol (gibi) belirtmek içindir `https` . Örneğin:

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspProtocol)]

Oluşturulan HTML:

```html
<a href="https://localhost/Home/About">About</a>
```

Örnekteki ana bilgisayar adı localhost 'tur. Tutturucu etiketi Yardımcısı, URL oluştururken Web sitesinin ortak etki alanını kullanır.

### <a name="asp-host"></a>ASP-ana bilgisayar

[ASP-Host](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Host*) ÖZNITELIĞI, URL 'niz için bir ana bilgisayar adı belirtmektir. Örneğin:

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspHost)]

Oluşturulan HTML:

```html
<a href="https://microsoft.com/Home/About">About</a>
```

### <a name="asp-page"></a>asp-sayfa

[ASP-Page](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Page*) özniteliği Razor sayfalarla kullanılır. Bir tutturucu etiketinin `href` öznitelik değerini belirli bir sayfaya ayarlamak için kullanın. Sayfa adının eğik çizgiyle ("/") önek olarak URL 'SI oluşturulur.

Aşağıdaki örnek, katılımcı sayfasına işaret eder Razor :

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspPage)]

Oluşturulan HTML:

```html
<a href="/Attendee">All Attendees</a>
```

`asp-page`Özniteliği `asp-route` ,, ve öznitelikleriyle birbirini dışlıyor `asp-controller` `asp-action` . Ancak, `asp-page` `asp-route-{value}` aşağıdaki işaretlemenin gösterdiği gibi yönlendirmeyi denetlemek için ile birlikte kullanılabilir:

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspPageAspRouteId)]

Oluşturulan HTML:

```html
<a href="/Attendee?attendeeid=10">View Attendee</a>
```

### <a name="asp-page-handler"></a>ASP-Page-Handler

[ASP-Page-Handler](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.PageHandler*) özniteliği Razor sayfalarla kullanılır. Belirli sayfa işleyicileriyle bağlantı için tasarlanmıştır.

Aşağıdaki sayfa işleyicisini göz önünde bulundurun:

[!code-csharp[](samples/TagHelpersBuiltIn/Pages/Attendee.cshtml.cs?name=snippet_OnGetProfileHandler)]

Sayfa modelinin ilişkili biçimlendirmesi `OnGetProfile` sayfa işleyicisine bağlanır. Göz önünde, `On<Verb>` sayfa işleyicisi yöntem adının ön eki öznitelik değerinde atlanacaktır `asp-page-handler` . Yöntem zaman uyumsuz olduğunda, `Async` sonek de atlanır.

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspPageHandler)]

Oluşturulan HTML:

```html
<a href="/Attendee?attendeeid=12&handler=Profile">Attendee Profile</a>
```

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:mvc/controllers/areas>
* <xref:razor-pages/index>
* <xref:mvc/compatibility-version>
