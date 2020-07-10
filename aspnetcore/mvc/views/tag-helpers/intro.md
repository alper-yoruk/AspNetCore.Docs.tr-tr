---
title: ASP.NET Core etiket yardımcıları
author: rick-anderson
description: Etiket yardımcılarını ve bunların ASP.NET Core nasıl kullanılacağını öğrenin.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 03/18/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/tag-helpers/intro
ms.openlocfilehash: d2791422e50082eab51c0735e2209e5b829d22f2
ms.sourcegitcommit: 50e7c970f327dbe92d45eaf4c21caa001c9106d0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/10/2020
ms.locfileid: "86212496"
---
# <a name="tag-helpers-in-aspnet-core"></a>ASP.NET Core etiket yardımcıları

Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)

## <a name="what-are-tag-helpers"></a>Etiket Yardımcıları nelerdir

Etiket Yardımcıları, sunucu tarafı kodun dosyalarda HTML öğeleri oluşturma ve işlemeye katılmasını sağlar Razor . Örneğin yerleşik, `ImageTagHelper` görüntü adına bir sürüm numarası ekleyebilir. Görüntü her değiştiğinde, sunucu görüntü için yeni bir benzersiz sürüm oluşturur, bu nedenle istemcilerin geçerli görüntüyü alma garantisi vardır (eski önbelleğe alınmış bir görüntü yerine). Yaygın görevler için, genel GitHub depolarında ve NuGet paketleri olarak formlar, bağlantılar, yükleme varlıkları ve daha fazlasını ve daha fazlasını oluşturma gibi birçok yerleşik etiket yardımcıları vardır. Etiket Yardımcıları C# dilinde yazılır ve öğe adı, öznitelik adı veya üst etikete göre HTML öğelerini hedefler. Örneğin, `LabelTagHelper` öznitelikler uygulandığında YERLEŞIK HTML öğesini hedefleyebilir `<label>` `LabelTagHelper` . [HTML Yardımcıları](https://stephenwalther.com/archive/2009/03/03/chapter-6-understanding-html-helpers)hakkında bilginiz varsa etiket yardımcıları, görünümlerde HTML ve C# arasındaki açık geçişleri azaltır Razor . Birçok durumda, HTML Yardımcıları belirli bir etiket Yardımcısı için alternatif bir yaklaşım sağlar, ancak bu etiket yardımcıların HTML yardımcılarını değiştirmez ve her HTML Yardımcısı için bir etiket Yardımcısı olmadığını bilmek önemlidir. [HTML yardımcılarına kıyasla etiket yardımcıları](#tag-helpers-compared-to-html-helpers) daha ayrıntılı olarak açıklanır.

## <a name="what-tag-helpers-provide"></a>Hangi etiket yardımcıları sağlar

**HTML kullanımı kolay bir geliştirme deneyimi** Çoğu bölümde, Razor Etiket Yardımcıları kullanan biçimlendirme standart HTML gibi görünür. Ön uç tasarımcıları HTML/CSS/JavaScript ile, Razor C# sözdizimi öğrenmeksizin düzenleyebilir Razor .

