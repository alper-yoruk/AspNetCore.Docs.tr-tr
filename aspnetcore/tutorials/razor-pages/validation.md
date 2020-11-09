---
title: 8. bölüm, doğrulama ekleme
author: rick-anderson
description: 'Sayfalardaki eğitim serisinin 5. bölümü Razor .'
ms.author: riande
ms.custom: mvc
ms.date: 09/29/2020
no-loc:
- 'Index'
- 'Create'
- 'Delete'
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: tutorials/razor-pages/validation
ms.openlocfilehash: 960e248d6f83b031004e354c98d8637674a403e1
ms.sourcegitcommit: 342588e10ae0054a6d6dc0fd11dae481006be099
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2020
ms.locfileid: "94360701"
---
# <a name="part-8-add-validation-to-an-aspnet-core-no-locrazor-page"></a><span data-ttu-id="ce2f5-103">8. bölüm, ASP.NET Core sayfasına doğrulama ekleme Razor</span><span class="sxs-lookup"><span data-stu-id="ce2f5-103">Part 8, add validation to an ASP.NET Core Razor Page</span></span>

<span data-ttu-id="ce2f5-104">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="ce2f5-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="ce2f5-105">Bu bölümde, doğrulama mantığı `Movie` modele eklenir.</span><span class="sxs-lookup"><span data-stu-id="ce2f5-105">In this section, validation logic is added to the `Movie` model.</span></span> <span data-ttu-id="ce2f5-106">Doğrulama kuralları, bir Kullanıcı bir filmi oluşturduğunda veya düzenleişinizde zorlanır.</span><span class="sxs-lookup"><span data-stu-id="ce2f5-106">The validation rules are enforced any time a user creates or edits a movie.</span></span>

## <a name="validation"></a><span data-ttu-id="ce2f5-107">Doğrulama</span><span class="sxs-lookup"><span data-stu-id="ce2f5-107">Validation</span></span>

<span data-ttu-id="ce2f5-108">Yazılım geliştirmeye yönelik temel bir temel [kuru](https://wikipedia.org/wiki/Don%27t_repeat_yourself) (" **D** on't **R** epeon **Y** ourself") olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="ce2f5-108">A key tenet of software development is called [DRY](https://wikipedia.org/wiki/Don%27t_repeat_yourself) (" **D** on't **R** epeat **Y** ourself").</span></span> <span data-ttu-id="ce2f5-109">Razor Sayfalar, işlevlerin bir kez belirtildiği ve uygulama genelinde yansıtıldığı durumlarda geliştirmeyi teşvik eder.</span><span class="sxs-lookup"><span data-stu-id="ce2f5-109">Razor Pages encourages development where functionality is specified once, and it's reflected throughout the app.</span></span> <span data-ttu-id="ce2f5-110">Kuru şu şekilde yardımcı olabilir:</span><span class="sxs-lookup"><span data-stu-id="ce2f5-110">DRY can help:</span></span>

* <span data-ttu-id="ce2f5-111">Uygulamadaki kod miktarını azaltın.</span><span class="sxs-lookup"><span data-stu-id="ce2f5-111">Reduce the amount of code in an app.</span></span>
* <span data-ttu-id="ce2f5-112">Kodu daha az hata haline getirin ve test ve bakım yapmayı kolaylaştırın.</span><span class="sxs-lookup"><span data-stu-id="ce2f5-112">Make the code less error prone, and easier to test and maintain.</span></span>

<span data-ttu-id="ce2f5-113">Sayfalar ve Entity Framework tarafından sunulan doğrulama desteği, Razor kurutma ilkesine iyi bir örnektir:</span><span class="sxs-lookup"><span data-stu-id="ce2f5-113">The validation support provided by Razor Pages and Entity Framework is a good example of the DRY principle:</span></span>

* <span data-ttu-id="ce2f5-114">Doğrulama kuralları, model sınıfında bildirimli olarak tek bir yerde belirtilir.</span><span class="sxs-lookup"><span data-stu-id="ce2f5-114">Validation rules are declaratively specified in one place, in the model class.</span></span>
* <span data-ttu-id="ce2f5-115">Kurallar uygulamada her yerde zorlanır.</span><span class="sxs-lookup"><span data-stu-id="ce2f5-115">Rules are enforced everywhere in the app.</span></span>

## <a name="add-validation-rules-to-the-movie-model"></a><span data-ttu-id="ce2f5-116">Film modeline doğrulama kuralları ekleme</span><span class="sxs-lookup"><span data-stu-id="ce2f5-116">Add validation rules to the movie model</span></span>

<span data-ttu-id="ce2f5-117">`DataAnnotations`Ad alanı şunları sağlar:</span><span class="sxs-lookup"><span data-stu-id="ce2f5-117">The `DataAnnotations` namespace provides:</span></span>

* <span data-ttu-id="ce2f5-118">Bir sınıfa veya özelliğe bildirimli olarak uygulanan bir yerleşik doğrulama öznitelikleri kümesi.</span><span class="sxs-lookup"><span data-stu-id="ce2f5-118">A set of built-in validation attributes that are applied declaratively to a class or property.</span></span>
* <span data-ttu-id="ce2f5-119">`[DataType]`Biçimlendirme ile yardım ve herhangi bir doğrulama sağlamayan gibi öznitelikleri biçimlendirme.</span><span class="sxs-lookup"><span data-stu-id="ce2f5-119">Formatting attributes like `[DataType]` that help with formatting and don't provide any validation.</span></span>

<span data-ttu-id="ce2f5-120">`Movie`Yerleşik `[Required]` , `[StringLength]` , `[RegularExpression]` ve doğrulama özniteliklerinden yararlanmak için sınıfı güncelleştirin `[Range]` .</span><span class="sxs-lookup"><span data-stu-id="ce2f5-120">Update the `Movie` class to take advantage of the built-in `[Required]`, `[StringLength]`, `[RegularExpression]`, and `[Range]` validation attributes.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet1)]

