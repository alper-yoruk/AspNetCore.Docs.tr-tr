---
title: ASP.NET Core etiket yardımcıları
author: rick-anderson
description: Etiket yardımcılarını ve bunların ASP.NET Core nasıl kullanılacağını öğrenin.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 03/18/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/tag-helpers/intro
ms.openlocfilehash: 7298e514938e8285a1e42afad0f2f71375e2781d
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776480"
---
# <a name="tag-helpers-in-aspnet-core"></a><span data-ttu-id="0a6ae-103">ASP.NET Core etiket yardımcıları</span><span class="sxs-lookup"><span data-stu-id="0a6ae-103">Tag Helpers in ASP.NET Core</span></span>

<span data-ttu-id="0a6ae-104">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="0a6ae-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

## <a name="what-are-tag-helpers"></a><span data-ttu-id="0a6ae-105">Etiket Yardımcıları nelerdir</span><span class="sxs-lookup"><span data-stu-id="0a6ae-105">What are Tag Helpers</span></span>

<span data-ttu-id="0a6ae-106">Etiket Yardımcıları, sunucu tarafı kodun Razor dosyalarda HTML öğeleri oluşturma ve işlemeye katılmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="0a6ae-106">Tag Helpers enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span> <span data-ttu-id="0a6ae-107">Örneğin yerleşik, görüntü adına bir sürüm `ImageTagHelper` numarası ekleyebilir.</span><span class="sxs-lookup"><span data-stu-id="0a6ae-107">For example, the built-in `ImageTagHelper` can append a version number to the image name.</span></span> <span data-ttu-id="0a6ae-108">Görüntü her değiştiğinde, sunucu görüntü için yeni bir benzersiz sürüm oluşturur, bu nedenle istemcilerin geçerli görüntüyü alma garantisi vardır (eski önbelleğe alınmış bir görüntü yerine).</span><span class="sxs-lookup"><span data-stu-id="0a6ae-108">Whenever the image changes, the server generates a new unique version for the image, so clients are guaranteed to get the current image (instead of a stale cached image).</span></span> <span data-ttu-id="0a6ae-109">Yaygın görevler için, genel GitHub depolarında ve NuGet paketleri olarak formlar, bağlantılar, yükleme varlıkları ve daha fazlasını ve daha fazlasını oluşturma gibi birçok yerleşik etiket yardımcıları vardır.</span><span class="sxs-lookup"><span data-stu-id="0a6ae-109">There are many built-in Tag Helpers for common tasks - such as creating forms, links, loading assets and more - and even more available in public GitHub repositories and as NuGet packages.</span></span> <span data-ttu-id="0a6ae-110">Etiket Yardımcıları C# dilinde yazılır ve öğe adı, öznitelik adı veya üst etikete göre HTML öğelerini hedefler.</span><span class="sxs-lookup"><span data-stu-id="0a6ae-110">Tag Helpers are authored in C#, and they target HTML elements based on element name, attribute name, or parent tag.</span></span> <span data-ttu-id="0a6ae-111">Örneğin, `LabelTagHelper` öznitelikler UYGULANDıĞıNDA yerleşik HTML `<label>` öğesini `LabelTagHelper` hedefleyebilir.</span><span class="sxs-lookup"><span data-stu-id="0a6ae-111">For example, the built-in `LabelTagHelper` can target the HTML `<label>` element when the `LabelTagHelper` attributes are applied.</span></span> <span data-ttu-id="0a6ae-112">[HTML Yardımcıları](https://stephenwalther.com/archive/2009/03/03/chapter-6-understanding-html-helpers)hakkında bilginiz varsa etiket yardımcıları, Razor görünümlerde HTML ve C# arasındaki açık geçişleri azaltır.</span><span class="sxs-lookup"><span data-stu-id="0a6ae-112">If you're familiar with [HTML Helpers](https://stephenwalther.com/archive/2009/03/03/chapter-6-understanding-html-helpers), Tag Helpers reduce the explicit transitions between HTML and C# in Razor views.</span></span> <span data-ttu-id="0a6ae-113">Birçok durumda, HTML Yardımcıları belirli bir etiket Yardımcısı için alternatif bir yaklaşım sağlar, ancak bu etiket yardımcıların HTML yardımcılarını değiştirmez ve her HTML Yardımcısı için bir etiket Yardımcısı olmadığını bilmek önemlidir.</span><span class="sxs-lookup"><span data-stu-id="0a6ae-113">In many cases, HTML Helpers provide an alternative approach to a specific Tag Helper, but it's important to recognize that Tag Helpers don't replace HTML Helpers and there's not a Tag Helper for each HTML Helper.</span></span> <span data-ttu-id="0a6ae-114">[HTML yardımcılarına kıyasla etiket yardımcıları](#tag-helpers-compared-to-html-helpers) daha ayrıntılı olarak açıklanır.</span><span class="sxs-lookup"><span data-stu-id="0a6ae-114">[Tag Helpers compared to HTML Helpers](#tag-helpers-compared-to-html-helpers) explains the differences in more detail.</span></span>

## <a name="what-tag-helpers-provide"></a><span data-ttu-id="0a6ae-115">Hangi etiket yardımcıları sağlar</span><span class="sxs-lookup"><span data-stu-id="0a6ae-115">What Tag Helpers provide</span></span>

<span data-ttu-id="0a6ae-116">**HTML kullanımı kolay bir geliştirme deneyimi** Çoğu bölümde, Razor etiket yardımcıları kullanan BIÇIMLENDIRME standart HTML gibi görünür.</span><span class="sxs-lookup"><span data-stu-id="0a6ae-116">**An HTML-friendly development experience** For the most part, Razor markup using Tag Helpers looks like standard HTML.</span></span> <span data-ttu-id="0a6ae-117">Ön uç tasarımcıları HTML/CSS/JavaScript ile, C# Razor Razor sözdizimi öğrenmeksizin düzenleyebilir.</span><span class="sxs-lookup"><span data-stu-id="0a6ae-117">Front-end designers conversant with HTML/CSS/JavaScript can edit Razor without learning C# Razor syntax.</span></span>

