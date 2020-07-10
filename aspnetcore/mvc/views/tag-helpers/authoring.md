---
title: ASP.NET Core etiket yardımcıları yazma
author: rick-anderson
description: ASP.NET Core etiket yardımcılarını yazmayı öğrenin.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/tag-helpers/authoring
ms.openlocfilehash: e630bf4b0c121796315be077623abae73d240b37
ms.sourcegitcommit: 50e7c970f327dbe92d45eaf4c21caa001c9106d0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/10/2020
ms.locfileid: "86212461"
---
# <a name="author-tag-helpers-in-aspnet-core"></a>ASP.NET Core etiket yardımcıları yazma

Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/authoring/sample) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="get-started-with-tag-helpers"></a>Etiket yardımcıları ile çalışmaya başlama

Bu öğreticide programlama etiketi yardımcıları hakkında bir giriş sunulmaktadır. [Etiket yardımcılarına giriş](intro.md) , etiket yardımcılarının sağladığı avantajları açıklar.

Etiket Yardımcısı, arabirimini uygulayan herhangi bir sınıftır `ITagHelper` . Bununla birlikte, bir etiket Yardımcısı yazdığınızda, genellikle ' dan türetirsiniz `TagHelper` , yönteme erişmenizi sağlar `Process` .

1. **Authoringtaghelmakası**adlı yeni bir ASP.NET Core projesi oluşturun. Bu proje için kimlik doğrulamaya gerek yok.

1. *Taghelmakası*adlı etiket yardımcılarını tutacak bir klasör oluşturun. *Taghelmakacıları* klasörü gerekli *değildir* , ancak makul bir kuraldır. Şimdi bazı basit etiket yardımcıları yazmaya başlaalım.

## <a name="a-minimal-tag-helper"></a>En az bir etiket Yardımcısı

Bu bölümde, bir e-posta etiketini güncelleştiren bir etiket Yardımcısı yazarsınız. Örnek:

```html
<email>Support</email>
```

Sunucu, bu biçimlendirmeyi şu şekilde dönüştürmek için e-posta etiketi yardımımız kullanacaktır:

```html
<a href="mailto:Support@contoso.com">Support@contoso.com</a>
```

Diğer bir deyişle, bu e-posta bağlantısını yapan bir bağlantı etikettir. Bir blog altyapısı yazıyorsanız ve BT 'nin pazarlama, destek ve diğer kişiler için aynı etki alanına e-posta göndermesini istiyorsanız bunu yapmak isteyebilirsiniz.

1. Aşağıdaki `EmailTagHelper` sınıfı *Taghelmakasları* klasörüne ekleyin.

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1EmailTagHelperCopy.cs)]

   * Etiket Yardımcıları, kök sınıf adı öğelerini hedefleyen bir adlandırma kuralı kullanır (sınıf adının sonunda *Taghelof* kısmı). Bu örnekte, **Emailtaghelper** 'nin kök adı *e-posta*olduğundan `<email>` etiketi hedeflenecek. Bu adlandırma kuralı, daha sonra bu şekilde nasıl geçersiz kılabileceğiniz hakkında etiket yardımcıları için çalışmalıdır.

   * `EmailTagHelper`Sınıfı öğesinden türetilir `TagHelper` . `TagHelper`Sınıfı, etiket yardımcıları yazmak için yöntemler ve özellikler sağlar.

   * Geçersiz kılınan `Process` Yöntem, çalıştırıldığında etiket Yardımcısı 'nın ne yaptığını denetler. `TagHelper`Sınıfı ayrıca aynı parametrelerle zaman uyumsuz bir sürüm ( `ProcessAsync` ) sağlar.

   * `Process`(Ve) bağlam parametresi, `ProcessAsync` geçerli HTML etiketinin yürütülmesiyle ilişkili bilgiler içerir.

   * `Process`(Ve) çıkış parametresi, `ProcessAsync` bir HTML etiketi ve içeriği oluşturmak için kullanılan orijinal kaynağın durum BILGISI olan HTML öğesi temsilcisini içerir.

   * Sınıfımızın adı, gerekli *olmayan* bir **ıghelper**sonekine sahiptir ancak en iyi yöntem kuralı olarak kabul edilir. Sınıfı şu şekilde bildirebilirsiniz:

   ```csharp
   public class Email : TagHelper
   ```

