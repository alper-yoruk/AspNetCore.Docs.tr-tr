---
title: ASP.NET Core etiket yardımcıları yazma
author: rick-anderson
description: ASP.NET Core etiket yardımcılarını yazmayı öğrenin.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/tag-helpers/authoring
ms.openlocfilehash: 0b60468b96ded559d180e7b3bf5f799ce2f4d7e3
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775095"
---
# <a name="author-tag-helpers-in-aspnet-core"></a>ASP.NET Core etiket yardımcıları yazma

Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/authoring/sample) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="get-started-with-tag-helpers"></a>Etiket yardımcıları ile çalışmaya başlama

Bu öğreticide programlama etiketi yardımcıları hakkında bir giriş sunulmaktadır. [Etiket yardımcılarına giriş](intro.md) , etiket yardımcılarının sağladığı avantajları açıklar.

Etiket Yardımcısı, `ITagHelper` arabirimini uygulayan herhangi bir sınıftır. Bununla birlikte, bir etiket Yardımcısı yazdığınızda, genellikle ' dan `TagHelper`türetirsiniz, `Process` yönteme erişmenizi sağlar.

1. **Authoringtaghelmakası**adlı yeni bir ASP.NET Core projesi oluşturun. Bu proje için kimlik doğrulamaya gerek yok.

1. *Taghelmakası*adlı etiket yardımcılarını tutacak bir klasör oluşturun. *Taghelmakacıları* klasörü gerekli *değildir* , ancak makul bir kuraldır. Şimdi bazı basit etiket yardımcıları yazmaya başlaalım.

## <a name="a-minimal-tag-helper"></a>En az bir etiket Yardımcısı

Bu bölümde, bir e-posta etiketini güncelleştiren bir etiket Yardımcısı yazarsınız. Örneğin:

```html
<email>Support</email>
```

Sunucu, bu biçimlendirmeyi şu şekilde dönüştürmek için e-posta etiketi yardımımız kullanacaktır:

```html
<a href="mailto:Support@contoso.com">Support@contoso.com</a>
```

Diğer bir deyişle, bu e-posta bağlantısını yapan bir bağlantı etikettir. Bir blog altyapısı yazıyorsanız ve BT 'nin pazarlama, destek ve diğer kişiler için aynı etki alanına e-posta göndermesini istiyorsanız bunu yapmak isteyebilirsiniz.

1. Aşağıdaki `EmailTagHelper` sınıfı *taghelmakasları* klasörüne ekleyin.

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1EmailTagHelperCopy.cs)]

   * Etiket Yardımcıları, kök sınıf adı öğelerini hedefleyen bir adlandırma kuralı kullanır (sınıf adının sonunda *Taghelof* kısmı). Bu örnekte, **Emailtaghelper** 'nin kök adı *e-posta*olduğundan `<email>` etiketi hedeflenecek. Bu adlandırma kuralı, daha sonra bu şekilde nasıl geçersiz kılabileceğiniz hakkında etiket yardımcıları için çalışmalıdır.

   * `EmailTagHelper` Sınıfı öğesinden `TagHelper`türetilir. Sınıfı `TagHelper` , etiket yardımcıları yazmak için yöntemler ve özellikler sağlar.

   * Geçersiz kılınan `Process` Yöntem, çalıştırıldığında etiket Yardımcısı 'nın ne yaptığını denetler. `TagHelper` Sınıfı ayrıca aynı parametrelerle zaman uyumsuz bir sürüm`ProcessAsync`() sağlar.

   * `Process` (Ve `ProcessAsync`) bağlam parametresi, geçerli HTML etiketinin yürütülmesiyle ilişkili bilgiler içerir.

   * `Process` (Ve `ProcessAsync`) çıkış parametresi, bir HTML etiketi ve içeriği oluşturmak için kullanılan orijinal KAYNAĞıN durum bilgisi olan HTML öğesi temsilcisini içerir.

   * Sınıfımızın adı, gerekli *olmayan* bir **ıghelper**sonekine sahiptir ancak en iyi yöntem kuralı olarak kabul edilir. Sınıfı şu şekilde bildirebilirsiniz:

   ```csharp
   public class Email : TagHelper
   ```