<span data-ttu-id="ce2f5-121">Doğrulama öznitelikleri, uygulanan model özellikleri üzerinde zorlamak için davranışı belirtir:</span><span class="sxs-lookup"><span data-stu-id="ce2f5-121">The validation attributes specify behavior to enforce on the model properties they're applied to:</span></span>

* <span data-ttu-id="ce2f5-122">`[Required]`Ve `[MinimumLength]` öznitelikleri bir özelliğin bir değere sahip olması gerektiğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="ce2f5-122">The `[Required]` and `[MinimumLength]` attributes indicate that a property must have a value.</span></span> <span data-ttu-id="ce2f5-123">Hiçbir şey, kullanıcının bu doğrulamayı karşılamak için boşluk girmesini engeller.</span><span class="sxs-lookup"><span data-stu-id="ce2f5-123">Nothing prevents a user from entering white space to satisfy this validation.</span></span>
* <span data-ttu-id="ce2f5-124">`[RegularExpression]`Öznitelik, hangi karakterlerin girişi yapabileceğini sınırlamak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ce2f5-124">The `[RegularExpression]` attribute is used to limit what characters can be input.</span></span> <span data-ttu-id="ce2f5-125">Yukarıdaki kodda `Genre` :</span><span class="sxs-lookup"><span data-stu-id="ce2f5-125">In the preceding code, `Genre`:</span></span>

  * <span data-ttu-id="ce2f5-126">Yalnızca harfler kullanılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="ce2f5-126">Must only use letters.</span></span>
  * <span data-ttu-id="ce2f5-127">İlk harfin büyük harfle olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="ce2f5-127">The first letter is required to be uppercase.</span></span> <span data-ttu-id="ce2f5-128">Boşluk, sayı ve özel karakterlere izin verilmez.</span><span class="sxs-lookup"><span data-stu-id="ce2f5-128">White space, numbers, and special characters are not allowed.</span></span>

* <span data-ttu-id="ce2f5-129">`RegularExpression` `Rating` Şunları yapın:</span><span class="sxs-lookup"><span data-stu-id="ce2f5-129">The `RegularExpression` `Rating`:</span></span>

  * <span data-ttu-id="ce2f5-130">İlk karakterin büyük harf olmasını gerektirir.</span><span class="sxs-lookup"><span data-stu-id="ce2f5-130">Requires that the first character be an uppercase letter.</span></span>
  * <span data-ttu-id="ce2f5-131">Sonraki boşlukların içindeki özel karakter ve sayılara izin verir.</span><span class="sxs-lookup"><span data-stu-id="ce2f5-131">Allows special characters and numbers in subsequent spaces.</span></span> <span data-ttu-id="ce2f5-132">"PG-13" bir derecelendirme için geçerlidir, ancak için başarısız olur `Genre` .</span><span class="sxs-lookup"><span data-stu-id="ce2f5-132">"PG-13" is valid for a rating, but fails for a `Genre`.</span></span>

* <span data-ttu-id="ce2f5-133">`[Range]` özniteliği, bir değeri belirtilen bir aralık içinde kısıtlar.</span><span class="sxs-lookup"><span data-stu-id="ce2f5-133">The `[Range]` attribute constrains a value to within a specified range.</span></span>
* <span data-ttu-id="ce2f5-134">`[StringLength]`Özniteliği bir dize özelliğinin en büyük uzunluğunu ve isteğe bağlı olarak en düşük uzunluğunu ayarlayabilir.</span><span class="sxs-lookup"><span data-stu-id="ce2f5-134">The `[StringLength]` attribute can set a maximum length of a string property, and optionally its minimum length.</span></span>
* <span data-ttu-id="ce2f5-135">,,,,,,,,,,,,,,,,,, Gibi değer türleri, `decimal` `int` `float` `DateTime` `[Required]`</span><span class="sxs-lookup"><span data-stu-id="ce2f5-135">Value types, such as `decimal`, `int`, `float`, `DateTime`, are inherently required and don't need the `[Required]` attribute.</span></span>

<span data-ttu-id="ce2f5-136">Yukarıdaki doğrulama kuralları tanıtım için kullanılır, bunlar bir üretim sistemi için en uygun değildir.</span><span class="sxs-lookup"><span data-stu-id="ce2f5-136">The preceding validation rules are used for demonstration, they are not optimal for a production system.</span></span> <span data-ttu-id="ce2f5-137">Örneğin, önceki bir filmi yalnızca iki karakter içeren bir filmi girmeyi önler ve içinde özel karakterlere izin vermez `Genre` .</span><span class="sxs-lookup"><span data-stu-id="ce2f5-137">For example, the preceding prevents entering a movie with only two chars and doesn't allow special characters in `Genre`.</span></span>