1. `EmailTagHelper`Sınıfı tüm görünümlerimize uygun hale getirmek için Razor , `addTagHelper` *views/_ViewImports. cshtml* dosyasına yönergesini ekleyin:

   [!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImportsCopyEmail.cshtml?highlight=2,3)]

   Yukarıdaki kod, derlemeizdeki tüm etiket yardımcılarını belirtmek için joker karakter sözdizimini kullanır. Sonraki ilk dize, `@addTagHelper` yüklenecek etiket yardımcısını (tüm etiket yardımcıları için "*" kullanın) ve ikinci dize olan "Authoringtaghelmakacıları", etiket Yardımcısı 'nın bulunduğu derlemeyi belirtir. Ayrıca, ikinci satırın joker karakter söz dizimini kullanarak ASP.NET Core MVC etiket yardımcılarına getirdiğine unutmayın (Bu yardımcılar [etiket yardımcılarına giriş](intro.md)bölümünde ele alınmıştır.) `@addTagHelper`Etiket yardımcısını görünüm için kullanılabilir hale getiren yönergedir Razor . Alternatif olarak, aşağıda gösterildiği gibi bir etiket yardımcısından tam nitelikli adı (FQN) sağlayabilirsiniz:

```csharp
@using AuthoringTagHelpers
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@addTagHelper AuthoringTagHelpers.TagHelpers.EmailTagHelper, AuthoringTagHelpers
```

<!--
the following snippet uses TagHelpers3 and should use TagHelpers (not the 3)
    [!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImports.cshtml?highlight=3&range=1-3)]
-->

Bir FQN kullanarak bir görünüme etiket Yardımcısı eklemek için, önce FQN ( `AuthoringTagHelpers.TagHelpers.EmailTagHelper` ) ve ardından **derleme adı** (*authoringtaghelmakaı* `namespace` ) eklersiniz. Çoğu geliştirici joker karakter söz dizimini kullanmayı tercih eder. [Etiket yardımcılarına giriş](intro.md) , etiket Yardımcısı ekleme, kaldırma, hiyerarşi ve joker karakter sözdizimi hakkında ayrıntılı bilgi sağlar.

1. *Görünümler/Home/Contact. cshtml* dosyasındaki biçimlendirmeyi şu değişikliklerle güncelleştirin:

   [!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Contact.cshtml?highlight=15,16&range=1-17)]

1. Uygulamayı çalıştırın ve HTML kaynağını görüntülemek için en sevdiğiniz tarayıcıyı kullanın, böylece e-posta etiketlerinin yer işareti işaretlemesi ile değiştirildiğini doğrulayabilirsiniz (örneğin, `<a>Support</a>` ). *Destek* ve *Pazarlama* bir bağlantı olarak işlenir, ancak `href` bunları işlevsel hale getirmek için bir özniteliğe sahip değildir. Sonraki bölümde bunu çözeceğiz.

## <a name="setattribute-and-setcontent"></a>SetAttribute ve SetContent

Bu bölümde, `EmailTagHelper` e-posta için geçerli bir tutturucu etiketi oluşturacak şekilde öğesini güncelleştireceğiz. Bir görünümden bilgi almak Razor (bir `mail-to` öznitelik biçiminde) ve bağlayıcıyı oluştururken kullanmak için güncelleştireceğiz.

`EmailTagHelper`Sınıfı şu şekilde güncelleştirin:

[!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelperMailTo.cs?range=6-22)]

* Etiket Yardımcıları için Pascal özellikli sınıf ve özellik adları, [Kebab durumlarına](https://stackoverflow.com/questions/11273282/whats-the-name-for-dash-separated-case/12273101)çevrilir. Bu nedenle, özniteliğini kullanmak için `MailTo` eşdeğer olarak kullanacaksınız `<email mail-to="value"/>` .

* Son satır, en az işlevsel etiket yardımımız için tamamlanan içeriği ayarlar.

* Vurgulanan satırda öznitelik ekleme söz dizimi gösterilmektedir:

[!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelperMailTo.cs?highlight=6&range=14-21)]

Bu yaklaşım, öznitelik koleksiyonunda mevcut olmadığı sürece "href" özniteliği için geçerlidir. `output.Attributes.Add`Etiketi öznitelikleri koleksiyonunun sonuna bir etiket Yardımcısı özniteliği eklemek için yöntemini de kullanabilirsiniz.