**HTML ve Razor biçimlendirme oluşturmaya yönelik zengin bir IntelliSense ORTAMı** , HTML yardımcılarından daha net bir şekilde, bu da görünümlerde biçimlendirme sırasında sunucu tarafında biçimlendirme oluşturma yaklaşımına sahiptir Razor . [HTML yardımcılarına kıyasla etiket yardımcıları](#tag-helpers-compared-to-html-helpers) daha ayrıntılı olarak açıklanır. [Etiket Yardımcıları Için IntelliSense desteği](#intellisense-support-for-tag-helpers) , IntelliSense ortamını açıklar. C# sözdizimiyle karşılaşılan geliştiricilerin bile Razor , c# işaretlemesi yazmadan etiket yardımcıları kullanılarak daha üretken olma Razor .

**Daha üretken olmanızı sağlamak ve yalnızca sunucuda bulunan bilgileri kullanarak daha sağlam, güvenilir ve sürdürülebilir kodlar elde etmek Için bir yol** Örneğin, geçmişe dönük olarak görüntülerin güncelleştirilmesi, görüntüyü değiştirdiğinizde görüntünün adını değiştiriydi. Görüntülerin performans nedenleriyle bir şekilde ön belleğe alınması gerekir ve bir görüntünün adını değiştirmediğiniz takdirde, istemcilerin eski bir kopya alıyor olması risklidir. Tarihsel olarak, bir görüntü düzenlendikten sonra ad değiştirilmelidir ve Web uygulamasındaki görüntünün güncellenmesi için gereken her başvuru. Bu çok işçiliği yoğun bir şekilde değil, bu da hataya açıktır (bir başvuruyu kaçırmanızı, yanlışlıkla yanlış dize girmeniz vb.) Yerleşik, `ImageTagHelper` sizin için otomatik olarak bunu yapabilir. Görüntü `ImageTagHelper` adına bir sürüm numarası ekleyebilir, böylece görüntü her değiştiğinde sunucu otomatik olarak görüntü için yeni bir benzersiz sürüm oluşturur. İstemcilerin geçerli görüntüyü alması garanti edilir. Bu sağlamlık ve işgücü tasarrufu, ile birlikte ücretsizdir `ImageTagHelper` .

Çoğu yerleşik etiket yardımcıları standart HTML öğelerini hedefleyin ve öğesi için sunucu tarafı öznitelikleri sağlar. Örneğin, `<input>` *Görünümler/hesap* klasöründeki birçok görünümde kullanılan öğesi `asp-for` özniteliği içerir. Bu öznitelik, belirtilen model özelliğinin adını işlenmiş HTML içine ayıklar. RazorAşağıdaki modelle bir görünüm düşünün:

```csharp
public class Movie
{
    public int ID { get; set; }
    public string Title { get; set; }
    public DateTime ReleaseDate { get; set; }
    public string Genre { get; set; }
    public decimal Price { get; set; }
}
```

Aşağıdaki Razor biçimlendirme:

```cshtml
<label asp-for="Movie.Title"></label>
```

Aşağıdaki HTML 'yi oluşturur:

```html
<label for="Movie_Title">Title</label>
```

`asp-for`Özniteliği `For` [Labeltaghelper](/dotnet/api/microsoft.aspnetcore.mvc.taghelpers.labeltaghelper?view=aspnetcore-2.0)özelliği tarafından kullanılabilir hale getirilir. Daha fazla bilgi için bkz. [Yazar etiketi yardımcıları](xref:mvc/views/tag-helpers/authoring) .

## <a name="managing-tag-helper-scope"></a>Etiket Yardımcısı kapsamını yönetme

Etiket Yardımcıları kapsamı `@addTagHelper` ,, `@removeTagHelper` ve "!" geri çevirme karakterinin bir birleşimi tarafından denetlenir.

<a name="add-helper-label"></a>

### <a name="addtaghelper-makes-tag-helpers-available"></a>`@addTagHelper`Etiket Yardımcıları kullanılabilir hale getirir

*Authoringtaghelmakaadı*adlı yeni bir ASP.NET Core Web uygulaması oluşturursanız, projenize aşağıdaki *Görünümler/_ViewImports. cshtml* dosyası eklenecektir:

[!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImportsCopy.cshtml?highlight=2&range=2-3)]

`@addTagHelper`Yönerge, etiket yardımcılarını görünüm için kullanılabilir hale getirir. Bu durumda, görünüm dosyası sayfalar */_ViewImports. cshtml*'dir ve varsayılan olarak *Sayfalar* klasörü ve alt klasörlerdeki tüm dosyalar tarafından devralınır. Etiket Yardımcıları kullanılabilir hale getirme. Yukarıdaki kod, \* belirtilen derlemedeki tüm etiket yardımcılarını (*Microsoft. Aspnetcore. Mvc. Taghelmakat*), *Görünümler* dizinindeki veya alt dizininde bulunan her görünüm dosyası için kullanılabilir olacağını belirtmek için joker karakter sözdizimini ("") kullanır. Sonraki ilk parametre, `@addTagHelper` yüklenecek etiket yardımcıları (" \* " tüm etiket yardımcıları için kullanıyoruz) ve ikinci parametre olan "Microsoft. aspnetcore. Mvc. Taghel,", etiket yardımcıları içeren derlemeyi belirtir. *Microsoft. AspNetCore. Mvc. Taghelmakatem* , yerleşik ASP.NET Core etiket yardımcıları için derlemedir.

Bu projedeki tüm etiket yardımcılarını ortaya çıkarmak için ( *Authoringtaghelmakatı*adlı bir derleme oluşturur), aşağıdakileri kullanın:

[!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImportsCopy.cshtml?highlight=3)]