<span data-ttu-id="0a6ae-118">**HTML Razor ve biçimlendirme oluşturmaya yönelik zengin bir IntelliSense ORTAMı** , HTML yardımcılarından daha net bir şekilde, bu da görünümlerde biçimlendirme sırasında Razor sunucu tarafında biçimlendirme oluşturma yaklaşımına sahiptir.</span><span class="sxs-lookup"><span data-stu-id="0a6ae-118">**A rich IntelliSense environment for creating HTML and Razor markup** This is in sharp contrast to HTML Helpers, the previous approach to server-side creation of markup in Razor views.</span></span> <span data-ttu-id="0a6ae-119">[HTML yardımcılarına kıyasla etiket yardımcıları](#tag-helpers-compared-to-html-helpers) daha ayrıntılı olarak açıklanır.</span><span class="sxs-lookup"><span data-stu-id="0a6ae-119">[Tag Helpers compared to HTML Helpers](#tag-helpers-compared-to-html-helpers) explains the differences in more detail.</span></span> <span data-ttu-id="0a6ae-120">[Etiket Yardımcıları Için IntelliSense desteği](#intellisense-support-for-tag-helpers) , IntelliSense ortamını açıklar.</span><span class="sxs-lookup"><span data-stu-id="0a6ae-120">[IntelliSense support for Tag Helpers](#intellisense-support-for-tag-helpers) explains the IntelliSense environment.</span></span> <span data-ttu-id="0a6ae-121">Razor C# sözdizimiyle karşılaşılan geliştiricilerin bile, c# Razor işaretlemesi yazmadan etiket yardımcıları kullanılarak daha üretken olma.</span><span class="sxs-lookup"><span data-stu-id="0a6ae-121">Even developers experienced with Razor C# syntax are more productive using Tag Helpers than writing C# Razor markup.</span></span>

<span data-ttu-id="0a6ae-122">**Daha üretken olmanızı sağlamak ve yalnızca sunucuda bulunan bilgileri kullanarak daha sağlam, güvenilir ve sürdürülebilir kodlar elde etmek Için bir yol** Örneğin, geçmişe dönük olarak görüntülerin güncelleştirilmesi, görüntüyü değiştirdiğinizde görüntünün adını değiştiriydi.</span><span class="sxs-lookup"><span data-stu-id="0a6ae-122">**A way to make you more productive and able to produce more robust, reliable, and maintainable code using information only available on the server** For example, historically the mantra on updating images was to change the name of the image when you change the image.</span></span> <span data-ttu-id="0a6ae-123">Görüntülerin performans nedenleriyle bir şekilde ön belleğe alınması gerekir ve bir görüntünün adını değiştirmediğiniz takdirde, istemcilerin eski bir kopya alıyor olması risklidir.</span><span class="sxs-lookup"><span data-stu-id="0a6ae-123">Images should be aggressively cached for performance reasons, and unless you change the name of an image, you risk clients getting a stale copy.</span></span> <span data-ttu-id="0a6ae-124">Tarihsel olarak, bir görüntü düzenlendikten sonra ad değiştirilmelidir ve Web uygulamasındaki görüntünün güncellenmesi için gereken her başvuru.</span><span class="sxs-lookup"><span data-stu-id="0a6ae-124">Historically, after an image was edited, the name had to be changed and each reference to the image in the web app needed to be updated.</span></span> <span data-ttu-id="0a6ae-125">Bu çok işçiliği yoğun bir şekilde değil, bu da hataya açıktır (bir başvuruyu kaçırmanızı, yanlışlıkla yanlış dize girmeniz vb.) Yerleşik `ImageTagHelper` , sizin için otomatik olarak bunu yapabilir.</span><span class="sxs-lookup"><span data-stu-id="0a6ae-125">Not only is this very labor intensive, it's also error prone (you could miss a reference, accidentally enter the wrong string, etc.) The built-in `ImageTagHelper` can do this for you automatically.</span></span> <span data-ttu-id="0a6ae-126">Görüntü `ImageTagHelper` adına bir sürüm numarası ekleyebilir, böylece görüntü her değiştiğinde sunucu otomatik olarak görüntü için yeni bir benzersiz sürüm oluşturur.</span><span class="sxs-lookup"><span data-stu-id="0a6ae-126">The `ImageTagHelper` can append a version number to the image name, so whenever the image changes, the server automatically generates a new unique version for the image.</span></span> <span data-ttu-id="0a6ae-127">İstemcilerin geçerli görüntüyü alması garanti edilir.</span><span class="sxs-lookup"><span data-stu-id="0a6ae-127">Clients are guaranteed to get the current image.</span></span> <span data-ttu-id="0a6ae-128">Bu sağlamlık ve işgücü tasarrufu, `ImageTagHelper`ile birlikte ücretsizdir.</span><span class="sxs-lookup"><span data-stu-id="0a6ae-128">This robustness and labor savings comes essentially free by using the `ImageTagHelper`.</span></span>

<span data-ttu-id="0a6ae-129">Çoğu yerleşik etiket yardımcıları standart HTML öğelerini hedefleyin ve öğesi için sunucu tarafı öznitelikleri sağlar.</span><span class="sxs-lookup"><span data-stu-id="0a6ae-129">Most built-in Tag Helpers target standard HTML elements and provide server-side attributes for the element.</span></span> <span data-ttu-id="0a6ae-130">Örneğin, *Görünümler/hesap* klasöründeki birçok görünümde kullanılan `asp-for` `<input>` öğesi özniteliği içerir.</span><span class="sxs-lookup"><span data-stu-id="0a6ae-130">For example, the `<input>` element used in many views in the *Views/Account* folder contains the `asp-for` attribute.</span></span> <span data-ttu-id="0a6ae-131">Bu öznitelik, belirtilen model özelliğinin adını işlenmiş HTML içine ayıklar.</span><span class="sxs-lookup"><span data-stu-id="0a6ae-131">This attribute extracts the name of the specified model property into the rendered HTML.</span></span> <span data-ttu-id="0a6ae-132">Aşağıdaki modelle Razor bir görünüm düşünün:</span><span class="sxs-lookup"><span data-stu-id="0a6ae-132">Consider a Razor view with the following model:</span></span>

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

<span data-ttu-id="0a6ae-133">Aşağıdaki Razor biçimlendirme:</span><span class="sxs-lookup"><span data-stu-id="0a6ae-133">The following Razor markup:</span></span>

```cshtml
<label asp-for="Movie.Title"></label>
```

<span data-ttu-id="0a6ae-134">Aşağıdaki HTML 'yi oluşturur:</span><span class="sxs-lookup"><span data-stu-id="0a6ae-134">Generates the following HTML:</span></span>

```html
<label for="Movie_Title">Title</label>
```

<span data-ttu-id="0a6ae-135">`asp-for` Özniteliği `For` [labeltaghelper](/dotnet/api/microsoft.aspnetcore.mvc.taghelpers.labeltaghelper?view=aspnetcore-2.0)özelliği tarafından kullanılabilir hale getirilir.</span><span class="sxs-lookup"><span data-stu-id="0a6ae-135">The `asp-for` attribute is made available by the `For` property in the [LabelTagHelper](/dotnet/api/microsoft.aspnetcore.mvc.taghelpers.labeltaghelper?view=aspnetcore-2.0).</span></span> <span data-ttu-id="0a6ae-136">Daha fazla bilgi için bkz. [Yazar etiketi yardımcıları](xref:mvc/views/tag-helpers/authoring) .</span><span class="sxs-lookup"><span data-stu-id="0a6ae-136">See [Author Tag Helpers](xref:mvc/views/tag-helpers/authoring) for more information.</span></span>

## <a name="managing-tag-helper-scope"></a><span data-ttu-id="0a6ae-137">Etiket Yardımcısı kapsamını yönetme</span><span class="sxs-lookup"><span data-stu-id="0a6ae-137">Managing Tag Helper scope</span></span>

<span data-ttu-id="0a6ae-138">Etiket Yardımcıları kapsamı `@addTagHelper`, `@removeTagHelper`, ve "!" geri çevirme karakterinin bir birleşimi tarafından denetlenir.</span><span class="sxs-lookup"><span data-stu-id="0a6ae-138">Tag Helpers scope is controlled by a combination of `@addTagHelper`, `@removeTagHelper`, and the "!" opt-out character.</span></span>

<a name="add-helper-label"></a>

### <a name="addtaghelper-makes-tag-helpers-available"></a><span data-ttu-id="0a6ae-139">`@addTagHelper`Etiket Yardımcıları kullanılabilir hale getirir</span><span class="sxs-lookup"><span data-stu-id="0a6ae-139">`@addTagHelper` makes Tag Helpers available</span></span>

<span data-ttu-id="0a6ae-140">*Authoringtaghelmakaadı*adlı yeni bir ASP.NET Core Web uygulaması oluşturursanız, projenize aşağıdaki *Görünümler/_ViewImports. cshtml* dosyası eklenecektir:</span><span class="sxs-lookup"><span data-stu-id="0a6ae-140">If you create a new ASP.NET Core web app named *AuthoringTagHelpers*, the following *Views/_ViewImports.cshtml* file will be added to your project:</span></span>

[!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImportsCopy.cshtml?highlight=2&range=2-3)]