1. *Görünümler/Home/Contact. cshtml* dosyasındaki biçimlendirmeyi şu değişikliklerle güncelleştirin:

   [!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/ContactCopy.cshtml?highlight=15,16)]

1. Uygulamayı çalıştırın ve doğru bağlantıları oluşturmadığını doğrulayın.

<a name="self-closing"></a>

   > [!NOTE]
   > E-posta etiketi kendinden kapanış ( `<email mail-to="Rick" />` ) yazarsanız, nihai çıkış de kendi kendini kapatıyor. Etiketi yalnızca bir başlangıç etiketiyle () yazma özelliğini etkinleştirmek için `<email mail-to="Rick">` , sınıfı şu şekilde işaretlemeniz gerekir:
   >
   > [!code-csharp[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelperMailVoid.cs?highlight=1&range=6-10)]

   Kendi kendine kapanan bir e-posta etiketi Yardımcısı ile çıkış olur `<a href="mailto:Rick@contoso.com" />` . Kendi kendine kapanan bağlantı etiketleri geçerli HTML değildir, bu nedenle bir tane oluşturmak istemezsiniz, ancak kendi kendini kapatan bir etiket Yardımcısı oluşturmak isteyebilirsiniz. Etiket Yardımcıları, `TagMode` bir etiketi okuduktan sonra özelliğin türünü ayarlar.

### <a name="processasync"></a>ProcessAsync

Bu bölümde, zaman uyumsuz bir e-posta Yardımcısı yazılacak.

1. `EmailTagHelper`Sınıfını aşağıdaki kodla değiştirin:

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelper.cs?range=6-17)]

   **Notlar:**

   * Bu sürüm, zaman uyumsuz `ProcessAsync` yöntemini kullanır. Zaman uyumsuz `GetChildContentAsync` bir `Task` içeren öğesini döndürür `TagHelperContent` .

   * `output`HTML öğesinin içeriğini almak için parametresini kullanın.

1. Etiket Yardımcısı 'nın hedef e-postayı kullanabilmesi için *Görünümler/giriş/ilgili. cshtml* dosyasında aşağıdaki değişikliği yapın.

   [!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Contact.cshtml?highlight=15,16&range=1-17)]

1. Uygulamayı çalıştırın ve geçerli bir e-posta bağlantıları oluşturmadığını doğrulayın.

### <a name="removeall-precontentsethtmlcontent-and-postcontentsethtmlcontent"></a>RemoveAll, PreContent. SetHtmlContent ve PostContent. SetHtmlContent

1. Aşağıdaki `BoldTagHelper` sınıfı *Taghelmakasları* klasörüne ekleyin.

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/BoldTagHelper.cs)]

   * `[HtmlTargetElement]`Özniteliği, "Bold" adlı BIR HTML özniteliği içeren herhangi BIR HTML öğesinin eşleşeceğini ve `Process` sınıftaki geçersiz kılma yönteminin çalıştırılacağını belirten bir öznitelik parametresi geçirir. Örneğimizde, `Process` yöntemi "Bold" özniteliğini kaldırır ve içeren biçimlendirmeyi ile çevreler `<strong></strong>` .

   * Varolan etiket içeriğini değiştirmek istemediğiniz için, metodu `<strong>` ve kapanış etiketiyle birlikte açma etiketini yöntemi ile yazmanız gerekir `PreContent.SetHtmlContent` `</strong>` `PostContent.SetHtmlContent` .

1. *About. cshtml* görünümünü bir öznitelik değeri içerecek şekilde değiştirin `bold` . Tamamlanan kod aşağıda gösterilmiştir.

   [!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/AboutBoldOnly.cshtml?highlight=7)]

1. Uygulamayı çalıştırın. En sevdiğiniz tarayıcıyı kullanarak kaynağı inceleyebilir ve işaretlemeyi doğrulayabilirsiniz.

   `[HtmlTargetElement]`Yukarıdaki özniteliği yalnızca "Bold" öznitelik adını sağlayan HTML işaretlemesini hedefler. `<bold>`Öğe etiket Yardımcısı tarafından değiştirilmedi.

1. `[HtmlTargetElement]`Öznitelik satırını açıklama satırı olarak `<bold>` , diğer bir deyişle, formun HTML biçimlemesini hedeflemek için varsayılan olarak kullanılır `<bold>` . Varsayılan adlandırma kuralının etiket adı **kalın**taghelper etiketli olacak şekilde eşleştiğini unutmayın `<bold>` .

