---
title: ASP.NET Core Razor Sayfasına doğrulama ekleme
author: rick-anderson
description: ASP.NET Core'daki Bir Razor Page'e doğrulamanın nasıl ekleyeceğinizi keşfedin.
ms.author: riande
ms.custom: mvc
ms.date: 7/23/2019
uid: tutorials/razor-pages/validation
ms.openlocfilehash: f283234ed8a32dc9b7904bc6fee1cc9c04741029
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78666021"
---
# <a name="add-validation-to-an-aspnet-core-razor-page"></a><span data-ttu-id="418a6-103">ASP.NET Core Razor Sayfasına doğrulama ekleme</span><span class="sxs-lookup"><span data-stu-id="418a6-103">Add validation to an ASP.NET Core Razor Page</span></span>

<span data-ttu-id="418a6-104">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="418a6-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="418a6-105">Bu bölümde modele `Movie` doğrulama mantığı eklenir.</span><span class="sxs-lookup"><span data-stu-id="418a6-105">In this section, validation logic is added to the `Movie` model.</span></span> <span data-ttu-id="418a6-106">Doğrulama kuralları, bir kullanıcı bir film oluşturduğunda veya her zaman geçerlilenir.</span><span class="sxs-lookup"><span data-stu-id="418a6-106">The validation rules are enforced any time a user creates or edits a movie.</span></span>

## <a name="validation"></a><span data-ttu-id="418a6-107">Doğrulama</span><span class="sxs-lookup"><span data-stu-id="418a6-107">Validation</span></span>

<span data-ttu-id="418a6-108">Yazılım geliştirmenin önemli bir ilkesi [DRY](https://wikipedia.org/wiki/Don%27t_repeat_yourself) ("**D**on't **R**epeat **Y**our self") olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="418a6-108">A key tenet of software development is called [DRY](https://wikipedia.org/wiki/Don%27t_repeat_yourself) ("**D**on't **R**epeat **Y**ourself").</span></span> <span data-ttu-id="418a6-109">Razor Pages, işlevselliğin bir kez belirtildiği ve uygulamanın tüm ekine yansıtıldığı şekilde geliştirmeyi teşvik eder.</span><span class="sxs-lookup"><span data-stu-id="418a6-109">Razor Pages encourages development where functionality is specified once, and it's reflected throughout the app.</span></span> <span data-ttu-id="418a6-110">DRY yardımcı olabilir:</span><span class="sxs-lookup"><span data-stu-id="418a6-110">DRY can help:</span></span>

* <span data-ttu-id="418a6-111">Bir uygulamadaki kod miktarını azaltın.</span><span class="sxs-lookup"><span data-stu-id="418a6-111">Reduce the amount of code in an app.</span></span>
* <span data-ttu-id="418a6-112">Kodu daha az hataya yatkın hale getirin ve sınanması ve bakımı daha kolay.</span><span class="sxs-lookup"><span data-stu-id="418a6-112">Make the code less error prone, and easier to test and maintain.</span></span>

<span data-ttu-id="418a6-113">Razor Pages ve Entity Framework tarafından sağlanan doğrulama desteği, DRY ilkesinin iyi bir örneğidir.</span><span class="sxs-lookup"><span data-stu-id="418a6-113">The validation support provided by Razor Pages and Entity Framework is a good example of the DRY principle.</span></span> <span data-ttu-id="418a6-114">Doğrulama kuralları bildirimsel olarak tek bir yerde (model sınıfında) belirtilir ve kurallar uygulamanın her yerinde uygulanır.</span><span class="sxs-lookup"><span data-stu-id="418a6-114">Validation rules are declaratively specified in one place (in the model class), and the rules are enforced everywhere in the app.</span></span>

## <a name="add-validation-rules-to-the-movie-model"></a><span data-ttu-id="418a6-115">Film modeline doğrulama kuralları ekleme</span><span class="sxs-lookup"><span data-stu-id="418a6-115">Add validation rules to the movie model</span></span>

<span data-ttu-id="418a6-116">DataAnnotations ad alanı, bir sınıfa veya özelliğe bildirimsel olarak uygulanan yerleşik doğrulama öznitelikleri kümesi sağlar.</span><span class="sxs-lookup"><span data-stu-id="418a6-116">The DataAnnotations namespace provides a set of built-in validation attributes that are applied declaratively to a class or property.</span></span> <span data-ttu-id="418a6-117">DataAnnotations da biçimlendirme `DataType` ile yardımcı gibi biçimlendirme öznitelikleri içerir ve herhangi bir doğrulama sağlamaz.</span><span class="sxs-lookup"><span data-stu-id="418a6-117">DataAnnotations also contains formatting attributes like `DataType` that help with formatting and don't provide any validation.</span></span>

<span data-ttu-id="418a6-118">Yerleşik `Movie` `Required`, , `StringLength`ve `RegularExpression` `Range` doğrulama özniteliklerinden yararlanmak için sınıfı güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="418a6-118">Update the `Movie` class to take advantage of the built-in `Required`, `StringLength`, `RegularExpression`, and `Range` validation attributes.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet1)]