<span data-ttu-id="0a6ae-141">`@addTagHelper` Yönerge, etiket yardımcılarını görünüm için kullanılabilir hale getirir.</span><span class="sxs-lookup"><span data-stu-id="0a6ae-141">The `@addTagHelper` directive makes Tag Helpers available to the view.</span></span> <span data-ttu-id="0a6ae-142">Bu durumda, görünüm dosyası sayfalar */_ViewImports. cshtml*'dir ve varsayılan olarak *Sayfalar* klasörü ve alt klasörlerdeki tüm dosyalar tarafından devralınır. Etiket Yardımcıları kullanılabilir hale getirme.</span><span class="sxs-lookup"><span data-stu-id="0a6ae-142">In this case, the view file is *Pages/_ViewImports.cshtml*, which by default is inherited by all files in the *Pages* folder and subfolders; making Tag Helpers available.</span></span> <span data-ttu-id="0a6ae-143">Yukarıdaki kod, belirtilen derlemedeki tüm etiket yardımcılarını\*(*Microsoft. Aspnetcore. Mvc. Taghelmakat*), *Görünümler* dizinindeki veya alt dizininde bulunan her görünüm dosyası için kullanılabilir olacağını belirtmek için joker karakter sözdizimini ("") kullanır.</span><span class="sxs-lookup"><span data-stu-id="0a6ae-143">The code above uses the wildcard syntax ("\*") to specify that all Tag Helpers in the specified assembly (*Microsoft.AspNetCore.Mvc.TagHelpers*) will be available to every view file in the *Views* directory or subdirectory.</span></span> <span data-ttu-id="0a6ae-144">Sonraki `@addTagHelper` ilk parametre, yüklenecek etiket yardımcıları ("\*" tüm etiket yardımcıları için kullanıyoruz) ve Ikinci parametre olan "Microsoft. aspnetcore. Mvc. Taghel,", etiket yardımcıları içeren derlemeyi belirtir.</span><span class="sxs-lookup"><span data-stu-id="0a6ae-144">The first parameter after `@addTagHelper` specifies the Tag Helpers to load (we are using "\*" for all Tag Helpers), and the second parameter "Microsoft.AspNetCore.Mvc.TagHelpers" specifies the assembly containing the Tag Helpers.</span></span> <span data-ttu-id="0a6ae-145">*Microsoft. AspNetCore. Mvc. Taghelmakatem* , yerleşik ASP.NET Core etiket yardımcıları için derlemedir.</span><span class="sxs-lookup"><span data-stu-id="0a6ae-145">*Microsoft.AspNetCore.Mvc.TagHelpers* is the assembly for the built-in ASP.NET Core Tag Helpers.</span></span>

<span data-ttu-id="0a6ae-146">Bu projedeki tüm etiket yardımcılarını ortaya çıkarmak için ( *Authoringtaghelmakatı*adlı bir derleme oluşturur), aşağıdakileri kullanın:</span><span class="sxs-lookup"><span data-stu-id="0a6ae-146">To expose all of the Tag Helpers in this project (which creates an assembly named *AuthoringTagHelpers*), you would use the following:</span></span>

[!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImportsCopy.cshtml?highlight=3)]

<span data-ttu-id="0a6ae-147">Projeniz varsayılan ad alanı ( `EmailTagHelper` `AuthoringTagHelpers.TagHelpers.EmailTagHelper`) Ile bir içeriyorsa, etiket yardımcısından tam nitelikli adı (FQN) sağlayabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="0a6ae-147">If your project contains an `EmailTagHelper` with the default namespace (`AuthoringTagHelpers.TagHelpers.EmailTagHelper`), you can provide the fully qualified name (FQN) of the Tag Helper:</span></span>

```cshtml
@using AuthoringTagHelpers
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@addTagHelper AuthoringTagHelpers.TagHelpers.EmailTagHelper, AuthoringTagHelpers
```

<span data-ttu-id="0a6ae-148">Bir FQN kullanarak bir görünüme bir etiket Yardımcısı eklemek için, önce FQN (`AuthoringTagHelpers.TagHelpers.EmailTagHelper`) ve ardından derleme adı (*Authoringtaghelmakaı*) eklersiniz.</span><span class="sxs-lookup"><span data-stu-id="0a6ae-148">To add a Tag Helper to a view using an FQN, you first add the FQN (`AuthoringTagHelpers.TagHelpers.EmailTagHelper`), and then the assembly name (*AuthoringTagHelpers*).</span></span> <span data-ttu-id="0a6ae-149">Çoğu geliştirici "\*" joker sözdizimini kullanmayı tercih eder.</span><span class="sxs-lookup"><span data-stu-id="0a6ae-149">Most developers prefer to use the  "\*" wildcard syntax.</span></span> <span data-ttu-id="0a6ae-150">Joker karakter sözdizimi "\*" joker KARAKTERINI bir FQN soneki olarak eklemenize olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="0a6ae-150">The wildcard syntax allows you to insert the wildcard character "\*" as the suffix in an FQN.</span></span> <span data-ttu-id="0a6ae-151">Örneğin, aşağıdaki yönergelerden herhangi biri içine alınacaktır `EmailTagHelper`:</span><span class="sxs-lookup"><span data-stu-id="0a6ae-151">For example, any of the following directives will bring in the `EmailTagHelper`:</span></span>

```cshtml
@addTagHelper AuthoringTagHelpers.TagHelpers.E*, AuthoringTagHelpers
@addTagHelper AuthoringTagHelpers.TagHelpers.Email*, AuthoringTagHelpers
```

<span data-ttu-id="0a6ae-152">Daha önce bahsedildiği gibi, `@addTagHelper` *views/_ViewImports. cshtml* dosyasına yönergesini eklemek etiket Yardımcısı ' nı *Görünümler* dizinindeki ve alt dizinlerindeki tüm görünüm dosyaları için kullanılabilir hale getirir.</span><span class="sxs-lookup"><span data-stu-id="0a6ae-152">As mentioned previously, adding the `@addTagHelper` directive to the *Views/_ViewImports.cshtml* file makes the Tag Helper available to all view files in the *Views* directory and subdirectories.</span></span> <span data-ttu-id="0a6ae-153">Etiketi yardımcısını yalnızca bu `@addTagHelper` görünümlerde kullanıma sunmak istiyorsanız, belirli görünüm dosyalarında yönergesini kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="0a6ae-153">You can use the `@addTagHelper` directive in specific view files if you want to opt-in to exposing the Tag Helper to only those views.</span></span>

<a name="remove-razor-directives-label"></a>