1. `EmailTagHelper` Sınıfı tüm görünümlerimize Razor uygun hale getirmek için, *views/_ViewImports. cshtml* dosyasına `addTagHelper` yönergesini ekleyin:

   [!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImportsCopyEmail.cshtml?highlight=2,3)]

   Yukarıdaki kod, derlemeizdeki tüm etiket yardımcılarını belirtmek için joker karakter sözdizimini kullanır. Sonraki `@addTagHelper` ilk dize, yüklenecek etiket yardımcısını (tüm etiket yardımcıları için "*" kullanın) ve ikinci dize olan "Authoringtaghelmakacıları", etiket Yardımcısı 'nın bulunduğu derlemeyi belirtir. Ayrıca, ikinci satırın joker karakter söz dizimini kullanarak ASP.NET Core MVC etiket yardımcılarına getirdiğine unutmayın (Bu yardımcılar [etiket yardımcılarına giriş](intro.md)bölümünde ele alınmıştır.) Etiket yardımcısını Razor Görünüm `@addTagHelper` için kullanılabilir hale getiren yönergedir. Alternatif olarak, aşağıda gösterildiği gibi bir etiket yardımcısından tam nitelikli adı (FQN) sağlayabilirsiniz:

```csharp
@using AuthoringTagHelpers
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@addTagHelper AuthoringTagHelpers.TagHelpers.EmailTagHelper, AuthoringTagHelpers
```

<!--
the following snippet uses TagHelpers3 and should use TagHelpers (not the 3)
    [!code-html[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImports.cshtml?highlight=3&range=1-3)]
-->

Bir FQN`AuthoringTagHelpers.TagHelpers.EmailTagHelper`kullanarak bir görünüme etiket Yardımcısı eklemek için, önce FQN () ve ardından **derleme adı** (*authoringtaghelmakaı* `namespace`) eklersiniz. Çoğu geliştirici joker karakter söz dizimini kullanmayı tercih eder. [Etiket yardımcılarına giriş](intro.md) , etiket Yardımcısı ekleme, kaldırma, hiyerarşi ve joker karakter sözdizimi hakkında ayrıntılı bilgi sağlar.

1. *Görünümler/Home/Contact. cshtml* dosyasındaki biçimlendirmeyi şu değişikliklerle güncelleştirin:

   [!code-html[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Contact.cshtml?highlight=15,16&range=1-17)]

1. Uygulamayı çalıştırın ve HTML kaynağını görüntülemek için en sevdiğiniz tarayıcıyı kullanın, böylece e-posta etiketlerinin yer işareti işaretlemesi ile değiştirildiğini doğrulayabilirsiniz (örneğin, `<a>Support</a>`). *Destek* ve *Pazarlama* bir bağlantı olarak işlenir, ancak bunları işlevsel hale getirmek için `href` bir özniteliğe sahip değildir. Sonraki bölümde bunu çözeceğiz.

## <a name="setattribute-and-setcontent"></a>SetAttribute ve SetContent

Bu bölümde, e-posta için geçerli `EmailTagHelper` bir tutturucu etiketi oluşturacak şekilde öğesini güncelleştireceğiz. Bir Razor görünümden bilgi almak (bir `mail-to` öznitelik biçiminde) ve bağlayıcıyı oluştururken kullanmak için güncelleştireceğiz.

`EmailTagHelper` Sınıfı şu şekilde güncelleştirin:

[!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelperMailTo.cs?range=6-22)]

* Etiket Yardımcıları için Pascal özellikli sınıf ve özellik adları, [Kebab durumlarına](https://stackoverflow.com/questions/11273282/whats-the-name-for-dash-separated-case/12273101)çevrilir. Bu nedenle, `MailTo` özniteliğini kullanmak için `<email mail-to="value"/>` eşdeğer olarak kullanacaksınız.

* Son satır, en az işlevsel etiket yardımımız için tamamlanan içeriği ayarlar.

* Vurgulanan satırda öznitelik ekleme söz dizimi gösterilmektedir:

[!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelperMailTo.cs?highlight=6&range=14-21)]

Bu yaklaşım, öznitelik koleksiyonunda mevcut olmadığı sürece "href" özniteliği için geçerlidir. Etiketi öznitelikleri koleksiyonunun sonuna bir `output.Attributes.Add` etiket Yardımcısı özniteliği eklemek için yöntemini de kullanabilirsiniz.

1. *Görünümler/Home/Contact. cshtml* dosyasındaki biçimlendirmeyi şu değişikliklerle güncelleştirin:

   [!code-html[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/ContactCopy.cshtml?highlight=15,16)]