<span data-ttu-id="418a6-119">Doğrulama öznitelikleri, uygulandıkları model özellikleriüzerinde zorlamak istediğiniz davranışı belirtir:</span><span class="sxs-lookup"><span data-stu-id="418a6-119">The validation attributes specify behavior that you want to enforce on the model properties they're applied to:</span></span>

* <span data-ttu-id="418a6-120">Ve `Required` `MinimumLength` öznitelikleri bir özelliğin bir değeri olması gerektiğini gösterir; ancak hiçbir şey, kullanıcının bu doğrulamayı karşılamak için beyaz alan girmesini engellemez.</span><span class="sxs-lookup"><span data-stu-id="418a6-120">The `Required` and `MinimumLength` attributes indicate that a property must have a value; but nothing prevents a user from entering white space to satisfy this validation.</span></span>
* <span data-ttu-id="418a6-121">Öznitelik, `RegularExpression` hangi karakterlerin giriş olabileceğini sınırlamak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="418a6-121">The `RegularExpression` attribute is used to limit what characters can be input.</span></span> <span data-ttu-id="418a6-122">Önceki kodda, "Tür":</span><span class="sxs-lookup"><span data-stu-id="418a6-122">In the preceding code, "Genre":</span></span>

  * <span data-ttu-id="418a6-123">Sadece harfleri kullanmalısınız.</span><span class="sxs-lookup"><span data-stu-id="418a6-123">Must only use letters.</span></span>
  * <span data-ttu-id="418a6-124">İlk harfin büyük harf olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="418a6-124">The first letter is required to be uppercase.</span></span> <span data-ttu-id="418a6-125">Beyaz boşluk, sayılar ve özel karakterlere izin verilmez.</span><span class="sxs-lookup"><span data-stu-id="418a6-125">White space, numbers, and special characters are not allowed.</span></span>

* <span data-ttu-id="418a6-126">`RegularExpression` "Derecelendirme":</span><span class="sxs-lookup"><span data-stu-id="418a6-126">The `RegularExpression` "Rating":</span></span>

  * <span data-ttu-id="418a6-127">İlk karakterin büyük harf olmasını gerektirir.</span><span class="sxs-lookup"><span data-stu-id="418a6-127">Requires that the first character be an uppercase letter.</span></span>
  * <span data-ttu-id="418a6-128">Sonraki alanlarda özel karakterlere ve sayılara izin verir.</span><span class="sxs-lookup"><span data-stu-id="418a6-128">Allows special characters and numbers in  subsequent spaces.</span></span> <span data-ttu-id="418a6-129">"PG-13" bir derecelendirme için geçerlidir, ancak bir "Tür" için başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="418a6-129">"PG-13" is valid for a rating, but fails for a "Genre".</span></span>

* <span data-ttu-id="418a6-130">`Range` özniteliği, bir değeri belirtilen bir aralık içinde kısıtlar.</span><span class="sxs-lookup"><span data-stu-id="418a6-130">The `Range` attribute constrains a value to within a specified range.</span></span>
* <span data-ttu-id="418a6-131">Öznitelik, `StringLength` dize özelliğinin en büyük uzunluğunu ve isteğe bağlı olarak en az uzunluğunu ayarlamanızı sağlar.</span><span class="sxs-lookup"><span data-stu-id="418a6-131">The `StringLength` attribute lets you set the maximum length of a string property, and optionally its minimum length.</span></span>
* <span data-ttu-id="418a6-132">Değer türleri (, `int` `float`, `DateTime`, , ) doğal `[Required]` `decimal`olarak gereklidir ve öznitelik gerekmez.</span><span class="sxs-lookup"><span data-stu-id="418a6-132">Value types (such as `decimal`, `int`, `float`, `DateTime`) are inherently required and don't need the `[Required]` attribute.</span></span>

<span data-ttu-id="418a6-133">doğrulama kurallarının ASP.NET Core tarafından otomatik olarak uygulanması, uygulamanızın daha sağlam olmasına yardımcı olur.</span><span class="sxs-lookup"><span data-stu-id="418a6-133">Having validation rules automatically enforced by ASP.NET Core helps make your app more robust.</span></span> <span data-ttu-id="418a6-134">Ayrıca, bir şeyi doğrulamayı ve yanlışlıkla kötü verilerin veritabanına girmesine izin vermeyi unutmamanızı sağlar.</span><span class="sxs-lookup"><span data-stu-id="418a6-134">It also ensures that you can't forget to validate something and inadvertently let bad data into the database.</span></span>