### <a name="removetaghelper-removes-tag-helpers"></a><span data-ttu-id="0a6ae-154">`@removeTagHelper`Etiket Yardımcıları kaldırır</span><span class="sxs-lookup"><span data-stu-id="0a6ae-154">`@removeTagHelper` removes Tag Helpers</span></span>

<span data-ttu-id="0a6ae-155">, `@removeTagHelper` İle aynı iki parametreye `@addTagHelper`sahiptir ve daha önce eklenmiş bir etiket yardımcısını kaldırır.</span><span class="sxs-lookup"><span data-stu-id="0a6ae-155">The `@removeTagHelper` has the same two parameters as `@addTagHelper`, and it removes a Tag Helper that was previously added.</span></span> <span data-ttu-id="0a6ae-156">Örneğin, belirli `@removeTagHelper` bir görünüme uygulandığında, belirtilen etiket Yardımcısı görünümden kaldırılır.</span><span class="sxs-lookup"><span data-stu-id="0a6ae-156">For example, `@removeTagHelper` applied to a specific view removes the specified Tag Helper from the view.</span></span> <span data-ttu-id="0a6ae-157">Bir `@removeTagHelper` *views/Folder/_ViewImports. cshtml* dosyasında kullanmak, belirtilen etiket yardımcısını *klasördeki*tüm görünümlerden kaldırır.</span><span class="sxs-lookup"><span data-stu-id="0a6ae-157">Using `@removeTagHelper` in a *Views/Folder/_ViewImports.cshtml* file removes the specified Tag Helper from all of the views in *Folder*.</span></span>

### <a name="controlling-tag-helper-scope-with-the-_viewimportscshtml-file"></a><span data-ttu-id="0a6ae-158">Etiket Yardımcısı kapsamını *_ViewImports. cshtml* dosyası ile denetleme</span><span class="sxs-lookup"><span data-stu-id="0a6ae-158">Controlling Tag Helper scope with the *_ViewImports.cshtml* file</span></span>

<span data-ttu-id="0a6ae-159">Herhangi bir görünüm klasörüne *_ViewImports. cshtml* ekleyebilirsiniz ve Görünüm altyapısı, bu dosya ve *Görünümler/_ViewImports. cshtml* dosyasından yönergeleri uygular.</span><span class="sxs-lookup"><span data-stu-id="0a6ae-159">You can add a *_ViewImports.cshtml* to any view folder, and the view engine applies the directives from both that file and the *Views/_ViewImports.cshtml* file.</span></span> <span data-ttu-id="0a6ae-160">*Giriş* görünümleri için boş bir *Görünümler/Home/_ViewImports. cshtml* dosyası eklediyseniz, *_ViewImports. cshtml* dosyası eklenebilir olduğundan hiçbir değişiklik olmaz.</span><span class="sxs-lookup"><span data-stu-id="0a6ae-160">If you added an empty *Views/Home/_ViewImports.cshtml* file for the *Home* views, there would be no change because the *_ViewImports.cshtml* file is additive.</span></span> <span data-ttu-id="0a6ae-161">`@addTagHelper` *Görünümler/Home/_ViewImports. cshtml* dosyasına ekleyeceğiniz tüm yönergeler (varsayılan *görünümlerde/_ViewImports. cshtml* dosyasında yer alan), bu etiket yardımcılarını yalnızca *giriş* klasöründeki görünümlerde kullanıma sunar.</span><span class="sxs-lookup"><span data-stu-id="0a6ae-161">Any `@addTagHelper` directives you add to the *Views/Home/_ViewImports.cshtml* file (that are not in the default *Views/_ViewImports.cshtml* file) would expose those Tag Helpers to views only in the *Home* folder.</span></span>

<a name="opt-out"></a>

### <a name="opting-out-of-individual-elements"></a><span data-ttu-id="0a6ae-162">Ayrı öğelerin dışında tutma</span><span class="sxs-lookup"><span data-stu-id="0a6ae-162">Opting out of individual elements</span></span>

<span data-ttu-id="0a6ae-163">Etiket Yardımcısı geri çevirme karakteriyle ("!") birlikte öğe düzeyinde bir etiket yardımcısını devre dışı bırakabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="0a6ae-163">You can disable a Tag Helper at the element level with the Tag Helper opt-out character ("!").</span></span> <span data-ttu-id="0a6ae-164">Örneğin, `Email` doğrulama, `<span>` etiket Yardımcısı geri çevirme karakteriyle birlikte öğesinde devre dışıdır:</span><span class="sxs-lookup"><span data-stu-id="0a6ae-164">For example, `Email` validation is disabled in the `<span>` with the Tag Helper opt-out character:</span></span>

```cshtml
<!span asp-validation-for="Email" class="text-danger"></!span>
```

<span data-ttu-id="0a6ae-165">Açılış ve kapanış etiketine etiket Yardımcısı geri çevirme karakterini uygulamanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="0a6ae-165">You must apply the Tag Helper opt-out character to the opening and closing tag.</span></span> <span data-ttu-id="0a6ae-166">(Açılış etiketine bir tane eklediğinizde, Visual Studio Düzenleyicisi, kapatma etiketine otomatik olarak bir kapanış karakteri ekler).</span><span class="sxs-lookup"><span data-stu-id="0a6ae-166">(The Visual Studio editor automatically adds the opt-out character to the closing tag when you add one to the opening tag).</span></span> <span data-ttu-id="0a6ae-167">Kabul etme karakterini ekledikten sonra, öğe ve etiket Yardımcısı öznitelikleri artık farklı bir yazı tipinde gösterilmez.</span><span class="sxs-lookup"><span data-stu-id="0a6ae-167">After you add the opt-out character, the element and Tag Helper attributes are no longer displayed in a distinctive font.</span></span>

<a name="prefix-razor-directives-label"></a>

### <a name="using-taghelperprefix-to-make-tag-helper-usage-explicit"></a><span data-ttu-id="0a6ae-168">Etiket `@tagHelperPrefix` Yardımcısı kullanımını açık hale getirmek için kullanma</span><span class="sxs-lookup"><span data-stu-id="0a6ae-168">Using `@tagHelperPrefix` to make Tag Helper usage explicit</span></span>

<span data-ttu-id="0a6ae-169">`@tagHelperPrefix` Yönergesi etiket Yardımcısı desteğini etkinleştirmek ve etiket Yardımcısı kullanımını açık hale getirmek için bir etiket öneki dizesi belirtmenize olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="0a6ae-169">The `@tagHelperPrefix` directive allows you to specify a tag prefix string to enable Tag Helper support and to make Tag Helper usage explicit.</span></span> <span data-ttu-id="0a6ae-170">Örneğin, aşağıdaki biçimlendirmeyi *views/_ViewImports. cshtml* dosyasına ekleyebilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="0a6ae-170">For example, you could add the following markup to the *Views/_ViewImports.cshtml* file:</span></span>

```cshtml
@tagHelperPrefix th:
```

