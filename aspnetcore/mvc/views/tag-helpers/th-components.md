---
title: ASP.NET Core içindeki etiket Yardımcısı bileşenleri
author: scottaddie
description: Etiket Yardımcısı bileşenlerinin ne olduğunu ve ASP.NET Core nasıl kullanılacağını öğrenin.
monikerRange: '>= aspnetcore-2.0'
ms.author: scaddie
ms.date: 06/12/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/tag-helpers/th-components
ms.openlocfilehash: df118cdc8346b99e4e5c60c9f0441c963543f4b4
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82767518"
---
# <a name="tag-helper-components-in-aspnet-core"></a>ASP.NET Core içindeki etiket Yardımcısı bileşenleri

[Scott Ade](https://twitter.com/Scott_Addie) ve [fiyaz bin hasan](https://github.com/fiyazbinhasan) tarafından

Etiket Yardımcısı bileşeni, sunucu tarafı kodundan HTML öğelerini koşullu olarak değiştirmenize veya eklemenize olanak sağlayan bir etiket yardımcıdır. Bu özellik ASP.NET Core 2,0 veya üzeri sürümlerde kullanılabilir.

ASP.NET Core iki yerleşik etiket Yardımcısı bileşeni içerir: `head` ve. `body` Bunlar <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers> ad alanında bulunur ve hem MVC hem Razor de sayfalarında kullanılabilir. Etiket Yardımcısı bileşenleri *_ViewImports. cshtml*'de uygulamayla kayıt gerektirmez.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/th-components/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="use-cases"></a>Uygulama alanları

Etiket Yardımcısı bileşenlerinin iki yaygın kullanım durumu şunlardır:

1. [İçine a `<link>` ekleme `<head>`.](#inject-into-html-head-element)
1. [İçine a `<script>` ekleme `<body>`.](#inject-into-html-body-element)

Aşağıdaki bölümlerde bu kullanım durumları açıklanır.

### <a name="inject-into-html-head-element"></a>HTML Head öğesine Ekle

HTML `<head>` öğesinin IÇINDE, CSS dosyaları genellikle HTML `<link>` öğesiyle içeri aktarılır. Aşağıdaki kod, `<link>` `head` etiket Yardımcısı bileşenini kullanarak öğesi öğesine `<head>` bir öğesi çıkarır:

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressStyleTagHelperComponent.cs)]

Yukarıdaki kodda:

* `AddressStyleTagHelperComponent`uygular <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent>. Soyutlama:
  * İle sınıfının başlatılmasına izin verir <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContext>.
  * HTML öğeleri eklemek veya değiştirmek için etiket Yardımcısı bileşenlerinin kullanılmasını sağlar.
* <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent.Order*> Özelliği, bileşenlerin işlendiği sırayı tanımlar. `Order`bir uygulamada etiket Yardımcısı bileşenlerinin birden çok kullanımı olduğunda gereklidir.
* <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent.ProcessAsync*>yürütme bağlamının <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContext.TagName*> özellik değerini olarak `head`karşılaştırır. Karşılaştırma true olarak değerlendirilirse, `_style` ALANıN içeriği HTML `<head>` öğesine eklenir.

### <a name="inject-into-html-body-element"></a>HTML Body öğesine Ekle

`body` Etiket Yardımcısı bileşeni, `<script>` `<body>` öğesine bir öğesi ekleyebilir. Aşağıdaki kod bu tekniği göstermektedir:

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressScriptTagHelperComponent.cs)]

`<script>` Öğesini depolamak için ayrı bir HTML dosyası kullanılır. HTML dosyası, kod temizleyici ve daha sürdürülebilir hale gelir. Önceki kod *TagHelpers/Templates/AddressToolTipScript.html* içeriğini okur ve etiket Yardımcısı çıktısına ekler. *Addresstooltipscript. html* dosyası aşağıdaki biçimlendirmeyi içerir:

[!code-html[](th-components/samples/RazorPagesSample/TagHelpers/Templates/AddressToolTipScript.html)]

