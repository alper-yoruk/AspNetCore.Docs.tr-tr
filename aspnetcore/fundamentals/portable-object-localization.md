---
title: ASP.NET Core taşınabilir nesne yerelleştirmesini yapılandırma
author: sebastienros
description: Bu makale, taşınabilir nesne dosyalarını tanıtır ve bunları Orchard Core çerçevesiyle ASP.NET Core bir uygulamada kullanmaya yönelik adımları özetler.
ms.author: scaddie
ms.date: 09/26/2017
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
uid: fundamentals/portable-object-localization
ms.openlocfilehash: f9c0ecd32f0e5c05782c2db76eff92bb64845db7
ms.sourcegitcommit: 53e01d6e9b70a18a05618f0011cf115a16633c21
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/05/2021
ms.locfileid: "97878405"
---
# <a name="configure-portable-object-localization-in-aspnet-core"></a><span data-ttu-id="187f5-103">ASP.NET Core taşınabilir nesne yerelleştirmesini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="187f5-103">Configure portable object localization in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="187f5-104">[Sébastien Ros](https://github.com/sebastienros), [Scott Ade](https://twitter.com/Scott_Addie) ve [Hisham bin ateya](https://github.com/hishamco) tarafından</span><span class="sxs-lookup"><span data-stu-id="187f5-104">By [Sébastien Ros](https://github.com/sebastienros), [Scott Addie](https://twitter.com/Scott_Addie) and [Hisham Bin Ateya](https://github.com/hishamco)</span></span>

<span data-ttu-id="187f5-105">Bu makalede, [Orchard Core](https://github.com/OrchardCMS/OrchardCore) Framework ile bir ASP.NET Core uygulamasında taşınabilir nesne (PO) dosyalarını kullanma adımları gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="187f5-105">This article walks through the steps for using Portable Object (PO) files in an ASP.NET Core application with the [Orchard Core](https://github.com/OrchardCMS/OrchardCore) framework.</span></span>

<span data-ttu-id="187f5-106">**Note:** Orchard Core bir Microsoft ürünü değildir.</span><span class="sxs-lookup"><span data-stu-id="187f5-106">**Note:** Orchard Core isn't a Microsoft product.</span></span> <span data-ttu-id="187f5-107">Sonuç olarak, Microsoft bu özellik için destek sağlamaz.</span><span class="sxs-lookup"><span data-stu-id="187f5-107">Consequently, Microsoft provides no support for this feature.</span></span>

<span data-ttu-id="187f5-108">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/3.x/POLocalization) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="187f5-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/3.x/POLocalization) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="what-is-a-po-file"></a><span data-ttu-id="187f5-109">PO dosyası nedir?</span><span class="sxs-lookup"><span data-stu-id="187f5-109">What is a PO file?</span></span>

<span data-ttu-id="187f5-110">PO dosyaları, belirli bir dil için çevrilmiş dizeleri içeren metin dosyaları olarak dağıtılır.</span><span class="sxs-lookup"><span data-stu-id="187f5-110">PO files are distributed as text files containing the translated strings for a given language.</span></span> <span data-ttu-id="187f5-111">*. Resx* dosyaları yerine PO dosyalarını kullanmanın bazı avantajları şunlardır:</span><span class="sxs-lookup"><span data-stu-id="187f5-111">Some advantages of using PO files instead of *.resx* files include:</span></span>
- <span data-ttu-id="187f5-112">PO dosyaları, pluralization 'ı destekler; *. resx* dosyaları pluralization 'yi desteklemez.</span><span class="sxs-lookup"><span data-stu-id="187f5-112">PO files support pluralization; *.resx* files don't support pluralization.</span></span>
- <span data-ttu-id="187f5-113">PO dosyaları *. resx* dosyaları gibi derlenmemektedir.</span><span class="sxs-lookup"><span data-stu-id="187f5-113">PO files aren't compiled like *.resx* files.</span></span> <span data-ttu-id="187f5-114">Bu nedenle, özelleştirilmiş araç ve derleme adımları gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="187f5-114">As such, specialized tooling and build steps aren't required.</span></span>
- <span data-ttu-id="187f5-115">PO dosyaları işbirliğine dayalı çevrimiçi Düzenle araçlarıyla iyi çalışır.</span><span class="sxs-lookup"><span data-stu-id="187f5-115">PO files work well with collaborative online editing tools.</span></span>

### <a name="example"></a><span data-ttu-id="187f5-116">Örnek</span><span class="sxs-lookup"><span data-stu-id="187f5-116">Example</span></span>

<span data-ttu-id="187f5-117">Aşağıda, çoğul biçimi ile birlikte olmak üzere Fransızca 'daki iki dizenin çevirisini içeren örnek bir PO dosyası verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="187f5-117">Here is a sample PO file containing the translation for two strings in French, including one with its plural form:</span></span>

<span data-ttu-id="187f5-118">*fr. Po*</span><span class="sxs-lookup"><span data-stu-id="187f5-118">*fr.po*</span></span>

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

<span data-ttu-id="187f5-119">Bu örnek aşağıdaki sözdizimini kullanır:</span><span class="sxs-lookup"><span data-stu-id="187f5-119">This example uses the following syntax:</span></span>

- <span data-ttu-id="187f5-120">`#:`: Çevrilecek dizenin bağlamını gösteren bir açıklama.</span><span class="sxs-lookup"><span data-stu-id="187f5-120">`#:`: A comment indicating the context of the string to be translated.</span></span> <span data-ttu-id="187f5-121">Aynı dize, kullanıldığı yere bağlı olarak farklı şekilde çevrilebilir.</span><span class="sxs-lookup"><span data-stu-id="187f5-121">The same string might be translated differently depending on where it's being used.</span></span>
- <span data-ttu-id="187f5-122">`msgid`: Çevrilmemiş dize.</span><span class="sxs-lookup"><span data-stu-id="187f5-122">`msgid`: The untranslated string.</span></span>
- <span data-ttu-id="187f5-123">`msgstr`: Çevrilmiş dize.</span><span class="sxs-lookup"><span data-stu-id="187f5-123">`msgstr`: The translated string.</span></span>

<span data-ttu-id="187f5-124">Plurselme desteği söz konusu olduğunda, daha fazla girdi tanımlanabilir.</span><span class="sxs-lookup"><span data-stu-id="187f5-124">In the case of pluralization support, more entries can be defined.</span></span>

- <span data-ttu-id="187f5-125">`msgid_plural`: Çevrilmemiş çoğul dize.</span><span class="sxs-lookup"><span data-stu-id="187f5-125">`msgid_plural`: The untranslated plural string.</span></span>
- <span data-ttu-id="187f5-126">`msgstr[0]`: 0 durumu için çevrilmiş dize.</span><span class="sxs-lookup"><span data-stu-id="187f5-126">`msgstr[0]`: The translated string for the case 0.</span></span>
- <span data-ttu-id="187f5-127">`msgstr[N]`: N. durum için çevrilmiş dize.</span><span class="sxs-lookup"><span data-stu-id="187f5-127">`msgstr[N]`: The translated string for the case N.</span></span>