1. Uygulamayı çalıştırın ve `<bold>` etiketin etiket Yardımcısı tarafından işlendiğini doğrulayın.

Birden çok özniteliğe sahip bir sınıfı dekorasyon `[HtmlTargetElement]` , hedeflerin MANTıKSAL veya mantıksal bir sonucu olarak sonuçlanır. Örneğin, aşağıdaki kodu kullanarak kalın bir etiket veya kalın bir öznitelik eşleşir.

[!code-csharp[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/zBoldTagHelperCopy.cs?highlight=1,2&range=5-15)]

Aynı deyime birden çok öznitelik eklendiğinde, çalışma zamanı bunları mantıksal ve olarak değerlendirir. Örneğin, aşağıdaki kodda, bir HTML öğesi eşleşmesi gereken "Bold" () adlı bir özniteliğe sahip "Bold" olarak adlandırılmalıdır `<bold bold />` .

```csharp
[HtmlTargetElement("bold", Attributes = "bold")]
   ```

Ayrıca, `[HtmlTargetElement]` hedeflenen öğenin adını değiştirmek için öğesini de kullanabilirsiniz. Örneğin, `BoldTagHelper` etiketlerini hedeflemek istiyorsanız `<MyBold>` aşağıdaki özniteliği kullanacaksınız:

```csharp
[HtmlTargetElement("MyBold")]
```

## <a name="pass-a-model-to-a-tag-helper"></a>Etiket Yardımcısı 'na model geçirme

1. *Modeller* klasörü ekleyin.

1. `WebsiteContext` *Modeller* klasörüne aşağıdaki sınıfı ekleyin:

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Models/WebsiteContext.cs)]

1. Aşağıdaki `WebsiteInformationTagHelper` sınıfı *Taghelmakasları* klasörüne ekleyin.

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/WebsiteInformationTagHelper.cs)]

   * Daha önce bahsedildiği gibi, etiket yardımcıları, etiket yardımcıları için Pascal-cased C# sınıf adlarını ve özelliklerini [Kebab örneğine](https://wiki.c2.com/?KebabCase)dönüştürür. Bu nedenle, içinde kullanmak `WebsiteInformationTagHelper` için Razor yazacaksınız `<website-information />` .

   * Hedef öğeyi özniteliğiyle açıkça tanımlamamanız `[HtmlTargetElement]` , bu nedenle varsayılan değer `website-information` hedeflenecek. Aşağıdaki özniteliği uyguladıysanız (Bu Not, Kebab büyük/küçük harf değil, ancak sınıf adıyla eşleşir):

   ```csharp
   [HtmlTargetElement("WebsiteInformation")]
   ```

   Kebab Case etiketi `<website-information />` eşleşmiyor. Özniteliğini kullanmak istiyorsanız `[HtmlTargetElement]` , aşağıda gösterildiği gibi Kebab durumunu kullanacaksınız:

   ```csharp
   [HtmlTargetElement("Website-Information")]
   ```

   * Kendi kendini kapatan öğelerde içerik yoktur. Bu örnekte, Razor biçimlendirme bir kendinden kapanış etiketi kullanacaktır, ancak etiket Yardımcısı bir [bölüm](https://www.w3.org/TR/html5/sections.html#the-section-element) öğesi oluşturacak (kendi kendini kapatmakta ve öğe içine içerik yazıyor `section` ). Bu nedenle, `TagMode` `StartTagAndEndTag` Çıkış yazmak için olarak ayarlamanız gerekir. Alternatif olarak, satır ayarını açıklama ekleyebilir `TagMode` ve bir kapanış etiketiyle biçimlendirme yazabilirsiniz. (Örnek biçimlendirme Bu öğreticinin ilerleyen kısımlarında verilmiştir.)

   * `$`Aşağıdaki satırda bulunan (dolar işareti), bir [enterpolasyonlu dize](/dotnet/csharp/language-reference/keywords/interpolated-strings)kullanır:

   ```cshtml
   $@"<ul><li><strong>Version:</strong> {Info.Version}</li>
   ```

1. Aşağıdaki işaretlemeyi *About. cshtml* görünümüne ekleyin. Vurgulanan biçimlendirme Web sitesi bilgilerini görüntüler.

   [!code-cshtml[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/About.cshtml?highlight=1,4-8, 18-999)]

   > [!NOTE]
   > RazorAşağıda gösterilen İşaretlemede:
   >
   > [!code-html[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/About.cshtml?range=18-18)]
   >
   > Razor`info`özniteliğin bir dize değil bir sınıf olduğunu ve C# kodu yazmak istediğinizi bilir. Dize olmayan herhangi bir etiket Yardımcısı özniteliği karakter olmadan yazılmalıdır `@` .

1. Uygulamayı çalıştırın ve Web sitesi bilgilerini görmek için hakkında görünümüne gidin.

   > [!NOTE]
   > Aşağıdaki biçimlendirmeyi bir kapanış etiketiyle kullanabilir ve `TagMode.StartTagAndEndTag` etiketini etiket yardımcısından kaldırabilirsiniz:
   >
   > [!code-html[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/AboutNotSelfClosing.cshtml?range=20-21)]

## <a name="condition-tag-helper"></a>Koşul etiketi Yardımcısı

Koşul etiketi Yardımcısı, doğru bir değer geçirildiğinde çıktıyı işler.

1. Aşağıdaki `ConditionTagHelper` sınıfı *Taghelmakasları* klasörüne ekleyin.

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/ConditionTagHelper.cs)]

