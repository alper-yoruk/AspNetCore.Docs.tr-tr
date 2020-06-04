---
<span data-ttu-id="a32cb-101">Başlık: taşınabilir nesne yerelleştirmesini yapılandırma ASP.NET Core Yazar: sebastıenros açıklaması: Bu makale, taşınabilir nesne dosyalarını tanıtır ve bunları Orchard Core çerçevesiyle bir ASP.NET Core uygulamasında kullanmaya yönelik adımları özetler.</span><span class="sxs-lookup"><span data-stu-id="a32cb-101">title: Configure portable object localization in ASP.NET Core author: sebastienros description: This article introduces Portable Object files and outlines steps for using them in an ASP.NET Core application with the Orchard Core framework.</span></span>
<span data-ttu-id="a32cb-102">MS. Author: scadzar MS. Date: 09/26/2017 No-loc:</span><span class="sxs-lookup"><span data-stu-id="a32cb-102">ms.author: scaddie ms.date: 09/26/2017 no-loc:</span></span>
- <span data-ttu-id="a32cb-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="a32cb-103">'Blazor'</span></span>
- <span data-ttu-id="a32cb-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="a32cb-104">'Identity'</span></span>
- <span data-ttu-id="a32cb-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="a32cb-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="a32cb-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="a32cb-106">'Razor'</span></span>
- <span data-ttu-id="a32cb-107">' SignalR ' uid: temel bilgiler/taşınabilir-nesne-yerelleştirme</span><span class="sxs-lookup"><span data-stu-id="a32cb-107">'SignalR' uid: fundamentals/portable-object-localization</span></span>

---
# <a name="configure-portable-object-localization-in-aspnet-core"></a><span data-ttu-id="a32cb-108">ASP.NET Core taşınabilir nesne yerelleştirmesini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="a32cb-108">Configure portable object localization in ASP.NET Core</span></span>

<span data-ttu-id="a32cb-109">[Sébastien Ros](https://github.com/sebastienros) ve [Scott Ade](https://twitter.com/Scott_Addie) tarafından</span><span class="sxs-lookup"><span data-stu-id="a32cb-109">By [Sébastien Ros](https://github.com/sebastienros) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="a32cb-110">Bu makalede, [Orchard Core](https://github.com/OrchardCMS/OrchardCore) Framework ile bir ASP.NET Core uygulamasında taşınabilir nesne (PO) dosyalarını kullanma adımları gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="a32cb-110">This article walks through the steps for using Portable Object (PO) files in an ASP.NET Core application with the [Orchard Core](https://github.com/OrchardCMS/OrchardCore) framework.</span></span>

<span data-ttu-id="a32cb-111">**Note:** Orchard Core bir Microsoft ürünü değildir.</span><span class="sxs-lookup"><span data-stu-id="a32cb-111">**Note:** Orchard Core isn't a Microsoft product.</span></span> <span data-ttu-id="a32cb-112">Sonuç olarak, Microsoft bu özellik için destek sağlamaz.</span><span class="sxs-lookup"><span data-stu-id="a32cb-112">Consequently, Microsoft provides no support for this feature.</span></span>

<span data-ttu-id="a32cb-113">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/POLocalization) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a32cb-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/POLocalization) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="what-is-a-po-file"></a><span data-ttu-id="a32cb-114">PO dosyası nedir?</span><span class="sxs-lookup"><span data-stu-id="a32cb-114">What is a PO file?</span></span>