1. Uygulamayı çalıştırın ve doğru bağlantıları oluşturmadığını doğrulayın.

<a name="self-closing"></a>

   > [!NOTE]
   > E-posta etiketi kendinden kapanış (`<email mail-to="Rick" />`) yazarsanız, nihai çıkış de kendi kendini kapatıyor. Etiketi yalnızca bir başlangıç etiketiyle (`<email mail-to="Rick">`) yazma özelliğini etkinleştirmek için, sınıfı şu şekilde işaretlemeniz gerekir:
   >
   > [!code-csharp[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelperMailVoid.cs?highlight=1&range=6-10)]

   Kendi kendine kapanan bir e-posta etiketi Yardımcısı ile çıkış olur `<a href="mailto:Rick@contoso.com" />`. Kendi kendine kapanan bağlantı etiketleri geçerli HTML değildir, bu nedenle bir tane oluşturmak istemezsiniz, ancak kendi kendini kapatan bir etiket Yardımcısı oluşturmak isteyebilirsiniz. Etiket Yardımcıları, bir etiketi okuduktan sonra `TagMode` özelliğin türünü ayarlar.

### <a name="processasync"></a>ProcessAsync

Bu bölümde, zaman uyumsuz bir e-posta Yardımcısı yazılacak.

1. `EmailTagHelper` Sınıfını aşağıdaki kodla değiştirin:

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelper.cs?range=6-17)]

   **Larını**

   * Bu sürüm, zaman uyumsuz `ProcessAsync` yöntemini kullanır. Zaman uyumsuz `GetChildContentAsync` bir `Task` içeren öğesini döndürür `TagHelperContent`.

   * HTML öğesinin `output` içeriğini almak için parametresini kullanın.

1. Etiket Yardımcısı 'nın hedef e-postayı kullanabilmesi için *Görünümler/giriş/ilgili. cshtml* dosyasında aşağıdaki değişikliği yapın.

   [!code-html[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Contact.cshtml?highlight=15,16&range=1-17)]

1. Uygulamayı çalıştırın ve geçerli bir e-posta bağlantıları oluşturmadığını doğrulayın.

### <a name="removeall-precontentsethtmlcontent-and-postcontentsethtmlcontent"></a>RemoveAll, PreContent. SetHtmlContent ve PostContent. SetHtmlContent

1. Aşağıdaki `BoldTagHelper` sınıfı *taghelmakasları* klasörüne ekleyin.

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/BoldTagHelper.cs)]

   * `[HtmlTargetElement]` Özniteliği, "Bold" ADLı bir HTML özniteliği içeren HERHANGI bir HTML öğesinin eşleşeceğini ve sınıftaki `Process` geçersiz kılma yönteminin çalıştırılacağını belirten bir öznitelik parametresi geçirir. Örneğimizde, `Process` yöntemi "Bold" özniteliğini kaldırır ve içeren biçimlendirmeyi ile `<strong></strong>`çevreler.

   * Varolan etiket içeriğini değiştirmek istemediğiniz için, `<strong>` metodu ve kapanış `PreContent.SetHtmlContent` `</strong>` etiketiyle `PostContent.SetHtmlContent` birlikte açma etiketini yöntemi ile yazmanız gerekir.

1. *About. cshtml* görünümünü bir `bold` öznitelik değeri içerecek şekilde değiştirin. Tamamlanan kod aşağıda gösterilmiştir.

   [!code-html[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/AboutBoldOnly.cshtml?highlight=7)]

1. Uygulamayı çalıştırın. En sevdiğiniz tarayıcıyı kullanarak kaynağı inceleyebilir ve işaretlemeyi doğrulayabilirsiniz.

   Yukarıdaki `[HtmlTargetElement]` özniteliği yalnızca "Bold" öznitelik adını sağlayan HTML işaretlemesini hedefler. `<bold>` Öğe etiket Yardımcısı tarafından değiştirilmedi.

1. `[HtmlTargetElement]` Öznitelik satırını açıklama satırı olarak, diğer bir deyişle, formun `<bold>` `<bold>`HTML biçimlemesini hedeflemek için varsayılan olarak kullanılır. Varsayılan adlandırma kuralının etiket adı **kalın**taghelper etiketli olacak şekilde `<bold>` eşleştiğini unutmayın.