Projeniz `EmailTagHelper` varsayılan ad alanı () ile bir içeriyorsa `AuthoringTagHelpers.TagHelpers.EmailTagHelper` , etiket yardımcısından tam nitelikli adı (FQN) sağlayabilirsiniz:

```cshtml
@using AuthoringTagHelpers
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@addTagHelper AuthoringTagHelpers.TagHelpers.EmailTagHelper, AuthoringTagHelpers
```

Bir FQN kullanarak bir görünüme bir etiket Yardımcısı eklemek için, önce FQN ( `AuthoringTagHelpers.TagHelpers.EmailTagHelper` ) ve ardından derleme adı (*Authoringtaghelmakaı*) eklersiniz. Çoğu geliştirici " \* " joker sözdizimini kullanmayı tercih eder. Joker karakter sözdizimi "" joker karakterini \* BIR FQN soneki olarak eklemenize olanak tanır. Örneğin, aşağıdaki yönergelerden herhangi biri içine alınacaktır `EmailTagHelper` :

```cshtml
@addTagHelper AuthoringTagHelpers.TagHelpers.E*, AuthoringTagHelpers
@addTagHelper AuthoringTagHelpers.TagHelpers.Email*, AuthoringTagHelpers
```

Daha önce bahsedildiği gibi, `@addTagHelper` *views/_ViewImports. cshtml* dosyasına yönergesini eklemek etiket Yardımcısı ' nı *Görünümler* dizinindeki ve alt dizinlerindeki tüm görünüm dosyaları için kullanılabilir hale getirir. `@addTagHelper`Etiketi yardımcısını yalnızca bu görünümlerde kullanıma sunmak istiyorsanız, belirli görünüm dosyalarında yönergesini kullanabilirsiniz.

<a name="remove-razor-directives-label"></a>

### <a name="removetaghelper-removes-tag-helpers"></a>`@removeTagHelper`Etiket Yardımcıları kaldırır

, İle `@removeTagHelper` aynı iki parametreye sahiptir `@addTagHelper` ve daha önce eklenmiş bir etiket yardımcısını kaldırır. Örneğin, `@removeTagHelper` belirli bir görünüme uygulandığında, belirtilen etiket Yardımcısı görünümden kaldırılır. `@removeTagHelper`Bir *views/Folder/_ViewImports. cshtml* dosyasında kullanmak, belirtilen etiket yardımcısını *klasördeki*tüm görünümlerden kaldırır.

### <a name="controlling-tag-helper-scope-with-the-_viewimportscshtml-file"></a>Etiket Yardımcısı kapsamını *_ViewImports. cshtml* dosyası ile denetleme

Herhangi bir görünüm klasörüne *_ViewImports. cshtml* ekleyebilirsiniz ve Görünüm altyapısı, bu dosya ve *Görünümler/_ViewImports. cshtml* dosyasından yönergeleri uygular. *Giriş* görünümleri için boş bir *Görünümler/Home/_ViewImports. cshtml* dosyası eklediyseniz, *_ViewImports. cshtml* dosyası eklenebilir olduğundan hiçbir değişiklik olmaz. `@addTagHelper` *Görünümler/Home/_ViewImports. cshtml* dosyasına ekleyeceğiniz tüm yönergeler (varsayılan *görünümlerde/_ViewImports. cshtml* dosyasında yer alan), bu etiket yardımcılarını yalnızca *giriş* klasöründeki görünümlerde kullanıma sunar.

<a name="opt-out"></a>

### <a name="opting-out-of-individual-elements"></a>Ayrı öğelerin dışında tutma

Etiket Yardımcısı geri çevirme karakteriyle ("!") birlikte öğe düzeyinde bir etiket yardımcısını devre dışı bırakabilirsiniz. Örneğin, `Email` doğrulama, `<span>` etiket Yardımcısı geri çevirme karakteriyle birlikte öğesinde devre dışıdır:

```cshtml
<!span asp-validation-for="Email" class="text-danger"></!span>
```

Açılış ve kapanış etiketine etiket Yardımcısı geri çevirme karakterini uygulamanız gerekir. (Açılış etiketine bir tane eklediğinizde, Visual Studio Düzenleyicisi, kapatma etiketine otomatik olarak bir kapanış karakteri ekler). Kabul etme karakterini ekledikten sonra, öğe ve etiket Yardımcısı öznitelikleri artık farklı bir yazı tipinde gösterilmez.

<a name="prefix-razor-directives-label"></a>

### <a name="using-taghelperprefix-to-make-tag-helper-usage-explicit"></a>`@tagHelperPrefix`Etiket Yardımcısı kullanımını açık hale getirmek için kullanma