<span data-ttu-id="ce2f5-138">Doğrulama kurallarının ASP.NET Core tarafından otomatik olarak zorlanmasına yardımcı olur:</span><span class="sxs-lookup"><span data-stu-id="ce2f5-138">Having validation rules automatically enforced by ASP.NET Core helps:</span></span>

* <span data-ttu-id="ce2f5-139">Uygulamanın daha sağlam olmasına yardımcı olur.</span><span class="sxs-lookup"><span data-stu-id="ce2f5-139">Helps make the app more robust.</span></span>
* <span data-ttu-id="ce2f5-140">Veritabanına geçersiz veri kaydetme olasılığını azaltın.</span><span class="sxs-lookup"><span data-stu-id="ce2f5-140">Reduce chances of saving invalid data to the database.</span></span>

### <a name="validation-error-ui-in-no-locrazor-pages"></a><span data-ttu-id="ce2f5-141">Sayfalarda doğrulama hatası Kullanıcı arabirimi Razor</span><span class="sxs-lookup"><span data-stu-id="ce2f5-141">Validation Error UI in Razor Pages</span></span>

<span data-ttu-id="ce2f5-142">Uygulamayı çalıştırın ve sayfalar/Filmler ' e gidin.</span><span class="sxs-lookup"><span data-stu-id="ce2f5-142">Run the app and navigate to Pages/Movies.</span></span>

<span data-ttu-id="ce2f5-143">**Create Yeni** bağlantıyı seçin.</span><span class="sxs-lookup"><span data-stu-id="ce2f5-143">Select the **Create New** link.</span></span> <span data-ttu-id="ce2f5-144">Formu, bazı geçersiz değerlerle doldurun.</span><span class="sxs-lookup"><span data-stu-id="ce2f5-144">Complete the form with some invalid values.</span></span> <span data-ttu-id="ce2f5-145">JQuery istemci tarafı doğrulaması hatayı algıladığında, bir hata iletisi görüntüler.</span><span class="sxs-lookup"><span data-stu-id="ce2f5-145">When jQuery client-side validation detects the error, it displays an error message.</span></span>

![Birden çok jQuery istemci tarafı doğrulama hatası içeren film görünümü formu](validation/_static/val.png)

[!INCLUDE[](~/includes/localization/currency.md)]

<span data-ttu-id="ce2f5-147">Formun geçersiz bir değer içeren her alanda otomatik olarak bir doğrulama hata iletisi nasıl oluşturulduğuna dikkat edin.</span><span class="sxs-lookup"><span data-stu-id="ce2f5-147">Notice how the form has automatically rendered a validation error message in each field containing an invalid value.</span></span> <span data-ttu-id="ce2f5-148">Hatalar, Kullanıcı JavaScript devre dışı bırakıldığında, JavaScript ve jQuery kullanılarak istemci tarafı ve sunucu tarafı ile zorlanır.</span><span class="sxs-lookup"><span data-stu-id="ce2f5-148">The errors are enforced both client-side, using JavaScript and jQuery, and server-side, when a user has JavaScript disabled.</span></span>

<span data-ttu-id="ce2f5-149">Önemli bir avantaj, ya da düzenleme sayfalarında **hiçbir** kod değişikliği gerekli değildir Create .</span><span class="sxs-lookup"><span data-stu-id="ce2f5-149">A significant benefit is that **no** code changes were necessary in the Create or Edit pages.</span></span> <span data-ttu-id="ce2f5-150">Veri ek açıklamaları modele uygulandıktan sonra, doğrulama kullanıcı arabirimi etkinleştirilmiştir.</span><span class="sxs-lookup"><span data-stu-id="ce2f5-150">Once data annotations were applied to the model, the validation UI was enabled.</span></span> <span data-ttu-id="ce2f5-151">RazorBu öğreticide oluşturulan sayfalar, model sınıfının özelliklerindeki doğrulama özniteliklerini kullanarak doğrulama kurallarını otomatik olarak çekti `Movie` .</span><span class="sxs-lookup"><span data-stu-id="ce2f5-151">The Razor Pages created in this tutorial automatically picked up the validation rules, using validation attributes on the properties of the `Movie` model class.</span></span> <span data-ttu-id="ce2f5-152">Düzenleme sayfasını kullanarak doğrulama testi, aynı doğrulama uygulanır.</span><span class="sxs-lookup"><span data-stu-id="ce2f5-152">Test validation using the Edit page, the same validation is applied.</span></span>

<span data-ttu-id="ce2f5-153">Form verileri, istemci tarafı doğrulama hatası kalmayana kadar sunucuya nakledilmez.</span><span class="sxs-lookup"><span data-stu-id="ce2f5-153">The form data isn't posted to the server until there are no client-side validation errors.</span></span> <span data-ttu-id="ce2f5-154">Form verilerinin aşağıdaki yaklaşımlardan bir veya daha fazlası tarafından nakledilmediğinden emin olun:</span><span class="sxs-lookup"><span data-stu-id="ce2f5-154">Verify form data isn't posted by one or more of the following approaches:</span></span>