<span data-ttu-id="0a6ae-171">Aşağıdaki kod görüntüsünde, etiket Yardımcısı ön eki olarak `th:`ayarlanır; bu nedenle yalnızca öneki `th:` kullanan öğeler etiket yardımcıları destekler (etiket Yardımcısı etkin öğeleri farklı bir yazı tipine sahiptir).</span><span class="sxs-lookup"><span data-stu-id="0a6ae-171">In the code image below, the Tag Helper prefix is set to `th:`, so only those elements using the prefix `th:` support Tag Helpers (Tag Helper-enabled elements have a distinctive font).</span></span> <span data-ttu-id="0a6ae-172">Ve öğeleri etiket Yardımcısı ön ekine sahiptir ve etiket Yardımcısı etkindir, ancak `<span>` öğesi değildir. `<input>` `<label>`</span><span class="sxs-lookup"><span data-stu-id="0a6ae-172">The `<label>` and `<input>` elements have the Tag Helper prefix and are Tag Helper-enabled, while the `<span>` element doesn't.</span></span>

![image](intro/_static/thp.png)

<span data-ttu-id="0a6ae-174">İçin `@addTagHelper` de geçerlidir aynı hiyerarşi kuralları için `@tagHelperPrefix`geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="0a6ae-174">The same hierarchy rules that apply to `@addTagHelper` also apply to `@tagHelperPrefix`.</span></span>

## <a name="self-closing-tag-helpers"></a><span data-ttu-id="0a6ae-175">Kendi kendine kapanan etiket yardımcıları</span><span class="sxs-lookup"><span data-stu-id="0a6ae-175">Self-closing Tag Helpers</span></span>

<span data-ttu-id="0a6ae-176">Birçok etiket yardımcıları, kendinden kapanış etiketleri olarak kullanılamaz.</span><span class="sxs-lookup"><span data-stu-id="0a6ae-176">Many Tag Helpers can't be used as self-closing tags.</span></span> <span data-ttu-id="0a6ae-177">Bazı etiket yardımcıları, kendinden kapanış etiketleri olacak şekilde tasarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="0a6ae-177">Some Tag Helpers are designed to be self-closing tags.</span></span> <span data-ttu-id="0a6ae-178">Kendi kendini kapatmak üzere tasarlanmamış bir etiket Yardımcısı kullanılması işlenmiş çıktıyı bastırır.</span><span class="sxs-lookup"><span data-stu-id="0a6ae-178">Using a Tag Helper that was not designed to be self-closing suppresses the rendered output.</span></span> <span data-ttu-id="0a6ae-179">Bir etiket Yardımcısı kendinden kapanış, işlenen çıktıda kendi kendine kapanan bir etikete neden olur.</span><span class="sxs-lookup"><span data-stu-id="0a6ae-179">Self-closing a Tag Helper results in a self-closing tag in the rendered output.</span></span> <span data-ttu-id="0a6ae-180">Daha fazla bilgi [için bkz.](xref:mvc/views/tag-helpers/authoring#self-closing) [etiket yardımcılarını yazma](xref:mvc/views/tag-helpers/authoring).</span><span class="sxs-lookup"><span data-stu-id="0a6ae-180">For more information, see [this note](xref:mvc/views/tag-helpers/authoring#self-closing) in [Authoring Tag Helpers](xref:mvc/views/tag-helpers/authoring).</span></span>

## <a name="c-in-tag-helpers-attributedeclaration"></a><span data-ttu-id="0a6ae-181">Etiket Yardımcıları özniteliğinde/bildiriminde C#</span><span class="sxs-lookup"><span data-stu-id="0a6ae-181">C# in Tag Helpers attribute/declaration</span></span> 

<span data-ttu-id="0a6ae-182">Etiket Yardımcıları, öğenin özniteliğinde veya etiket bildirim alanında C# ' ın kullanılmasına izin vermez.</span><span class="sxs-lookup"><span data-stu-id="0a6ae-182">Tag Helpers do not allow C# in the element's attribute or tag declaration area.</span></span> <span data-ttu-id="0a6ae-183">Örneğin, aşağıdaki kod geçerli değildir:</span><span class="sxs-lookup"><span data-stu-id="0a6ae-183">For example, the following code is not valid:</span></span>

```cshtml
<input asp-for="LastName"  
       @(Model?.LicenseId == null ? "disabled" : string.Empty) />
```

<span data-ttu-id="0a6ae-184">Yukarıdaki kod şöyle yazılabilir:</span><span class="sxs-lookup"><span data-stu-id="0a6ae-184">The preceding code can be written as:</span></span>

```cshtml
<input asp-for="LastName" 
       disabled="@(Model?.LicenseId == null)" />
```

## <a name="intellisense-support-for-tag-helpers"></a><span data-ttu-id="0a6ae-185">Etiket Yardımcıları için IntelliSense desteği</span><span class="sxs-lookup"><span data-stu-id="0a6ae-185">IntelliSense support for Tag Helpers</span></span>

<span data-ttu-id="0a6ae-186">Visual Studio 'da yeni bir ASP.NET Core Web uygulaması oluşturduğunuzda, "Microsoft. AspNetCore" NuGet paketini ekler. Razor. Araçlar ".</span><span class="sxs-lookup"><span data-stu-id="0a6ae-186">When you create a new ASP.NET Core web app in Visual Studio, it adds the NuGet package "Microsoft.AspNetCore.Razor.Tools".</span></span> <span data-ttu-id="0a6ae-187">Bu, etiket Yardımcısı araçları ekleyen pakettir.</span><span class="sxs-lookup"><span data-stu-id="0a6ae-187">This is the package that adds Tag Helper tooling.</span></span>

<span data-ttu-id="0a6ae-188">Bir HTML `<label>` öğesi yazmayı düşünün.</span><span class="sxs-lookup"><span data-stu-id="0a6ae-188">Consider writing an HTML `<label>` element.</span></span> <span data-ttu-id="0a6ae-189">Visual Studio Düzenleyicisi ' ne `<l` girdikten hemen sonra IntelliSense eşleşen öğeleri görüntüler:</span><span class="sxs-lookup"><span data-stu-id="0a6ae-189">As soon as you enter `<l` in the Visual Studio editor, IntelliSense displays matching elements:</span></span>

![image](intro/_static/label.png)

<span data-ttu-id="0a6ae-191">Yalnızca HTML Yardımı (bunun altındaki "@" symbol with "<>") alınamaz.</span><span class="sxs-lookup"><span data-stu-id="0a6ae-191">Not only do you get HTML help, but the icon (the "@" symbol with "<>" under it).</span></span>

![image](intro/_static/tagSym.png)

<span data-ttu-id="0a6ae-193">öğesi etiket yardımcıları tarafından hedeflenen şekilde tanımlar.</span><span class="sxs-lookup"><span data-stu-id="0a6ae-193">identifies the element as targeted by Tag Helpers.</span></span> <span data-ttu-id="0a6ae-194">Saf HTML öğeleri (gibi `fieldset`) "<>" simgesini görüntüler.</span><span class="sxs-lookup"><span data-stu-id="0a6ae-194">Pure HTML elements (such as the `fieldset`) display the "<>" icon.</span></span>

<span data-ttu-id="0a6ae-195">Saf HTML `<label>` etıketı, HTML etiketini (varsayılan Visual Studio renk teması ile) bir kahverengi yazı tipinde, kırmızı olan özniteliklere ve öznitelik değerlerini mavi olarak görüntüler.</span><span class="sxs-lookup"><span data-stu-id="0a6ae-195">A pure HTML `<label>` tag displays the HTML tag (with the default Visual Studio color theme) in a brown font, the attributes in red, and the attribute values in blue.</span></span>

![image](intro/_static/LableHtmlTag.png)

