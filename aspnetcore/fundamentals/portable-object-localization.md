---
title: Taşınabilir nesne yerelleştirmeyi ASP.NET Core'da yapılandırma
author: sebastienros
description: Bu makalede, Taşınabilir Nesne dosyaları tanıtır ve Orchard Core çerçeve ile ASP.NET Core uygulamasında bunları kullanmak için adımları özetliyor.
ms.author: scaddie
ms.date: 09/26/2017
uid: fundamentals/portable-object-localization
ms.openlocfilehash: 08002564eb68bc04eebaeafed560202d0d69958a
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78656193"
---
# <a name="configure-portable-object-localization-in-aspnet-core"></a>Taşınabilir nesne yerelleştirmeyi ASP.NET Core'da yapılandırma

Yazar: [Sébastien Ros](https://github.com/sebastienros) ve [Scott Addie](https://twitter.com/Scott_Addie)

Bu makalede, [Orchard Core](https://github.com/OrchardCMS/OrchardCore) çerçevesi ile ASP.NET Core uygulamasında Taşınabilir Nesne (PO) dosyalarını kullanma adımları üzerinden yürür.

**Not:** Orchard Core bir Microsoft ürünü değildir. Sonuç olarak, Microsoft bu özellik için destek sağlamaz.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/POLocalization) ( nasıl[indirilir](xref:index#how-to-download-a-sample))

## <a name="what-is-a-po-file"></a>Po dosyası nedir?

PO dosyaları, belirli bir dil için çevrilen dizeleri içeren metin dosyaları olarak dağıtılır. *.resx* dosyaları yerine PO dosyalarını kullanmanın bazı avantajları şunlardır:
- PO dosyaları çoğullaştırmayı destekler; *.resx* dosyaları çoğullaştırmayı desteklemez.
- PO dosyaları *.resx* dosyaları gibi derlenmez. Bu nedenle, özel takım lama ve oluşturma adımları gerekmez.
- PO dosyaları işbirlikçi çevrimiçi düzenleme araçlarıyla iyi çalışır.

### <a name="example"></a>Örnek

Burada, biri çoğul formuyla olmak üzere, Fransızca'da iki dize için çeviri içeren örnek bir PO dosyası veremilmektedir:

*fr.po*

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

Bu örnekte aşağıdaki sözdizimi kullanır:

- `#:`: Çevrilecek dize bağlamını gösteren bir yorum. Aynı dize, nerede kullanıldığına bağlı olarak farklı şekilde çevrilebilir.
- `msgid`: Çevrilmemiş dize.
- `msgstr`: Çevrilmiş dize.

Çoğullaştırma desteği söz konusu olduğunda, daha fazla giriş tanımlanabilir.

- `msgid_plural`: Çevrilmemiş çoğul dize.
- `msgstr[0]`: Servis talebi 0 için çevrilmiş dize.
- `msgstr[N]`: N davası için çevrilmiş dize.

PO dosya belirtimi [burada](https://www.gnu.org/savannah-checkouts/gnu/gettext/manual/html_node/PO-Files.html)bulunabilir.

## <a name="configuring-po-file-support-in-aspnet-core"></a>ASP.NET Core'da PO dosya desteğini yapılandırma

Bu örnek, Visual Studio 2017 proje şablonundan oluşturulan ASP.NET Core MVC uygulamasına dayanmaktadır.

### <a name="referencing-the-package"></a>Pakete başvurma

`OrchardCore.Localization.Core` NuGet paketine bir başvuru ekleyin. [MyGet'de](https://www.myget.org/) aşağıdaki paket kaynağından temin edilebilir:https://www.myget.org/F/orchardcore-preview/api/v3/index.json

*.csproj* dosyası artık aşağıdakine benzer bir satır içerir (sürüm numarası değişebilir):

[!code-xml[](localization/sample/POLocalization/POLocalization.csproj?range=9)]

### <a name="registering-the-service"></a>Hizmetin kaydedilmesi

`ConfigureServices` *Startup.cs*yöntemine gerekli hizmetleri ekleyin:

[!code-csharp[](localization/sample/POLocalization/Startup.cs?name=snippet_ConfigureServices&highlight=4-21)]

`Configure` *Startup.cs*yöntemine gerekli ara ware ekleyin:

[!code-csharp[](localization/sample/POLocalization/Startup.cs?name=snippet_Configure&highlight=15)]

Seçtiğiniz Razor görünümüne aşağıdaki kodu ekleyin. *About.cshtml* bu örnekte kullanılır.

[!code-cshtml[](localization/sample/POLocalization/Views/Home/About.cshtml)]

Bir `IViewLocalizer` örnek enjekte edilir ve metin "Merhaba dünya!" çevirmek için kullanılır.

### <a name="creating-a-po-file"></a>PO dosyası oluşturma

Uygulama kök klasörünüzde * \<kültür kodu>.po* adlı bir dosya oluşturun. Bu örnekte, Fransızca dil kullanıldığından dosya adı *fr.po'dur:*

[!code-text[](localization/sample/POLocalization/fr.po)]

Bu dosya, hem çevirmek için dize hem de Fransızca çevrilmiş dizeyi depolar. Çeviriler gerekirse ana kültürlerine geri döner. Bu örnekte, *fr.po* istenen kültür `fr-FR` veya `fr-CA`.

### <a name="testing-the-application"></a>Uygulamayı test etme

Uygulamanızı çalıştırın ve URL'ye `/Home/About`gidin. Metin **Merhaba dünya!** görüntülenir.

URL'ye `/Home/About?culture=fr-FR`gidin. Metin **Bonjour le monde!** görüntülenir.

## <a name="pluralization"></a>Çoğullaştırma

PO dosyaları, aynı dize bir kardinallik dayalı farklı çevrilmesi gerektiğinde yararlı olan çoğullaştırma formları, destekler. Bu görev, her dilin, kardinalliğe bağlı olarak hangi dizeyi kullanacağını seçmek için özel kurallar tanımlaması nedeniyle karmaşık hale getirilir.

Orchard Yerelleştirme paketi, bu farklı çoğul formları otomatik olarak çağırmak için bir API sağlar.

### <a name="creating-pluralization-po-files"></a>Çoğullaştırma PO dosyaları oluşturma

Daha önce bahsedilen *fr.po* dosyasına aşağıdaki içeriği ekleyin:

```text
msgid "There is one item."
msgid_plural "There are {0} items."
msgstr[0] "Il y a un élément."
msgstr[1] "Il y a {0} éléments."
```

Bakınız [Po dosyası nedir?](#what-is-a-po-file)

### <a name="adding-a-language-using-different-pluralization-forms"></a>Farklı çoğullaştırma biçimlerini kullanarak dil ekleme

Önceki örnekte İngilizce ve Fransızca dizeleri kullanılmıştır. İngilizce ve Fransızca sadece iki çoğuloluşturma biçimine sahiptir ve aynı biçim kurallarını paylaşır, yani bir kardinallik ilk çoğul forma eşlenir. Diğer kardinallik ikinci çoğul forma eşlenir.

Tüm diller aynı kuralları paylaşmaz. Bu üç çoğul formları vardır Çek dili ile gösterilmiştir.

Dosyayı `cs.po` aşağıdaki gibi oluşturun ve çoğullaştırmanın üç farklı çeviriye nasıl ihtiyaç duyduğuna dikkat edin:

[!code-text[](localization/sample/POLocalization/cs.po)]

Çek yerelleştirmelerini kabul `"cs"` etmek için, yöntemdeki desteklenen `ConfigureServices` kültürler listesine ekleyin:

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

Çeşitli kardinaller için yerelleştirilmiş, çoğul dizeleri işlemek için *Görünümler/Ana Sayfa/About.cshtml* dosyasını edin:

```cshtml
<p>@Localizer.Plural(1, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(2, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(5, "There is one item.", "There are {0} items.")</p>
```

**Not:** Gerçek bir dünya senaryosunda, bir değişken sayıyı temsil etmek için kullanılır. Burada, çok özel bir durumda ortaya çıkarmak için üç farklı değerlerile aynı kodu tekrarlayın.

Kültürleri değiştirdikten sonra aşağıdakileri görürsünüz:

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

Çek kültürü için üç çevirinin farklı olduğunu unutmayın. Fransız ve İngiliz kültürleri, son çevrilen iki dize için aynı yapıyı paylaşırlar.

## <a name="advanced-tasks"></a>Gelişmiş görevler

### <a name="contextualizing-strings"></a>Bağlamsallaştırma dizeleri

Uygulamalar genellikle çeşitli yerlerde çevrilecek dizeleri içerir. Aynı dize, bir uygulama içinde belirli konumlarda farklı bir çeviriye sahip olabilir (Razor görünümleri veya sınıf dosyaları). Bir PO dosyası, temsil edilen dizeyi kategorilere ayırmak için kullanılabilecek bir dosya bağlamı kavramını destekler. Dosya bağlamı kullanılarak, dosya bağlamına (veya dosya bağlamının olmamasına) bağlı olarak bir dize farklı şekilde çevrilebilir.

PO yerelleştirme hizmetleri, tam sınıfın adını veya bir dize çevirirken kullanılan görünümü kullanır. Bu, `msgctxt` girişteki değeri ayarlayarak gerçekleştirilir.

Önceki *fr.po* örneğine küçük bir ek düşünün. *Views/Home/About.cshtml* adresinde bulunan bir Jilet görünümü, ayrılmış `msgctxt` girişin değerini ayarlayarak dosya bağlamı olarak tanımlanabilir:

```text
msgctxt "Views.Home.About"
msgid "Hello world!"
msgstr "Bonjour le monde!"
```

Bu `msgctxt` şekilde ayarlandığında, `/Home/About?culture=fr-FR`metin çevirisi . Çeviri, `/Home/Contact?culture=fr-FR`'' ile gezinirken oluşmaz.

Belirli bir giriş belirli bir dosya bağlamıyla eşleştirildiğinde, Orchard Core'un geri dönüş mekanizması bağlam olmadan uygun bir PO dosyası arar. *Görünümler/Ev/İletişim.cshtml*için tanımlanmış belirli bir dosya bağlamı `/Home/Contact?culture=fr-FR` olmadığını varsayarsak, örneğin bir PO dosyası yükler:

[!code-text[](localization/sample/POLocalization/fr.po)]

### <a name="changing-the-location-of-po-files"></a>PO dosyalarının konumunu değiştirme

PO dosyalarının varsayılan konumu şu `ConfigureServices`şekilde değiştirilebilir:

```csharp
services.AddPortableObjectLocalization(options => options.ResourcesPath = "Localization");
```

Bu örnekte, PO dosyaları *Yerelleştirme* klasöründen yüklenir.

### <a name="implementing-a-custom-logic-for-finding-localization-files"></a>Yerelleştirme dosyalarını bulmak için özel bir mantık uygulama

PO dosyalarını bulmak için daha karmaşık `OrchardCore.Localization.PortableObject.ILocalizationFileLocationProvider` bir mantık gerektiğinde, arabirim uygulanabilir ve bir hizmet olarak kaydedilebilir. Bu, PO dosyaları farklı konumlarda depolanabilirse veya dosyaların klasörler hiyerarşisi içinde bulunması gerektiğinde kullanışlıdır.

### <a name="using-a-different-default-pluralized-language"></a>Farklı varsayılan çoğul dil kullanma

Paket, iki `Plural` çoğul forma özgü bir uzantı yöntemi içerir. Daha çoğul form lar gerektiren diller için bir uzantı yöntemi oluşturun. Bir uzantı yöntemiyle, özgün dizelerin doğrudan kodda &mdash; zaten kullanılabilen varsayılan dil için herhangi bir yerelleştirme dosyası sağlamanız gerekmez.

Bir dizi çeviri `Plural(int count, string[] pluralForms, params object[] arguments)` kabul eden daha genel aşırı yüklemeyi kullanabilirsiniz.