* <span data-ttu-id="ce2f5-155">Yöntemine bir kesme noktası koyun `OnPostAsync` .</span><span class="sxs-lookup"><span data-stu-id="ce2f5-155">Put a break point in the `OnPostAsync` method.</span></span> <span data-ttu-id="ce2f5-156">Seçerek veya kaydederek formu gönderebilirsiniz **Create** . **Save**</span><span class="sxs-lookup"><span data-stu-id="ce2f5-156">Submit the form by selecting **Create** or **Save**.</span></span> <span data-ttu-id="ce2f5-157">Kesme noktası hiçbir şekilde isabet ettirilmez.</span><span class="sxs-lookup"><span data-stu-id="ce2f5-157">The break point is never hit.</span></span>
* <span data-ttu-id="ce2f5-158">[Fiddler aracını](https://www.telerik.com/fiddler)kullanın.</span><span class="sxs-lookup"><span data-stu-id="ce2f5-158">Use the [Fiddler tool](https://www.telerik.com/fiddler).</span></span>
* <span data-ttu-id="ce2f5-159">Ağ trafiğini izlemek için tarayıcı Geliştirici Araçları ' nı kullanın.</span><span class="sxs-lookup"><span data-stu-id="ce2f5-159">Use the browser developer tools to monitor network traffic.</span></span>

### <a name="server-side-validation"></a><span data-ttu-id="ce2f5-160">Sunucu tarafı doğrulaması</span><span class="sxs-lookup"><span data-stu-id="ce2f5-160">Server-side validation</span></span>

<span data-ttu-id="ce2f5-161">Tarayıcıda JavaScript devre dışı bırakıldığında, formun hatalarla gönderilmesi sunucuya gönderilir.</span><span class="sxs-lookup"><span data-stu-id="ce2f5-161">When JavaScript is disabled in the browser, submitting the form with errors will post to the server.</span></span>

<span data-ttu-id="ce2f5-162">İsteğe bağlı, test sunucusu-tarafı doğrulaması:</span><span class="sxs-lookup"><span data-stu-id="ce2f5-162">Optional, test server-side validation:</span></span>

1. <span data-ttu-id="ce2f5-163">Tarayıcıda JavaScript 'ı devre dışı bırakın.</span><span class="sxs-lookup"><span data-stu-id="ce2f5-163">Disable JavaScript in the browser.</span></span> <span data-ttu-id="ce2f5-164">JavaScript tarayıcı geliştirici araçları kullanılarak devre dışı bırakılabilir.</span><span class="sxs-lookup"><span data-stu-id="ce2f5-164">JavaScript can be disabled using browser's developer tools.</span></span> <span data-ttu-id="ce2f5-165">Tarayıcıda JavaScript devre dışı bırakılamaz, başka bir tarayıcı deneyin.</span><span class="sxs-lookup"><span data-stu-id="ce2f5-165">If JavaScript cannot be disabled in the browser, try another browser.</span></span>
1. <span data-ttu-id="ce2f5-166">`OnPostAsync`Veya düzenleme sayfasının yönteminde bir kesme noktası ayarlayın Create .</span><span class="sxs-lookup"><span data-stu-id="ce2f5-166">Set a break point in the `OnPostAsync` method of the Create or Edit page.</span></span>
1. <span data-ttu-id="ce2f5-167">Geçersiz verilerle form gönderme.</span><span class="sxs-lookup"><span data-stu-id="ce2f5-167">Submit a form with invalid data.</span></span>
1. <span data-ttu-id="ce2f5-168">Model durumunun geçersiz olduğunu doğrulayın:</span><span class="sxs-lookup"><span data-stu-id="ce2f5-168">Verify the model state is invalid:</span></span>

   ```csharp
    if (!ModelState.IsValid)
    {
       return Page();
    }
   ```
  
<span data-ttu-id="ce2f5-169">Alternatif olarak, [sunucuda istemci tarafı doğrulamayı devre dışı bırakın](xref:mvc/models/validation#disable-client-side-validation).</span><span class="sxs-lookup"><span data-stu-id="ce2f5-169">Alternatively, [Disable client-side validation on the server](xref:mvc/models/validation#disable-client-side-validation).</span></span>

<span data-ttu-id="ce2f5-170">Aşağıdaki kod, öğreticide daha önce *Create . cshtml* sayfa scafkatın bir bölümünü gösterir.</span><span class="sxs-lookup"><span data-stu-id="ce2f5-170">The following code shows a portion of the *Create.cshtml* page scaffolded earlier in the tutorial.</span></span> <span data-ttu-id="ce2f5-171">Bu, Create ve düzenleme sayfaları tarafından şu şekilde kullanılır:</span><span class="sxs-lookup"><span data-stu-id="ce2f5-171">It's used by the Create and Edit pages to:</span></span>

* <span data-ttu-id="ce2f5-172">Başlangıç formunu görüntüleyin.</span><span class="sxs-lookup"><span data-stu-id="ce2f5-172">Display the initial form.</span></span>
* <span data-ttu-id="ce2f5-173">Hata durumunda formu yeniden görüntüleyin.</span><span class="sxs-lookup"><span data-stu-id="ce2f5-173">Redisplay the form in the event of an error.</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Create.cshtml?range=14-20)]