Yukarıdaki kod, bir [önyükleme araç ipucu pencere öğesini](https://getbootstrap.com/docs/3.3/javascript/#tooltips) bir `<address>` `printable` özniteliği içeren herhangi bir öğeye bağlar. Bir fare işaretçisi öğenin üzerine geldiğinde efekt görünür.

## <a name="register-a-component"></a>Bir bileşeni kaydetme

Uygulamanın etiket Yardımcısı bileşenleri koleksiyonuna bir etiket Yardımcısı bileşeni eklenmelidir. Koleksiyona eklemenin üç yolu vardır:

* [Hizmetler kapsayıcısı aracılığıyla kayıt](#registration-via-services-container)
* [Dosya üzerinden Razor kayıt](#registration-via-razor-file)
* [Sayfa modeli veya denetleyici aracılığıyla kaydolma](#registration-via-page-model-or-controller)

### <a name="registration-via-services-container"></a>Hizmetler kapsayıcısı aracılığıyla kayıt

Etiket Yardımcısı bileşen sınıfı ile <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.ITagHelperComponentManager>yönetilmemişse, [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection) sistemine kaydedilmelidir. Aşağıdaki `Startup.ConfigureServices` kod, `AddressStyleTagHelperComponent` ve `AddressScriptTagHelperComponent` sınıflarını geçici bir [yaşam süresine](xref:fundamentals/dependency-injection#lifetime-and-registration-options)kaydeder:

[!code-csharp[](th-components/samples/RazorPagesSample/Startup.cs?name=snippet_ConfigureServices&highlight=12-15)]

### <a name="registration-via-razor-file"></a>Dosya üzerinden Razor kayıt

Etiket Yardımcısı bileşeni, DI ile kayıtlı değilse, bir Razor sayfalar sayfasından veya bir MVC görünümünden kayıt olabilir. Bu teknik, eklenen işaretlemeyi ve bileşen yürütme sırasını bir Razor dosyadan denetlemek için kullanılır.

`ITagHelperComponentManager`Etiket Yardımcısı bileşenleri eklemek veya uygulamadan kaldırmak için kullanılır. Aşağıdaki kod bu tekniği ile `AddressTagHelperComponent`göstermektedir:

[!code-cshtml[](th-components/samples/RazorPagesSample/Pages/Contact.cshtml?name=snippet_ITagHelperComponentManager)]

Yukarıdaki kodda:

* `@inject` Yönergesi bir örneği sağlar `ITagHelperComponentManager`. Örnek, Razor dosyadaki aşağı akış erişimi için adlı `manager` bir değişkene atanır.
* Bir örneği `AddressTagHelperComponent` , uygulamanın etiket Yardımcısı bileşenleri koleksiyonuna eklenir.

`AddressTagHelperComponent`, `markup` ve `order` parametrelerini kabul eden bir oluşturucuya uyacak şekilde değiştirilmiştir:

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressTagHelperComponent.cs?name=snippet_Constructor)]

Belirtilen `markup` parametresi, aşağıdaki gibi ' `ProcessAsync` de kullanılır:

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressTagHelperComponent.cs?name=snippet_ProcessAsync&highlight=10-11)]

### <a name="registration-via-page-model-or-controller"></a>Sayfa modeli veya denetleyici aracılığıyla kaydolma

Etiket Yardımcısı bileşeni, DI ile kayıtlı değilse, bir Razor sayfalar sayfa modelinden veya bir MVC denetleyicisinden kaydedilebilir. Bu teknik, C# mantığını Razor dosyalardan ayırmak için yararlıdır.

Bir örneğine erişmek için Oluşturucu ekleme kullanılır `ITagHelperComponentManager`. Etiket Yardımcısı bileşeni, örneğin etiket Yardımcısı bileşenleri koleksiyonuna eklenir. Aşağıdaki Razor sayfalar sayfa modelinde bu teknik gösterilmektedir `AddressTagHelperComponent`:

[!code-csharp[](th-components/samples/RazorPagesSample/Pages/Index.cshtml.cs?name=snippet_IndexModelClass)]

Yukarıdaki kodda:

* Bir örneğine erişmek için Oluşturucu ekleme kullanılır `ITagHelperComponentManager`.
* Bir örneği `AddressTagHelperComponent` , uygulamanın etiket Yardımcısı bileşenleri koleksiyonuna eklenir.

## <a name="create-a-component"></a>Bileşen oluşturma

Özel bir etiket Yardımcısı bileşeni oluşturmak için:

* Öğesinden türeten <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.TagHelperComponentTagHelper>bir ortak sınıf oluşturun.
* Sınıfına bir [`[HtmlTargetElement]`](xref:Microsoft.AspNetCore.Razor.TagHelpers.HtmlTargetElementAttribute) öznitelik uygulayın. Hedef HTML öğesinin adını belirtin.
* *Isteğe bağlı*: türün [`[EditorBrowsable(EditorBrowsableState.Never)]`](xref:System.ComponentModel.EditorBrowsableAttribute) IntelliSense 'de görüntülenmesini engellemek için sınıfa bir öznitelik uygulayın.

Aşağıdaki kod, `<address>` HTML öğesini hedefleyen özel bir etiket Yardımcısı bileşeni oluşturur:

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressTagHelperComponentTagHelper.cs)]

HTML işaretlemesini aşağıdaki `address` gibi eklemek için özel etiket Yardımcısı bileşenini kullanın:

```csharp
public class AddressTagHelperComponent : TagHelperComponent
{
    private readonly string _printableButton =
        "<button type='button' class='btn btn-info' onclick=\"window.open(" +
        "'https://binged.it/2AXRRYw')\">" +
        "<span class='glyphicon glyphicon-road' aria-hidden='true'></span>" +
        "</button>";

    public override int Order => 3;

    public override async Task ProcessAsync(TagHelperContext context,
                                            TagHelperOutput output)
    {
        if (string.Equals(context.TagName, "address",
                StringComparison.OrdinalIgnoreCase) &&
            output.Attributes.ContainsName("printable"))
        {
            var content = await output.GetChildContentAsync();
            output.Content.SetHtmlContent(
                $"<div>{content.GetContent()}</div>{_printableButton}");
        }
    }
}
```

Önceki `ProcessAsync` Yöntem, eşleşen <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContent.SetHtmlContent*> `<address>` öğeye için belirtilen HTML 'yi çıkartır. Ekleme şu durumlarda oluşur:

* Yürütme bağlamının `TagName` Özellik değeri eşittir `address`.
* Karşılık gelen `<address>` öğenin bir `printable` özniteliği vardır.

Örneğin, aşağıdaki `<address>` öğeyi `if` işlerken ifade true olarak değerlendirilir:

[!code-cshtml[](th-components/samples/RazorPagesSample/Pages/Contact.cshtml?name=snippet_AddressPrintable)]

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:fundamentals/dependency-injection>
* <xref:mvc/views/dependency-injection>
* <xref:mvc/views/tag-helpers/builtin-th/Index>
