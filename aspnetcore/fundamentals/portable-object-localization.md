---
title: ASP.NET Core taşınabilir nesne yerelleştirmesini yapılandırma
author: sebastienros
description: Bu makale, taşınabilir nesne dosyalarını tanıtır ve bunları Orchard Core çerçevesiyle ASP.NET Core bir uygulamada kullanmaya yönelik adımları özetler.
ms.author: scaddie
ms.date: 09/26/2017
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/portable-object-localization
ms.openlocfilehash: 57498cc2a773e5147b6eda653cc89d303f238b78
ms.sourcegitcommit: cd73744bd75fdefb31d25ab906df237f07ee7a0a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/05/2020
ms.locfileid: "84355090"
---
# <a name="configure-portable-object-localization-in-aspnet-core"></a>ASP.NET Core taşınabilir nesne yerelleştirmesini yapılandırma

[Sébastien Ros](https://github.com/sebastienros) ve [Scott Ade](https://twitter.com/Scott_Addie) tarafından

Bu makalede, [Orchard Core](https://github.com/OrchardCMS/OrchardCore) Framework ile bir ASP.NET Core uygulamasında taşınabilir nesne (PO) dosyalarını kullanma adımları gösterilmektedir.

**Note:** Orchard Core bir Microsoft ürünü değildir. Sonuç olarak, Microsoft bu özellik için destek sağlamaz.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/POLocalization) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="what-is-a-po-file"></a>PO dosyası nedir?

PO dosyaları, belirli bir dil için çevrilmiş dizeleri içeren metin dosyaları olarak dağıtılır. *. Resx* dosyaları yerine PO dosyalarını kullanmanın bazı avantajları şunlardır:
- PO dosyaları, pluralization 'ı destekler; *. resx* dosyaları pluralization 'yi desteklemez.
- PO dosyaları *. resx* dosyaları gibi derlenmemektedir. Bu nedenle, özelleştirilmiş araç ve derleme adımları gerekli değildir.
- PO dosyaları işbirliğine dayalı çevrimiçi Düzenle araçlarıyla iyi çalışır.

### <a name="example"></a>Örnek

Aşağıda, çoğul biçimi ile birlikte olmak üzere Fransızca 'daki iki dizenin çevirisini içeren örnek bir PO dosyası verilmiştir:

*fr. Po*

```text
#: Services/EmailService.cs:29
msgid "Enter a comma separated list of email addresses."
msgstr "Entrez une liste d'emails séparés par une virgule."

#: Views/Email.cshtml:112
msgid "The email address is \"{0}\"."
msgid_plural "The email addresses are \"{0}\"."
msgstr[0] "L'adresse email est \"{0}\"."
msgstr[1] "Les adresses email sont \"{0}\""
```

Bu örnek aşağıdaki sözdizimini kullanır:

- `#:`: Çevrilecek dizenin bağlamını gösteren bir açıklama. Aynı dize, kullanıldığı yere bağlı olarak farklı şekilde çevrilebilir.
- `msgid`: Çevrilmemiş dize.
- `msgstr`: Çevrilmiş dize.

Plurselme desteği söz konusu olduğunda, daha fazla girdi tanımlanabilir.

- `msgid_plural`: Çevrilmemiş çoğul dize.
- `msgstr[0]`: 0 durumu için çevrilmiş dize.
- `msgstr[N]`: N. durum için çevrilmiş dize.

PO dosya belirtimi [burada](https://www.gnu.org/savannah-checkouts/gnu/gettext/manual/html_node/PO-Files.html)bulunabilir.

## <a name="configuring-po-file-support-in-aspnet-core"></a>ASP.NET Core 'de PO dosya desteğini yapılandırma

Bu örnek, Visual Studio 2017 proje şablonundan oluşturulan ASP.NET Core MVC uygulamasını temel alır.

### <a name="referencing-the-package"></a>Pakete başvurma

`OrchardCore.Localization.Core`NuGet paketine başvuru ekleyin. Aşağıdaki paket kaynağında [Myget](https://www.myget.org/) üzerinde kullanılabilir:https://www.myget.org/F/orchardcore-preview/api/v3/index.json

*. Csproj* dosyası artık aşağıdakine benzer bir satır içerir (sürüm numarası farklılık gösterebilir):

[!code-xml[](localization/sample/2.x/POLocalization/POLocalization.csproj?range=9)]

### <a name="registering-the-service"></a>Hizmet kaydediliyor

Gerekli Hizmetleri `ConfigureServices` *Startup.cs*yöntemine ekleyin:

[!code-csharp[](localization/sample/2.x/POLocalization/Startup.cs?name=snippet_ConfigureServices&highlight=4-21)]

Gerekli ara yazılımı `Configure` *Startup.cs*yöntemine ekleyin:

[!code-csharp[](localization/sample/2.x/POLocalization/Startup.cs?name=snippet_Configure&highlight=15)]

Aşağıdaki kodu tercih ettiğiniz görünüme ekleyin Razor . Bu örnekte *. cshtml hakkında* kullanılır.

[!code-cshtml[](localization/sample/2.x/POLocalization/Views/Home/About.cshtml)]

`IViewLocalizer`"Hello World!" metnini dönüştürmek için bir örnek eklenmiş ve kullanılır.

### <a name="creating-a-po-file"></a>PO dosyası oluşturma

Uygulama kök klasörünüzde * \<culture code> . Po* adlı bir dosya oluşturun. Bu örnekte, Fransızca dili kullanıldığından dosya adı *fr. Po* olur:

[!code-text[](localization/sample/2.x/POLocalization/fr.po)]

Bu dosya, hem çevrilecek dizeyi hem de Fransızca çevrilmiş dizeyi depolar. Çevirileri, gerekirse üst kültürüne döndürülür. Bu örnekte, istenen kültür veya ise *fr. Po* dosyası kullanılır `fr-FR` `fr-CA` .

### <a name="testing-the-application"></a>Uygulamayı test etme

Uygulamanızı çalıştırın ve URL 'ye gidin `/Home/About` . **Merhaba Dünya metni!** görüntülenir.

URL 'ye gidin `/Home/About?culture=fr-FR` . **Bonjour Le Monde metni!** görüntülenir.

## <a name="pluralization"></a>Çoğullaştırma

Po dosyaları, aynı dizenin kardinalite göre farklı şekilde çevrilmesi gerektiğinde yararlı olan çoğullaştırma formlarını destekler. Bu görev, her dilin kardinalite göre hangi dizenin kullanılacağını seçmek için özel kurallar tanımladığı konusunda karmaşık hale getirilir.

Orchard yerelleştirme paketi, bu farklı çoğul formları otomatik olarak çağırmak için bir API sağlar.

### <a name="creating-pluralization-po-files"></a>Çoğullaştırma Po dosyaları oluşturma

Aşağıdaki içeriği daha önce bahsedilen *fr. Po* dosyasına ekleyin:

```text
msgid "There is one item."
msgid_plural "There are {0} items."
msgstr[0] "Il y a un élément."
msgstr[1] "Il y a {0} éléments."
```

Bu örnekteki her girdinin ne olduğunu gösteren bir açıklama için bkz. [po dosyası nedir?](#what-is-a-po-file) .

### <a name="adding-a-language-using-different-pluralization-forms"></a>Farklı çoğullaştırma formları kullanarak dil ekleme

Önceki örnekte İngilizce ve Fransızca dizeleri kullanılmıştır. İngilizce ve Fransızca yalnızca iki plurtasyon biçimine sahiptir ve aynı form kurallarını paylaşır, bu da bir kardinalitesi ilk plural formuyla eşlenir. Diğer herhangi bir kardinalite ikinci çoğul formla eşleştirilir.

Dillerin hepsi aynı kuralları paylaşmaz. Bu, üç plural formu bulunan Çekçe dil ile gösterilmiştir.

`cs.po`Dosyayı aşağıdaki gibi oluşturun ve plurun üç farklı çeviriyi nasıl ihtiyacı olduğunu aklınızda yapın:

[!code-text[](localization/sample/2.x/POLocalization/cs.po)]

Çekçe yerelleştirmeleri kabul etmek için, `"cs"` yönteminde desteklenen kültürlerin listesine ekleyin `ConfigureServices` :

```csharp
var supportedCultures = new List<CultureInfo>
{
    new CultureInfo("en-US"),
    new CultureInfo("en"),
    new CultureInfo("fr-FR"),
    new CultureInfo("fr"),
    new CultureInfo("cs")
};
```

Farklı kart aralıkları için yerelleştirilmiş, çoğul dizeleri işlemek üzere *Görünümler/Home/about. cshtml* dosyasını düzenleyin:

```cshtml
<p>@Localizer.Plural(1, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(2, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(5, "There is one item.", "There are {0} items.")</p>
```

**Note:** Gerçek bir Dünya senaryosunda, sayıyı temsil etmek için bir değişken kullanılır. Burada, çok özel bir durumu ortaya çıkarmak için aynı kodu üç farklı değerle tekrarlarız.

Kültürleri değiştirme sırasında, aşağıdakileri görürsünüz:

`/Home/About` için:

```html
There is one item.
There are 2 items.
There are 5 items.
```

`/Home/About?culture=fr` için:

```html
Il y a un élément.
Il y a 2 éléments.
Il y a 5 éléments.
```

`/Home/About?culture=cs` için:

```html
Existuje jedna položka.
Existují 2 položky.
Existuje 5 položek.
```

Çekçe kültür için, üç çevirilerin farklı olduğunu unutmayın. Fransızca ve Ingilizce kültürler, son çevrilen iki dize için aynı yapıyı paylaşır.

## <a name="advanced-tasks"></a>Gelişmiş görevler

### <a name="contextualizing-strings"></a>Contextualleme dizeleri

Uygulamalar genellikle birkaç yerde çevrilecek dizeleri içerir. Aynı dize bir uygulama içindeki belirli konumlarda farklı bir çeviriye sahip olabilir ( Razor görünümler veya sınıf dosyaları). Bir PO dosyası, temsil edilen dizeyi kategorilere ayırmak için kullanılabilecek bir dosya bağlamı kavramını destekler. Dosya bağlamını kullanarak, dosya bağlamına (veya bir dosya bağlamının olmamasından) bağlı olarak bir dize farklı şekilde çevrilebilir.

PO yerelleştirme hizmetleri, tam sınıfın veya bir dize çevrilirken kullanılan görünümün adını kullanır. Bu, girişteki değer ayarlanarak gerçekleştirilir `msgctxt` .

Önceki *fr. Po* örneğine küçük bir ek göz önünde bulundurun. Razor *Görünümler/Home/about. cshtml* 'de bulunan bir görünüm, ayrılmış girdinin değeri ayarlanarak dosya bağlamı olarak tanımlanabilir `msgctxt` :

```text
msgctxt "Views.Home.About"
msgid "Hello world!"
msgstr "Bonjour le monde!"
```

`msgctxt`Bu şekilde ayarlandığında, ' ye gidildiğinde metin çevirisi oluşur `/Home/About?culture=fr-FR` . Çeviri, ' a gidildiğinde gerçekleşmez `/Home/Contact?culture=fr-FR` .

Belirli bir giriş belirli bir dosya bağlamıyla eşleşmediğinde, Orchard Core 'un geri dönüş mekanizması bağlam olmadan uygun bir PO dosyası arar. *Görünümler/Home/Contact. cshtml*için tanımlanan belirli bir dosya bağlamı olmadığı varsayılarak, gezinmek IÇIN `/Home/Contact?culture=fr-FR` bir PO dosyası yükler:

[!code-text[](localization/sample/2.x/POLocalization/fr.po)]

### <a name="changing-the-location-of-po-files"></a>PO dosyalarının konumunu değiştirme

PO dosyalarının varsayılan konumu ' de değiştirilebilir `ConfigureServices` :

```csharp
services.AddPortableObjectLocalization(options => options.ResourcesPath = "Localization");
```

Bu örnekte, PO dosyaları *Yerelleştirme* klasöründen yüklenir.

### <a name="implementing-a-custom-logic-for-finding-localization-files"></a>Yerelleştirme dosyalarını bulmak için özel bir mantık uygulama

PO dosyalarını bulmak için daha karmaşık mantık gerektiğinde, `OrchardCore.Localization.PortableObject.ILocalizationFileLocationProvider` arabirim bir hizmet olarak uygulanabilir ve kaydedilebilir. Bu, PO dosyaları farklı konumlarda depolanabileceği veya dosyaların bir klasör hiyerarşisi içinde bulunması gerektiğinde kullanışlıdır.

### <a name="using-a-different-default-pluralized-language"></a>Farklı bir varsayılan plurar dili kullanma

Paket, `Plural` iki plural formlarına özgü bir genişletme yöntemi içerir. Daha fazla çoğul biçim gerektiren diller için bir genişletme yöntemi oluşturun. Uzantı yöntemiyle, varsayılan dil için herhangi bir yerelleştirme dosyası sağlamanız gerekmez, &mdash; özgün dizeler doğrudan kodda zaten kullanılabilir.

`Plural(int count, string[] pluralForms, params object[] arguments)`Çevirilerin dize dizisini kabul eden daha genel aşırı yüklemeyi kullanabilirsiniz.