### <a name="validation-error-ui-in-razor-pages"></a><span data-ttu-id="418a6-135">Razor Sayfalarda Doğrulama Hatası UI</span><span class="sxs-lookup"><span data-stu-id="418a6-135">Validation Error UI in Razor Pages</span></span>

<span data-ttu-id="418a6-136">Uygulamayı çalıştırın ve Sayfalar/Filmler'e gidin.</span><span class="sxs-lookup"><span data-stu-id="418a6-136">Run the app and navigate to Pages/Movies.</span></span>

<span data-ttu-id="418a6-137">Yeni **Oluştur** bağlantısını seçin.</span><span class="sxs-lookup"><span data-stu-id="418a6-137">Select the **Create New** link.</span></span> <span data-ttu-id="418a6-138">Formu bazı geçersiz değerlerle tamamlayın.</span><span class="sxs-lookup"><span data-stu-id="418a6-138">Complete the form with some invalid values.</span></span> <span data-ttu-id="418a6-139">jQuery istemci tarafı doğrulama hatası algılar, bir hata iletisi görüntüler.</span><span class="sxs-lookup"><span data-stu-id="418a6-139">When jQuery client-side validation detects the error, it displays an error message.</span></span>

![Birden çok jQuery istemci tarafı doğrulama hatası olan film görüntüleme formu](validation/_static/val.png)

[!INCLUDE[](~/includes/localization/currency.md)]

<span data-ttu-id="418a6-141">Formun geçersiz bir değer içeren her alanda otomatik olarak bir doğrulama hatası iletisi nasıl oluşturduğuna dikkat edin.</span><span class="sxs-lookup"><span data-stu-id="418a6-141">Notice how the form has automatically rendered a validation error message in each field containing an invalid value.</span></span> <span data-ttu-id="418a6-142">Hatalar hem istemci tarafı (JavaScript ve jQuery kullanarak) hem de sunucu tarafı (bir kullanıcı JavaScript devre dışı olduğunda) uygulanır.</span><span class="sxs-lookup"><span data-stu-id="418a6-142">The errors are enforced both client-side (using JavaScript and jQuery) and server-side (when a user has JavaScript disabled).</span></span>

<span data-ttu-id="418a6-143">Önemli bir yararı, Oluştur veya Edit sayfalarında **hiçbir** kod değişikliği gerekli olmasıdır.</span><span class="sxs-lookup"><span data-stu-id="418a6-143">A significant benefit is that **no** code changes were necessary in the Create  or Edit pages.</span></span> <span data-ttu-id="418a6-144">Modele DataAnnotations uygulandıktan sonra doğrulama UI'si etkinleştirildi.</span><span class="sxs-lookup"><span data-stu-id="418a6-144">Once DataAnnotations were applied to the model, the validation UI was enabled.</span></span> <span data-ttu-id="418a6-145">Bu öğreticide oluşturulan Razor Pages, doğrulama kurallarını otomatik olarak aldı `Movie` (model sınıfının özellikleri üzerinde doğrulama öznitelikleri ni kullanarak).</span><span class="sxs-lookup"><span data-stu-id="418a6-145">The Razor Pages created in this tutorial automatically picked up the validation rules (using validation attributes on the properties of the `Movie` model class).</span></span> <span data-ttu-id="418a6-146">Edit sayfasını kullanarak test doğrulama, aynı doğrulama uygulanır.</span><span class="sxs-lookup"><span data-stu-id="418a6-146">Test validation using the Edit page, the same validation is applied.</span></span>

<span data-ttu-id="418a6-147">İstemci tarafı doğrulama hatası olmadan form verileri sunucuya nakledilemiyor.</span><span class="sxs-lookup"><span data-stu-id="418a6-147">The form data isn't posted to the server until there are no client-side validation errors.</span></span> <span data-ttu-id="418a6-148">Form verilerinin aşağıdaki yaklaşımlardan biri veya birkaçı tarafından yayınlanmadığını doğrulayın:</span><span class="sxs-lookup"><span data-stu-id="418a6-148">Verify form data isn't posted by one or more of the following approaches:</span></span>