<span data-ttu-id="0a6ae-197">Girdikten `<label`sonra, ıNTELLISENSE kullanılabilir HTML/CSS özniteliklerini ve etikete yardımcı hedeflenen öznitelikleri listeler:</span><span class="sxs-lookup"><span data-stu-id="0a6ae-197">After you enter `<label`, IntelliSense lists the available HTML/CSS attributes and the Tag Helper-targeted attributes:</span></span>

![image](intro/_static/labelattr.png)

<span data-ttu-id="0a6ae-199">IntelliSense deyimin tamamlanması, seçili değere sahip ifadeyi tamamlamak için sekme tuşunu girmenizi sağlar:</span><span class="sxs-lookup"><span data-stu-id="0a6ae-199">IntelliSense statement completion allows you to enter the tab key to complete the statement with the selected value:</span></span>

![image](intro/_static/stmtcomplete.png)

<span data-ttu-id="0a6ae-201">Etiket Yardımcısı özniteliği girildiğinde, Tag ve Attribute yazı tipleri değişir.</span><span class="sxs-lookup"><span data-stu-id="0a6ae-201">As soon as a Tag Helper attribute is entered, the tag and attribute fonts change.</span></span> <span data-ttu-id="0a6ae-202">Varsayılan Visual Studio "mavi" veya "hafif" renkli temasını kullanarak yazı tipi kalın mor olur.</span><span class="sxs-lookup"><span data-stu-id="0a6ae-202">Using the default Visual Studio "Blue" or "Light" color theme, the font is bold purple.</span></span> <span data-ttu-id="0a6ae-203">"Koyu" temasını kullanıyorsanız, yazı tipi kalın deniz mavisi olur.</span><span class="sxs-lookup"><span data-stu-id="0a6ae-203">If you're using the "Dark" theme the font is bold teal.</span></span> <span data-ttu-id="0a6ae-204">Bu belgedeki görüntüler varsayılan tema kullanılarak alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="0a6ae-204">The images in this document were taken using the default theme.</span></span>

![image](intro/_static/labelaspfor2.png)