1. *Views/Home/Index. cshtml* dosyasının içeriğini aşağıdaki biçimlendirme ile değiştirin:

   [!code-cshtml[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Index.cshtml)]

1. `Index` `Home` Denetleyicisindeki yöntemi aşağıdaki kodla değiştirin:

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Controllers/HomeController.cs?range=9-18)]

1. Uygulamayı çalıştırın ve giriş sayfasına gidin. Koşullu içindeki biçimlendirme `div` işlenmez. URL 'ye sorgu dizesi ekleyin `?approved=true` (örneğin, `http://localhost:1235/Home/Index?approved=true` ). `approved`true olarak ayarlanır ve koşullu biçimlendirme görüntülenir.

> [!NOTE]
> Kalın etiket Yardımcısı ile yaptığınız gibi bir dize belirtmek yerine, hedeflenecek özniteliği belirtmek için [NameOf](/dotnet/csharp/language-reference/keywords/nameof) işlecini kullanın:
>
> [!code-csharp[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/zConditionTagHelperCopy.cs?highlight=1,2,5&range=5-18)]
>
> [NameOf](/dotnet/csharp/language-reference/keywords/nameof) işlecinin kodun yeniden düzenlenmiş olması gerekir (adı olarak değiştirmek isteyebilirsiniz `RedCondition` ).

### <a name="avoid-tag-helper-conflicts"></a>Etiket Yardımcısı çakışmalarından kaçının

Bu bölümde, bir çift bağlantı otomatik bağlama etiketi yardımcılarını yazarsınız. İlki, HTTP ile başlayan ve aynı URL 'yi içeren bir HTML Tutturucu etiketine (ve bu nedenle URL 'ye bir bağlantı oluşturan) bir URL içeren biçimlendirmenin yerini alır. İkincisi, WWW ile başlayan bir URL için aynı olur.

Bu iki yardımcılar yakından ilişkili olduğundan ve gelecekte yeniden düzenleme yapacağından, bunları aynı dosyada tutacağız.

1. Aşağıdaki `AutoLinkerHttpTagHelper` sınıfı *Taghelmakasları* klasörüne ekleyin.

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinker.cs?range=7-19)]

   >[!NOTE]
   >`AutoLinkerHttpTagHelper`Sınıfı öğeleri hedefler `p` ve bağlayıcıyı oluşturmak için [Regex](/dotnet/standard/base-types/regular-expression-language-quick-reference) kullanır.

1. *Görünümler/Home/Contact. cshtml* dosyasının sonuna aşağıdaki biçimlendirmeyi ekleyin:

   [!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Contact.cshtml?highlight=19)]

1. Uygulamayı çalıştırın ve etiket Yardımcısı 'nın bağlayıcıyı doğru bir şekilde işlediğini doğrulayın.

1. `AutoLinker` `AutoLinkerWwwTagHelper` Www metnini, özgün www metnini de içeren bir tutturucu etiketine dönüştürecek olacak şekilde sınıfını güncelleştirin. Güncelleştirilmiş kod aşağıda vurgulanır:

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinker.cs?highlight=15-34&range=7-34)]