1. Uygulamayı çalıştırın ve `<bold>` etiketin etiket Yardımcısı tarafından işlendiğini doğrulayın.

Birden çok `[HtmlTargetElement]` özniteliğe sahip bir sınıfı dekorasyon, HEDEFLERIN mantıksal veya mantıksal bir sonucu olarak sonuçlanır. Örneğin, aşağıdaki kodu kullanarak kalın bir etiket veya kalın bir öznitelik eşleşir.

[!code-csharp[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/zBoldTagHelperCopy.cs?highlight=1,2&range=5-15)]

Aynı deyime birden çok öznitelik eklendiğinde, çalışma zamanı bunları mantıksal ve olarak değerlendirir. Örneğin, aşağıdaki kodda, bir HTML öğesi eşleşmesi gereken "Bold" (`<bold bold />`) adlı bir özniteliğe sahip "Bold" olarak adlandırılmalıdır.

```csharp
[HtmlTargetElement("bold", Attributes = "bold")]
   ```

Ayrıca, `[HtmlTargetElement]` hedeflenen öğenin adını değiştirmek için öğesini de kullanabilirsiniz. Örneğin, `BoldTagHelper` etiketlerini hedeflemek `<MyBold>` istiyorsanız aşağıdaki özniteliği kullanacaksınız:

```csharp
[HtmlTargetElement("MyBold")]
```

## <a name="pass-a-model-to-a-tag-helper"></a>Etiket Yardımcısı 'na model geçirme

1. *Modeller* klasörü ekleyin.

1. Modeller klasörüne aşağıdaki `WebsiteContext` sınıfı ekleyin: *Models*

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Models/WebsiteContext.cs)]

1. Aşağıdaki `WebsiteInformationTagHelper` sınıfı *taghelmakasları* klasörüne ekleyin.

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/WebsiteInformationTagHelper.cs)]

   * Daha önce bahsedildiği gibi, etiket yardımcıları, etiket yardımcıları için Pascal-cased C# sınıf adlarını ve özelliklerini [Kebab örneğine](https://wiki.c2.com/?KebabCase)dönüştürür. Bu nedenle, `WebsiteInformationTagHelper` içinde Razorkullanmak için yazacaksınız. `<website-information />`

   * Hedef öğeyi `[HtmlTargetElement]` özniteliğiyle açıkça tanımlamamanız, `website-information` bu nedenle varsayılan değer hedeflenecek. Aşağıdaki özniteliği uyguladıysanız (Bu Not, Kebab büyük/küçük harf değil, ancak sınıf adıyla eşleşir):

   ```csharp
   [HtmlTargetElement("WebsiteInformation")]
   ```

   Kebab Case etiketi `<website-information />` eşleşmiyor. `[HtmlTargetElement]` Özniteliğini kullanmak istiyorsanız, aşağıda gösterildiği gibi Kebab durumunu kullanacaksınız:

   ```csharp
   [HtmlTargetElement("Website-Information")]
   ```

   * Kendi kendini kapatan öğelerde içerik yoktur. Bu örnekte, Razor biçimlendirme bir kendinden kapanış etiketi kullanacaktır, ancak etiket Yardımcısı bir [bölüm](https://www.w3.org/TR/html5/sections.html#the-section-element) öğesi oluşturacak (kendi kendini kapatmakta ve `section` öğe içine içerik yazıyor). Bu nedenle, çıkış yazmak için `TagMode` olarak `StartTagAndEndTag` ayarlamanız gerekir. Alternatif olarak, satır ayarını `TagMode` açıklama ekleyebilir ve bir kapanış etiketiyle biçimlendirme yazabilirsiniz. (Örnek biçimlendirme Bu öğreticinin ilerleyen kısımlarında verilmiştir.)

   * Aşağıdaki `$` satırda bulunan (dolar işareti), bir [enterpolasyonlu dize](/dotnet/csharp/language-reference/keywords/interpolated-strings)kullanır:

   ```cshtml
   $@"<ul><li><strong>Version:</strong> {Info.Version}</li>
   ```

1. Aşağıdaki işaretlemeyi *About. cshtml* görünümüne ekleyin. Vurgulanan biçimlendirme Web sitesi bilgilerini görüntüler.

   [!code-html[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/About.cshtml?highlight=1,4-8, 18-999)]

   > [!NOTE]
   > Aşağıda gösterilen Razor İşaretlemede:
   >
   > [!code-html[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/About.cshtml?range=18-18)]
   >
   > Razor`info` özniteliğin bir dize değil bir sınıf olduğunu ve C# kodu yazmak istediğinizi bilir. Dize olmayan herhangi bir etiket Yardımcısı özniteliği `@` karakter olmadan yazılmalıdır.

1. Uygulamayı çalıştırın ve Web sitesi bilgilerini görmek için hakkında görünümüne gidin.

   > [!NOTE]
   > Aşağıdaki biçimlendirmeyi bir kapanış etiketiyle kullanabilir ve etiketini etiket yardımcısından kaldırabilirsiniz `TagMode.StartTagAndEndTag` :
   >
   > [!code-html[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/AboutNotSelfClosing.cshtml?range=20-21)]

## <a name="condition-tag-helper"></a>Koşul etiketi Yardımcısı

Koşul etiketi Yardımcısı, doğru bir değer geçirildiğinde çıktıyı işler.

1. Aşağıdaki `ConditionTagHelper` sınıfı *taghelmakasları* klasörüne ekleyin.

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/ConditionTagHelper.cs)]