<span data-ttu-id="187f5-128">PO dosya belirtimi [burada](https://www.gnu.org/savannah-checkouts/gnu/gettext/manual/html_node/PO-Files.html)bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="187f5-128">The PO file specification can be found [here](https://www.gnu.org/savannah-checkouts/gnu/gettext/manual/html_node/PO-Files.html).</span></span>

## <a name="configuring-po-file-support-in-aspnet-core"></a><span data-ttu-id="187f5-129">ASP.NET Core 'de PO dosya desteğini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="187f5-129">Configuring PO file support in ASP.NET Core</span></span>

<span data-ttu-id="187f5-130">Bu örnek, Visual Studio 2017 proje şablonundan oluşturulan ASP.NET Core MVC uygulamasını temel alır.</span><span class="sxs-lookup"><span data-stu-id="187f5-130">This example is based on an ASP.NET Core MVC application generated from a Visual Studio 2017 project template.</span></span>

### <a name="referencing-the-package"></a><span data-ttu-id="187f5-131">Pakete başvurma</span><span class="sxs-lookup"><span data-stu-id="187f5-131">Referencing the package</span></span>

<span data-ttu-id="187f5-132">`OrchardCore.Localization.Core`NuGet paketine başvuru ekleyin.</span><span class="sxs-lookup"><span data-stu-id="187f5-132">Add a reference to the `OrchardCore.Localization.Core` NuGet package.</span></span> <span data-ttu-id="187f5-133">Aşağıdaki paket kaynağında [Myget](https://www.myget.org/) üzerinde kullanılabilir: https://www.myget.org/F/orchardcore-preview/api/v3/index.json</span><span class="sxs-lookup"><span data-stu-id="187f5-133">It's available on [MyGet](https://www.myget.org/) at the following package source: https://www.myget.org/F/orchardcore-preview/api/v3/index.json</span></span>

<span data-ttu-id="187f5-134">*. Csproj* dosyası artık aşağıdakine benzer bir satır içerir (sürüm numarası farklılık gösterebilir):</span><span class="sxs-lookup"><span data-stu-id="187f5-134">The *.csproj* file now contains a line similar to the following (version number may vary):</span></span>

[!code-xml[](localization/sample/3.x/POLocalization/POLocalization.csproj?range=8)]

### <a name="registering-the-service"></a><span data-ttu-id="187f5-135">Hizmet kaydediliyor</span><span class="sxs-lookup"><span data-stu-id="187f5-135">Registering the service</span></span>

<span data-ttu-id="187f5-136">Gerekli Hizmetleri `ConfigureServices` *Startup.cs* yöntemine ekleyin:</span><span class="sxs-lookup"><span data-stu-id="187f5-136">Add the required services to the `ConfigureServices` method of *Startup.cs*:</span></span>

[!code-csharp[](localization/sample/3.x/POLocalization/Startup.cs?name=snippet_ConfigureServices&highlight=4-21)]

<span data-ttu-id="187f5-137">Gerekli ara yazılımı `Configure` *Startup.cs* yöntemine ekleyin:</span><span class="sxs-lookup"><span data-stu-id="187f5-137">Add the required middleware to the `Configure` method of *Startup.cs*:</span></span>

[!code-csharp[](localization/sample/3.x/POLocalization/Startup.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="187f5-138">Aşağıdaki kodu tercih ettiğiniz görünüme ekleyin Razor .</span><span class="sxs-lookup"><span data-stu-id="187f5-138">Add the following code to your Razor view of choice.</span></span> <span data-ttu-id="187f5-139">Bu örnekte *. cshtml hakkında* kullanılır.</span><span class="sxs-lookup"><span data-stu-id="187f5-139">*About.cshtml* is used in this example.</span></span>

[!code-cshtml[](localization/sample/3.x/POLocalization/Views/Home/About.cshtml)]

<span data-ttu-id="187f5-140">`IViewLocalizer`"Hello World!" metnini dönüştürmek için bir örnek eklenmiş ve kullanılır.</span><span class="sxs-lookup"><span data-stu-id="187f5-140">An `IViewLocalizer` instance is injected and used to translate the text "Hello world!".</span></span>

### <a name="creating-a-po-file"></a><span data-ttu-id="187f5-141">PO dosyası oluşturma</span><span class="sxs-lookup"><span data-stu-id="187f5-141">Creating a PO file</span></span>

<span data-ttu-id="187f5-142">Uygulama kök klasörünüzde *\<culture code> . Po* adlı bir dosya oluşturun.</span><span class="sxs-lookup"><span data-stu-id="187f5-142">Create a file named *\<culture code>.po* in your application root folder.</span></span> <span data-ttu-id="187f5-143">Bu örnekte, Fransızca dili kullanıldığından dosya adı *fr. Po* olur:</span><span class="sxs-lookup"><span data-stu-id="187f5-143">In this example, the file name is *fr.po* because the French language is used:</span></span>

[!code-text[](localization/sample/3.x/POLocalization/fr.po)]

<span data-ttu-id="187f5-144">Bu dosya, hem çevrilecek dizeyi hem de Fransızca çevrilmiş dizeyi depolar.</span><span class="sxs-lookup"><span data-stu-id="187f5-144">This file stores both the string to translate and the French-translated string.</span></span> <span data-ttu-id="187f5-145">Çevirileri, gerekirse üst kültürüne döndürülür.</span><span class="sxs-lookup"><span data-stu-id="187f5-145">Translations revert to their parent culture, if necessary.</span></span> <span data-ttu-id="187f5-146">Bu örnekte, istenen kültür veya ise *fr. Po* dosyası kullanılır `fr-FR` `fr-CA` .</span><span class="sxs-lookup"><span data-stu-id="187f5-146">In this example, the *fr.po* file is used if the requested culture is `fr-FR` or `fr-CA`.</span></span>

### <a name="testing-the-application"></a><span data-ttu-id="187f5-147">Uygulamayı test etme</span><span class="sxs-lookup"><span data-stu-id="187f5-147">Testing the application</span></span>

<span data-ttu-id="187f5-148">Uygulamanızı çalıştırın ve URL 'ye gidin `/Home/About` .</span><span class="sxs-lookup"><span data-stu-id="187f5-148">Run your application, and navigate to the URL `/Home/About`.</span></span> <span data-ttu-id="187f5-149">**Merhaba Dünya metni!**</span><span class="sxs-lookup"><span data-stu-id="187f5-149">The text **Hello world!**</span></span> <span data-ttu-id="187f5-150">görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="187f5-150">is displayed.</span></span>

<span data-ttu-id="187f5-151">URL 'ye gidin `/Home/About?culture=fr-FR` .</span><span class="sxs-lookup"><span data-stu-id="187f5-151">Navigate to the URL `/Home/About?culture=fr-FR`.</span></span> <span data-ttu-id="187f5-152">**Bonjour Le Monde metni!**</span><span class="sxs-lookup"><span data-stu-id="187f5-152">The text **Bonjour le monde!**</span></span> <span data-ttu-id="187f5-153">görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="187f5-153">is displayed.</span></span>

## <a name="pluralization"></a><span data-ttu-id="187f5-154">Çoğullaştırma</span><span class="sxs-lookup"><span data-stu-id="187f5-154">Pluralization</span></span>

<span data-ttu-id="187f5-155">Po dosyaları, aynı dizenin kardinalite göre farklı şekilde çevrilmesi gerektiğinde yararlı olan çoğullaştırma formlarını destekler.</span><span class="sxs-lookup"><span data-stu-id="187f5-155">PO files support pluralization forms, which is useful when the same string needs to be translated differently based on a cardinality.</span></span> <span data-ttu-id="187f5-156">Bu görev, her dilin kardinalite göre hangi dizenin kullanılacağını seçmek için özel kurallar tanımladığı konusunda karmaşık hale getirilir.</span><span class="sxs-lookup"><span data-stu-id="187f5-156">This task is made complicated by the fact that each language defines custom rules to select which string to use based on the cardinality.</span></span>

<span data-ttu-id="187f5-157">Orchard yerelleştirme paketi, bu farklı çoğul formları otomatik olarak çağırmak için bir API sağlar.</span><span class="sxs-lookup"><span data-stu-id="187f5-157">The Orchard Localization package provides an API to invoke these different plural forms automatically.</span></span>

### <a name="creating-pluralization-po-files"></a><span data-ttu-id="187f5-158">Çoğullaştırma Po dosyaları oluşturma</span><span class="sxs-lookup"><span data-stu-id="187f5-158">Creating pluralization PO files</span></span>

<span data-ttu-id="187f5-159">Aşağıdaki içeriği daha önce bahsedilen *fr. Po* dosyasına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="187f5-159">Add the following content to the previously mentioned *fr.po* file:</span></span>

```text
msgid "There is one item."
msgid_plural "There are {0} items."
msgstr[0] "Il y a un élément."
msgstr[1] "Il y a {0} éléments."
```

<span data-ttu-id="187f5-160">Bu örnekteki her girdinin ne olduğunu gösteren bir açıklama için bkz. [po dosyası nedir?](#what-is-a-po-file) .</span><span class="sxs-lookup"><span data-stu-id="187f5-160">See [What is a PO file?](#what-is-a-po-file) for an explanation of what each entry in this example represents.</span></span>

### <a name="adding-a-language-using-different-pluralization-forms"></a><span data-ttu-id="187f5-161">Farklı çoğullaştırma formları kullanarak dil ekleme</span><span class="sxs-lookup"><span data-stu-id="187f5-161">Adding a language using different pluralization forms</span></span>

<span data-ttu-id="187f5-162">Önceki örnekte İngilizce ve Fransızca dizeleri kullanılmıştır.</span><span class="sxs-lookup"><span data-stu-id="187f5-162">English and French strings were used in the previous example.</span></span> <span data-ttu-id="187f5-163">İngilizce ve Fransızca yalnızca iki plurtasyon biçimine sahiptir ve aynı form kurallarını paylaşır, bu da bir kardinalitesi ilk plural formuyla eşlenir.</span><span class="sxs-lookup"><span data-stu-id="187f5-163">English and French have only two pluralization forms and share the same form rules, which is that a cardinality of one is mapped to the first plural form.</span></span> <span data-ttu-id="187f5-164">Diğer herhangi bir kardinalite ikinci çoğul formla eşleştirilir.</span><span class="sxs-lookup"><span data-stu-id="187f5-164">Any other cardinality is mapped to the second plural form.</span></span>

<span data-ttu-id="187f5-165">Dillerin hepsi aynı kuralları paylaşmaz.</span><span class="sxs-lookup"><span data-stu-id="187f5-165">Not all languages share the same rules.</span></span> <span data-ttu-id="187f5-166">Bu, üç plural formu bulunan Çekçe dil ile gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="187f5-166">This is illustrated with the Czech language, which has three plural forms.</span></span>

<span data-ttu-id="187f5-167">`cs.po`Dosyayı aşağıdaki gibi oluşturun ve plurun üç farklı çeviriyi nasıl ihtiyacı olduğunu aklınızda yapın:</span><span class="sxs-lookup"><span data-stu-id="187f5-167">Create the `cs.po` file as follows, and note how the pluralization needs three different translations:</span></span>

[!code-text[](localization/sample/3.x/POLocalization/cs.po)]

<span data-ttu-id="187f5-168">Çekçe yerelleştirmeleri kabul etmek için, `"cs"` yönteminde desteklenen kültürlerin listesine ekleyin `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="187f5-168">To accept Czech localizations, add `"cs"` to the list of supported cultures in the `ConfigureServices` method:</span></span>

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

<span data-ttu-id="187f5-169">Farklı kart aralıkları için yerelleştirilmiş, çoğul dizeleri işlemek üzere *Görünümler/Home/about. cshtml* dosyasını düzenleyin:</span><span class="sxs-lookup"><span data-stu-id="187f5-169">Edit the *Views/Home/About.cshtml* file to render localized, plural strings for several cardinalities:</span></span>

```cshtml
<p>@Localizer.Plural(1, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(2, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(5, "There is one item.", "There are {0} items.")</p>
```

<span data-ttu-id="187f5-170">**Note:** Gerçek bir Dünya senaryosunda, sayıyı temsil etmek için bir değişken kullanılır.</span><span class="sxs-lookup"><span data-stu-id="187f5-170">**Note:** In a real world scenario, a variable would be used to represent the count.</span></span> <span data-ttu-id="187f5-171">Burada, çok özel bir durumu ortaya çıkarmak için aynı kodu üç farklı değerle tekrarlarız.</span><span class="sxs-lookup"><span data-stu-id="187f5-171">Here, we repeat the same code with three different values to expose a very specific case.</span></span>

<span data-ttu-id="187f5-172">Kültürleri değiştirme sırasında, aşağıdakileri görürsünüz:</span><span class="sxs-lookup"><span data-stu-id="187f5-172">Upon switching cultures, you see the following:</span></span>

<span data-ttu-id="187f5-173">`/Home/About` için:</span><span class="sxs-lookup"><span data-stu-id="187f5-173">For `/Home/About`:</span></span>

```html
There is one item.
There are 2 items.
There are 5 items.
```

<span data-ttu-id="187f5-174">`/Home/About?culture=fr` için:</span><span class="sxs-lookup"><span data-stu-id="187f5-174">For `/Home/About?culture=fr`:</span></span>

```html
Il y a un élément.
Il y a 2 éléments.
Il y a 5 éléments.
```

<span data-ttu-id="187f5-175">`/Home/About?culture=cs` için:</span><span class="sxs-lookup"><span data-stu-id="187f5-175">For `/Home/About?culture=cs`:</span></span>

```html
Existuje jedna položka.
Existují 2 položky.
Existuje 5 položek.
```

<span data-ttu-id="187f5-176">Çekçe kültür için, üç çevirilerin farklı olduğunu unutmayın.</span><span class="sxs-lookup"><span data-stu-id="187f5-176">Note that for the Czech culture, the three translations are different.</span></span> <span data-ttu-id="187f5-177">Fransızca ve Ingilizce kültürler, son çevrilen iki dize için aynı yapıyı paylaşır.</span><span class="sxs-lookup"><span data-stu-id="187f5-177">The French and English cultures share the same construction for the two last translated strings.</span></span>

## <a name="advanced-tasks"></a><span data-ttu-id="187f5-178">Gelişmiş görevler</span><span class="sxs-lookup"><span data-stu-id="187f5-178">Advanced tasks</span></span>

### <a name="contextualizing-strings"></a><span data-ttu-id="187f5-179">Contextualleme dizeleri</span><span class="sxs-lookup"><span data-stu-id="187f5-179">Contextualizing strings</span></span>

<span data-ttu-id="187f5-180">Uygulamalar genellikle birkaç yerde çevrilecek dizeleri içerir.</span><span class="sxs-lookup"><span data-stu-id="187f5-180">Applications often contain the strings to be translated in several places.</span></span> <span data-ttu-id="187f5-181">Aynı dize bir uygulama içindeki belirli konumlarda farklı bir çeviriye sahip olabilir ( Razor görünümler veya sınıf dosyaları).</span><span class="sxs-lookup"><span data-stu-id="187f5-181">The same string may have a different translation in certain locations within an app (Razor views or class files).</span></span> <span data-ttu-id="187f5-182">Bir PO dosyası, temsil edilen dizeyi kategorilere ayırmak için kullanılabilecek bir dosya bağlamı kavramını destekler.</span><span class="sxs-lookup"><span data-stu-id="187f5-182">A PO file supports the notion of a file context, which can be used to categorize the string being represented.</span></span> <span data-ttu-id="187f5-183">Dosya bağlamını kullanarak, dosya bağlamına (veya bir dosya bağlamının olmamasından) bağlı olarak bir dize farklı şekilde çevrilebilir.</span><span class="sxs-lookup"><span data-stu-id="187f5-183">Using a file context, a string can be translated differently, depending on the file context (or lack of a file context).</span></span>

<span data-ttu-id="187f5-184">PO yerelleştirme hizmetleri, tam sınıfın veya bir dize çevrilirken kullanılan görünümün adını kullanır.</span><span class="sxs-lookup"><span data-stu-id="187f5-184">The PO localization services use the name of the full class or the view that's used when translating a string.</span></span> <span data-ttu-id="187f5-185">Bu, girişteki değer ayarlanarak gerçekleştirilir `msgctxt` .</span><span class="sxs-lookup"><span data-stu-id="187f5-185">This is accomplished by setting the value on the `msgctxt` entry.</span></span>

<span data-ttu-id="187f5-186">Önceki *fr. Po* örneğine küçük bir ek göz önünde bulundurun.</span><span class="sxs-lookup"><span data-stu-id="187f5-186">Consider a minor addition to the previous *fr.po* example.</span></span> <span data-ttu-id="187f5-187">Razor *Görünümler/Home/about. cshtml* 'de bulunan bir görünüm, ayrılmış girdinin değeri ayarlanarak dosya bağlamı olarak tanımlanabilir `msgctxt` :</span><span class="sxs-lookup"><span data-stu-id="187f5-187">A Razor view located at *Views/Home/About.cshtml* can be defined as the file context by setting the reserved `msgctxt` entry's value:</span></span>

```text
msgctxt "Views.Home.About"
msgid "Hello world!"
msgstr "Bonjour le monde!"
```

<span data-ttu-id="187f5-188">`msgctxt`Bu şekilde ayarlandığında, ' ye gidildiğinde metin çevirisi oluşur `/Home/About?culture=fr-FR` .</span><span class="sxs-lookup"><span data-stu-id="187f5-188">With the `msgctxt` set as such, text translation occurs when navigating to `/Home/About?culture=fr-FR`.</span></span> <span data-ttu-id="187f5-189">Çeviri, ' a gidildiğinde gerçekleşmez `/Home/Contact?culture=fr-FR` .</span><span class="sxs-lookup"><span data-stu-id="187f5-189">The translation won't occur when navigating to `/Home/Contact?culture=fr-FR`.</span></span>

<span data-ttu-id="187f5-190">Belirli bir giriş belirli bir dosya bağlamıyla eşleşmediğinde, Orchard Core 'un geri dönüş mekanizması bağlam olmadan uygun bir PO dosyası arar.</span><span class="sxs-lookup"><span data-stu-id="187f5-190">When no specific entry is matched with a given file context, Orchard Core's fallback mechanism looks for an appropriate PO file without a context.</span></span> <span data-ttu-id="187f5-191">*Görünümler/Home/Contact. cshtml* için tanımlanan belirli bir dosya bağlamı olmadığı varsayılarak, gezinmek IÇIN `/Home/Contact?culture=fr-FR` bir PO dosyası yükler:</span><span class="sxs-lookup"><span data-stu-id="187f5-191">Assuming there's no specific file context defined for *Views/Home/Contact.cshtml*, navigating to `/Home/Contact?culture=fr-FR` loads a PO file such as:</span></span>

[!code-text[](localization/sample/3.x/POLocalization/fr.po)]

### <a name="changing-the-location-of-po-files"></a><span data-ttu-id="187f5-192">PO dosyalarının konumunu değiştirme</span><span class="sxs-lookup"><span data-stu-id="187f5-192">Changing the location of PO files</span></span>

<span data-ttu-id="187f5-193">PO dosyalarının varsayılan konumu ' de değiştirilebilir `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="187f5-193">The default location of PO files can be changed in `ConfigureServices`:</span></span>

```csharp
services.AddPortableObjectLocalization(options => options.ResourcesPath = "Localization");
```

<span data-ttu-id="187f5-194">Bu örnekte, PO dosyaları *Yerelleştirme* klasöründen yüklenir.</span><span class="sxs-lookup"><span data-stu-id="187f5-194">In this example, the PO files are loaded from the *Localization* folder.</span></span>

### <a name="implementing-a-custom-logic-for-finding-localization-files"></a><span data-ttu-id="187f5-195">Yerelleştirme dosyalarını bulmak için özel bir mantık uygulama</span><span class="sxs-lookup"><span data-stu-id="187f5-195">Implementing a custom logic for finding localization files</span></span>

<span data-ttu-id="187f5-196">PO dosyalarını bulmak için daha karmaşık mantık gerektiğinde, `OrchardCore.Localization.PortableObject.ILocalizationFileLocationProvider` arabirim bir hizmet olarak uygulanabilir ve kaydedilebilir.</span><span class="sxs-lookup"><span data-stu-id="187f5-196">When more complex logic is needed to locate PO files, the `OrchardCore.Localization.PortableObject.ILocalizationFileLocationProvider` interface can be implemented and registered as a service.</span></span> <span data-ttu-id="187f5-197">Bu, PO dosyaları farklı konumlarda depolanabileceği veya dosyaların bir klasör hiyerarşisi içinde bulunması gerektiğinde kullanışlıdır.</span><span class="sxs-lookup"><span data-stu-id="187f5-197">This is useful when PO files can be stored in varying locations or when the files have to be found within a hierarchy of folders.</span></span>

### <a name="using-a-different-default-pluralized-language"></a><span data-ttu-id="187f5-198">Farklı bir varsayılan plurar dili kullanma</span><span class="sxs-lookup"><span data-stu-id="187f5-198">Using a different default pluralized language</span></span>

<span data-ttu-id="187f5-199">Paket, `Plural` iki plural formlarına özgü bir genişletme yöntemi içerir.</span><span class="sxs-lookup"><span data-stu-id="187f5-199">The package includes a `Plural` extension method that's specific to two plural forms.</span></span> <span data-ttu-id="187f5-200">Daha fazla çoğul biçim gerektiren diller için bir genişletme yöntemi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="187f5-200">For languages requiring more plural forms, create an extension method.</span></span> <span data-ttu-id="187f5-201">Uzantı yöntemiyle, varsayılan dil için herhangi bir yerelleştirme dosyası sağlamanız gerekmez, &mdash; özgün dizeler doğrudan kodda zaten kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="187f5-201">With an extension method, you won't need to provide any localization file for the default language &mdash; the original strings are already available directly in the code.</span></span>

<span data-ttu-id="187f5-202">`Plural(int count, string[] pluralForms, params object[] arguments)`Çevirilerin dize dizisini kabul eden daha genel aşırı yüklemeyi kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="187f5-202">You can use the more generic `Plural(int count, string[] pluralForms, params object[] arguments)` overload which accepts a string array of translations.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="187f5-203">[Sébastien Ros](https://github.com/sebastienros) ve [Scott Ade](https://twitter.com/Scott_Addie) tarafından</span><span class="sxs-lookup"><span data-stu-id="187f5-203">By [Sébastien Ros](https://github.com/sebastienros) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="187f5-204">Bu makalede, [Orchard Core](https://github.com/OrchardCMS/OrchardCore) Framework ile bir ASP.NET Core uygulamasında taşınabilir nesne (PO) dosyalarını kullanma adımları gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="187f5-204">This article walks through the steps for using Portable Object (PO) files in an ASP.NET Core application with the [Orchard Core](https://github.com/OrchardCMS/OrchardCore) framework.</span></span>

<span data-ttu-id="187f5-205">**Note:** Orchard Core bir Microsoft ürünü değildir.</span><span class="sxs-lookup"><span data-stu-id="187f5-205">**Note:** Orchard Core isn't a Microsoft product.</span></span> <span data-ttu-id="187f5-206">Sonuç olarak, Microsoft bu özellik için destek sağlamaz.</span><span class="sxs-lookup"><span data-stu-id="187f5-206">Consequently, Microsoft provides no support for this feature.</span></span>

<span data-ttu-id="187f5-207">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/2.x/POLocalization) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="187f5-207">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/2.x/POLocalization) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="what-is-a-po-file"></a><span data-ttu-id="187f5-208">PO dosyası nedir?</span><span class="sxs-lookup"><span data-stu-id="187f5-208">What is a PO file?</span></span>

<span data-ttu-id="187f5-209">PO dosyaları, belirli bir dil için çevrilmiş dizeleri içeren metin dosyaları olarak dağıtılır.</span><span class="sxs-lookup"><span data-stu-id="187f5-209">PO files are distributed as text files containing the translated strings for a given language.</span></span> <span data-ttu-id="187f5-210">*. Resx* dosyaları yerine PO dosyalarını kullanmanın bazı avantajları şunlardır:</span><span class="sxs-lookup"><span data-stu-id="187f5-210">Some advantages of using PO files instead *.resx* files include:</span></span>
- <span data-ttu-id="187f5-211">PO dosyaları, pluralization 'ı destekler; *. resx* dosyaları pluralization 'yi desteklemez.</span><span class="sxs-lookup"><span data-stu-id="187f5-211">PO files support pluralization; *.resx* files don't support pluralization.</span></span>
- <span data-ttu-id="187f5-212">PO dosyaları *. resx* dosyaları gibi derlenmemektedir.</span><span class="sxs-lookup"><span data-stu-id="187f5-212">PO files aren't compiled like *.resx* files.</span></span> <span data-ttu-id="187f5-213">Bu nedenle, özelleştirilmiş araç ve derleme adımları gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="187f5-213">As such, specialized tooling and build steps aren't required.</span></span>
- <span data-ttu-id="187f5-214">PO dosyaları işbirliğine dayalı çevrimiçi Düzenle araçlarıyla iyi çalışır.</span><span class="sxs-lookup"><span data-stu-id="187f5-214">PO files work well with collaborative online editing tools.</span></span>

### <a name="example"></a><span data-ttu-id="187f5-215">Örnek</span><span class="sxs-lookup"><span data-stu-id="187f5-215">Example</span></span>

<span data-ttu-id="187f5-216">Aşağıda, çoğul biçimi ile birlikte olmak üzere Fransızca 'daki iki dizenin çevirisini içeren örnek bir PO dosyası verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="187f5-216">Here is a sample PO file containing the translation for two strings in French, including one with its plural form:</span></span>

<span data-ttu-id="187f5-217">*fr. Po*</span><span class="sxs-lookup"><span data-stu-id="187f5-217">*fr.po*</span></span>

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

<span data-ttu-id="187f5-218">Bu örnek aşağıdaki sözdizimini kullanır:</span><span class="sxs-lookup"><span data-stu-id="187f5-218">This example uses the following syntax:</span></span>

- <span data-ttu-id="187f5-219">`#:`: Çevrilecek dizenin bağlamını gösteren bir açıklama.</span><span class="sxs-lookup"><span data-stu-id="187f5-219">`#:`: A comment indicating the context of the string to be translated.</span></span> <span data-ttu-id="187f5-220">Aynı dize, kullanıldığı yere bağlı olarak farklı şekilde çevrilebilir.</span><span class="sxs-lookup"><span data-stu-id="187f5-220">The same string might be translated differently depending on where it's being used.</span></span>
- <span data-ttu-id="187f5-221">`msgid`: Çevrilmemiş dize.</span><span class="sxs-lookup"><span data-stu-id="187f5-221">`msgid`: The untranslated string.</span></span>
- <span data-ttu-id="187f5-222">`msgstr`: Çevrilmiş dize.</span><span class="sxs-lookup"><span data-stu-id="187f5-222">`msgstr`: The translated string.</span></span>

<span data-ttu-id="187f5-223">Plurselme desteği söz konusu olduğunda, daha fazla girdi tanımlanabilir.</span><span class="sxs-lookup"><span data-stu-id="187f5-223">In the case of pluralization support, more entries can be defined.</span></span>

- <span data-ttu-id="187f5-224">`msgid_plural`: Çevrilmemiş çoğul dize.</span><span class="sxs-lookup"><span data-stu-id="187f5-224">`msgid_plural`: The untranslated plural string.</span></span>
- <span data-ttu-id="187f5-225">`msgstr[0]`: 0 durumu için çevrilmiş dize.</span><span class="sxs-lookup"><span data-stu-id="187f5-225">`msgstr[0]`: The translated string for the case 0.</span></span>
- <span data-ttu-id="187f5-226">`msgstr[N]`: N. durum için çevrilmiş dize.</span><span class="sxs-lookup"><span data-stu-id="187f5-226">`msgstr[N]`: The translated string for the case N.</span></span>

<span data-ttu-id="187f5-227">PO dosya belirtimi [burada](https://www.gnu.org/savannah-checkouts/gnu/gettext/manual/html_node/PO-Files.html)bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="187f5-227">The PO file specification can be found [here](https://www.gnu.org/savannah-checkouts/gnu/gettext/manual/html_node/PO-Files.html).</span></span>

## <a name="configuring-po-file-support-in-aspnet-core"></a><span data-ttu-id="187f5-228">ASP.NET Core 'de PO dosya desteğini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="187f5-228">Configuring PO file support in ASP.NET Core</span></span>

<span data-ttu-id="187f5-229">Bu örnek, Visual Studio 2017 proje şablonundan oluşturulan ASP.NET Core MVC uygulamasını temel alır.</span><span class="sxs-lookup"><span data-stu-id="187f5-229">This example is based on an ASP.NET Core MVC application generated from a Visual Studio 2017 project template.</span></span>

### <a name="referencing-the-package"></a><span data-ttu-id="187f5-230">Pakete başvurma</span><span class="sxs-lookup"><span data-stu-id="187f5-230">Referencing the package</span></span>

<span data-ttu-id="187f5-231">`OrchardCore.Localization.Core`NuGet paketine başvuru ekleyin.</span><span class="sxs-lookup"><span data-stu-id="187f5-231">Add a reference to the `OrchardCore.Localization.Core` NuGet package.</span></span> <span data-ttu-id="187f5-232">Aşağıdaki paket kaynağında [Myget](https://www.myget.org/) üzerinde kullanılabilir: https://www.myget.org/F/orchardcore-preview/api/v3/index.json</span><span class="sxs-lookup"><span data-stu-id="187f5-232">It's available on [MyGet](https://www.myget.org/) at the following package source: https://www.myget.org/F/orchardcore-preview/api/v3/index.json</span></span>

<span data-ttu-id="187f5-233">*. Csproj* dosyası artık aşağıdakine benzer bir satır içerir (sürüm numarası farklılık gösterebilir):</span><span class="sxs-lookup"><span data-stu-id="187f5-233">The *.csproj* file now contains a line similar to the following (version number may vary):</span></span>

[!code-xml[](localization/sample/2.x/POLocalization/POLocalization.csproj?range=9)]

### <a name="registering-the-service"></a><span data-ttu-id="187f5-234">Hizmet kaydediliyor</span><span class="sxs-lookup"><span data-stu-id="187f5-234">Registering the service</span></span>

<span data-ttu-id="187f5-235">Gerekli Hizmetleri `ConfigureServices` *Startup.cs* yöntemine ekleyin:</span><span class="sxs-lookup"><span data-stu-id="187f5-235">Add the required services to the `ConfigureServices` method of *Startup.cs*:</span></span>

[!code-csharp[](localization/sample/2.x/POLocalization/Startup.cs?name=snippet_ConfigureServices&highlight=4-21)]

<span data-ttu-id="187f5-236">Gerekli ara yazılımı `Configure` *Startup.cs* yöntemine ekleyin:</span><span class="sxs-lookup"><span data-stu-id="187f5-236">Add the required middleware to the `Configure` method of *Startup.cs*:</span></span>

[!code-csharp[](localization/sample/2.x/POLocalization/Startup.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="187f5-237">Aşağıdaki kodu tercih ettiğiniz görünüme ekleyin Razor .</span><span class="sxs-lookup"><span data-stu-id="187f5-237">Add the following code to your Razor view of choice.</span></span> <span data-ttu-id="187f5-238">Bu örnekte *. cshtml hakkında* kullanılır.</span><span class="sxs-lookup"><span data-stu-id="187f5-238">*About.cshtml* is used in this example.</span></span>

[!code-cshtml[](localization/sample/2.x/POLocalization/Views/Home/About.cshtml)]

<span data-ttu-id="187f5-239">`IViewLocalizer`"Hello World!" metnini dönüştürmek için bir örnek eklenmiş ve kullanılır.</span><span class="sxs-lookup"><span data-stu-id="187f5-239">An `IViewLocalizer` instance is injected and used to translate the text "Hello world!".</span></span>

### <a name="creating-a-po-file"></a><span data-ttu-id="187f5-240">PO dosyası oluşturma</span><span class="sxs-lookup"><span data-stu-id="187f5-240">Creating a PO file</span></span>

<span data-ttu-id="187f5-241">Uygulama kök klasörünüzde *\<culture code> . Po* adlı bir dosya oluşturun.</span><span class="sxs-lookup"><span data-stu-id="187f5-241">Create a file named *\<culture code>.po* in your application root folder.</span></span> <span data-ttu-id="187f5-242">Bu örnekte, Fransızca dili kullanıldığından dosya adı *fr. Po* olur:</span><span class="sxs-lookup"><span data-stu-id="187f5-242">In this example, the file name is *fr.po* because the French language is used:</span></span>

[!code-text[](localization/sample/2.x/POLocalization/fr.po)]

<span data-ttu-id="187f5-243">Bu dosya, hem çevrilecek dizeyi hem de Fransızca çevrilmiş dizeyi depolar.</span><span class="sxs-lookup"><span data-stu-id="187f5-243">This file stores both the string to translate and the French-translated string.</span></span> <span data-ttu-id="187f5-244">Çevirileri, gerekirse üst kültürüne döndürülür.</span><span class="sxs-lookup"><span data-stu-id="187f5-244">Translations revert to their parent culture, if necessary.</span></span> <span data-ttu-id="187f5-245">Bu örnekte, istenen kültür veya ise *fr. Po* dosyası kullanılır `fr-FR` `fr-CA` .</span><span class="sxs-lookup"><span data-stu-id="187f5-245">In this example, the *fr.po* file is used if the requested culture is `fr-FR` or `fr-CA`.</span></span>

### <a name="testing-the-application"></a><span data-ttu-id="187f5-246">Uygulamayı test etme</span><span class="sxs-lookup"><span data-stu-id="187f5-246">Testing the application</span></span>

<span data-ttu-id="187f5-247">Uygulamanızı çalıştırın ve URL 'ye gidin `/Home/About` .</span><span class="sxs-lookup"><span data-stu-id="187f5-247">Run your application, and navigate to the URL `/Home/About`.</span></span> <span data-ttu-id="187f5-248">**Merhaba Dünya metni!**</span><span class="sxs-lookup"><span data-stu-id="187f5-248">The text **Hello world!**</span></span> <span data-ttu-id="187f5-249">görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="187f5-249">is displayed.</span></span>

<span data-ttu-id="187f5-250">URL 'ye gidin `/Home/About?culture=fr-FR` .</span><span class="sxs-lookup"><span data-stu-id="187f5-250">Navigate to the URL `/Home/About?culture=fr-FR`.</span></span> <span data-ttu-id="187f5-251">**Bonjour Le Monde metni!**</span><span class="sxs-lookup"><span data-stu-id="187f5-251">The text **Bonjour le monde!**</span></span> <span data-ttu-id="187f5-252">görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="187f5-252">is displayed.</span></span>

## <a name="pluralization"></a><span data-ttu-id="187f5-253">Çoğullaştırma</span><span class="sxs-lookup"><span data-stu-id="187f5-253">Pluralization</span></span>

<span data-ttu-id="187f5-254">Po dosyaları, aynı dizenin kardinalite göre farklı şekilde çevrilmesi gerektiğinde yararlı olan çoğullaştırma formlarını destekler.</span><span class="sxs-lookup"><span data-stu-id="187f5-254">PO files support pluralization forms, which is useful when the same string needs to be translated differently based on a cardinality.</span></span> <span data-ttu-id="187f5-255">Bu görev, her dilin kardinalite göre hangi dizenin kullanılacağını seçmek için özel kurallar tanımladığı konusunda karmaşık hale getirilir.</span><span class="sxs-lookup"><span data-stu-id="187f5-255">This task is made complicated by the fact that each language defines custom rules to select which string to use based on the cardinality.</span></span>

<span data-ttu-id="187f5-256">Orchard yerelleştirme paketi, bu farklı çoğul formları otomatik olarak çağırmak için bir API sağlar.</span><span class="sxs-lookup"><span data-stu-id="187f5-256">The Orchard Localization package provides an API to invoke these different plural forms automatically.</span></span>

### <a name="creating-pluralization-po-files"></a><span data-ttu-id="187f5-257">Çoğullaştırma Po dosyaları oluşturma</span><span class="sxs-lookup"><span data-stu-id="187f5-257">Creating pluralization PO files</span></span>

<span data-ttu-id="187f5-258">Aşağıdaki içeriği daha önce bahsedilen *fr. Po* dosyasına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="187f5-258">Add the following content to the previously mentioned *fr.po* file:</span></span>

```text
msgid "There is one item."
msgid_plural "There are {0} items."
msgstr[0] "Il y a un élément."
msgstr[1] "Il y a {0} éléments."
```

<span data-ttu-id="187f5-259">Bu örnekteki her girdinin ne olduğunu gösteren bir açıklama için bkz. [po dosyası nedir?](#what-is-a-po-file) .</span><span class="sxs-lookup"><span data-stu-id="187f5-259">See [What is a PO file?](#what-is-a-po-file) for an explanation of what each entry in this example represents.</span></span>

### <a name="adding-a-language-using-different-pluralization-forms"></a><span data-ttu-id="187f5-260">Farklı çoğullaştırma formları kullanarak dil ekleme</span><span class="sxs-lookup"><span data-stu-id="187f5-260">Adding a language using different pluralization forms</span></span>

<span data-ttu-id="187f5-261">Önceki örnekte İngilizce ve Fransızca dizeleri kullanılmıştır.</span><span class="sxs-lookup"><span data-stu-id="187f5-261">English and French strings were used in the previous example.</span></span> <span data-ttu-id="187f5-262">İngilizce ve Fransızca yalnızca iki plurtasyon biçimine sahiptir ve aynı form kurallarını paylaşır, bu da bir kardinalitesi ilk plural formuyla eşlenir.</span><span class="sxs-lookup"><span data-stu-id="187f5-262">English and French have only two pluralization forms and share the same form rules, which is that a cardinality of one is mapped to the first plural form.</span></span> <span data-ttu-id="187f5-263">Diğer herhangi bir kardinalite ikinci çoğul formla eşleştirilir.</span><span class="sxs-lookup"><span data-stu-id="187f5-263">Any other cardinality is mapped to the second plural form.</span></span>

<span data-ttu-id="187f5-264">Dillerin hepsi aynı kuralları paylaşmaz.</span><span class="sxs-lookup"><span data-stu-id="187f5-264">Not all languages share the same rules.</span></span> <span data-ttu-id="187f5-265">Bu, üç plural formu bulunan Çekçe dil ile gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="187f5-265">This is illustrated with the Czech language, which has three plural forms.</span></span>

<span data-ttu-id="187f5-266">`cs.po`Dosyayı aşağıdaki gibi oluşturun ve plurun üç farklı çeviriyi nasıl ihtiyacı olduğunu aklınızda yapın:</span><span class="sxs-lookup"><span data-stu-id="187f5-266">Create the `cs.po` file as follows, and note how the pluralization needs three different translations:</span></span>

[!code-text[](localization/sample/2.x/POLocalization/cs.po)]

<span data-ttu-id="187f5-267">Çekçe yerelleştirmeleri kabul etmek için, `"cs"` yönteminde desteklenen kültürlerin listesine ekleyin `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="187f5-267">To accept Czech localizations, add `"cs"` to the list of supported cultures in the `ConfigureServices` method:</span></span>

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

<span data-ttu-id="187f5-268">Farklı kart aralıkları için yerelleştirilmiş, çoğul dizeleri işlemek üzere *Görünümler/Home/about. cshtml* dosyasını düzenleyin:</span><span class="sxs-lookup"><span data-stu-id="187f5-268">Edit the *Views/Home/About.cshtml* file to render localized, plural strings for several cardinalities:</span></span>

```cshtml
<p>@Localizer.Plural(1, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(2, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(5, "There is one item.", "There are {0} items.")</p>
```

<span data-ttu-id="187f5-269">**Note:** Gerçek bir Dünya senaryosunda, sayıyı temsil etmek için bir değişken kullanılır.</span><span class="sxs-lookup"><span data-stu-id="187f5-269">**Note:** In a real world scenario, a variable would be used to represent the count.</span></span> <span data-ttu-id="187f5-270">Burada, çok özel bir durumu ortaya çıkarmak için aynı kodu üç farklı değerle tekrarlarız.</span><span class="sxs-lookup"><span data-stu-id="187f5-270">Here, we repeat the same code with three different values to expose a very specific case.</span></span>

<span data-ttu-id="187f5-271">Kültürleri değiştirme sırasında, aşağıdakileri görürsünüz:</span><span class="sxs-lookup"><span data-stu-id="187f5-271">Upon switching cultures, you see the following:</span></span>

<span data-ttu-id="187f5-272">`/Home/About` için:</span><span class="sxs-lookup"><span data-stu-id="187f5-272">For `/Home/About`:</span></span>

```html
There is one item.
There are 2 items.
There are 5 items.
```

<span data-ttu-id="187f5-273">`/Home/About?culture=fr` için:</span><span class="sxs-lookup"><span data-stu-id="187f5-273">For `/Home/About?culture=fr`:</span></span>

```html
Il y a un élément.
Il y a 2 éléments.
Il y a 5 éléments.
```

<span data-ttu-id="187f5-274">`/Home/About?culture=cs` için:</span><span class="sxs-lookup"><span data-stu-id="187f5-274">For `/Home/About?culture=cs`:</span></span>

```html
Existuje jedna položka.
Existují 2 položky.
Existuje 5 položek.
```

<span data-ttu-id="187f5-275">Çekçe kültür için, üç çevirilerin farklı olduğunu unutmayın.</span><span class="sxs-lookup"><span data-stu-id="187f5-275">Note that for the Czech culture, the three translations are different.</span></span> <span data-ttu-id="187f5-276">Fransızca ve Ingilizce kültürler, son çevrilen iki dize için aynı yapıyı paylaşır.</span><span class="sxs-lookup"><span data-stu-id="187f5-276">The French and English cultures share the same construction for the two last translated strings.</span></span>

## <a name="advanced-tasks"></a><span data-ttu-id="187f5-277">Gelişmiş görevler</span><span class="sxs-lookup"><span data-stu-id="187f5-277">Advanced tasks</span></span>

### <a name="contextualizing-strings"></a><span data-ttu-id="187f5-278">Contextualleme dizeleri</span><span class="sxs-lookup"><span data-stu-id="187f5-278">Contextualizing strings</span></span>

<span data-ttu-id="187f5-279">Uygulamalar genellikle birkaç yerde çevrilecek dizeleri içerir.</span><span class="sxs-lookup"><span data-stu-id="187f5-279">Applications often contain the strings to be translated in several places.</span></span> <span data-ttu-id="187f5-280">Aynı dize bir uygulama içindeki belirli konumlarda farklı bir çeviriye sahip olabilir ( Razor görünümler veya sınıf dosyaları).</span><span class="sxs-lookup"><span data-stu-id="187f5-280">The same string may have a different translation in certain locations within an app (Razor views or class files).</span></span> <span data-ttu-id="187f5-281">Bir PO dosyası, temsil edilen dizeyi kategorilere ayırmak için kullanılabilecek bir dosya bağlamı kavramını destekler.</span><span class="sxs-lookup"><span data-stu-id="187f5-281">A PO file supports the notion of a file context, which can be used to categorize the string being represented.</span></span> <span data-ttu-id="187f5-282">Dosya bağlamını kullanarak, dosya bağlamına (veya bir dosya bağlamının olmamasından) bağlı olarak bir dize farklı şekilde çevrilebilir.</span><span class="sxs-lookup"><span data-stu-id="187f5-282">Using a file context, a string can be translated differently, depending on the file context (or lack of a file context).</span></span>

<span data-ttu-id="187f5-283">PO yerelleştirme hizmetleri, tam sınıfın veya bir dize çevrilirken kullanılan görünümün adını kullanır.</span><span class="sxs-lookup"><span data-stu-id="187f5-283">The PO localization services use the name of the full class or the view that's used when translating a string.</span></span> <span data-ttu-id="187f5-284">Bu, girişteki değer ayarlanarak gerçekleştirilir `msgctxt` .</span><span class="sxs-lookup"><span data-stu-id="187f5-284">This is accomplished by setting the value on the `msgctxt` entry.</span></span>

<span data-ttu-id="187f5-285">Önceki *fr. Po* örneğine küçük bir ek göz önünde bulundurun.</span><span class="sxs-lookup"><span data-stu-id="187f5-285">Consider a minor addition to the previous *fr.po* example.</span></span> <span data-ttu-id="187f5-286">Razor *Görünümler/Home/about. cshtml* 'de bulunan bir görünüm, ayrılmış girdinin değeri ayarlanarak dosya bağlamı olarak tanımlanabilir `msgctxt` :</span><span class="sxs-lookup"><span data-stu-id="187f5-286">A Razor view located at *Views/Home/About.cshtml* can be defined as the file context by setting the reserved `msgctxt` entry's value:</span></span>

```text
msgctxt "Views.Home.About"
msgid "Hello world!"
msgstr "Bonjour le monde!"
```

<span data-ttu-id="187f5-287">`msgctxt`Bu şekilde ayarlandığında, ' ye gidildiğinde metin çevirisi oluşur `/Home/About?culture=fr-FR` .</span><span class="sxs-lookup"><span data-stu-id="187f5-287">With the `msgctxt` set as such, text translation occurs when navigating to `/Home/About?culture=fr-FR`.</span></span> <span data-ttu-id="187f5-288">Çeviri, ' a gidildiğinde gerçekleşmez `/Home/Contact?culture=fr-FR` .</span><span class="sxs-lookup"><span data-stu-id="187f5-288">The translation won't occur when navigating to `/Home/Contact?culture=fr-FR`.</span></span>

<span data-ttu-id="187f5-289">Belirli bir giriş belirli bir dosya bağlamıyla eşleşmediğinde, Orchard Core 'un geri dönüş mekanizması bağlam olmadan uygun bir PO dosyası arar.</span><span class="sxs-lookup"><span data-stu-id="187f5-289">When no specific entry is matched with a given file context, Orchard Core's fallback mechanism looks for an appropriate PO file without a context.</span></span> <span data-ttu-id="187f5-290">*Görünümler/Home/Contact. cshtml* için tanımlanan belirli bir dosya bağlamı olmadığı varsayılarak, gezinmek IÇIN `/Home/Contact?culture=fr-FR` bir PO dosyası yükler:</span><span class="sxs-lookup"><span data-stu-id="187f5-290">Assuming there's no specific file context defined for *Views/Home/Contact.cshtml*, navigating to `/Home/Contact?culture=fr-FR` loads a PO file such as:</span></span>

[!code-text[](localization/sample/2.x/POLocalization/fr.po)]

### <a name="changing-the-location-of-po-files"></a><span data-ttu-id="187f5-291">PO dosyalarının konumunu değiştirme</span><span class="sxs-lookup"><span data-stu-id="187f5-291">Changing the location of PO files</span></span>

<span data-ttu-id="187f5-292">PO dosyalarının varsayılan konumu ' de değiştirilebilir `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="187f5-292">The default location of PO files can be changed in `ConfigureServices`:</span></span>

```csharp
services.AddPortableObjectLocalization(options => options.ResourcesPath = "Localization");
```

<span data-ttu-id="187f5-293">Bu örnekte, PO dosyaları *Yerelleştirme* klasöründen yüklenir.</span><span class="sxs-lookup"><span data-stu-id="187f5-293">In this example, the PO files are loaded from the *Localization* folder.</span></span>

### <a name="implementing-a-custom-logic-for-finding-localization-files"></a><span data-ttu-id="187f5-294">Yerelleştirme dosyalarını bulmak için özel bir mantık uygulama</span><span class="sxs-lookup"><span data-stu-id="187f5-294">Implementing a custom logic for finding localization files</span></span>

<span data-ttu-id="187f5-295">PO dosyalarını bulmak için daha karmaşık mantık gerektiğinde, `OrchardCore.Localization.PortableObject.ILocalizationFileLocationProvider` arabirim bir hizmet olarak uygulanabilir ve kaydedilebilir.</span><span class="sxs-lookup"><span data-stu-id="187f5-295">When more complex logic is needed to locate PO files, the `OrchardCore.Localization.PortableObject.ILocalizationFileLocationProvider` interface can be implemented and registered as a service.</span></span> <span data-ttu-id="187f5-296">Bu, PO dosyaları farklı konumlarda depolanabileceği veya dosyaların bir klasör hiyerarşisi içinde bulunması gerektiğinde kullanışlıdır.</span><span class="sxs-lookup"><span data-stu-id="187f5-296">This is useful when PO files can be stored in varying locations or when the files have to be found within a hierarchy of folders.</span></span>

### <a name="using-a-different-default-pluralized-language"></a><span data-ttu-id="187f5-297">Farklı bir varsayılan plurar dili kullanma</span><span class="sxs-lookup"><span data-stu-id="187f5-297">Using a different default pluralized language</span></span>

<span data-ttu-id="187f5-298">Paket, `Plural` iki plural formlarına özgü bir genişletme yöntemi içerir.</span><span class="sxs-lookup"><span data-stu-id="187f5-298">The package includes a `Plural` extension method that's specific to two plural forms.</span></span> <span data-ttu-id="187f5-299">Daha fazla çoğul biçim gerektiren diller için bir genişletme yöntemi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="187f5-299">For languages requiring more plural forms, create an extension method.</span></span> <span data-ttu-id="187f5-300">Uzantı yöntemiyle, varsayılan dil için herhangi bir yerelleştirme dosyası sağlamanız gerekmez, &mdash; özgün dizeler doğrudan kodda zaten kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="187f5-300">With an extension method, you won't need to provide any localization file for the default language &mdash; the original strings are already available directly in the code.</span></span>

<span data-ttu-id="187f5-301">`Plural(int count, string[] pluralForms, params object[] arguments)`Çevirilerin dize dizisini kabul eden daha genel aşırı yüklemeyi kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="187f5-301">You can use the more generic `Plural(int count, string[] pluralForms, params object[] arguments)` overload which accepts a string array of translations.</span></span>

::: moniker-end