<span data-ttu-id="ce2f5-174">[Giriş etiketi Yardımcısı](xref:mvc/views/working-with-forms) , [dataaçıklamaların](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) özniteliklerini kullanır ve istemci tarafında jQuery doğrulaması için gerekli HTML özniteliklerini üretir.</span><span class="sxs-lookup"><span data-stu-id="ce2f5-174">The [Input Tag Helper](xref:mvc/views/working-with-forms) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span> <span data-ttu-id="ce2f5-175">[Doğrulama etiketi Yardımcısı](xref:mvc/views/working-with-forms#the-validation-tag-helpers) doğrulama hatalarını görüntüler.</span><span class="sxs-lookup"><span data-stu-id="ce2f5-175">The [Validation Tag Helper](xref:mvc/views/working-with-forms#the-validation-tag-helpers) displays validation errors.</span></span> <span data-ttu-id="ce2f5-176">Daha fazla bilgi için bkz. [doğrulama](xref:mvc/models/validation) .</span><span class="sxs-lookup"><span data-stu-id="ce2f5-176">See [Validation](xref:mvc/models/validation) for more information.</span></span>

<span data-ttu-id="ce2f5-177">CreateVe düzenleme sayfalarında hiçbir doğrulama kuralı yoktur.</span><span class="sxs-lookup"><span data-stu-id="ce2f5-177">The Create and Edit pages have no validation rules in them.</span></span> <span data-ttu-id="ce2f5-178">Doğrulama kuralları ve hata dizeleri yalnızca `Movie` sınıfında belirtilmiştir.</span><span class="sxs-lookup"><span data-stu-id="ce2f5-178">The validation rules and the error strings are specified only in the `Movie` class.</span></span> <span data-ttu-id="ce2f5-179">Bu doğrulama kuralları Razor , modeli düzenlediğiniz sayfalara otomatik olarak uygulanır `Movie` .</span><span class="sxs-lookup"><span data-stu-id="ce2f5-179">These validation rules are automatically applied to Razor Pages that edit the `Movie` model.</span></span>

<span data-ttu-id="ce2f5-180">Doğrulama mantığının değişmesi gerektiğinde, yalnızca modelde yapılır.</span><span class="sxs-lookup"><span data-stu-id="ce2f5-180">When validation logic needs to change, it's done only in the model.</span></span> <span data-ttu-id="ce2f5-181">Doğrulama, uygulamanın tamamında tutarlı bir şekilde uygulanır, doğrulama mantığı tek bir yerde tanımlanır.</span><span class="sxs-lookup"><span data-stu-id="ce2f5-181">Validation is applied consistently throughout the application, validation logic is defined in one place.</span></span> <span data-ttu-id="ce2f5-182">Tek bir yerde doğrulama, kodun temiz kalmasına yardımcı olur ve bakım ve güncelleştirme işlemlerini kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="ce2f5-182">Validation in one place helps keep the code clean, and makes it easier to maintain and update.</span></span>

## <a name="use-datatype-attributes"></a><span data-ttu-id="ce2f5-183">DataType özniteliklerini kullan</span><span class="sxs-lookup"><span data-stu-id="ce2f5-183">Use DataType Attributes</span></span>

<span data-ttu-id="ce2f5-184">Sınıfını inceleyin `Movie` .</span><span class="sxs-lookup"><span data-stu-id="ce2f5-184">Examine the `Movie` class.</span></span> <span data-ttu-id="ce2f5-185">`System.ComponentModel.DataAnnotations`Ad alanı, yerleşik doğrulama öznitelikleri kümesine ek olarak biçimlendirme öznitelikleri sağlar.</span><span class="sxs-lookup"><span data-stu-id="ce2f5-185">The `System.ComponentModel.DataAnnotations` namespace provides formatting attributes in addition to the built-in set of validation attributes.</span></span> <span data-ttu-id="ce2f5-186">`[DataType]` özniteliği `ReleaseDate` ve `Price` özelliklerine uygulanır.</span><span class="sxs-lookup"><span data-stu-id="ce2f5-186">The `[DataType]` attribute is applied to the `ReleaseDate` and `Price` properties.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie/Models/MovieDateRatingDA.cs?highlight=2,6&name=snippet2)]

<span data-ttu-id="ce2f5-187">`[DataType]`Öznitelikleri şunları sağlar:</span><span class="sxs-lookup"><span data-stu-id="ce2f5-187">The `[DataType]` attributes provide:</span></span>

* <span data-ttu-id="ce2f5-188">Görüntüleme altyapısının verileri biçimlendirmek için ipuçları.</span><span class="sxs-lookup"><span data-stu-id="ce2f5-188">Hints for the view engine to format the data.</span></span>
* <span data-ttu-id="ce2f5-189">`<a>`URL 'ler ve e-posta için gibi öznitelikleri sağlar `<a href="mailto:EmailAddress.com">` .</span><span class="sxs-lookup"><span data-stu-id="ce2f5-189">Supplies attributes such as `<a>` for URL's and `<a href="mailto:EmailAddress.com">` for email.</span></span>