1. *Views/Home/Index. cshtml* dosyasının içeriğini aşağıdaki biçimlendirme ile değiştirin:

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Index.cshtml)]

1. `Home` Denetleyicisindeki `Index` yöntemi aşağıdaki kodla değiştirin:

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Controllers/HomeController.cs?range=9-18)]

1. Uygulamayı çalıştırın ve giriş sayfasına gidin. Koşullu `div` içindeki biçimlendirme işlenmez. URL 'ye sorgu dizesi `?approved=true` ekleyin (örneğin, `http://localhost:1235/Home/Index?approved=true`). `approved`true olarak ayarlanır ve koşullu biçimlendirme görüntülenir.

> [!NOTE]
> Kalın etiket Yardımcısı ile yaptığınız gibi bir dize belirtmek yerine, hedeflenecek özniteliği belirtmek için [NameOf](/dotnet/csharp/language-reference/keywords/nameof) işlecini kullanın:
>
> [!code-csharp[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/zConditionTagHelperCopy.cs?highlight=1,2,5&range=5-18)]
>
> [NameOf](/dotnet/csharp/language-reference/keywords/nameof) işlecinin kodun yeniden düzenlenmiş olması gerekir (adı olarak `RedCondition`değiştirmek isteyebilirsiniz).

### <a name="avoid-tag-helper-conflicts"></a>Etiket Yardımcısı çakışmalarından kaçının

Bu bölümde, bir çift bağlantı otomatik bağlama etiketi yardımcılarını yazarsınız. İlki, HTTP ile başlayan ve aynı URL 'yi içeren bir HTML Tutturucu etiketine (ve bu nedenle URL 'ye bir bağlantı oluşturan) bir URL içeren biçimlendirmenin yerini alır. İkincisi, WWW ile başlayan bir URL için aynı olur.

Bu iki yardımcılar yakından ilişkili olduğundan ve gelecekte yeniden düzenleme yapacağından, bunları aynı dosyada tutacağız.

1. Aşağıdaki `AutoLinkerHttpTagHelper` sınıfı *taghelmakasları* klasörüne ekleyin.

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinker.cs?range=7-19)]

   >[!NOTE]
   >`AutoLinkerHttpTagHelper` Sınıfı öğeleri hedefler `p` ve bağlayıcıyı oluşturmak için [Regex](/dotnet/standard/base-types/regular-expression-language-quick-reference) kullanır.

1. *Görünümler/Home/Contact. cshtml* dosyasının sonuna aşağıdaki biçimlendirmeyi ekleyin:

   [!code-html[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Contact.cshtml?highlight=19)]

1. Uygulamayı çalıştırın ve etiket Yardımcısı 'nın bağlayıcıyı doğru bir şekilde işlediğini doğrulayın.

1. Www metnini `AutoLinker` , `AutoLinkerWwwTagHelper` özgün www metnini de içeren bir tutturucu etiketine dönüştürecek olacak şekilde sınıfını güncelleştirin. Güncelleştirilmiş kod aşağıda vurgulanır:

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinker.cs?highlight=15-34&range=7-34)]

