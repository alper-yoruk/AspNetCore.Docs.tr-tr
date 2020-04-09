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
# <a name="configure-portable-object-localization-in-aspnet-core"></a><span data-ttu-id="87f96-103">Taşınabilir nesne yerelleştirmeyi ASP.NET Core'da yapılandırma</span><span class="sxs-lookup"><span data-stu-id="87f96-103">Configure portable object localization in ASP.NET Core</span></span>

<span data-ttu-id="87f96-104">Yazar: [Sébastien Ros](https://github.com/sebastienros) ve [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="87f96-104">By [Sébastien Ros](https://github.com/sebastienros) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="87f96-105">Bu makalede, [Orchard Core](https://github.com/OrchardCMS/OrchardCore) çerçevesi ile ASP.NET Core uygulamasında Taşınabilir Nesne (PO) dosyalarını kullanma adımları üzerinden yürür.</span><span class="sxs-lookup"><span data-stu-id="87f96-105">This article walks through the steps for using Portable Object (PO) files in an ASP.NET Core application with the [Orchard Core](https://github.com/OrchardCMS/OrchardCore) framework.</span></span>

<span data-ttu-id="87f96-106">**Not:** Orchard Core bir Microsoft ürünü değildir.</span><span class="sxs-lookup"><span data-stu-id="87f96-106">**Note:** Orchard Core isn't a Microsoft product.</span></span> <span data-ttu-id="87f96-107">Sonuç olarak, Microsoft bu özellik için destek sağlamaz.</span><span class="sxs-lookup"><span data-stu-id="87f96-107">Consequently, Microsoft provides no support for this feature.</span></span>

<span data-ttu-id="87f96-108">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/POLocalization) ( nasıl[indirilir](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="87f96-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/POLocalization) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="what-is-a-po-file"></a><span data-ttu-id="87f96-109">Po dosyası nedir?</span><span class="sxs-lookup"><span data-stu-id="87f96-109">What is a PO file?</span></span>

<span data-ttu-id="87f96-110">PO dosyaları, belirli bir dil için çevrilen dizeleri içeren metin dosyaları olarak dağıtılır.</span><span class="sxs-lookup"><span data-stu-id="87f96-110">PO files are distributed as text files containing the translated strings for a given language.</span></span> <span data-ttu-id="87f96-111">*.resx* dosyaları yerine PO dosyalarını kullanmanın bazı avantajları şunlardır:</span><span class="sxs-lookup"><span data-stu-id="87f96-111">Some advantages of using PO files instead *.resx* files include:</span></span>
- <span data-ttu-id="87f96-112">PO dosyaları çoğullaştırmayı destekler; *.resx* dosyaları çoğullaştırmayı desteklemez.</span><span class="sxs-lookup"><span data-stu-id="87f96-112">PO files support pluralization; *.resx* files don't support pluralization.</span></span>
- <span data-ttu-id="87f96-113">PO dosyaları *.resx* dosyaları gibi derlenmez.</span><span class="sxs-lookup"><span data-stu-id="87f96-113">PO files aren't compiled like *.resx* files.</span></span> <span data-ttu-id="87f96-114">Bu nedenle, özel takım lama ve oluşturma adımları gerekmez.</span><span class="sxs-lookup"><span data-stu-id="87f96-114">As such, specialized tooling and build steps aren't required.</span></span>
- <span data-ttu-id="87f96-115">PO dosyaları işbirlikçi çevrimiçi düzenleme araçlarıyla iyi çalışır.</span><span class="sxs-lookup"><span data-stu-id="87f96-115">PO files work well with collaborative online editing tools.</span></span>

### <a name="example"></a><span data-ttu-id="87f96-116">Örnek</span><span class="sxs-lookup"><span data-stu-id="87f96-116">Example</span></span>

<span data-ttu-id="87f96-117">Burada, biri çoğul formuyla olmak üzere, Fransızca'da iki dize için çeviri içeren örnek bir PO dosyası veremilmektedir:</span><span class="sxs-lookup"><span data-stu-id="87f96-117">Here is a sample PO file containing the translation for two strings in French, including one with its plural form:</span></span>

<span data-ttu-id="87f96-118">*fr.po*</span><span class="sxs-lookup"><span data-stu-id="87f96-118">*fr.po*</span></span>

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

<span data-ttu-id="87f96-119">Bu örnekte aşağıdaki sözdizimi kullanır:</span><span class="sxs-lookup"><span data-stu-id="87f96-119">This example uses the following syntax:</span></span>

- <span data-ttu-id="87f96-120">`#:`: Çevrilecek dize bağlamını gösteren bir yorum.</span><span class="sxs-lookup"><span data-stu-id="87f96-120">`#:`: A comment indicating the context of the string to be translated.</span></span> <span data-ttu-id="87f96-121">Aynı dize, nerede kullanıldığına bağlı olarak farklı şekilde çevrilebilir.</span><span class="sxs-lookup"><span data-stu-id="87f96-121">The same string might be translated differently depending on where it's being used.</span></span>
- <span data-ttu-id="87f96-122">`msgid`: Çevrilmemiş dize.</span><span class="sxs-lookup"><span data-stu-id="87f96-122">`msgid`: The untranslated string.</span></span>
- <span data-ttu-id="87f96-123">`msgstr`: Çevrilmiş dize.</span><span class="sxs-lookup"><span data-stu-id="87f96-123">`msgstr`: The translated string.</span></span>

<span data-ttu-id="87f96-124">Çoğullaştırma desteği söz konusu olduğunda, daha fazla giriş tanımlanabilir.</span><span class="sxs-lookup"><span data-stu-id="87f96-124">In the case of pluralization support, more entries can be defined.</span></span>

- <span data-ttu-id="87f96-125">`msgid_plural`: Çevrilmemiş çoğul dize.</span><span class="sxs-lookup"><span data-stu-id="87f96-125">`msgid_plural`: The untranslated plural string.</span></span>
- <span data-ttu-id="87f96-126">`msgstr[0]`: Servis talebi 0 için çevrilmiş dize.</span><span class="sxs-lookup"><span data-stu-id="87f96-126">`msgstr[0]`: The translated string for the case 0.</span></span>
- <span data-ttu-id="87f96-127">`msgstr[N]`: N davası için çevrilmiş dize.</span><span class="sxs-lookup"><span data-stu-id="87f96-127">`msgstr[N]`: The translated string for the case N.</span></span>

<span data-ttu-id="87f96-128">PO dosya belirtimi [burada](https://www.gnu.org/savannah-checkouts/gnu/gettext/manual/html_node/PO-Files.html)bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="87f96-128">The PO file specification can be found [here](https://www.gnu.org/savannah-checkouts/gnu/gettext/manual/html_node/PO-Files.html).</span></span>

## <a name="configuring-po-file-support-in-aspnet-core"></a><span data-ttu-id="87f96-129">ASP.NET Core'da PO dosya desteğini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="87f96-129">Configuring PO file support in ASP.NET Core</span></span>

<span data-ttu-id="87f96-130">Bu örnek, Visual Studio 2017 proje şablonundan oluşturulan ASP.NET Core MVC uygulamasına dayanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="87f96-130">This example is based on an ASP.NET Core MVC application generated from a Visual Studio 2017 project template.</span></span>

### <a name="referencing-the-package"></a><span data-ttu-id="87f96-131">Pakete başvurma</span><span class="sxs-lookup"><span data-stu-id="87f96-131">Referencing the package</span></span>

<span data-ttu-id="87f96-132">`OrchardCore.Localization.Core` NuGet paketine bir başvuru ekleyin.</span><span class="sxs-lookup"><span data-stu-id="87f96-132">Add a reference to the `OrchardCore.Localization.Core` NuGet package.</span></span> <span data-ttu-id="87f96-133">[MyGet'de](https://www.myget.org/) aşağıdaki paket kaynağından temin edilebilir:https://www.myget.org/F/orchardcore-preview/api/v3/index.json</span><span class="sxs-lookup"><span data-stu-id="87f96-133">It's available on [MyGet](https://www.myget.org/) at the following package source: https://www.myget.org/F/orchardcore-preview/api/v3/index.json</span></span>

<span data-ttu-id="87f96-134">*.csproj* dosyası artık aşağıdakine benzer bir satır içerir (sürüm numarası değişebilir):</span><span class="sxs-lookup"><span data-stu-id="87f96-134">The *.csproj* file now contains a line similar to the following (version number may vary):</span></span>

[!code-xml[](localization/sample/POLocalization/POLocalization.csproj?range=9)]

### <a name="registering-the-service"></a><span data-ttu-id="87f96-135">Hizmetin kaydedilmesi</span><span class="sxs-lookup"><span data-stu-id="87f96-135">Registering the service</span></span>

<span data-ttu-id="87f96-136">`ConfigureServices` *Startup.cs*yöntemine gerekli hizmetleri ekleyin:</span><span class="sxs-lookup"><span data-stu-id="87f96-136">Add the required services to the `ConfigureServices` method of *Startup.cs*:</span></span>

[!code-csharp[](localization/sample/POLocalization/Startup.cs?name=snippet_ConfigureServices&highlight=4-21)]

<span data-ttu-id="87f96-137">`Configure` *Startup.cs*yöntemine gerekli ara ware ekleyin:</span><span class="sxs-lookup"><span data-stu-id="87f96-137">Add the required middleware to the `Configure` method of *Startup.cs*:</span></span>

[!code-csharp[](localization/sample/POLocalization/Startup.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="87f96-138">Seçtiğiniz Razor görünümüne aşağıdaki kodu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="87f96-138">Add the following code to your Razor view of choice.</span></span> <span data-ttu-id="87f96-139">*About.cshtml* bu örnekte kullanılır.</span><span class="sxs-lookup"><span data-stu-id="87f96-139">*About.cshtml* is used in this example.</span></span>

[!code-cshtml[](localization/sample/POLocalization/Views/Home/About.cshtml)]

<span data-ttu-id="87f96-140">Bir `IViewLocalizer` örnek enjekte edilir ve metin "Merhaba dünya!" çevirmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="87f96-140">An `IViewLocalizer` instance is injected and used to translate the text "Hello world!".</span></span>

### <a name="creating-a-po-file"></a><span data-ttu-id="87f96-141">PO dosyası oluşturma</span><span class="sxs-lookup"><span data-stu-id="87f96-141">Creating a PO file</span></span>

<span data-ttu-id="87f96-142">Uygulama kök klasörünüzde \* \<kültür kodu>.po\* adlı bir dosya oluşturun.</span><span class="sxs-lookup"><span data-stu-id="87f96-142">Create a file named *\<culture code>.po* in your application root folder.</span></span> <span data-ttu-id="87f96-143">Bu örnekte, Fransızca dil kullanıldığından dosya adı *fr.po'dur:*</span><span class="sxs-lookup"><span data-stu-id="87f96-143">In this example, the file name is *fr.po* because the French language is used:</span></span>

[!code-text[](localization/sample/POLocalization/fr.po)]

<span data-ttu-id="87f96-144">Bu dosya, hem çevirmek için dize hem de Fransızca çevrilmiş dizeyi depolar.</span><span class="sxs-lookup"><span data-stu-id="87f96-144">This file stores both the string to translate and the French-translated string.</span></span> <span data-ttu-id="87f96-145">Çeviriler gerekirse ana kültürlerine geri döner.</span><span class="sxs-lookup"><span data-stu-id="87f96-145">Translations revert to their parent culture, if necessary.</span></span> <span data-ttu-id="87f96-146">Bu örnekte, *fr.po* istenen kültür `fr-FR` veya `fr-CA`.</span><span class="sxs-lookup"><span data-stu-id="87f96-146">In this example, the *fr.po* file is used if the requested culture is `fr-FR` or `fr-CA`.</span></span>

### <a name="testing-the-application"></a><span data-ttu-id="87f96-147">Uygulamayı test etme</span><span class="sxs-lookup"><span data-stu-id="87f96-147">Testing the application</span></span>

<span data-ttu-id="87f96-148">Uygulamanızı çalıştırın ve URL'ye `/Home/About`gidin.</span><span class="sxs-lookup"><span data-stu-id="87f96-148">Run your application, and navigate to the URL `/Home/About`.</span></span> <span data-ttu-id="87f96-149">Metin **Merhaba dünya!**</span><span class="sxs-lookup"><span data-stu-id="87f96-149">The text **Hello world!**</span></span> <span data-ttu-id="87f96-150">görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="87f96-150">is displayed.</span></span>

<span data-ttu-id="87f96-151">URL'ye `/Home/About?culture=fr-FR`gidin.</span><span class="sxs-lookup"><span data-stu-id="87f96-151">Navigate to the URL `/Home/About?culture=fr-FR`.</span></span> <span data-ttu-id="87f96-152">Metin **Bonjour le monde!**</span><span class="sxs-lookup"><span data-stu-id="87f96-152">The text **Bonjour le monde!**</span></span> <span data-ttu-id="87f96-153">görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="87f96-153">is displayed.</span></span>

## <a name="pluralization"></a><span data-ttu-id="87f96-154">Çoğullaştırma</span><span class="sxs-lookup"><span data-stu-id="87f96-154">Pluralization</span></span>

<span data-ttu-id="87f96-155">PO dosyaları, aynı dize bir kardinallik dayalı farklı çevrilmesi gerektiğinde yararlı olan çoğullaştırma formları, destekler.</span><span class="sxs-lookup"><span data-stu-id="87f96-155">PO files support pluralization forms, which is useful when the same string needs to be translated differently based on a cardinality.</span></span> <span data-ttu-id="87f96-156">Bu görev, her dilin, kardinalliğe bağlı olarak hangi dizeyi kullanacağını seçmek için özel kurallar tanımlaması nedeniyle karmaşık hale getirilir.</span><span class="sxs-lookup"><span data-stu-id="87f96-156">This task is made complicated by the fact that each language defines custom rules to select which string to use based on the cardinality.</span></span>

<span data-ttu-id="87f96-157">Orchard Yerelleştirme paketi, bu farklı çoğul formları otomatik olarak çağırmak için bir API sağlar.</span><span class="sxs-lookup"><span data-stu-id="87f96-157">The Orchard Localization package provides an API to invoke these different plural forms automatically.</span></span>

### <a name="creating-pluralization-po-files"></a><span data-ttu-id="87f96-158">Çoğullaştırma PO dosyaları oluşturma</span><span class="sxs-lookup"><span data-stu-id="87f96-158">Creating pluralization PO files</span></span>

<span data-ttu-id="87f96-159">Daha önce bahsedilen *fr.po* dosyasına aşağıdaki içeriği ekleyin:</span><span class="sxs-lookup"><span data-stu-id="87f96-159">Add the following content to the previously mentioned *fr.po* file:</span></span>

```text
msgid "There is one item."
msgid_plural "There are {0} items."
msgstr[0] "Il y a un élément."
msgstr[1] "Il y a {0} éléments."
```

<span data-ttu-id="87f96-160">Bakınız [Po dosyası nedir?](#what-is-a-po-file)</span><span class="sxs-lookup"><span data-stu-id="87f96-160">See [What is a PO file?](#what-is-a-po-file) for an explanation of what each entry in this example represents.</span></span>

### <a name="adding-a-language-using-different-pluralization-forms"></a><span data-ttu-id="87f96-161">Farklı çoğullaştırma biçimlerini kullanarak dil ekleme</span><span class="sxs-lookup"><span data-stu-id="87f96-161">Adding a language using different pluralization forms</span></span>

<span data-ttu-id="87f96-162">Önceki örnekte İngilizce ve Fransızca dizeleri kullanılmıştır.</span><span class="sxs-lookup"><span data-stu-id="87f96-162">English and French strings were used in the previous example.</span></span> <span data-ttu-id="87f96-163">İngilizce ve Fransızca sadece iki çoğuloluşturma biçimine sahiptir ve aynı biçim kurallarını paylaşır, yani bir kardinallik ilk çoğul forma eşlenir.</span><span class="sxs-lookup"><span data-stu-id="87f96-163">English and French have only two pluralization forms and share the same form rules, which is that a cardinality of one is mapped to the first plural form.</span></span> <span data-ttu-id="87f96-164">Diğer kardinallik ikinci çoğul forma eşlenir.</span><span class="sxs-lookup"><span data-stu-id="87f96-164">Any other cardinality is mapped to the second plural form.</span></span>

<span data-ttu-id="87f96-165">Tüm diller aynı kuralları paylaşmaz.</span><span class="sxs-lookup"><span data-stu-id="87f96-165">Not all languages share the same rules.</span></span> <span data-ttu-id="87f96-166">Bu üç çoğul formları vardır Çek dili ile gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="87f96-166">This is illustrated with the Czech language, which has three plural forms.</span></span>

<span data-ttu-id="87f96-167">Dosyayı `cs.po` aşağıdaki gibi oluşturun ve çoğullaştırmanın üç farklı çeviriye nasıl ihtiyaç duyduğuna dikkat edin:</span><span class="sxs-lookup"><span data-stu-id="87f96-167">Create the `cs.po` file as follows, and note how the pluralization needs three different translations:</span></span>

[!code-text[](localization/sample/POLocalization/cs.po)]

<span data-ttu-id="87f96-168">Çek yerelleştirmelerini kabul `"cs"` etmek için, yöntemdeki desteklenen `ConfigureServices` kültürler listesine ekleyin:</span><span class="sxs-lookup"><span data-stu-id="87f96-168">To accept Czech localizations, add `"cs"` to the list of supported cultures in the `ConfigureServices` method:</span></span>

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

<span data-ttu-id="87f96-169">Çeşitli kardinaller için yerelleştirilmiş, çoğul dizeleri işlemek için *Görünümler/Ana Sayfa/About.cshtml* dosyasını edin:</span><span class="sxs-lookup"><span data-stu-id="87f96-169">Edit the *Views/Home/About.cshtml* file to render localized, plural strings for several cardinalities:</span></span>

```cshtml
<p>@Localizer.Plural(1, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(2, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(5, "There is one item.", "There are {0} items.")</p>
```

<span data-ttu-id="87f96-170">**Not:** Gerçek bir dünya senaryosunda, bir değişken sayıyı temsil etmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="87f96-170">**Note:** In a real world scenario, a variable would be used to represent the count.</span></span> <span data-ttu-id="87f96-171">Burada, çok özel bir durumda ortaya çıkarmak için üç farklı değerlerile aynı kodu tekrarlayın.</span><span class="sxs-lookup"><span data-stu-id="87f96-171">Here, we repeat the same code with three different values to expose a very specific case.</span></span>

<span data-ttu-id="87f96-172">Kültürleri değiştirdikten sonra aşağıdakileri görürsünüz:</span><span class="sxs-lookup"><span data-stu-id="87f96-172">Upon switching cultures, you see the following:</span></span>

<span data-ttu-id="87f96-173">`/Home/About` için:</span><span class="sxs-lookup"><span data-stu-id="87f96-173">For `/Home/About`:</span></span>

```html
There is one item.
There are 2 items.
There are 5 items.
```

<span data-ttu-id="87f96-174">`/Home/About?culture=fr` için:</span><span class="sxs-lookup"><span data-stu-id="87f96-174">For `/Home/About?culture=fr`:</span></span>

```html
Il y a un élément.
Il y a 2 éléments.
Il y a 5 éléments.
```

<span data-ttu-id="87f96-175">`/Home/About?culture=cs` için:</span><span class="sxs-lookup"><span data-stu-id="87f96-175">For `/Home/About?culture=cs`:</span></span>

```html
Existuje jedna položka.
Existují 2 položky.
Existuje 5 položek.
```

<span data-ttu-id="87f96-176">Çek kültürü için üç çevirinin farklı olduğunu unutmayın.</span><span class="sxs-lookup"><span data-stu-id="87f96-176">Note that for the Czech culture, the three translations are different.</span></span> <span data-ttu-id="87f96-177">Fransız ve İngiliz kültürleri, son çevrilen iki dize için aynı yapıyı paylaşırlar.</span><span class="sxs-lookup"><span data-stu-id="87f96-177">The French and English cultures share the same construction for the two last translated strings.</span></span>

## <a name="advanced-tasks"></a><span data-ttu-id="87f96-178">Gelişmiş görevler</span><span class="sxs-lookup"><span data-stu-id="87f96-178">Advanced tasks</span></span>

### <a name="contextualizing-strings"></a><span data-ttu-id="87f96-179">Bağlamsallaştırma dizeleri</span><span class="sxs-lookup"><span data-stu-id="87f96-179">Contextualizing strings</span></span>

<span data-ttu-id="87f96-180">Uygulamalar genellikle çeşitli yerlerde çevrilecek dizeleri içerir.</span><span class="sxs-lookup"><span data-stu-id="87f96-180">Applications often contain the strings to be translated in several places.</span></span> <span data-ttu-id="87f96-181">Aynı dize, bir uygulama içinde belirli konumlarda farklı bir çeviriye sahip olabilir (Razor görünümleri veya sınıf dosyaları).</span><span class="sxs-lookup"><span data-stu-id="87f96-181">The same string may have a different translation in certain locations within an app (Razor views or class files).</span></span> <span data-ttu-id="87f96-182">Bir PO dosyası, temsil edilen dizeyi kategorilere ayırmak için kullanılabilecek bir dosya bağlamı kavramını destekler.</span><span class="sxs-lookup"><span data-stu-id="87f96-182">A PO file supports the notion of a file context, which can be used to categorize the string being represented.</span></span> <span data-ttu-id="87f96-183">Dosya bağlamı kullanılarak, dosya bağlamına (veya dosya bağlamının olmamasına) bağlı olarak bir dize farklı şekilde çevrilebilir.</span><span class="sxs-lookup"><span data-stu-id="87f96-183">Using a file context, a string can be translated differently, depending on the file context (or lack of a file context).</span></span>

<span data-ttu-id="87f96-184">PO yerelleştirme hizmetleri, tam sınıfın adını veya bir dize çevirirken kullanılan görünümü kullanır.</span><span class="sxs-lookup"><span data-stu-id="87f96-184">The PO localization services use the name of the full class or the view that's used when translating a string.</span></span> <span data-ttu-id="87f96-185">Bu, `msgctxt` girişteki değeri ayarlayarak gerçekleştirilir.</span><span class="sxs-lookup"><span data-stu-id="87f96-185">This is accomplished by setting the value on the `msgctxt` entry.</span></span>

<span data-ttu-id="87f96-186">Önceki *fr.po* örneğine küçük bir ek düşünün.</span><span class="sxs-lookup"><span data-stu-id="87f96-186">Consider a minor addition to the previous *fr.po* example.</span></span> <span data-ttu-id="87f96-187">*Views/Home/About.cshtml* adresinde bulunan bir Jilet görünümü, ayrılmış `msgctxt` girişin değerini ayarlayarak dosya bağlamı olarak tanımlanabilir:</span><span class="sxs-lookup"><span data-stu-id="87f96-187">A Razor view located at *Views/Home/About.cshtml* can be defined as the file context by setting the reserved `msgctxt` entry's value:</span></span>

```text
msgctxt "Views.Home.About"
msgid "Hello world!"
msgstr "Bonjour le monde!"
```

<span data-ttu-id="87f96-188">Bu `msgctxt` şekilde ayarlandığında, `/Home/About?culture=fr-FR`metin çevirisi .</span><span class="sxs-lookup"><span data-stu-id="87f96-188">With the `msgctxt` set as such, text translation occurs when navigating to `/Home/About?culture=fr-FR`.</span></span> <span data-ttu-id="87f96-189">Çeviri, `/Home/Contact?culture=fr-FR`'' ile gezinirken oluşmaz.</span><span class="sxs-lookup"><span data-stu-id="87f96-189">The translation won't occur when navigating to `/Home/Contact?culture=fr-FR`.</span></span>

<span data-ttu-id="87f96-190">Belirli bir giriş belirli bir dosya bağlamıyla eşleştirildiğinde, Orchard Core'un geri dönüş mekanizması bağlam olmadan uygun bir PO dosyası arar.</span><span class="sxs-lookup"><span data-stu-id="87f96-190">When no specific entry is matched with a given file context, Orchard Core's fallback mechanism looks for an appropriate PO file without a context.</span></span> <span data-ttu-id="87f96-191">*Görünümler/Ev/İletişim.cshtml*için tanımlanmış belirli bir dosya bağlamı `/Home/Contact?culture=fr-FR` olmadığını varsayarsak, örneğin bir PO dosyası yükler:</span><span class="sxs-lookup"><span data-stu-id="87f96-191">Assuming there's no specific file context defined for *Views/Home/Contact.cshtml*, navigating to `/Home/Contact?culture=fr-FR` loads a PO file such as:</span></span>

[!code-text[](localization/sample/POLocalization/fr.po)]

### <a name="changing-the-location-of-po-files"></a><span data-ttu-id="87f96-192">PO dosyalarının konumunu değiştirme</span><span class="sxs-lookup"><span data-stu-id="87f96-192">Changing the location of PO files</span></span>

<span data-ttu-id="87f96-193">PO dosyalarının varsayılan konumu şu `ConfigureServices`şekilde değiştirilebilir:</span><span class="sxs-lookup"><span data-stu-id="87f96-193">The default location of PO files can be changed in `ConfigureServices`:</span></span>

```csharp
services.AddPortableObjectLocalization(options => options.ResourcesPath = "Localization");
```

<span data-ttu-id="87f96-194">Bu örnekte, PO dosyaları *Yerelleştirme* klasöründen yüklenir.</span><span class="sxs-lookup"><span data-stu-id="87f96-194">In this example, the PO files are loaded from the *Localization* folder.</span></span>

### <a name="implementing-a-custom-logic-for-finding-localization-files"></a><span data-ttu-id="87f96-195">Yerelleştirme dosyalarını bulmak için özel bir mantık uygulama</span><span class="sxs-lookup"><span data-stu-id="87f96-195">Implementing a custom logic for finding localization files</span></span>

<span data-ttu-id="87f96-196">PO dosyalarını bulmak için daha karmaşık `OrchardCore.Localization.PortableObject.ILocalizationFileLocationProvider` bir mantık gerektiğinde, arabirim uygulanabilir ve bir hizmet olarak kaydedilebilir.</span><span class="sxs-lookup"><span data-stu-id="87f96-196">When more complex logic is needed to locate PO files, the `OrchardCore.Localization.PortableObject.ILocalizationFileLocationProvider` interface can be implemented and registered as a service.</span></span> <span data-ttu-id="87f96-197">Bu, PO dosyaları farklı konumlarda depolanabilirse veya dosyaların klasörler hiyerarşisi içinde bulunması gerektiğinde kullanışlıdır.</span><span class="sxs-lookup"><span data-stu-id="87f96-197">This is useful when PO files can be stored in varying locations or when the files have to be found within a hierarchy of folders.</span></span>

### <a name="using-a-different-default-pluralized-language"></a><span data-ttu-id="87f96-198">Farklı varsayılan çoğul dil kullanma</span><span class="sxs-lookup"><span data-stu-id="87f96-198">Using a different default pluralized language</span></span>

<span data-ttu-id="87f96-199">Paket, iki `Plural` çoğul forma özgü bir uzantı yöntemi içerir.</span><span class="sxs-lookup"><span data-stu-id="87f96-199">The package includes a `Plural` extension method that's specific to two plural forms.</span></span> <span data-ttu-id="87f96-200">Daha çoğul form lar gerektiren diller için bir uzantı yöntemi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="87f96-200">For languages requiring more plural forms, create an extension method.</span></span> <span data-ttu-id="87f96-201">Bir uzantı yöntemiyle, özgün dizelerin doğrudan kodda &mdash; zaten kullanılabilen varsayılan dil için herhangi bir yerelleştirme dosyası sağlamanız gerekmez.</span><span class="sxs-lookup"><span data-stu-id="87f96-201">With an extension method, you won't need to provide any localization file for the default language &mdash; the original strings are already available directly in the code.</span></span>

<span data-ttu-id="87f96-202">Bir dizi çeviri `Plural(int count, string[] pluralForms, params object[] arguments)` kabul eden daha genel aşırı yüklemeyi kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="87f96-202">You can use the more generic `Plural(int count, string[] pluralForms, params object[] arguments)` overload which accepts a string array of translations.</span></span>