<span data-ttu-id="ce2f5-190">`[RegularExpression]`Veri biçimini doğrulamak için özniteliğini kullanın.</span><span class="sxs-lookup"><span data-stu-id="ce2f5-190">Use the `[RegularExpression]` attribute to validate the format of the data.</span></span> <span data-ttu-id="ce2f5-191">`[DataType]`Özniteliği, veritabanı iç türünden daha belirgin bir veri türü belirtmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ce2f5-191">The `[DataType]` attribute is used to specify a data type that's more specific than the database intrinsic type.</span></span> <span data-ttu-id="ce2f5-192">`[DataType]` öznitelikler doğrulama öznitelikleri değildir.</span><span class="sxs-lookup"><span data-stu-id="ce2f5-192">`[DataType]` attributes aren't validation attributes.</span></span> <span data-ttu-id="ce2f5-193">Örnek uygulamada, yalnızca tarih ve saat olmadan görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="ce2f5-193">In the sample application, only the date is displayed, without time.</span></span>

<span data-ttu-id="ce2f5-194">`DataType`Sabit listesi,,,, ve gibi birçok veri türü sağlar `Date` `Time` `PhoneNumber` `Currency` `EmailAddress` .</span><span class="sxs-lookup"><span data-stu-id="ce2f5-194">The `DataType` enumeration provides many data types, such as `Date`, `Time`, `PhoneNumber`, `Currency`, `EmailAddress`, and more.</span></span> 

<span data-ttu-id="ce2f5-195">`[DataType]`Öznitelikler:</span><span class="sxs-lookup"><span data-stu-id="ce2f5-195">The `[DataType]` attributes:</span></span>

* <span data-ttu-id="ce2f5-196">Uygulamanın, türe özgü özellikleri otomatik olarak sağlamasına olanak sağlayabilir.</span><span class="sxs-lookup"><span data-stu-id="ce2f5-196">Can enable the application to automatically provide type-specific features.</span></span> <span data-ttu-id="ce2f5-197">Örneğin, için bir `mailto:` bağlantı oluşturulabilir `DataType.EmailAddress` .</span><span class="sxs-lookup"><span data-stu-id="ce2f5-197">For example, a `mailto:` link can be created for `DataType.EmailAddress`.</span></span>
* <span data-ttu-id="ce2f5-198">`DataType.Date`HTML5 'i destekleyen tarayıcılarda bir tarih seçici sağlayabilir.</span><span class="sxs-lookup"><span data-stu-id="ce2f5-198">Can provide a date selector `DataType.Date` in browsers that support HTML5.</span></span>
* <span data-ttu-id="ce2f5-199">HTML 5 `data-` tarayıcıların kullandığı "veri Dash" özniteliği olan HTML 5 ' i yayın.</span><span class="sxs-lookup"><span data-stu-id="ce2f5-199">Emit HTML 5 `data-`, pronounced "data dash", attributes that HTML 5 browsers consume.</span></span>
* <span data-ttu-id="ce2f5-200">Herhangi bir **doğrulama sağlamamayın** .</span><span class="sxs-lookup"><span data-stu-id="ce2f5-200">Do **not** provide any validation.</span></span>

<span data-ttu-id="ce2f5-201">`DataType.Date` görüntülenen tarihin biçimini belirtmez.</span><span class="sxs-lookup"><span data-stu-id="ce2f5-201">`DataType.Date` doesn't specify the format of the date that's displayed.</span></span> <span data-ttu-id="ce2f5-202">Varsayılan olarak, veri alanı sunucu ' a göre varsayılan biçimlere göre görüntülenir `CultureInfo` .</span><span class="sxs-lookup"><span data-stu-id="ce2f5-202">By default, the data field is displayed according to the default formats based on the server's `CultureInfo`.</span></span>

<span data-ttu-id="ce2f5-203">`[Column(TypeName = "decimal(18, 2)")]`Entity Framework Core veri ek açıklaması gerekir, bu nedenle `Price` veritabanında para birimiyle doğru şekilde eşleşebilirler.</span><span class="sxs-lookup"><span data-stu-id="ce2f5-203">The `[Column(TypeName = "decimal(18, 2)")]` data annotation is required so Entity Framework Core can correctly map `Price` to currency in the database.</span></span> <span data-ttu-id="ce2f5-204">Daha fazla bilgi için bkz. [veri türleri](/ef/core/modeling/relational/data-types).</span><span class="sxs-lookup"><span data-stu-id="ce2f5-204">For more information, see [Data Types](/ef/core/modeling/relational/data-types).</span></span>