* <span data-ttu-id="418a6-149">`OnPostAsync` Yönteme bir kesme noktası koyun.</span><span class="sxs-lookup"><span data-stu-id="418a6-149">Put a break point in the `OnPostAsync` method.</span></span> <span data-ttu-id="418a6-150">Formu gönderin **(Oluştur** veya **Kaydet'i**seçin ).</span><span class="sxs-lookup"><span data-stu-id="418a6-150">Submit the form (select **Create** or **Save**).</span></span> <span data-ttu-id="418a6-151">Kırılma noktası asla vurulmaz.</span><span class="sxs-lookup"><span data-stu-id="418a6-151">The break point is never hit.</span></span>
* <span data-ttu-id="418a6-152">[Fiddler aracını](https://www.telerik.com/fiddler)kullanın.</span><span class="sxs-lookup"><span data-stu-id="418a6-152">Use the [Fiddler tool](https://www.telerik.com/fiddler).</span></span>
* <span data-ttu-id="418a6-153">Ağ trafiğini izlemek için tarayıcı geliştirici araçlarını kullanın.</span><span class="sxs-lookup"><span data-stu-id="418a6-153">Use the browser developer tools to monitor network traffic.</span></span>

### <a name="server-side-validation"></a><span data-ttu-id="418a6-154">Sunucu tarafı doğrulama</span><span class="sxs-lookup"><span data-stu-id="418a6-154">Server-side validation</span></span>

<span data-ttu-id="418a6-155">JavaScript tarayıcıda devre dışı bırakıldığında, formu hatalarla göndermek sunucuya gönderir.</span><span class="sxs-lookup"><span data-stu-id="418a6-155">When JavaScript is disabled in the browser, submitting the form with errors will post to the server.</span></span>

<span data-ttu-id="418a6-156">İsteğe bağlı, test sunucu tarafı doğrulama:</span><span class="sxs-lookup"><span data-stu-id="418a6-156">Optional, test server-side validation:</span></span>

* <span data-ttu-id="418a6-157">Tarayıcıda JavaScript'i devre dışı bıraktı.</span><span class="sxs-lookup"><span data-stu-id="418a6-157">Disable JavaScript in the browser.</span></span> <span data-ttu-id="418a6-158">Tarayıcının geliştirici araçlarını kullanarak JavaScript'i devre dışı kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="418a6-158">You can disable JavaScript using browser's developer tools.</span></span> <span data-ttu-id="418a6-159">Tarayıcıda JavaScript'i devre dışı atamıyorsanız, başka bir tarayıcı deneyin.</span><span class="sxs-lookup"><span data-stu-id="418a6-159">If you can't disable JavaScript in the browser, try another browser.</span></span>
* <span data-ttu-id="418a6-160">Oluştur veya Yap `OnPostAsync` sayfasının yönteminde bir kesme noktası ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="418a6-160">Set a break point in the `OnPostAsync` method of the Create or Edit page.</span></span>
* <span data-ttu-id="418a6-161">Geçersiz verileriçeren bir form gönderin.</span><span class="sxs-lookup"><span data-stu-id="418a6-161">Submit a form with invalid data.</span></span>
* <span data-ttu-id="418a6-162">Model durumunun geçersiz olduğunu doğrulayın:</span><span class="sxs-lookup"><span data-stu-id="418a6-162">Verify the model state is invalid:</span></span>

  ```csharp
   if (!ModelState.IsValid)
   {
      return Page();
   }
  ```
  
<span data-ttu-id="418a6-163">Alternatif olarak, [istemci tarafı doğrulamayı sunucuda devre dışı atabilirsiniz.](xref:mvc/models/validation#disable-client-side-validation)</span><span class="sxs-lookup"><span data-stu-id="418a6-163">Alternatively, you can [Disable client-side validation on the server](xref:mvc/models/validation#disable-client-side-validation).</span></span>

<span data-ttu-id="418a6-164">Aşağıdaki kod, öğreticide daha önce scaffolded *Create.cshtml* sayfasının bir bölümünü gösterir.</span><span class="sxs-lookup"><span data-stu-id="418a6-164">The following code shows a portion of the *Create.cshtml* page scaffolded earlier in the tutorial.</span></span> <span data-ttu-id="418a6-165">İlk formu görüntülemek ve bir hata durumunda formu yeniden görüntülemek için Oluştur ve Düzenleme sayfaları tarafından kullanılır.</span><span class="sxs-lookup"><span data-stu-id="418a6-165">It's used by the Create and Edit pages to display the initial form and to redisplay the form in the event of an error.</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Create.cshtml?range=14-20)]

<span data-ttu-id="418a6-166">[Giriş Etiketi Yardımcısı,](xref:mvc/views/working-with-forms) [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) özniteliklerini kullanır ve istemci tarafında jQuery Doğrulaması için gerekli HTML özniteliklerini üretir.</span><span class="sxs-lookup"><span data-stu-id="418a6-166">The [Input Tag Helper](xref:mvc/views/working-with-forms) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span> <span data-ttu-id="418a6-167">[Doğrulama Etiketi Yardımcısı](xref:mvc/views/working-with-forms#the-validation-tag-helpers) doğrulama hatalarını görüntüler.</span><span class="sxs-lookup"><span data-stu-id="418a6-167">The [Validation Tag Helper](xref:mvc/views/working-with-forms#the-validation-tag-helpers) displays validation errors.</span></span> <span data-ttu-id="418a6-168">Daha fazla bilgi için [Doğrulama'ya](xref:mvc/models/validation) bakın.</span><span class="sxs-lookup"><span data-stu-id="418a6-168">See [Validation](xref:mvc/models/validation) for more information.</span></span>

<span data-ttu-id="418a6-169">Oluştur ve Edet sayfalarında doğrulama kuralları yoktur.</span><span class="sxs-lookup"><span data-stu-id="418a6-169">The Create and Edit pages have no validation rules in them.</span></span> <span data-ttu-id="418a6-170">Doğrulama kuralları ve hata dizeleri yalnızca `Movie` sınıfta belirtilir.</span><span class="sxs-lookup"><span data-stu-id="418a6-170">The validation rules and the error strings are specified only in the `Movie` class.</span></span> <span data-ttu-id="418a6-171">Bu doğrulama kuralları, `Movie` modeli düzenlemeyapan Razor Pages'e otomatik olarak uygulanır.</span><span class="sxs-lookup"><span data-stu-id="418a6-171">These validation rules are automatically applied to Razor Pages that edit the `Movie` model.</span></span>

<span data-ttu-id="418a6-172">Doğrulama mantığının değişmesi gerektiğinde, yalnızca modelde yapılır.</span><span class="sxs-lookup"><span data-stu-id="418a6-172">When validation logic needs to change, it's done only in the model.</span></span> <span data-ttu-id="418a6-173">Doğrulama uygulama boyunca tutarlı bir şekilde uygulanır (doğrulama mantığı tek bir yerde tanımlanır).</span><span class="sxs-lookup"><span data-stu-id="418a6-173">Validation is applied consistently throughout the application (validation logic is defined in one place).</span></span> <span data-ttu-id="418a6-174">Tek bir yerde doğrulama, kodun temiz tutulmasına yardımcı olur ve korumayı ve güncelleştirmeyi kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="418a6-174">Validation in one place helps keep the code clean, and makes it easier to maintain and update.</span></span>

## <a name="using-datatype-attributes"></a><span data-ttu-id="418a6-175">DataType Özniteliklerini Kullanma</span><span class="sxs-lookup"><span data-stu-id="418a6-175">Using DataType Attributes</span></span>

<span data-ttu-id="418a6-176">`Movie` Sınıfı inceleyin.</span><span class="sxs-lookup"><span data-stu-id="418a6-176">Examine the `Movie` class.</span></span> <span data-ttu-id="418a6-177">Ad `System.ComponentModel.DataAnnotations` alanı, yerleşik doğrulama öznitelikleri kümesine ek olarak biçimlendirme öznitelikleri sağlar.</span><span class="sxs-lookup"><span data-stu-id="418a6-177">The `System.ComponentModel.DataAnnotations` namespace provides formatting attributes in addition to the built-in set of validation attributes.</span></span> <span data-ttu-id="418a6-178">`DataType` özniteliği `ReleaseDate` ve `Price` özelliklerine uygulanır.</span><span class="sxs-lookup"><span data-stu-id="418a6-178">The `DataType` attribute is applied to the `ReleaseDate` and `Price` properties.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie/Models/MovieDateRatingDA.cs?highlight=2,6&name=snippet2)]

<span data-ttu-id="418a6-179">Öznitelikler yalnızca `DataType` görünüm altyapısının verileri biçimlendirmesi için ipuçları `<a>` sağlar (ve `<a href="mailto:EmailAddress.com">` URL'ler ve e-postalar gibi öznitelikleri sağlar).</span><span class="sxs-lookup"><span data-stu-id="418a6-179">The `DataType` attributes only provide hints for the view engine to format the data (and supplies attributes such as `<a>` for URL's and `<a href="mailto:EmailAddress.com">` for email).</span></span> <span data-ttu-id="418a6-180">Verilerin `RegularExpression` biçimini doğrulamak için özniteliği kullanın.</span><span class="sxs-lookup"><span data-stu-id="418a6-180">Use the `RegularExpression` attribute to validate the format of the data.</span></span> <span data-ttu-id="418a6-181">Öznitelik, `DataType` veritabanı içsel türünden daha spesifik bir veri türünü belirtmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="418a6-181">The `DataType` attribute is used to specify a data type that's more specific than the database intrinsic type.</span></span> <span data-ttu-id="418a6-182">`DataType`öznitelikleri doğrulama öznitelikleri değildir.</span><span class="sxs-lookup"><span data-stu-id="418a6-182">`DataType` attributes are not validation attributes.</span></span> <span data-ttu-id="418a6-183">Örnek uygulamada, yalnızca tarih, zaman olmadan görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="418a6-183">In the sample application, only the date is displayed, without time.</span></span>

<span data-ttu-id="418a6-184">Numaralandırma, `DataType` Tarih, Saat, Telefon Numarası, Para Birimi, E-posta Adresi ve daha fazlası gibi birçok veri türü sağlar.</span><span class="sxs-lookup"><span data-stu-id="418a6-184">The `DataType` Enumeration provides for many data types, such as Date, Time, PhoneNumber, Currency, EmailAddress, and more.</span></span> <span data-ttu-id="418a6-185">Öznitelik, `DataType` uygulamanın türe özgü özellikleri otomatik olarak sağlamasına da olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="418a6-185">The `DataType` attribute can also enable the application to automatically provide type-specific features.</span></span> <span data-ttu-id="418a6-186">Örneğin, bir `mailto:` bağlantı için `DataType.EmailAddress`oluşturulabilir.</span><span class="sxs-lookup"><span data-stu-id="418a6-186">For example, a `mailto:` link can be created for `DataType.EmailAddress`.</span></span> <span data-ttu-id="418a6-187">`DataType.Date` HTML5'i destekleyen tarayıcılarda bir tarih seçici sağlanabilir.</span><span class="sxs-lookup"><span data-stu-id="418a6-187">A date selector can be provided for `DataType.Date` in browsers that support HTML5.</span></span> <span data-ttu-id="418a6-188">Öznitelikler, `DataType` HTML 5 `data-` tarayıcılarının tükettiği HTML 5 (belirgin veri tiresi) özniteliklerini yayır.</span><span class="sxs-lookup"><span data-stu-id="418a6-188">The `DataType` attributes emits HTML 5 `data-` (pronounced data dash) attributes that HTML 5 browsers consume.</span></span> <span data-ttu-id="418a6-189">Öznitelikler `DataType` herhangi bir doğrulama **sağlamaz.**</span><span class="sxs-lookup"><span data-stu-id="418a6-189">The `DataType` attributes do **not** provide any validation.</span></span>

<span data-ttu-id="418a6-190">`DataType.Date`görüntülenen tarihin biçimini belirtmez.</span><span class="sxs-lookup"><span data-stu-id="418a6-190">`DataType.Date` doesn't specify the format of the date that's displayed.</span></span> <span data-ttu-id="418a6-191">Varsayılan olarak, veri alanı sunucunun `CultureInfo`.</span><span class="sxs-lookup"><span data-stu-id="418a6-191">By default, the data field is displayed according to the default formats based on the server's `CultureInfo`.</span></span>

<span data-ttu-id="418a6-192">Entity `[Column(TypeName = "decimal(18, 2)")]` Framework Core'un veritabanındaki para birimiyle `Price` doğru şekilde eşlenebilmeleri için veri ek açıklamagereklidir.</span><span class="sxs-lookup"><span data-stu-id="418a6-192">The `[Column(TypeName = "decimal(18, 2)")]` data annotation is required so Entity Framework Core can correctly map `Price` to currency in the database.</span></span> <span data-ttu-id="418a6-193">Daha fazla bilgi için [Bkz. Veri Türleri.](/ef/core/modeling/relational/data-types)</span><span class="sxs-lookup"><span data-stu-id="418a6-193">For more information, see [Data Types](/ef/core/modeling/relational/data-types).</span></span>

<span data-ttu-id="418a6-194">Öznitelik, `DisplayFormat` tarih biçimini açıkça belirtmek için kullanılır:</span><span class="sxs-lookup"><span data-stu-id="418a6-194">The `DisplayFormat` attribute is used to explicitly specify the date format:</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
public DateTime ReleaseDate { get; set; }
```

<span data-ttu-id="418a6-195">Ayar, `ApplyFormatInEditMode` düzenleme için değer görüntülendiğinde biçimlendirmenin uygulanması gerektiğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="418a6-195">The `ApplyFormatInEditMode` setting specifies that the formatting should be applied when the value is displayed for editing.</span></span> <span data-ttu-id="418a6-196">Bazı alanlar için bu davranışı istemeyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="418a6-196">You might not want that behavior for some fields.</span></span> <span data-ttu-id="418a6-197">Örneğin, para birimi değerlerinde, büyük olasılıkla eski iş bir arada geçici iş yerinde para birimi simgesini istemezsiniz.</span><span class="sxs-lookup"><span data-stu-id="418a6-197">For example, in currency values, you probably don't want the currency symbol in the edit UI.</span></span>

<span data-ttu-id="418a6-198">Öznitelik `DisplayFormat` kendisi tarafından kullanılabilir, ancak özniteliği kullanmak `DataType` genellikle iyi bir fikirdir.</span><span class="sxs-lookup"><span data-stu-id="418a6-198">The `DisplayFormat` attribute can be used by itself, but it's generally a good idea to use the `DataType` attribute.</span></span> <span data-ttu-id="418a6-199">Bu `DataType` özellik, verilerin bir ekranda nasıl işlenir, aksine anlamsallarını iletir ve DisplayFormat ile elde edilemediğiniz aşağıdaki avantajları sağlar:</span><span class="sxs-lookup"><span data-stu-id="418a6-199">The `DataType` attribute conveys the semantics of the data as opposed to how to render it on a screen, and provides the following benefits that you don't get with DisplayFormat:</span></span>

* <span data-ttu-id="418a6-200">Tarayıcı HTML5 özelliklerini etkinleştirebilir (örneğin bir takvim denetimi, yerel para birimi simgesi, e-posta bağlantıları, vb. göstermek için)</span><span class="sxs-lookup"><span data-stu-id="418a6-200">The browser can enable HTML5 features (for example to show a calendar control, the locale-appropriate currency symbol, email links, etc.)</span></span>
* <span data-ttu-id="418a6-201">Varsayılan olarak, tarayıcı verileri bulunduğunuz yerin temeline göre doğru biçimi kullanarak işler.</span><span class="sxs-lookup"><span data-stu-id="418a6-201">By default, the browser will render data using the correct format based on your locale.</span></span>
* <span data-ttu-id="418a6-202">Öznitelik, `DataType` ASP.NET Core çerçevesinin verileri işlemek için doğru alan şablonu seçmesini sağlayabilir.</span><span class="sxs-lookup"><span data-stu-id="418a6-202">The `DataType` attribute can enable the ASP.NET Core framework to choose the right field template to render the data.</span></span> <span data-ttu-id="418a6-203">Kendisi `DisplayFormat` tarafından kullanılan if dize şablonu kullanır.</span><span class="sxs-lookup"><span data-stu-id="418a6-203">The `DisplayFormat` if used by itself uses the string template.</span></span>

<span data-ttu-id="418a6-204">Not: jQuery doğrulama öznitelik ile `Range` çalışmıyor `DateTime`ve .</span><span class="sxs-lookup"><span data-stu-id="418a6-204">Note: jQuery validation doesn't work with the `Range` attribute and `DateTime`.</span></span> <span data-ttu-id="418a6-205">Örneğin, tarih belirtilen aralıkta olsa bile, aşağıdaki kod her zaman istemci tarafı doğrulama hatası görüntüler:</span><span class="sxs-lookup"><span data-stu-id="418a6-205">For example, the following code will always display a client-side validation error, even when the date is in the specified range:</span></span>

```csharp
[Range(typeof(DateTime), "1/1/1966", "1/1/2020")]
   ```

<span data-ttu-id="418a6-206">Genellikle modellerinizde zor tarihleri derlemek iyi bir uygulama değildir, `Range` bu nedenle `DateTime` özniteliğini kullanarak ve önerilmez.</span><span class="sxs-lookup"><span data-stu-id="418a6-206">It's generally not a good practice to compile hard dates in your models, so using the `Range` attribute and `DateTime` is discouraged.</span></span>

<span data-ttu-id="418a6-207">Aşağıdaki kod, öznitelikleri tek bir satırda birleştirir:</span><span class="sxs-lookup"><span data-stu-id="418a6-207">The following code shows combining attributes on one line:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDAmult.cs?name=snippet1)]

<span data-ttu-id="418a6-208">[Razor Pages ve EF Core ile başlayın,](xref:data/ef-rp/intro) Razor Pages ile gelişmiş EF Core operasyonlarını gösterir.</span><span class="sxs-lookup"><span data-stu-id="418a6-208">[Get started with Razor Pages and EF Core](xref:data/ef-rp/intro) shows advanced EF Core operations with Razor Pages.</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="418a6-209">Geçişleri uygulama</span><span class="sxs-lookup"><span data-stu-id="418a6-209">Apply migrations</span></span>

<span data-ttu-id="418a6-210">Sınıfa uygulanan DataAnnotations şemayı değiştirir.</span><span class="sxs-lookup"><span data-stu-id="418a6-210">The DataAnnotations applied to the class change the schema.</span></span> <span data-ttu-id="418a6-211">Örneğin, `Title` alana uygulanan DataAnnotations:</span><span class="sxs-lookup"><span data-stu-id="418a6-211">For example, the DataAnnotations applied to the `Title` field:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet11)]

* <span data-ttu-id="418a6-212">Karakterleri 60 ile sınırlar.</span><span class="sxs-lookup"><span data-stu-id="418a6-212">Limits the characters to 60.</span></span>
* <span data-ttu-id="418a6-213">Değer vermiyor. `null`</span><span class="sxs-lookup"><span data-stu-id="418a6-213">Doesn't allow a `null` value.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="418a6-214">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="418a6-214">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="418a6-215">Tablo `Movie` şu anda aşağıdaki şema vardır:</span><span class="sxs-lookup"><span data-stu-id="418a6-215">The `Movie` table currently has the following schema:</span></span>

```sql
CREATE TABLE [dbo].[Movie] (
    [ID]          INT             IDENTITY (1, 1) NOT NULL,
    [Title]       NVARCHAR (MAX)  NULL,
    [ReleaseDate] DATETIME2 (7)   NOT NULL,
    [Genre]       NVARCHAR (MAX)  NULL,
    [Price]       DECIMAL (18, 2) NOT NULL,
    [Rating]      NVARCHAR (MAX)  NULL,
    CONSTRAINT [PK_Movie] PRIMARY KEY CLUSTERED ([ID] ASC)
);
```

<span data-ttu-id="418a6-216">Önceki şema değişiklikleri EF'nin bir özel durum atmasını neden etmez.</span><span class="sxs-lookup"><span data-stu-id="418a6-216">The preceding schema changes don't cause EF to throw an exception.</span></span> <span data-ttu-id="418a6-217">Ancak, şema modeli ile tutarlı olması için bir geçiş oluşturun.</span><span class="sxs-lookup"><span data-stu-id="418a6-217">However, create a migration so the schema is consistent with the model.</span></span>

<span data-ttu-id="418a6-218">**Araçlar** menüsünden **NuGet Paket Yöneticisi > Paket Yöneticisi Konsolu'nu**seçin.</span><span class="sxs-lookup"><span data-stu-id="418a6-218">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
<span data-ttu-id="418a6-219">PMC'de aşağıdaki komutları girin:</span><span class="sxs-lookup"><span data-stu-id="418a6-219">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration New_DataAnnotations
Update-Database
```

<span data-ttu-id="418a6-220">`Update-Database`sınıfın `Up` yöntemlerini `New_DataAnnotations` çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="418a6-220">`Update-Database` runs the `Up` methods of the `New_DataAnnotations` class.</span></span> <span data-ttu-id="418a6-221">Yöntemi `Up` inceleyin:</span><span class="sxs-lookup"><span data-stu-id="418a6-221">Examine the `Up` method:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Migrations/20190724163003_New_DataAnnotations.cs?name=snippet)]