<span data-ttu-id="a32cb-115">PO dosyaları, belirli bir dil için çevrilmiş dizeleri içeren metin dosyaları olarak dağıtılır.</span><span class="sxs-lookup"><span data-stu-id="a32cb-115">PO files are distributed as text files containing the translated strings for a given language.</span></span> <span data-ttu-id="a32cb-116">*. Resx* dosyaları yerine PO dosyalarını kullanmanın bazı avantajları şunlardır:</span><span class="sxs-lookup"><span data-stu-id="a32cb-116">Some advantages of using PO files instead *.resx* files include:</span></span>
- <span data-ttu-id="a32cb-117">PO dosyaları, pluralization 'ı destekler; *. resx* dosyaları pluralization 'yi desteklemez.</span><span class="sxs-lookup"><span data-stu-id="a32cb-117">PO files support pluralization; *.resx* files don't support pluralization.</span></span>
- <span data-ttu-id="a32cb-118">PO dosyaları *. resx* dosyaları gibi derlenmemektedir.</span><span class="sxs-lookup"><span data-stu-id="a32cb-118">PO files aren't compiled like *.resx* files.</span></span> <span data-ttu-id="a32cb-119">Bu nedenle, özelleştirilmiş araç ve derleme adımları gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="a32cb-119">As such, specialized tooling and build steps aren't required.</span></span>
- <span data-ttu-id="a32cb-120">PO dosyaları işbirliğine dayalı çevrimiçi Düzenle araçlarıyla iyi çalışır.</span><span class="sxs-lookup"><span data-stu-id="a32cb-120">PO files work well with collaborative online editing tools.</span></span>

### <a name="example"></a><span data-ttu-id="a32cb-121">Örnek</span><span class="sxs-lookup"><span data-stu-id="a32cb-121">Example</span></span>

<span data-ttu-id="a32cb-122">Aşağıda, çoğul biçimi ile birlikte olmak üzere Fransızca 'daki iki dizenin çevirisini içeren örnek bir PO dosyası verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="a32cb-122">Here is a sample PO file containing the translation for two strings in French, including one with its plural form:</span></span>

<span data-ttu-id="a32cb-123">*fr. Po*</span><span class="sxs-lookup"><span data-stu-id="a32cb-123">*fr.po*</span></span>

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

<span data-ttu-id="a32cb-124">Bu örnek aşağıdaki sözdizimini kullanır:</span><span class="sxs-lookup"><span data-stu-id="a32cb-124">This example uses the following syntax:</span></span>

- <span data-ttu-id="a32cb-125">`#:`: Çevrilecek dizenin bağlamını gösteren bir açıklama.</span><span class="sxs-lookup"><span data-stu-id="a32cb-125">`#:`: A comment indicating the context of the string to be translated.</span></span> <span data-ttu-id="a32cb-126">Aynı dize, kullanıldığı yere bağlı olarak farklı şekilde çevrilebilir.</span><span class="sxs-lookup"><span data-stu-id="a32cb-126">The same string might be translated differently depending on where it's being used.</span></span>
- <span data-ttu-id="a32cb-127">`msgid`: Çevrilmemiş dize.</span><span class="sxs-lookup"><span data-stu-id="a32cb-127">`msgid`: The untranslated string.</span></span>
- <span data-ttu-id="a32cb-128">`msgstr`: Çevrilmiş dize.</span><span class="sxs-lookup"><span data-stu-id="a32cb-128">`msgstr`: The translated string.</span></span>

<span data-ttu-id="a32cb-129">Plurselme desteği söz konusu olduğunda, daha fazla girdi tanımlanabilir.</span><span class="sxs-lookup"><span data-stu-id="a32cb-129">In the case of pluralization support, more entries can be defined.</span></span>

- <span data-ttu-id="a32cb-130">`msgid_plural`: Çevrilmemiş çoğul dize.</span><span class="sxs-lookup"><span data-stu-id="a32cb-130">`msgid_plural`: The untranslated plural string.</span></span>
- <span data-ttu-id="a32cb-131">`msgstr[0]`: 0 durumu için çevrilmiş dize.</span><span class="sxs-lookup"><span data-stu-id="a32cb-131">`msgstr[0]`: The translated string for the case 0.</span></span>
- <span data-ttu-id="a32cb-132">`msgstr[N]`: N. durum için çevrilmiş dize.</span><span class="sxs-lookup"><span data-stu-id="a32cb-132">`msgstr[N]`: The translated string for the case N.</span></span>