<span data-ttu-id="ce2f5-205">`[DisplayFormat]`Öznitelik, tarih biçimini açıkça belirtmek için kullanılır:</span><span class="sxs-lookup"><span data-stu-id="ce2f5-205">The `[DisplayFormat]` attribute is used to explicitly specify the date format:</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
public DateTime ReleaseDate { get; set; }
```

<span data-ttu-id="ce2f5-206">`ApplyFormatInEditMode`Ayar, değer düzenlenmek üzere görüntülendiğinde biçimlendirmenin uygulanacağını belirtir.</span><span class="sxs-lookup"><span data-stu-id="ce2f5-206">The `ApplyFormatInEditMode` setting specifies that the formatting will be applied when the value is displayed for editing.</span></span> <span data-ttu-id="ce2f5-207">Bu davranış bazı alanlar için istenmeyebilir.</span><span class="sxs-lookup"><span data-stu-id="ce2f5-207">That behavior may not be wanted for some fields.</span></span> <span data-ttu-id="ce2f5-208">Örneğin, para birimi değerlerinde, para birimi simgesi genellikle düzenleme Kullanıcı arabiriminde istenmez.</span><span class="sxs-lookup"><span data-stu-id="ce2f5-208">For example, in currency values, the currency symbol is usually not wanted in the edit UI.</span></span>

<span data-ttu-id="ce2f5-209">`[DisplayFormat]`Özniteliği kendisi tarafından kullanılabilir, ancak genellikle özniteliği kullanmak iyi bir fikir olabilir `[DataType]` .</span><span class="sxs-lookup"><span data-stu-id="ce2f5-209">The `[DisplayFormat]` attribute can be used by itself, but it's generally a good idea to use the `[DataType]` attribute.</span></span> <span data-ttu-id="ce2f5-210">`[DataType]`Özniteliği, bir ekranda nasıl işleneceğini değil, verilerin semantiğini alır.</span><span class="sxs-lookup"><span data-stu-id="ce2f5-210">The `[DataType]` attribute conveys the semantics of the data as opposed to how to render it on a screen.</span></span> <span data-ttu-id="ce2f5-211">`[DataType]`Özniteliği, ile kullanılamayan aşağıdaki avantajları sağlar `[DisplayFormat]` :</span><span class="sxs-lookup"><span data-stu-id="ce2f5-211">The `[DataType]` attribute provides the following benefits that aren't available with `[DisplayFormat]`:</span></span>

* <span data-ttu-id="ce2f5-212">Tarayıcı HTML5 özelliklerini etkinleştirerek, örneğin bir Takvim denetimini, yerel ayara uygun para birimi sembolünü, e-posta bağlantılarını vb. göstermek için kullanabilir.</span><span class="sxs-lookup"><span data-stu-id="ce2f5-212">The browser can enable HTML5 features, for example to show a calendar control, the locale-appropriate currency symbol, email links, etc.</span></span>
* <span data-ttu-id="ce2f5-213">Varsayılan olarak tarayıcı, verileri yerel ayarlarına göre doğru biçimi kullanarak işler.</span><span class="sxs-lookup"><span data-stu-id="ce2f5-213">By default, the browser renders data using the correct format based on its locale.</span></span>
* <span data-ttu-id="ce2f5-214">`[DataType]`Öznitelik, ASP.NET Core çerçevesinin verileri işlemek için doğru alan şablonunu seçmesini sağlayabilir.</span><span class="sxs-lookup"><span data-stu-id="ce2f5-214">The `[DataType]` attribute can enable the ASP.NET Core framework to choose the right field template to render the data.</span></span> <span data-ttu-id="ce2f5-215">`DisplayFormat`Kendisi tarafından kullanılıyorsa, dize şablonunu kullanır.</span><span class="sxs-lookup"><span data-stu-id="ce2f5-215">The `DisplayFormat`, if used by itself, uses the string template.</span></span>

<span data-ttu-id="ce2f5-216">**Note:** jQuery doğrulaması, `[Range]` ve özniteliğiyle çalışmaz `DateTime` .</span><span class="sxs-lookup"><span data-stu-id="ce2f5-216">**Note:** jQuery validation doesn't work with the `[Range]` attribute and `DateTime`.</span></span> <span data-ttu-id="ce2f5-217">Örneğin, aşağıdaki kod, tarih belirtilen aralıkta olduğunda bile her zaman bir istemci tarafı doğrulama hatası görüntüler:</span><span class="sxs-lookup"><span data-stu-id="ce2f5-217">For example, the following code will always display a client-side validation error, even when the date is in the specified range:</span></span>

```csharp
[Range(typeof(DateTime), "1/1/1966", "1/1/2020")]
   ```

<span data-ttu-id="ce2f5-218">Modellerdeki sabit tarihleri dermaktan kaçınmak en iyi uygulamadır, bu nedenle `[Range]` özniteliği `DateTime` kullanılır ve önerilmez.</span><span class="sxs-lookup"><span data-stu-id="ce2f5-218">It's a best practice to avoid compiling hard dates in models, so using the `[Range]` attribute and `DateTime` is discouraged.</span></span> <span data-ttu-id="ce2f5-219">Tarih aralıkları ve kodda belirtmek yerine sık değişikliğe tabi diğer değerler için [yapılandırma](xref:fundamentals/configuration/index) kullanın.</span><span class="sxs-lookup"><span data-stu-id="ce2f5-219">Use [Configuration](xref:fundamentals/configuration/index) for date ranges and other values that are subject to frequent change rather than specifying it in code.</span></span>

<span data-ttu-id="ce2f5-220">Aşağıdaki kod, öznitelikleri tek bir satırda birleştirmeyi gösterir:</span><span class="sxs-lookup"><span data-stu-id="ce2f5-220">The following code shows combining attributes on one line:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDAmult.cs?name=snippet1)]

<span data-ttu-id="ce2f5-221">[Kullanmaya Razor başlayın Sayfalar ve EF Core](xref:data/ef-rp/intro) sayfalar ile gelişmiş EF Core işlemlerini gösterir Razor .</span><span class="sxs-lookup"><span data-stu-id="ce2f5-221">[Get started with Razor Pages and EF Core](xref:data/ef-rp/intro) shows advanced EF Core operations with Razor Pages.</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="ce2f5-222">Geçişleri Uygula</span><span class="sxs-lookup"><span data-stu-id="ce2f5-222">Apply migrations</span></span>

<span data-ttu-id="ce2f5-223">Sınıfa uygulanan Datanot açıklamaları şemayı değiştirir.</span><span class="sxs-lookup"><span data-stu-id="ce2f5-223">The DataAnnotations applied to the class changes the schema.</span></span> <span data-ttu-id="ce2f5-224">Örneğin, alanına uygulanan veri ek açıklamaları `Title` :</span><span class="sxs-lookup"><span data-stu-id="ce2f5-224">For example, the DataAnnotations applied to the `Title` field:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet11)]

* <span data-ttu-id="ce2f5-225">Karakterleri 60 olarak sınırlandırır.</span><span class="sxs-lookup"><span data-stu-id="ce2f5-225">Limits the characters to 60.</span></span>
* <span data-ttu-id="ce2f5-226">Bir değere izin vermez `null` .</span><span class="sxs-lookup"><span data-stu-id="ce2f5-226">Doesn't allow a `null` value.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ce2f5-227">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ce2f5-227">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ce2f5-228">`Movie`Tabloda Şu anda aşağıdaki şema vardır:</span><span class="sxs-lookup"><span data-stu-id="ce2f5-228">The `Movie` table currently has the following schema:</span></span>

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

<span data-ttu-id="ce2f5-229">Önceki şema değişiklikleri, EF 'in özel durum oluşturmasına neden olmaz.</span><span class="sxs-lookup"><span data-stu-id="ce2f5-229">The preceding schema changes don't cause EF to throw an exception.</span></span> <span data-ttu-id="ce2f5-230">Ancak, şemanın modelle tutarlı olması için bir geçiş oluşturun.</span><span class="sxs-lookup"><span data-stu-id="ce2f5-230">However, create a migration so the schema is consistent with the model.</span></span>

<span data-ttu-id="ce2f5-231">**Araçlar** menüsünde **NuGet Paket Yöneticisi > Paket Yöneticisi konsolu** ' nu seçin.</span><span class="sxs-lookup"><span data-stu-id="ce2f5-231">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
<span data-ttu-id="ce2f5-232">PMC 'de aşağıdaki komutları girin:</span><span class="sxs-lookup"><span data-stu-id="ce2f5-232">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration New_DataAnnotations
Update-Database
```