<span data-ttu-id="0a6ae-206">Visual Studio *CompleteWord* kısayolunu (Ctrl + Ara Çubuğu ' nu çift tırnak işaretleri ("") içinde [varsayılan](/visualstudio/ide/default-keyboard-shortcuts-in-visual-studio) olarak girebilirsiniz ve c# sınıfında olduğu gibi artık c# dilinde olursunuz.</span><span class="sxs-lookup"><span data-stu-id="0a6ae-206">You can enter the Visual Studio *CompleteWord* shortcut (Ctrl +spacebar is the [default](/visualstudio/ide/default-keyboard-shortcuts-in-visual-studio) inside the double quotes (""), and you are now in C#, just like you would be in a C# class.</span></span> <span data-ttu-id="0a6ae-207">IntelliSense, sayfa modelindeki tüm yöntemleri ve özellikleri görüntüler.</span><span class="sxs-lookup"><span data-stu-id="0a6ae-207">IntelliSense displays all the methods and properties on the page model.</span></span> <span data-ttu-id="0a6ae-208">Özellik türü olduğu için yöntemler ve özellikler kullanılabilir `ModelExpression`.</span><span class="sxs-lookup"><span data-stu-id="0a6ae-208">The methods and properties are available because the property type is `ModelExpression`.</span></span> <span data-ttu-id="0a6ae-209">Aşağıdaki görüntüde `Register` görünümü düzenledim, bu nedenle `RegisterViewModel` kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="0a6ae-209">In the image below, I'm editing the `Register` view, so the `RegisterViewModel` is available.</span></span>

![image](intro/_static/intellemail.png)

<span data-ttu-id="0a6ae-211">IntelliSense, sayfada modelin kullanabileceği özellikleri ve yöntemleri listeler.</span><span class="sxs-lookup"><span data-stu-id="0a6ae-211">IntelliSense lists the properties and methods available to the model on the page.</span></span> <span data-ttu-id="0a6ae-212">Zengin IntelliSense ortamı, CSS sınıfını seçmenize yardımcı olur:</span><span class="sxs-lookup"><span data-stu-id="0a6ae-212">The rich IntelliSense environment helps you select the CSS class:</span></span>

![image](intro/_static/iclass.png)

![image](intro/_static/intel3.png)

## <a name="tag-helpers-compared-to-html-helpers"></a><span data-ttu-id="0a6ae-215">HTML Yardımcıları ile karşılaştırılan etiket yardımcıları</span><span class="sxs-lookup"><span data-stu-id="0a6ae-215">Tag Helpers compared to HTML Helpers</span></span>

<span data-ttu-id="0a6ae-216">Etiket Yardımcıları Razor görünümlerde HTML öğelerine Iliştirirken [HTML Yardımcıları](https://stephenwalther.com/archive/2009/03/03/chapter-6-understanding-html-helpers) Razor görünümlerde HTML ile yöntemler olarak çağrılır.</span><span class="sxs-lookup"><span data-stu-id="0a6ae-216">Tag Helpers attach to HTML elements in Razor views, while [HTML Helpers](https://stephenwalther.com/archive/2009/03/03/chapter-6-understanding-html-helpers) are invoked as methods interspersed with HTML in Razor views.</span></span> <span data-ttu-id="0a6ae-217">CSS sınıfı " Razor Caption" IÇEREN bir HTML etiketi oluşturan aşağıdaki biçimlendirmeyi göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="0a6ae-217">Consider the following Razor markup, which creates an HTML label with the CSS class "caption":</span></span>

```cshtml
@Html.Label("FirstName", "First Name:", new {@class="caption"})
```

<span data-ttu-id="0a6ae-218">At (`@`) simgesi bunun kodun Razor başlangıcı olduğunu söyler.</span><span class="sxs-lookup"><span data-stu-id="0a6ae-218">The at (`@`) symbol tells Razor this is the start of code.</span></span> <span data-ttu-id="0a6ae-219">Sonraki iki parametre ("FirstName" ve "First Name:") dizelerdir, bu nedenle [IntelliSense](/visualstudio/ide/using-intellisense) yardımcı olamaz.</span><span class="sxs-lookup"><span data-stu-id="0a6ae-219">The next two parameters ("FirstName" and "First Name:") are strings, so [IntelliSense](/visualstudio/ide/using-intellisense) can't help.</span></span> <span data-ttu-id="0a6ae-220">Son bağımsız değişken:</span><span class="sxs-lookup"><span data-stu-id="0a6ae-220">The last argument:</span></span>

```cshtml
new {@class="caption"}
```

<span data-ttu-id="0a6ae-221">Öznitelikleri temsil etmek için kullanılan anonim bir nesnedir.</span><span class="sxs-lookup"><span data-stu-id="0a6ae-221">Is an anonymous object used to represent attributes.</span></span> <span data-ttu-id="0a6ae-222">, `class` C# ' de ayrılmış bir anahtar sözcük olduğundan, C# `@` ' ı bir sembol (özellik `@class=` adı) olarak yorumlamaya zorlamak için simgesini kullanın.</span><span class="sxs-lookup"><span data-stu-id="0a6ae-222">Because `class` is a reserved keyword in C#, you use the `@` symbol to force C# to interpret `@class=` as a symbol (property name).</span></span> <span data-ttu-id="0a6ae-223">Bir ön uç tasarımcısına (HTML/CSS/JavaScript ve diğer istemci teknolojilerine tanıdık ancak C# ve Razorhakkında bilgi sahibi olmayan bir kişiye), satırın çoğu yabancı olur.</span><span class="sxs-lookup"><span data-stu-id="0a6ae-223">To a front-end designer (someone familiar with HTML/CSS/JavaScript and other client technologies but not familiar with C# and Razor), most of the line is foreign.</span></span> <span data-ttu-id="0a6ae-224">Tüm satır, IntelliSense 'den yardım olmadan yazılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="0a6ae-224">The entire line must be authored with no help from IntelliSense.</span></span>

<span data-ttu-id="0a6ae-225">`LabelTagHelper`Kullanarak, aynı biçimlendirme şöyle yazılabilir:</span><span class="sxs-lookup"><span data-stu-id="0a6ae-225">Using the `LabelTagHelper`, the same markup can be written as:</span></span>

```cshtml
<label class="caption" asp-for="FirstName"></label>
```

<span data-ttu-id="0a6ae-226">Etiket yardımcı sürümüyle, Visual Studio Düzenleyicisi ' ne girdiğinizde `<l` IntelliSense eşleşen öğeleri görüntüler:</span><span class="sxs-lookup"><span data-stu-id="0a6ae-226">With the Tag Helper version, as soon as you enter `<l` in the Visual Studio editor, IntelliSense displays matching elements:</span></span>

![image](intro/_static/label.png)

<span data-ttu-id="0a6ae-228">IntelliSense, tüm satırı yazmanıza yardımcı olur.</span><span class="sxs-lookup"><span data-stu-id="0a6ae-228">IntelliSense helps you write the entire line.</span></span>

<span data-ttu-id="0a6ae-229">Aşağıdaki kod görüntüsünde, Visual Studio ile birlikte gelen ASP.NET 4.5. x MVC şablonundan oluşturulan *Görünümler/Account/Register. cshtml* Razor görünümünün form kısmı gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="0a6ae-229">The following code image shows the Form portion of the *Views/Account/Register.cshtml* Razor view generated from the ASP.NET 4.5.x MVC template included with Visual Studio.</span></span>

![image](intro/_static/regCS.png)

<span data-ttu-id="0a6ae-231">Visual Studio Düzenleyicisi, bir gri arka plan ile C# kodunu görüntüler.</span><span class="sxs-lookup"><span data-stu-id="0a6ae-231">The Visual Studio editor displays C# code with a grey background.</span></span> <span data-ttu-id="0a6ae-232">Örneğin, `AntiForgeryToken` HTML Yardımcısı:</span><span class="sxs-lookup"><span data-stu-id="0a6ae-232">For example, the `AntiForgeryToken` HTML Helper:</span></span>

```cshtml
@Html.AntiForgeryToken()
```

<span data-ttu-id="0a6ae-233">gri bir arka planla görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="0a6ae-233">is displayed with a grey background.</span></span> <span data-ttu-id="0a6ae-234">Kayıt görünümündeki biçimlendirmenin çoğu C# ' dir.</span><span class="sxs-lookup"><span data-stu-id="0a6ae-234">Most of the markup in the Register view is C#.</span></span> <span data-ttu-id="0a6ae-235">Etiket Yardımcıları kullanarak eşdeğer yaklaşımla karşılaştırın:</span><span class="sxs-lookup"><span data-stu-id="0a6ae-235">Compare that to the equivalent approach using Tag Helpers:</span></span>

![image](intro/_static/regTH.png)

<span data-ttu-id="0a6ae-237">Biçimlendirme çok daha temiz ve HTML Yardımcıları yaklaşımıyla daha kolay okunabilir, düzenlenebilir ve korunur.</span><span class="sxs-lookup"><span data-stu-id="0a6ae-237">The markup is much cleaner and easier to read, edit, and maintain than the HTML Helpers approach.</span></span> <span data-ttu-id="0a6ae-238">C# kodu, sunucunun hakkında bilgi sahibi olması gereken en düşük düzeyde azaltılır.</span><span class="sxs-lookup"><span data-stu-id="0a6ae-238">The C# code is reduced to the minimum that the server needs to know about.</span></span> <span data-ttu-id="0a6ae-239">Visual Studio Düzenleyicisi, bir etiket Yardımcısı tarafından hedeflenen biçimlendirmeyi farklı bir yazı tipinde görüntüler.</span><span class="sxs-lookup"><span data-stu-id="0a6ae-239">The Visual Studio editor displays markup targeted by a Tag Helper in a distinctive font.</span></span>

<span data-ttu-id="0a6ae-240">*E-posta* grubunu göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="0a6ae-240">Consider the *Email* group:</span></span>

[!code-csharp[](intro/sample/Register.cshtml?range=12-18)]

<span data-ttu-id="0a6ae-241">"ASP-" özniteliklerinin her biri "e-posta" değerine sahiptir, ancak "e-posta" bir dize değildir.</span><span class="sxs-lookup"><span data-stu-id="0a6ae-241">Each of the "asp-" attributes has a value of "Email", but "Email" isn't a string.</span></span> <span data-ttu-id="0a6ae-242">Bu bağlamda, "e-posta", `RegisterViewModel`Için C# model ifadesi özelliğidir.</span><span class="sxs-lookup"><span data-stu-id="0a6ae-242">In this context, "Email" is the C# model expression property for the `RegisterViewModel`.</span></span>

<span data-ttu-id="0a6ae-243">Visual Studio Düzenleyicisi, kayıt formunun etiket Yardımcısı yaklaşımında **Tüm** biçimlendirmeyi yazmanıza yardımcı olur, ancak Visual STUDIO, HTML Yardımcıları yaklaşımında kodun çoğu için yardım vermez.</span><span class="sxs-lookup"><span data-stu-id="0a6ae-243">The Visual Studio editor helps you write **all** of the markup in the Tag Helper approach of the register form, while Visual Studio provides no help for most of the code in the HTML Helpers approach.</span></span> <span data-ttu-id="0a6ae-244">[Etiket Yardımcıları Için IntelliSense desteği](#intellisense-support-for-tag-helpers) , Visual Studio düzenleyicisinde etiket yardımcıları ile çalışma hakkında ayrıntılı bilgiler alır.</span><span class="sxs-lookup"><span data-stu-id="0a6ae-244">[IntelliSense support for Tag Helpers](#intellisense-support-for-tag-helpers) goes into detail on working with Tag Helpers in the Visual Studio editor.</span></span>

## <a name="tag-helpers-compared-to-web-server-controls"></a><span data-ttu-id="0a6ae-245">Web sunucusu denetimleriyle karşılaştırılan etiket yardımcıları</span><span class="sxs-lookup"><span data-stu-id="0a6ae-245">Tag Helpers compared to Web Server Controls</span></span>

* <span data-ttu-id="0a6ae-246">Etiket Yardımcıları ilişkili oldukları öğeye sahip değil; yalnızca öğe ve içerik işleme katılır.</span><span class="sxs-lookup"><span data-stu-id="0a6ae-246">Tag Helpers don't own the element they're associated with; they simply participate in the rendering of the element and content.</span></span> <span data-ttu-id="0a6ae-247">ASP.NET [Web sunucusu denetimleri](https://msdn.microsoft.com/library/7698y1f0.aspx) bir sayfada bildirilmiştir ve çağrılır.</span><span class="sxs-lookup"><span data-stu-id="0a6ae-247">ASP.NET [Web Server controls](https://msdn.microsoft.com/library/7698y1f0.aspx) are declared and invoked on a page.</span></span>

* <span data-ttu-id="0a6ae-248">[Web sunucusu denetimlerinde](https://msdn.microsoft.com/library/zsyt68f1.aspx) geliştirme ve hata ayıklama zor hale getirmek için önemsiz olmayan bir yaşam döngüsü vardır.</span><span class="sxs-lookup"><span data-stu-id="0a6ae-248">[Web Server controls](https://msdn.microsoft.com/library/zsyt68f1.aspx) have a non-trivial lifecycle that can make developing and debugging difficult.</span></span>

* <span data-ttu-id="0a6ae-249">Web sunucusu denetimleri, istemci denetimi kullanarak istemci Belge Nesne Modeli (DOM) öğelerine işlevsellik eklemenize olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="0a6ae-249">Web Server controls allow you to add functionality to the client Document Object Model (DOM) elements by using a client control.</span></span> <span data-ttu-id="0a6ae-250">Etiket yardımcıların DOM 'ı yok.</span><span class="sxs-lookup"><span data-stu-id="0a6ae-250">Tag Helpers have no DOM.</span></span>

* <span data-ttu-id="0a6ae-251">Web sunucusu denetimleri otomatik tarayıcı algılamayı içerir.</span><span class="sxs-lookup"><span data-stu-id="0a6ae-251">Web Server controls include automatic browser detection.</span></span> <span data-ttu-id="0a6ae-252">Etiket yardımcılarına tarayıcı bilgisi yok.</span><span class="sxs-lookup"><span data-stu-id="0a6ae-252">Tag Helpers have no knowledge of the browser.</span></span>

* <span data-ttu-id="0a6ae-253">Birden çok etiket yardımcıları aynı öğe üzerinde işlem yapabilir (bkz. [etiket Yardımcısı çakışmalarını önleme](xref:mvc/views/tag-helpers/authoring#avoid-tag-helper-conflicts) ), genellikle Web sunucusu denetimleri oluşturamazsınız.</span><span class="sxs-lookup"><span data-stu-id="0a6ae-253">Multiple Tag Helpers can act on the same element (see [Avoiding Tag Helper conflicts](xref:mvc/views/tag-helpers/authoring#avoid-tag-helper-conflicts) ) while you typically can't compose Web Server controls.</span></span>

* <span data-ttu-id="0a6ae-254">Etiket Yardımcıları, kapsamında oldukları HTML öğelerinin etiketini ve içeriğini değiştirebilir ancak sayfada başka bir şeyi doğrudan değiştirmez.</span><span class="sxs-lookup"><span data-stu-id="0a6ae-254">Tag Helpers can modify the tag and content of HTML elements that they're scoped to, but don't directly modify anything else on a page.</span></span> <span data-ttu-id="0a6ae-255">Web sunucusu denetimleri, daha az belirli bir kapsama sahiptir ve Sayfanızın diğer bölümlerini etkileyen eylemler gerçekleştirebilir; istenmeyen yan etkileri etkinleştirme.</span><span class="sxs-lookup"><span data-stu-id="0a6ae-255">Web Server controls have a less specific scope and can perform actions that affect other parts of your page; enabling unintended side effects.</span></span>

* <span data-ttu-id="0a6ae-256">Web sunucusu denetimleri, dizeleri nesnelere dönüştürmek için tür dönüştürücüler kullanır.</span><span class="sxs-lookup"><span data-stu-id="0a6ae-256">Web Server controls use type converters to convert strings into objects.</span></span> <span data-ttu-id="0a6ae-257">Etiket Yardımcıları sayesinde yerel olarak C# dilinde çalışır, bu nedenle tür dönüştürmesi yapmanız gerekmez.</span><span class="sxs-lookup"><span data-stu-id="0a6ae-257">With Tag Helpers, you work natively in C#, so you don't need to do type conversion.</span></span>

* <span data-ttu-id="0a6ae-258">Web sunucusu denetimleri, bileşenlerin ve denetimlerin çalışma zamanı ve tasarım zamanı davranışını uygulamak için [System. ComponentModel](/dotnet/api/system.componentmodel) kullanır.</span><span class="sxs-lookup"><span data-stu-id="0a6ae-258">Web Server controls use [System.ComponentModel](/dotnet/api/system.componentmodel) to implement the run-time and design-time behavior of components and controls.</span></span> <span data-ttu-id="0a6ae-259">`System.ComponentModel`öznitelikleri ve tür dönüştürücülerini uygulamak, veri kaynaklarına bağlama ve lisans bileşenlerine yönelik temel sınıfları ve arabirimleri içerir.</span><span class="sxs-lookup"><span data-stu-id="0a6ae-259">`System.ComponentModel` includes the base classes and interfaces for implementing attributes and type converters, binding to data sources, and licensing components.</span></span> <span data-ttu-id="0a6ae-260">Genellikle `TagHelper`, ve `TagHelper` temel sınıftan türetilen yardımcıları etiketleyerek yalnızca iki yöntemi gösterir `Process` ve. `ProcessAsync`</span><span class="sxs-lookup"><span data-stu-id="0a6ae-260">Contrast that to Tag Helpers, which typically derive from `TagHelper`, and the `TagHelper` base class exposes only two methods, `Process` and `ProcessAsync`.</span></span>

## <a name="customizing-the-tag-helper-element-font"></a><span data-ttu-id="0a6ae-261">Etiket Yardımcısı öğe yazı tipini özelleştirme</span><span class="sxs-lookup"><span data-stu-id="0a6ae-261">Customizing the Tag Helper element font</span></span>

<span data-ttu-id="0a6ae-262">Yazı tipini ve renklendirmeyi **Araçlar** > **Seçenekler** > **ortam** > **yazı tiplerinden ve renklerinden**özelleştirebilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="0a6ae-262">You can customize the font and colorization from **Tools** > **Options** > **Environment** > **Fonts and Colors**:</span></span>

![image](intro/_static/fontoptions2.png)

[!INCLUDE[](~/includes/built-in-TH.md)]

## <a name="additional-resources"></a><span data-ttu-id="0a6ae-264">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="0a6ae-264">Additional resources</span></span>

* [<span data-ttu-id="0a6ae-265">Yazar etiketi yardımcıları</span><span class="sxs-lookup"><span data-stu-id="0a6ae-265">Author Tag Helpers</span></span>](xref:mvc/views/tag-helpers/authoring)
* [<span data-ttu-id="0a6ae-266">Formlarla Çalışma</span><span class="sxs-lookup"><span data-stu-id="0a6ae-266">Working with Forms</span></span>](xref:mvc/views/working-with-forms)
* <span data-ttu-id="0a6ae-267">[GitHub 'Daki Taghelpersamples](https://github.com/dpaquette/TagHelperSamples) , [önyükleme](https://getbootstrap.com/)Ile çalışmaya yönelik etiket Yardımcısı örnekleri içerir.</span><span class="sxs-lookup"><span data-stu-id="0a6ae-267">[TagHelperSamples on GitHub](https://github.com/dpaquette/TagHelperSamples) contains Tag Helper samples for working with [Bootstrap](https://getbootstrap.com/).</span></span>