<span data-ttu-id="418a6-222">Güncelleştirilmiş `Movie` tabloda aşağıdaki şema vardır:</span><span class="sxs-lookup"><span data-stu-id="418a6-222">The updated `Movie` table has the following schema:</span></span>

```sql
CREATE TABLE [dbo].[Movie] (
    [ID]          INT             IDENTITY (1, 1) NOT NULL,
    [Title]       NVARCHAR (60)   NOT NULL,
    [ReleaseDate] DATETIME2 (7)   NOT NULL,
    [Genre]       NVARCHAR (30)   NOT NULL,
    [Price]       DECIMAL (18, 2) NOT NULL,
    [Rating]      NVARCHAR (5)    NOT NULL,
    CONSTRAINT [PK_Movie] PRIMARY KEY CLUSTERED ([ID] ASC)
);
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="418a6-223">Mac için Visual Studio Code / Visual Studio</span><span class="sxs-lookup"><span data-stu-id="418a6-223">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="418a6-224">SQLite için geçiş ler gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="418a6-224">Migrations are not required for SQLite.</span></span>

---

### <a name="publish-to-azure"></a><span data-ttu-id="418a6-225">Azure’da Yayımlama</span><span class="sxs-lookup"><span data-stu-id="418a6-225">Publish to Azure</span></span>

<span data-ttu-id="418a6-226">Azure'a dağıtım hakkında daha fazla bilgi için [Bkz. Tutorial: SQL Veritabanı ile Azure'da ASP.NET Core uygulaması oluşturun.](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)</span><span class="sxs-lookup"><span data-stu-id="418a6-226">For information on deploying to Azure, see [Tutorial: Build an ASP.NET Core app in Azure with SQL Database](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb).</span></span>

<span data-ttu-id="418a6-227">Razor Pages bu giriş tamamladığınız için teşekkür ederiz.</span><span class="sxs-lookup"><span data-stu-id="418a6-227">Thanks for completing this introduction to Razor Pages.</span></span> <span data-ttu-id="418a6-228">[Razor Pages ve EF Core ile başlayın](xref:data/ef-rp/intro) bu öğretici için mükemmel bir takip.</span><span class="sxs-lookup"><span data-stu-id="418a6-228">[Get started with Razor Pages and EF Core](xref:data/ef-rp/intro) is an excellent follow up to this tutorial.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="418a6-229">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="418a6-229">Additional resources</span></span>

* <xref:mvc/views/working-with-forms>
* <xref:fundamentals/localization>
* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/views/tag-helpers/authoring>
* [<span data-ttu-id="418a6-230">Bu öğreticinin YouTube sürümü</span><span class="sxs-lookup"><span data-stu-id="418a6-230">YouTube version of this tutorial</span></span>](https://youtu.be/b63m66eu7us)

> [!div class="step-by-step"]
> [<span data-ttu-id="418a6-231">Önceki: Yeni bir alan ekleme</span><span class="sxs-lookup"><span data-stu-id="418a6-231">Previous: Adding a new field</span></span>](xref:tutorials/razor-pages/new-field)