<span data-ttu-id="ce2f5-233">`Update-Database``Up`sınıfının yöntemlerini çalıştırır `New_DataAnnotations` .</span><span class="sxs-lookup"><span data-stu-id="ce2f5-233">`Update-Database` runs the `Up` methods of the `New_DataAnnotations` class.</span></span> <span data-ttu-id="ce2f5-234">`Up` yöntemini inceleyin:</span><span class="sxs-lookup"><span data-stu-id="ce2f5-234">Examine the `Up` method:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Migrations/20190724163003_New_DataAnnotations.cs?name=snippet)]

<span data-ttu-id="ce2f5-235">Güncelleştirilmiş `Movie` tablo aşağıdaki şemaya sahiptir:</span><span class="sxs-lookup"><span data-stu-id="ce2f5-235">The updated `Movie` table has the following schema:</span></span>

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

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="ce2f5-236">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ce2f5-236">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="ce2f5-237">SQLite için geçişler gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="ce2f5-237">Migrations are not required for SQLite.</span></span>

---

### <a name="publish-to-azure"></a><span data-ttu-id="ce2f5-238">Azure’da Yayımlama</span><span class="sxs-lookup"><span data-stu-id="ce2f5-238">Publish to Azure</span></span>

<span data-ttu-id="ce2f5-239">Azure 'a dağıtma hakkında bilgi için bkz. [öğretici: Azure 'DA SQL veritabanı ile ASP.NET Core uygulama oluşturma](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb).</span><span class="sxs-lookup"><span data-stu-id="ce2f5-239">For information on deploying to Azure, see [Tutorial: Build an ASP.NET Core app in Azure with SQL Database](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb).</span></span>

<span data-ttu-id="ce2f5-240">Bu sayfalara giriş tamamlanırken teşekkürler Razor .</span><span class="sxs-lookup"><span data-stu-id="ce2f5-240">Thanks for completing this introduction to Razor Pages.</span></span> <span data-ttu-id="ce2f5-241">[Kullanmaya Razor başlayın Sayfalar ve EF Core](xref:data/ef-rp/intro) Bu öğreticiye harika bir izdir.</span><span class="sxs-lookup"><span data-stu-id="ce2f5-241">[Get started with Razor Pages and EF Core](xref:data/ef-rp/intro) is an excellent follow up to this tutorial.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ce2f5-242">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="ce2f5-242">Additional resources</span></span>

* <xref:mvc/views/working-with-forms>
* <xref:fundamentals/localization>
* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/views/tag-helpers/authoring>

> [!div class="step-by-step"]
> [<span data-ttu-id="ce2f5-243">Önceki: yeni bir alan ekleyin</span><span class="sxs-lookup"><span data-stu-id="ce2f5-243">Previous: Add a new field</span></span>](xref:tutorials/razor-pages/new-field)