<span data-ttu-id="a32cb-133">PO dosya belirtimi [burada](https://www.gnu.org/savannah-checkouts/gnu/gettext/manual/html_node/PO-Files.html)bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="a32cb-133">The PO file specification can be found [here](https://www.gnu.org/savannah-checkouts/gnu/gettext/manual/html_node/PO-Files.html).</span></span>

## <a name="configuring-po-file-support-in-aspnet-core"></a><span data-ttu-id="a32cb-134">ASP.NET Core 'de PO dosya desteğini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="a32cb-134">Configuring PO file support in ASP.NET Core</span></span>

<span data-ttu-id="a32cb-135">Bu örnek, Visual Studio 2017 proje şablonundan oluşturulan ASP.NET Core MVC uygulamasını temel alır.</span><span class="sxs-lookup"><span data-stu-id="a32cb-135">This example is based on an ASP.NET Core MVC application generated from a Visual Studio 2017 project template.</span></span>

### <a name="referencing-the-package"></a><span data-ttu-id="a32cb-136">Pakete başvurma</span><span class="sxs-lookup"><span data-stu-id="a32cb-136">Referencing the package</span></span>

<span data-ttu-id="a32cb-137">`OrchardCore.Localization.Core`NuGet paketine başvuru ekleyin.</span><span class="sxs-lookup"><span data-stu-id="a32cb-137">Add a reference to the `OrchardCore.Localization.Core` NuGet package.</span></span> <span data-ttu-id="a32cb-138">Aşağıdaki paket kaynağında [Myget](https://www.myget.org/) üzerinde kullanılabilir:https://www.myget.org/F/orchardcore-preview/api/v3/index.json</span><span class="sxs-lookup"><span data-stu-id="a32cb-138">It's available on [MyGet](https://www.myget.org/) at the following package source: https://www.myget.org/F/orchardcore-preview/api/v3/index.json</span></span>

<span data-ttu-id="a32cb-139">*. Csproj* dosyası artık aşağıdakine benzer bir satır içerir (sürüm numarası farklılık gösterebilir):</span><span class="sxs-lookup"><span data-stu-id="a32cb-139">The *.csproj* file now contains a line similar to the following (version number may vary):</span></span>

[!code-xml[](localization/sample/2.x/POLocalization/POLocalization.csproj?range=9)]

### <a name="registering-the-service"></a><span data-ttu-id="a32cb-140">Hizmet kaydediliyor</span><span class="sxs-lookup"><span data-stu-id="a32cb-140">Registering the service</span></span>

<span data-ttu-id="a32cb-141">Gerekli Hizmetleri `ConfigureServices` *Startup.cs*yöntemine ekleyin:</span><span class="sxs-lookup"><span data-stu-id="a32cb-141">Add the required services to the `ConfigureServices` method of *Startup.cs*:</span></span>

[!code-csharp[](localization/sample/2.x/POLocalization/Startup.cs?name=snippet_ConfigureServices&highlight=4-21)]

<span data-ttu-id="a32cb-142">Gerekli ara yazılımı `Configure` *Startup.cs*yöntemine ekleyin:</span><span class="sxs-lookup"><span data-stu-id="a32cb-142">Add the required middleware to the `Configure` method of *Startup.cs*:</span></span>

[!code-csharp[](localization/sample/2.x/POLocalization/Startup.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="a32cb-143">Aşağıdaki kodu tercih ettiğiniz görünüme ekleyin Razor .</span><span class="sxs-lookup"><span data-stu-id="a32cb-143">Add the following code to your Razor view of choice.</span></span> <span data-ttu-id="a32cb-144">Bu örnekte *. cshtml hakkında* kullanılır.</span><span class="sxs-lookup"><span data-stu-id="a32cb-144">*About.cshtml* is used in this example.</span></span>

[!code-cshtml[](localization/sample/2.x/POLocalization/Views/Home/About.cshtml)]

<span data-ttu-id="a32cb-145">`IViewLocalizer`"Hello World!" metnini dönüştürmek için bir örnek eklenmiş ve kullanılır.</span><span class="sxs-lookup"><span data-stu-id="a32cb-145">An `IViewLocalizer` instance is injected and used to translate the text "Hello world!".</span></span>

### <a name="creating-a-po-file"></a><span data-ttu-id="a32cb-146">PO dosyası oluşturma</span><span class="sxs-lookup"><span data-stu-id="a32cb-146">Creating a PO file</span></span>

<span data-ttu-id="a32cb-147">Uygulama kök klasörünüzde \* \<culture code> . Po\* adlı bir dosya oluşturun.</span><span class="sxs-lookup"><span data-stu-id="a32cb-147">Create a file named *\<culture code>.po* in your application root folder.</span></span> <span data-ttu-id="a32cb-148">Bu örnekte, Fransızca dili kullanıldığından dosya adı *fr. Po* olur:</span><span class="sxs-lookup"><span data-stu-id="a32cb-148">In this example, the file name is *fr.po* because the French language is used:</span></span>

[!code-text[](localization/sample/2.x/POLocalization/fr.po)]

<span data-ttu-id="a32cb-149">Bu dosya, hem çevrilecek dizeyi hem de Fransızca çevrilmiş dizeyi depolar.</span><span class="sxs-lookup"><span data-stu-id="a32cb-149">This file stores both the string to translate and the French-translated string.</span></span> <span data-ttu-id="a32cb-150">Çevirileri, gerekirse üst kültürüne döndürülür.</span><span class="sxs-lookup"><span data-stu-id="a32cb-150">Translations revert to their parent culture, if necessary.</span></span> <span data-ttu-id="a32cb-151">Bu örnekte, istenen kültür veya ise *fr. Po* dosyası kullanılır `fr-FR` `fr-CA` .</span><span class="sxs-lookup"><span data-stu-id="a32cb-151">In this example, the *fr.po* file is used if the requested culture is `fr-FR` or `fr-CA`.</span></span>

### <a name="testing-the-application"></a><span data-ttu-id="a32cb-152">Uygulamayı test etme</span><span class="sxs-lookup"><span data-stu-id="a32cb-152">Testing the application</span></span>

<span data-ttu-id="a32cb-153">Uygulamanızı çalıştırın ve URL 'ye gidin `/Home/About` .</span><span class="sxs-lookup"><span data-stu-id="a32cb-153">Run your application, and navigate to the URL `/Home/About`.</span></span> <span data-ttu-id="a32cb-154">**Merhaba Dünya metni!**</span><span class="sxs-lookup"><span data-stu-id="a32cb-154">The text **Hello world!**</span></span> <span data-ttu-id="a32cb-155">görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="a32cb-155">is displayed.</span></span>

<span data-ttu-id="a32cb-156">URL 'ye gidin `/Home/About?culture=fr-FR` .</span><span class="sxs-lookup"><span data-stu-id="a32cb-156">Navigate to the URL `/Home/About?culture=fr-FR`.</span></span> <span data-ttu-id="a32cb-157">**Bonjour Le Monde metni!**</span><span class="sxs-lookup"><span data-stu-id="a32cb-157">The text **Bonjour le monde!**</span></span> <span data-ttu-id="a32cb-158">görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="a32cb-158">is displayed.</span></span>

## <a name="pluralization"></a><span data-ttu-id="a32cb-159">Çoğullaştırma</span><span class="sxs-lookup"><span data-stu-id="a32cb-159">Pluralization</span></span>

<span data-ttu-id="a32cb-160">Po dosyaları, aynı dizenin kardinalite göre farklı şekilde çevrilmesi gerektiğinde yararlı olan çoğullaştırma formlarını destekler.</span><span class="sxs-lookup"><span data-stu-id="a32cb-160">PO files support pluralization forms, which is useful when the same string needs to be translated differently based on a cardinality.</span></span> <span data-ttu-id="a32cb-161">Bu görev, her dilin kardinalite göre hangi dizenin kullanılacağını seçmek için özel kurallar tanımladığı konusunda karmaşık hale getirilir.</span><span class="sxs-lookup"><span data-stu-id="a32cb-161">This task is made complicated by the fact that each language defines custom rules to select which string to use based on the cardinality.</span></span>

<span data-ttu-id="a32cb-162">Orchard yerelleştirme paketi, bu farklı çoğul formları otomatik olarak çağırmak için bir API sağlar.</span><span class="sxs-lookup"><span data-stu-id="a32cb-162">The Orchard Localization package provides an API to invoke these different plural forms automatically.</span></span>

### <a name="creating-pluralization-po-files"></a><span data-ttu-id="a32cb-163">Çoğullaştırma Po dosyaları oluşturma</span><span class="sxs-lookup"><span data-stu-id="a32cb-163">Creating pluralization PO files</span></span>

<span data-ttu-id="a32cb-164">Aşağıdaki içeriği daha önce bahsedilen *fr. Po* dosyasına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="a32cb-164">Add the following content to the previously mentioned *fr.po* file:</span></span>

```text
msgid "There is one item."
msgid_plural "There are {0} items."
msgstr[0] "Il y a un élément."
msgstr[1] "Il y a {0} éléments."
```

<span data-ttu-id="a32cb-165">Bu örnekteki her girdinin ne olduğunu gösteren bir açıklama için bkz. [po dosyası nedir?](#what-is-a-po-file) .</span><span class="sxs-lookup"><span data-stu-id="a32cb-165">See [What is a PO file?](#what-is-a-po-file) for an explanation of what each entry in this example represents.</span></span>

### <a name="adding-a-language-using-different-pluralization-forms"></a><span data-ttu-id="a32cb-166">Farklı çoğullaştırma formları kullanarak dil ekleme</span><span class="sxs-lookup"><span data-stu-id="a32cb-166">Adding a language using different pluralization forms</span></span>

<span data-ttu-id="a32cb-167">Önceki örnekte İngilizce ve Fransızca dizeleri kullanılmıştır.</span><span class="sxs-lookup"><span data-stu-id="a32cb-167">English and French strings were used in the previous example.</span></span> <span data-ttu-id="a32cb-168">İngilizce ve Fransızca yalnızca iki plurtasyon biçimine sahiptir ve aynı form kurallarını paylaşır, bu da bir kardinalitesi ilk plural formuyla eşlenir.</span><span class="sxs-lookup"><span data-stu-id="a32cb-168">English and French have only two pluralization forms and share the same form rules, which is that a cardinality of one is mapped to the first plural form.</span></span> <span data-ttu-id="a32cb-169">Diğer herhangi bir kardinalite ikinci çoğul formla eşleştirilir.</span><span class="sxs-lookup"><span data-stu-id="a32cb-169">Any other cardinality is mapped to the second plural form.</span></span>

<span data-ttu-id="a32cb-170">Dillerin hepsi aynı kuralları paylaşmaz.</span><span class="sxs-lookup"><span data-stu-id="a32cb-170">Not all languages share the same rules.</span></span> <span data-ttu-id="a32cb-171">Bu, üç plural formu bulunan Çekçe dil ile gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="a32cb-171">This is illustrated with the Czech language, which has three plural forms.</span></span>

<span data-ttu-id="a32cb-172">`cs.po`Dosyayı aşağıdaki gibi oluşturun ve plurun üç farklı çeviriyi nasıl ihtiyacı olduğunu aklınızda yapın:</span><span class="sxs-lookup"><span data-stu-id="a32cb-172">Create the `cs.po` file as follows, and note how the pluralization needs three different translations:</span></span>

[!code-text[](localization/sample/2.x/POLocalization/cs.po)]

<span data-ttu-id="a32cb-173">Çekçe yerelleştirmeleri kabul etmek için, `"cs"` yönteminde desteklenen kültürlerin listesine ekleyin `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="a32cb-173">To accept Czech localizations, add `"cs"` to the list of supported cultures in the `ConfigureServices` method:</span></span>

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

<span data-ttu-id="a32cb-174">Farklı kart aralıkları için yerelleştirilmiş, çoğul dizeleri işlemek üzere *Görünümler/Home/about. cshtml* dosyasını düzenleyin:</span><span class="sxs-lookup"><span data-stu-id="a32cb-174">Edit the *Views/Home/About.cshtml* file to render localized, plural strings for several cardinalities:</span></span>

```cshtml
<p>@Localizer.Plural(1, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(2, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(5, "There is one item.", "There are {0} items.")</p>
```

<span data-ttu-id="a32cb-175">**Note:** Gerçek bir Dünya senaryosunda, sayıyı temsil etmek için bir değişken kullanılır.</span><span class="sxs-lookup"><span data-stu-id="a32cb-175">**Note:** In a real world scenario, a variable would be used to represent the count.</span></span> <span data-ttu-id="a32cb-176">Burada, çok özel bir durumu ortaya çıkarmak için aynı kodu üç farklı değerle tekrarlarız.</span><span class="sxs-lookup"><span data-stu-id="a32cb-176">Here, we repeat the same code with three different values to expose a very specific case.</span></span>

<span data-ttu-id="a32cb-177">Kültürleri değiştirme sırasında, aşağıdakileri görürsünüz:</span><span class="sxs-lookup"><span data-stu-id="a32cb-177">Upon switching cultures, you see the following:</span></span>

<span data-ttu-id="a32cb-178">`/Home/About` için:</span><span class="sxs-lookup"><span data-stu-id="a32cb-178">For `/Home/About`:</span></span>

```html
There is one item.
There are 2 items.
There are 5 items.
```

<span data-ttu-id="a32cb-179">`/Home/About?culture=fr` için:</span><span class="sxs-lookup"><span data-stu-id="a32cb-179">For `/Home/About?culture=fr`:</span></span>

```html
Il y a un élément.
Il y a 2 éléments.
Il y a 5 éléments.
```

<span data-ttu-id="a32cb-180">`/Home/About?culture=cs` için:</span><span class="sxs-lookup"><span data-stu-id="a32cb-180">For `/Home/About?culture=cs`:</span></span>

```html
Existuje jedna položka.
Existují 2 položky.
Existuje 5 položek.
```

<span data-ttu-id="a32cb-181">Çekçe kültür için, üç çevirilerin farklı olduğunu unutmayın.</span><span class="sxs-lookup"><span data-stu-id="a32cb-181">Note that for the Czech culture, the three translations are different.</span></span> <span data-ttu-id="a32cb-182">Fransızca ve Ingilizce kültürler, son çevrilen iki dize için aynı yapıyı paylaşır.</span><span class="sxs-lookup"><span data-stu-id="a32cb-182">The French and English cultures share the same construction for the two last translated strings.</span></span>

## <a name="advanced-tasks"></a><span data-ttu-id="a32cb-183">Gelişmiş görevler</span><span class="sxs-lookup"><span data-stu-id="a32cb-183">Advanced tasks</span></span>

### <a name="contextualizing-strings"></a><span data-ttu-id="a32cb-184">Contextualleme dizeleri</span><span class="sxs-lookup"><span data-stu-id="a32cb-184">Contextualizing strings</span></span>

<span data-ttu-id="a32cb-185">Uygulamalar genellikle birkaç yerde çevrilecek dizeleri içerir.</span><span class="sxs-lookup"><span data-stu-id="a32cb-185">Applications often contain the strings to be translated in several places.</span></span> <span data-ttu-id="a32cb-186">Aynı dize bir uygulama içindeki belirli konumlarda farklı bir çeviriye sahip olabilir ( Razor görünümler veya sınıf dosyaları).</span><span class="sxs-lookup"><span data-stu-id="a32cb-186">The same string may have a different translation in certain locations within an app (Razor views or class files).</span></span> <span data-ttu-id="a32cb-187">Bir PO dosyası, temsil edilen dizeyi kategorilere ayırmak için kullanılabilecek bir dosya bağlamı kavramını destekler.</span><span class="sxs-lookup"><span data-stu-id="a32cb-187">A PO file supports the notion of a file context, which can be used to categorize the string being represented.</span></span> <span data-ttu-id="a32cb-188">Dosya bağlamını kullanarak, dosya bağlamına (veya bir dosya bağlamının olmamasından) bağlı olarak bir dize farklı şekilde çevrilebilir.</span><span class="sxs-lookup"><span data-stu-id="a32cb-188">Using a file context, a string can be translated differently, depending on the file context (or lack of a file context).</span></span>

<span data-ttu-id="a32cb-189">PO yerelleştirme hizmetleri, tam sınıfın veya bir dize çevrilirken kullanılan görünümün adını kullanır.</span><span class="sxs-lookup"><span data-stu-id="a32cb-189">The PO localization services use the name of the full class or the view that's used when translating a string.</span></span> <span data-ttu-id="a32cb-190">Bu, girişteki değer ayarlanarak gerçekleştirilir `msgctxt` .</span><span class="sxs-lookup"><span data-stu-id="a32cb-190">This is accomplished by setting the value on the `msgctxt` entry.</span></span>

<span data-ttu-id="a32cb-191">Önceki *fr. Po* örneğine küçük bir ek göz önünde bulundurun.</span><span class="sxs-lookup"><span data-stu-id="a32cb-191">Consider a minor addition to the previous *fr.po* example.</span></span> <span data-ttu-id="a32cb-192">Razor *Görünümler/Home/about. cshtml* 'de bulunan bir görünüm, ayrılmış girdinin değeri ayarlanarak dosya bağlamı olarak tanımlanabilir `msgctxt` :</span><span class="sxs-lookup"><span data-stu-id="a32cb-192">A Razor view located at *Views/Home/About.cshtml* can be defined as the file context by setting the reserved `msgctxt` entry's value:</span></span>

```text
msgctxt "Views.Home.About"
msgid "Hello world!"
msgstr "Bonjour le monde!"
```

<span data-ttu-id="a32cb-193">`msgctxt`Bu şekilde ayarlandığında, ' ye gidildiğinde metin çevirisi oluşur `/Home/About?culture=fr-FR` .</span><span class="sxs-lookup"><span data-stu-id="a32cb-193">With the `msgctxt` set as such, text translation occurs when navigating to `/Home/About?culture=fr-FR`.</span></span> <span data-ttu-id="a32cb-194">Çeviri, ' a gidildiğinde gerçekleşmez `/Home/Contact?culture=fr-FR` .</span><span class="sxs-lookup"><span data-stu-id="a32cb-194">The translation won't occur when navigating to `/Home/Contact?culture=fr-FR`.</span></span>

<span data-ttu-id="a32cb-195">Belirli bir giriş belirli bir dosya bağlamıyla eşleşmediğinde, Orchard Core 'un geri dönüş mekanizması bağlam olmadan uygun bir PO dosyası arar.</span><span class="sxs-lookup"><span data-stu-id="a32cb-195">When no specific entry is matched with a given file context, Orchard Core's fallback mechanism looks for an appropriate PO file without a context.</span></span> <span data-ttu-id="a32cb-196">*Görünümler/Home/Contact. cshtml*için tanımlanan belirli bir dosya bağlamı olmadığı varsayılarak, gezinmek IÇIN `/Home/Contact?culture=fr-FR` bir PO dosyası yükler:</span><span class="sxs-lookup"><span data-stu-id="a32cb-196">Assuming there's no specific file context defined for *Views/Home/Contact.cshtml*, navigating to `/Home/Contact?culture=fr-FR` loads a PO file such as:</span></span>

[!code-text[](localization/sample/2.x/POLocalization/fr.po)]

### <a name="changing-the-location-of-po-files"></a><span data-ttu-id="a32cb-197">PO dosyalarının konumunu değiştirme</span><span class="sxs-lookup"><span data-stu-id="a32cb-197">Changing the location of PO files</span></span>

<span data-ttu-id="a32cb-198">PO dosyalarının varsayılan konumu ' de değiştirilebilir `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="a32cb-198">The default location of PO files can be changed in `ConfigureServices`:</span></span>

```csharp
services.AddPortableObjectLocalization(options => options.ResourcesPath = "Localization");
```

<span data-ttu-id="a32cb-199">Bu örnekte, PO dosyaları *Yerelleştirme* klasöründen yüklenir.</span><span class="sxs-lookup"><span data-stu-id="a32cb-199">In this example, the PO files are loaded from the *Localization* folder.</span></span>

### <a name="implementing-a-custom-logic-for-finding-localization-files"></a><span data-ttu-id="a32cb-200">Yerelleştirme dosyalarını bulmak için özel bir mantık uygulama</span><span class="sxs-lookup"><span data-stu-id="a32cb-200">Implementing a custom logic for finding localization files</span></span>

<span data-ttu-id="a32cb-201">PO dosyalarını bulmak için daha karmaşık mantık gerektiğinde, `OrchardCore.Localization.PortableObject.ILocalizationFileLocationProvider` arabirim bir hizmet olarak uygulanabilir ve kaydedilebilir.</span><span class="sxs-lookup"><span data-stu-id="a32cb-201">When more complex logic is needed to locate PO files, the `OrchardCore.Localization.PortableObject.ILocalizationFileLocationProvider` interface can be implemented and registered as a service.</span></span> <span data-ttu-id="a32cb-202">Bu, PO dosyaları farklı konumlarda depolanabileceği veya dosyaların bir klasör hiyerarşisi içinde bulunması gerektiğinde kullanışlıdır.</span><span class="sxs-lookup"><span data-stu-id="a32cb-202">This is useful when PO files can be stored in varying locations or when the files have to be found within a hierarchy of folders.</span></span>

### <a name="using-a-different-default-pluralized-language"></a><span data-ttu-id="a32cb-203">Farklı bir varsayılan plurar dili kullanma</span><span class="sxs-lookup"><span data-stu-id="a32cb-203">Using a different default pluralized language</span></span>

<span data-ttu-id="a32cb-204">Paket, `Plural` iki plural formlarına özgü bir genişletme yöntemi içerir.</span><span class="sxs-lookup"><span data-stu-id="a32cb-204">The package includes a `Plural` extension method that's specific to two plural forms.</span></span> <span data-ttu-id="a32cb-205">Daha fazla çoğul biçim gerektiren diller için bir genişletme yöntemi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="a32cb-205">For languages requiring more plural forms, create an extension method.</span></span> <span data-ttu-id="a32cb-206">Uzantı yöntemiyle, varsayılan dil için herhangi bir yerelleştirme dosyası sağlamanız gerekmez, &mdash; özgün dizeler doğrudan kodda zaten kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="a32cb-206">With an extension method, you won't need to provide any localization file for the default language &mdash; the original strings are already available directly in the code.</span></span>

<span data-ttu-id="a32cb-207">`Plural(int count, string[] pluralForms, params object[] arguments)`Çevirilerin dize dizisini kabul eden daha genel aşırı yüklemeyi kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="a32cb-207">You can use the more generic `Plural(int count, string[] pluralForms, params object[] arguments)` overload which accepts a string array of translations.</span></span>