1. Uygulamayı çalıştırın. Www metninin bir bağlantı olarak işlendiğine, ancak HTTP metninin olmadığına dikkat edin. Her iki sınıfa bir kesme noktası yerleştirirseniz, önce HTTP etiketi yardımcı sınıfının çalıştığını görebilirsiniz. Bu sorun, etiket Yardımcısı çıktısının önbelleğe alınması ve WWW etiketi Yardımcısı çalıştırıldığında, HTTP etiketi yardımcısından önbelleğe alınmış çıkışın üzerine yazar. Öğreticide daha sonra, etiket yardımcıların içinde çalıştığı sırayı nasıl denetleyebiliriz. Kodu aşağıdaki kodla düzeltireceğiz:

   [!code-csharp[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinkerCopy.cs?highlight=5,6,10,21,22,26&range=8-37)]

   > [!NOTE]
   > Otomatik bağlama etiketi yardımcıların ilk sürümünde, hedefin içeriğini aşağıdaki kodla aldınız:
   >
   > [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinker.cs?range=12)]
   >
   > Diğer bir deyişle, `GetChildContentAsync` yöntemine geçirilen öğesini kullanarak çağırın `TagHelperOutput` `ProcessAsync` . Daha önce belirtildiği gibi, çıkış önbelleğe alındığından, WINS çalıştırmak için son etiket Yardımcısı. Bu sorunu aşağıdaki kodla çözdük:
   >
   > [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z2AutoLinkerCopy.cs?range=34-35)]
   >
   > Yukarıdaki kod, içeriğin değiştirilip değiştirilmediğini denetler ve varsa çıkış arabelleğinden içeriği alır.

1. Uygulamayı çalıştırın ve iki bağlantısının beklendiği gibi çalıştığını doğrulayın. Otomatik bağlayıcı etiket yardımımız doğru ve tamamlanmış olsa da, hafif bir sorun ortaya çıkabilir. Önce WWW etiketi Yardımcısı çalışıyorsa, www bağlantıları doğru olmayacaktır. `Order`Etiketin çalıştığı sırayı denetlemek için aşırı yüklemeyi ekleyerek kodu güncelleştirin. `Order`Özelliği, aynı öğeyi hedefleyen diğer etiket yardımcıları ile ilişkili yürütme sırasını belirler. Varsayılan sıra değeri sıfırdır ve ilk olarak değerleri daha düşük olan örnekler yürütülür.

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z2AutoLinkerCopy.cs?highlight=5,6,7,8&range=8-15)]

   Yukarıdaki kod, HTTP etiketi Yardımcısı 'nın WWW etiketi Yardımcısı 'ndan önce çalışmasını güvence altına alır. `Order`Olarak değiştirin `MaxValue` ve www etiketi için oluşturulan biçimlendirmenin yanlış olduğunu doğrulayın.

## <a name="inspect-and-retrieve-child-content"></a>Alt içeriği İncele ve al

Etiket Yardımcıları, içerik almak için çeşitli özellikler sağlar.

* Sonucu öğesine eklenebilir `GetChildContentAsync` `output.Content` .
* Sonucunu `GetChildContentAsync` ile inceleyebilirsiniz `GetContent` .
* Değişiklik yaparsanız `output.Content` , `GetChildContentAsync` Otomatik bağlayıcı örneğimizde olduğu gibi çağırmadığınız sürece TagHelper gövdesi yürütülmez veya işlenmez:

[!code-csharp[](../../views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinkerCopy.cs?highlight=5,6,10&range=8-21)]

* Birden çok çağrı `GetChildContentAsync` aynı değeri döndürür ve `TagHelper` önbelleğe alınmış sonucun kullanılmamasını belirten yanlış bir parametre geçirmediğiniz takdirde gövde yeniden yürütülmez.

## <a name="load-minified-partial-view-taghelper"></a>Mini yükleme küçük bir kısmı görüntüleme TagHelper

Üretim ortamlarında, Mini karşılaşılan kısmi görünümler yüklenirken performans artırılabilir. Üretimde küçültülmüş kısmi görünümden faydalanmak için:

* Kısmi görünümleri mini görüntülemesi için derleme öncesi bir işlem oluşturun/ayarlayın.
* Geliştirme dışı ortamlarda mini kullanılan kısmi görünümleri yüklemek için aşağıdaki kodu kullanın.

[!code-csharp[](authoring/sample/AuthoringTagHelpers/src/MinifiedVersionTagHelper.cs?name=snippet)]