`@tagHelperPrefix`Yönergesi etiket Yardımcısı desteğini etkinleştirmek ve etiket Yardımcısı kullanımını açık hale getirmek için bir etiket öneki dizesi belirtmenize olanak tanır. Örneğin, aşağıdaki biçimlendirmeyi *views/_ViewImports. cshtml* dosyasına ekleyebilirsiniz:

```cshtml
@tagHelperPrefix th:
```

Aşağıdaki kod görüntüsünde, etiket Yardımcısı ön eki olarak ayarlanır; `th:` Bu nedenle yalnızca öneki kullanan öğeler `th:` Etiket Yardımcıları destekler (etiket Yardımcısı etkin öğeleri farklı bir yazı tipine sahiptir). `<label>`Ve `<input>` öğeleri etiket Yardımcısı ön ekine sahiptir ve etiket Yardımcısı etkindir, ancak `<span>` öğesi değildir.

![image](intro/_static/thp.png)

İçin de geçerlidir aynı hiyerarşi kuralları `@addTagHelper` için geçerlidir `@tagHelperPrefix` .

## <a name="self-closing-tag-helpers"></a>Kendi kendine kapanan etiket yardımcıları

Birçok etiket yardımcıları, kendinden kapanış etiketleri olarak kullanılamaz. Bazı etiket yardımcıları, kendinden kapanış etiketleri olacak şekilde tasarlanmıştır. Kendi kendini kapatmak üzere tasarlanmamış bir etiket Yardımcısı kullanılması işlenmiş çıktıyı bastırır. Bir etiket Yardımcısı kendinden kapanış, işlenen çıktıda kendi kendine kapanan bir etikete neden olur. Daha fazla bilgi [için bkz.](xref:mvc/views/tag-helpers/authoring#self-closing) [etiket yardımcılarını yazma](xref:mvc/views/tag-helpers/authoring).

## <a name="c-in-tag-helpers-attributedeclaration"></a>Etiket Yardımcıları özniteliğinde/bildiriminde C# 

Etiket Yardımcıları, öğenin özniteliğinde veya etiket bildirim alanında C# ' ın kullanılmasına izin vermez. Örneğin, aşağıdaki kod geçerli değildir:

```cshtml
<input asp-for="LastName"  
       @(Model?.LicenseId == null ? "disabled" : string.Empty) />
```

Yukarıdaki kod şöyle yazılabilir:

```cshtml
<input asp-for="LastName" 
       disabled="@(Model?.LicenseId == null)" />
```

## <a name="intellisense-support-for-tag-helpers"></a>Etiket Yardımcıları için IntelliSense desteği

Visual Studio 'da yeni bir ASP.NET Core Web uygulaması oluşturduğunuzda, "Microsoft. AspNetCore" NuGet paketini Razor ekler. Araçlar ". Bu, etiket Yardımcısı araçları ekleyen pakettir.

Bir HTML öğesi yazmayı düşünün `<label>` . Visual Studio Düzenleyicisi ' ne girdikten hemen sonra `<l` IntelliSense eşleşen öğeleri görüntüler:

![image](intro/_static/label.png)

Yalnızca HTML Yardımı ( @" symbol with " bunun altındaki "<>") alınamaz.

![image](intro/_static/tagSym.png)

öğesi etiket yardımcıları tarafından hedeflenen şekilde tanımlar. Saf HTML öğeleri (gibi `fieldset` ) "<>" simgesini görüntüler.

Saf HTML `<label>` etiketi, HTML etiketini (varsayılan Visual Studio renk teması ile) bir kahverengi yazı tipinde, kırmızı olan özniteliklere ve öznitelik değerlerini mavi olarak görüntüler.

![image](intro/_static/LableHtmlTag.png)

Girdikten sonra `<label` , IntelliSense KULLANILABILIR HTML/CSS özniteliklerini ve etikete yardımcı hedeflenen öznitelikleri listeler:

![image](intro/_static/labelattr.png)

IntelliSense deyimin tamamlanması, seçili değere sahip ifadeyi tamamlamak için sekme tuşunu girmenizi sağlar:

![image](intro/_static/stmtcomplete.png)

Etiket Yardımcısı özniteliği girildiğinde, Tag ve Attribute yazı tipleri değişir. Varsayılan Visual Studio "mavi" veya "hafif" renkli temasını kullanarak yazı tipi kalın mor olur. "Koyu" temasını kullanıyorsanız, yazı tipi kalın deniz mavisi olur. Bu belgedeki görüntüler varsayılan tema kullanılarak alınmıştır.

![image](intro/_static/labelaspfor2.png)

Visual Studio *CompleteWord* kısayolunu (Ctrl + Ara Çubuğu ' nu çift tırnak işaretleri ("") içinde [varsayılan](/visualstudio/ide/default-keyboard-shortcuts-in-visual-studio) olarak girebilirsiniz ve c# sınıfında olduğu gibi artık c# dilinde olursunuz. IntelliSense, sayfa modelindeki tüm yöntemleri ve özellikleri görüntüler. Özellik türü olduğu için yöntemler ve özellikler kullanılabilir `ModelExpression` . Aşağıdaki görüntüde görünümü düzenledim, bu `Register` nedenle `RegisterViewModel` kullanılabilir.

![image](intro/_static/intellemail.png)

IntelliSense, sayfada modelin kullanabileceği özellikleri ve yöntemleri listeler. Zengin IntelliSense ortamı, CSS sınıfını seçmenize yardımcı olur:

![image](intro/_static/iclass.png)

![image](intro/_static/intel3.png)

## <a name="tag-helpers-compared-to-html-helpers"></a>HTML Yardımcıları ile karşılaştırılan etiket yardımcıları

Etiket Yardımcıları görünümlerde HTML öğelerine Razor iliştirirken [HTML YARDıMCıLARı](https://stephenwalther.com/archive/2009/03/03/chapter-6-understanding-html-helpers) görünümlerde HTML ile yöntemler olarak çağrılır Razor . RazorCSS sınıfı "Caption" içeren BIR HTML etiketi oluşturan aşağıdaki biçimlendirmeyi göz önünde bulundurun:

```cshtml
@Html.Label("FirstName", "First Name:", new {@class="caption"})
```

At ( `@` ) simgesi Razor bunun kodun başlangıcı olduğunu söyler. Sonraki iki parametre ("FirstName" ve "First Name:") dizelerdir, bu nedenle [IntelliSense](/visualstudio/ide/using-intellisense) yardımcı olamaz. Son bağımsız değişken:

```cshtml
new {@class="caption"}
```

Öznitelikleri temsil etmek için kullanılan anonim bir nesnedir. `class`, C# ' de ayrılmış bir anahtar sözcük olduğundan, C# ' ı `@` `@class=` bir sembol (özellik adı) olarak yorumlamaya zorlamak için simgesini kullanın. Bir ön uç tasarımcısına (HTML/CSS/JavaScript ve diğer istemci teknolojilerine tanıdık ancak C# ve hakkında bilgi sahibi olmayan bir kişiye Razor ), satırın çoğu yabancı olur. Tüm satır, IntelliSense 'den yardım olmadan yazılmalıdır.

Kullanarak, `LabelTagHelper` aynı biçimlendirme şöyle yazılabilir:

```cshtml
<label class="caption" asp-for="FirstName"></label>
```

Etiket yardımcı sürümüyle, `<l` Visual Studio Düzenleyicisi ' ne girdiğinizde IntelliSense eşleşen öğeleri görüntüler:

![image](intro/_static/label.png)

IntelliSense, tüm satırı yazmanıza yardımcı olur.

Aşağıdaki kod görüntüsünde, Visual Studio ile birlikte gelen ASP.NET 4.5. x MVC şablonundan oluşturulan *Görünümler/Account/Register. cshtml* görünümünün form kısmı gösterilmektedir Razor .

![image](intro/_static/regCS.png)

Visual Studio Düzenleyicisi, bir gri arka plan ile C# kodunu görüntüler. Örneğin, `AntiForgeryToken` HTML Yardımcısı:

```cshtml
@Html.AntiForgeryToken()
```

gri bir arka planla görüntülenir. Kayıt görünümündeki biçimlendirmenin çoğu C# ' dir. Etiket Yardımcıları kullanarak eşdeğer yaklaşımla karşılaştırın:

![image](intro/_static/regTH.png)

Biçimlendirme çok daha temiz ve HTML Yardımcıları yaklaşımıyla daha kolay okunabilir, düzenlenebilir ve korunur. C# kodu, sunucunun hakkında bilgi sahibi olması gereken en düşük düzeyde azaltılır. Visual Studio Düzenleyicisi, bir etiket Yardımcısı tarafından hedeflenen biçimlendirmeyi farklı bir yazı tipinde görüntüler.

*E-posta* grubunu göz önünde bulundurun:

[!code-cshtml[](intro/sample/Register.cshtml?range=12-18)]

"ASP-" özniteliklerinin her biri "e-posta" değerine sahiptir, ancak "e-posta" bir dize değildir. Bu bağlamda, "e-posta", için C# model ifadesi özelliğidir `RegisterViewModel` .

Visual Studio Düzenleyicisi, kayıt formunun etiket Yardımcısı yaklaşımında **Tüm** biçimlendirmeyi yazmanıza yardımcı olur, ancak Visual STUDIO, HTML Yardımcıları yaklaşımında kodun çoğu için yardım vermez. [Etiket Yardımcıları Için IntelliSense desteği](#intellisense-support-for-tag-helpers) , Visual Studio düzenleyicisinde etiket yardımcıları ile çalışma hakkında ayrıntılı bilgiler alır.

## <a name="tag-helpers-compared-to-web-server-controls"></a>Web sunucusu denetimleriyle karşılaştırılan etiket yardımcıları

* Etiket Yardımcıları ilişkili oldukları öğeye sahip değil; yalnızca öğe ve içerik işleme katılır. ASP.NET [Web sunucusu denetimleri](https://msdn.microsoft.com/library/7698y1f0.aspx) bir sayfada bildirilmiştir ve çağrılır.

* [Web sunucusu denetimlerinde](https://msdn.microsoft.com/library/zsyt68f1.aspx) geliştirme ve hata ayıklama zor hale getirmek için önemsiz olmayan bir yaşam döngüsü vardır.

* Web sunucusu denetimleri, istemci denetimi kullanarak istemci Belge Nesne Modeli (DOM) öğelerine işlevsellik eklemenize olanak tanır. Etiket yardımcıların DOM 'ı yok.

* Web sunucusu denetimleri otomatik tarayıcı algılamayı içerir. Etiket yardımcılarına tarayıcı bilgisi yok.

* Birden çok etiket yardımcıları aynı öğe üzerinde işlem yapabilir (bkz. [etiket Yardımcısı çakışmalarını önleme](xref:mvc/views/tag-helpers/authoring#avoid-tag-helper-conflicts) ), genellikle Web sunucusu denetimleri oluşturamazsınız.

* Etiket Yardımcıları, kapsamında oldukları HTML öğelerinin etiketini ve içeriğini değiştirebilir ancak sayfada başka bir şeyi doğrudan değiştirmez. Web sunucusu denetimleri, daha az belirli bir kapsama sahiptir ve Sayfanızın diğer bölümlerini etkileyen eylemler gerçekleştirebilir; istenmeyen yan etkileri etkinleştirme.

* Web sunucusu denetimleri, dizeleri nesnelere dönüştürmek için tür dönüştürücüler kullanır. Etiket Yardımcıları sayesinde yerel olarak C# dilinde çalışır, bu nedenle tür dönüştürmesi yapmanız gerekmez.

* Web sunucusu denetimleri, bileşenlerin ve denetimlerin çalışma zamanı ve tasarım zamanı davranışını uygulamak için [System. ComponentModel](/dotnet/api/system.componentmodel) kullanır. `System.ComponentModel`öznitelikleri ve tür dönüştürücülerini uygulamak, veri kaynaklarına bağlama ve lisans bileşenlerine yönelik temel sınıfları ve arabirimleri içerir. Genellikle, ve temel sınıftan türetilen yardımcıları etiketleyerek `TagHelper` `TagHelper` yalnızca iki yöntemi gösterir `Process` ve `ProcessAsync` .

## <a name="customizing-the-tag-helper-element-font"></a>Etiket Yardımcısı öğe yazı tipini özelleştirme

Yazı tipini ve renklendirmeyi **Araçlar**  >  **Seçenekler**  >  **ortam**  >  **yazı tiplerinden ve renklerinden**özelleştirebilirsiniz:

![image](intro/_static/fontoptions2.png)

[!INCLUDE[](~/includes/built-in-TH.md)]

## <a name="additional-resources"></a>Ek kaynaklar

* [Yazar etiketi yardımcıları](xref:mvc/views/tag-helpers/authoring)
* [Formlarla Çalışma](xref:mvc/views/working-with-forms)
* [GitHub 'Daki Taghelpersamples](https://github.com/dpaquette/TagHelperSamples) , [önyükleme](https://getbootstrap.com/)Ile çalışmaya yönelik etiket Yardımcısı örnekleri içerir.