1. Uygulamayı çalıştırın. Www metninin bir bağlantı olarak işlendiğine, ancak HTTP metninin olmadığına dikkat edin. Her iki sınıfa bir kesme noktası yerleştirirseniz, önce HTTP etiketi yardımcı sınıfının çalıştığını görebilirsiniz. Bu sorun, etiket Yardımcısı çıktısının önbelleğe alınması ve WWW etiketi Yardımcısı çalıştırıldığında, HTTP etiketi yardımcısından önbelleğe alınmış çıkışın üzerine yazar. Öğreticide daha sonra, etiket yardımcıların içinde çalıştığı sırayı nasıl denetleyebiliriz. Kodu aşağıdaki kodla düzeltireceğiz:

   [!code-csharp[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinkerCopy.cs?highlight=5,6,10,21,22,26&range=8-37)]

   > [!NOTE]
   > Otomatik bağlama etiketi yardımcıların ilk sürümünde, hedefin içeriğini aşağıdaki kodla aldınız:
   >
   > [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinker.cs?range=12)]
   >
   > Diğer bir deyişle, `GetChildContentAsync` `TagHelperOutput` `ProcessAsync` yöntemine geçirilen öğesini kullanarak çağırın. Daha önce belirtildiği gibi, çıkış önbelleğe alındığından, WINS çalıştırmak için son etiket Yardımcısı. Bu sorunu aşağıdaki kodla çözdük:
   >
   > [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z2AutoLinkerCopy.cs?range=34-35)]
   >
   > Yukarıdaki kod, içeriğin değiştirilip değiştirilmediğini denetler ve varsa çıkış arabelleğinden içeriği alır.

1. Uygulamayı çalıştırın ve iki bağlantısının beklendiği gibi çalıştığını doğrulayın. Otomatik bağlayıcı etiket yardımımız doğru ve tamamlanmış olsa da, hafif bir sorun ortaya çıkabilir. Önce WWW etiketi Yardımcısı çalışıyorsa, www bağlantıları doğru olmayacaktır. Etiketin çalıştığı sırayı denetlemek için `Order` aşırı yüklemeyi ekleyerek kodu güncelleştirin. `Order` Özelliği, aynı öğeyi hedefleyen diğer etiket yardımcıları ile ilişkili yürütme sırasını belirler. Varsayılan sıra değeri sıfırdır ve ilk olarak değerleri daha düşük olan örnekler yürütülür.

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z2AutoLinkerCopy.cs?highlight=5,6,7,8&range=8-15)]

   Yukarıdaki kod, HTTP etiketi Yardımcısı 'nın WWW etiketi Yardımcısı 'ndan önce çalışmasını güvence altına alır. Olarak `Order` `MaxValue` değiştirin ve www etiketi için oluşturulan biçimlendirmenin yanlış olduğunu doğrulayın.

## <a name="inspect-and-retrieve-child-content"></a>Alt içeriği İncele ve al

Etiket Yardımcıları, içerik almak için çeşitli özellikler sağlar.

* Sonucu `GetChildContentAsync` öğesine `output.Content`eklenebilir.
* Sonucunu `GetChildContentAsync` ile `GetContent`inceleyebilirsiniz.
* Değişiklik `output.Content`yaparsanız, otomatik bağlayıcı örneğimizde olduğu gibi çağırmadığınız `GetChildContentAsync` sürece taghelper gövdesi yürütülmez veya işlenmez:

[!code-csharp[](../../views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinkerCopy.cs?highlight=5,6,10&range=8-21)]

* Birden çok çağrı `GetChildContentAsync` aynı değeri döndürür ve önbelleğe alınmış sonucun kullanılmamasını belirten `TagHelper` yanlış bir parametre geçirmediğiniz takdirde gövde yeniden yürütülmez.

## <a name="load-minified-partial-view-taghelper"></a>Mini yükleme küçük bir kısmı görüntüleme TagHelper

Üretim ortamlarında, Mini karşılaşılan kısmi görünümler yüklenirken performans artırılabilir. Üretimde küçültülmüş kısmi görünümden faydalanmak için:

* Kısmi görünümleri mini görüntülemesi için derleme öncesi bir işlem oluşturun/ayarlayın.
* Geliştirme dışı ortamlarda mini kullanılan kısmi görünümleri yüklemek için aşağıdaki kodu kullanın.

[!code-csharp[](authoring/sample/AuthoringTagHelpers/src/MinifiedVersionTagHelper.cs?name=snippet)]
