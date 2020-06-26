---
title: ASP.NET Core MVC 'de model doğrulaması
author: rick-anderson
description: ASP.NET Core MVC ve sayfalarında model doğrulama hakkında bilgi edinin Razor .
ms.author: riande
ms.custom: mvc
ms.date: 12/15/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/models/validation
ms.openlocfilehash: e76ddad795237991c49bf2a3b1ca840795989260
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85404918"
---
# <a name="model-validation-in-aspnet-core-mvc-and-razor-pages"></a><span data-ttu-id="f9edf-103">ASP.NET Core MVC ve sayfalarda model doğrulaması Razor</span><span class="sxs-lookup"><span data-stu-id="f9edf-103">Model validation in ASP.NET Core MVC and Razor Pages</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f9edf-104">[Kirk Larkaya](https://github.com/serpent5) göre</span><span class="sxs-lookup"><span data-stu-id="f9edf-104">By [Kirk Larkin](https://github.com/serpent5)</span></span>

<span data-ttu-id="f9edf-105">Bu makalede, ASP.NET Core MVC veya Pages uygulamasında Kullanıcı girişinin nasıl doğrulanacağı açıklanır Razor .</span><span class="sxs-lookup"><span data-stu-id="f9edf-105">This article explains how to validate user input in an ASP.NET Core MVC or Razor Pages app.</span></span>

<span data-ttu-id="f9edf-106">[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/samples) ([nasıl indirilir](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="f9edf-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="model-state"></a><span data-ttu-id="f9edf-107">Model durumu</span><span class="sxs-lookup"><span data-stu-id="f9edf-107">Model state</span></span>

<span data-ttu-id="f9edf-108">Model durumu iki alt sistemden gelen hataları temsil eder: model bağlama ve model doğrulama.</span><span class="sxs-lookup"><span data-stu-id="f9edf-108">Model state represents errors that come from two subsystems: model binding and model validation.</span></span> <span data-ttu-id="f9edf-109">[Model bağlamasından](model-binding.md) kaynaklanan hatalar genellikle veri dönüştürme hatalardır.</span><span class="sxs-lookup"><span data-stu-id="f9edf-109">Errors that originate from [model binding](model-binding.md) are generally data conversion errors.</span></span> <span data-ttu-id="f9edf-110">Örneğin, bir tamsayı alanına bir "x" girilir.</span><span class="sxs-lookup"><span data-stu-id="f9edf-110">For example, an "x" is entered in an integer field.</span></span> <span data-ttu-id="f9edf-111">Model bağlama sonrasında model doğrulaması oluşur ve verilerin iş kurallarına uygun olmadığı rapor hataları raporlar.</span><span class="sxs-lookup"><span data-stu-id="f9edf-111">Model validation occurs after model binding and reports errors where data doesn't conform to business rules.</span></span> <span data-ttu-id="f9edf-112">Örneğin, 1 ile 5 arasında bir derecelendirme bekleyen bir alana 0 girilir.</span><span class="sxs-lookup"><span data-stu-id="f9edf-112">For example, a 0 is entered in a field that expects a rating between 1 and 5.</span></span>

<span data-ttu-id="f9edf-113">Hem model bağlama hem de model doğrulama, bir denetleyici eyleminin veya bir sayfa işleyici yönteminin yürütülmesinden önce oluşur Razor .</span><span class="sxs-lookup"><span data-stu-id="f9edf-113">Both model binding and model validation occur before the execution of a controller action or a Razor Pages handler method.</span></span> <span data-ttu-id="f9edf-114">Web uygulamaları için uygulama, `ModelState.IsValid` uygun şekilde inceleme ve tepki verme sorumluluğundadır.</span><span class="sxs-lookup"><span data-stu-id="f9edf-114">For web apps, it's the app's responsibility to inspect `ModelState.IsValid` and react appropriately.</span></span> <span data-ttu-id="f9edf-115">Web Apps genellikle sayfayı bir hata iletisiyle yeniden görüntülerdi:</span><span class="sxs-lookup"><span data-stu-id="f9edf-115">Web apps typically redisplay the page with an error message:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Pages/Movies/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=3-6)]

<span data-ttu-id="f9edf-116">Web API denetleyicilerinin `ModelState.IsValid` özniteliğe sahip olup olmadığını kontrol etmek zorunda değildir `[ApiController]` .</span><span class="sxs-lookup"><span data-stu-id="f9edf-116">Web API controllers don't have to check `ModelState.IsValid` if they have the `[ApiController]` attribute.</span></span> <span data-ttu-id="f9edf-117">Bu durumda, model durumu geçersiz olduğunda hata ayrıntılarını içeren bir otomatik HTTP 400 yanıtı döndürülür.</span><span class="sxs-lookup"><span data-stu-id="f9edf-117">In that case, an automatic HTTP 400 response containing error details is returned when model state is invalid.</span></span> <span data-ttu-id="f9edf-118">Daha fazla bilgi için bkz. [OTOMATIK HTTP 400 yanıtları](xref:web-api/index#automatic-http-400-responses).</span><span class="sxs-lookup"><span data-stu-id="f9edf-118">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span>

## <a name="rerun-validation"></a><span data-ttu-id="f9edf-119">Doğrulamayı yeniden çalıştır</span><span class="sxs-lookup"><span data-stu-id="f9edf-119">Rerun validation</span></span>

<span data-ttu-id="f9edf-120">Doğrulama otomatiktir, ancak el ile yinelemek isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="f9edf-120">Validation is automatic, but you might want to repeat it manually.</span></span> <span data-ttu-id="f9edf-121">Örneğin, bir özellik için bir değer hesaplamanıza ve özelliği hesaplanan değere ayarladıktan sonra doğrulamayı yeniden çalıştırmak isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="f9edf-121">For example, you might compute a value for a property and want to rerun validation after setting the property to the computed value.</span></span> <span data-ttu-id="f9edf-122">Doğrulamayı yeniden çalıştırmak için, `TryValidateModel` yöntemi aşağıda gösterildiği gibi çağırın:</span><span class="sxs-lookup"><span data-stu-id="f9edf-122">To rerun validation, call the `TryValidateModel` method, as shown here:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Pages/Movies/Create.cshtml.cs?name=snippet_TryValidate&highlight=3-6)]

## <a name="validation-attributes"></a><span data-ttu-id="f9edf-123">Doğrulama öznitelikleri</span><span class="sxs-lookup"><span data-stu-id="f9edf-123">Validation attributes</span></span>

<span data-ttu-id="f9edf-124">Doğrulama öznitelikleri, model özellikleri için doğrulama kuralları belirtmenize olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="f9edf-124">Validation attributes let you specify validation rules for model properties.</span></span> <span data-ttu-id="f9edf-125">Örnek uygulamadaki aşağıdaki örnek, doğrulama öznitelikleriyle açıklama eklenmiş bir model sınıfı gösterir.</span><span class="sxs-lookup"><span data-stu-id="f9edf-125">The following example from the sample app shows a model class that is annotated with validation attributes.</span></span> <span data-ttu-id="f9edf-126">`[ClassicMovie]`Özniteliği özel bir doğrulama özniteliğidir ve diğerleri yerleşik olarak bulunur.</span><span class="sxs-lookup"><span data-stu-id="f9edf-126">The `[ClassicMovie]` attribute is a custom validation attribute and the others are built-in.</span></span> <span data-ttu-id="f9edf-127">Gösterilmez `[ClassicMovieWithClientValidator]` .</span><span class="sxs-lookup"><span data-stu-id="f9edf-127">Not shown is `[ClassicMovieWithClientValidator]`.</span></span> <span data-ttu-id="f9edf-128">`[ClassicMovieWithClientValidator]`özel bir öznitelik uygulamak için alternatif bir yol gösterir.</span><span class="sxs-lookup"><span data-stu-id="f9edf-128">`[ClassicMovieWithClientValidator]` shows an alternative way to implement a custom attribute.</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Models/Movie.cs?name=snippet_Class)]

## <a name="built-in-attributes"></a><span data-ttu-id="f9edf-129">Yerleşik öznitelikler</span><span class="sxs-lookup"><span data-stu-id="f9edf-129">Built-in attributes</span></span>

<span data-ttu-id="f9edf-130">Yerleşik doğrulama özniteliklerinden bazıları şunlardır:</span><span class="sxs-lookup"><span data-stu-id="f9edf-130">Here are some of the built-in validation attributes:</span></span>

* <span data-ttu-id="f9edf-131">`[CreditCard]`: Özelliğin kredi kartı biçimine sahip olduğunu doğrular.</span><span class="sxs-lookup"><span data-stu-id="f9edf-131">`[CreditCard]`: Validates that the property has a credit card format.</span></span> <span data-ttu-id="f9edf-132">[JQuery doğrulaması ek yöntemleri](https://cdnjs.cloudflare.com/ajax/libs/jquery-validate/1.19.1/additional-methods.min.js)gerektirir.</span><span class="sxs-lookup"><span data-stu-id="f9edf-132">Requires [jQuery Validation Additional Methods](https://cdnjs.cloudflare.com/ajax/libs/jquery-validate/1.19.1/additional-methods.min.js).</span></span>
* <span data-ttu-id="f9edf-133">`[Compare]`: Bir modeldeki iki özelliği eşleştiğini doğrular.</span><span class="sxs-lookup"><span data-stu-id="f9edf-133">`[Compare]`: Validates that two properties in a model match.</span></span>
* <span data-ttu-id="f9edf-134">`[EmailAddress]`: Özelliğin bir e-posta biçimine sahip olduğunu doğrular.</span><span class="sxs-lookup"><span data-stu-id="f9edf-134">`[EmailAddress]`: Validates that the property has an email format.</span></span>
* <span data-ttu-id="f9edf-135">`[Phone]`: Özelliğin bir telefon numarası biçimine sahip olduğunu doğrular.</span><span class="sxs-lookup"><span data-stu-id="f9edf-135">`[Phone]`: Validates that the property has a telephone number format.</span></span>
* <span data-ttu-id="f9edf-136">`[Range]`: Özellik değerinin belirtilen bir aralık dahilinde olduğunu doğrular.</span><span class="sxs-lookup"><span data-stu-id="f9edf-136">`[Range]`: Validates that the property value falls within a specified range.</span></span>
* <span data-ttu-id="f9edf-137">`[RegularExpression]`: Özellik değerinin belirtilen bir normal ifadeyle eşleştiğini doğrular.</span><span class="sxs-lookup"><span data-stu-id="f9edf-137">`[RegularExpression]`: Validates that the property value matches a specified regular expression.</span></span>
* <span data-ttu-id="f9edf-138">`[Required]`: Alanın null olduğunu doğrular.</span><span class="sxs-lookup"><span data-stu-id="f9edf-138">`[Required]`: Validates that the field is not null.</span></span> <span data-ttu-id="f9edf-139">Bu özniteliğin davranışı hakkındaki ayrıntılar için bkz. [ `[Required]` özniteliği](#required-attribute) .</span><span class="sxs-lookup"><span data-stu-id="f9edf-139">See [`[Required]` attribute](#required-attribute) for details about this attribute's behavior.</span></span>
* <span data-ttu-id="f9edf-140">`[StringLength]`: Dize özellik değerinin belirtilen uzunluk sınırını aşmadığını doğrular.</span><span class="sxs-lookup"><span data-stu-id="f9edf-140">`[StringLength]`: Validates that a string property value doesn't exceed a specified length limit.</span></span>
* <span data-ttu-id="f9edf-141">`[Url]`: Özelliğin bir URL biçimine sahip olduğunu doğrular.</span><span class="sxs-lookup"><span data-stu-id="f9edf-141">`[Url]`: Validates that the property has a URL format.</span></span>
* <span data-ttu-id="f9edf-142">`[Remote]`: Sunucuda bir eylem yöntemi çağırarak istemcide girişi doğrular.</span><span class="sxs-lookup"><span data-stu-id="f9edf-142">`[Remote]`: Validates input on the client by calling an action method on the server.</span></span> <span data-ttu-id="f9edf-143">Bu özniteliğin davranışı hakkındaki ayrıntılar için bkz. [ `[Remote]` özniteliği](#remote-attribute) .</span><span class="sxs-lookup"><span data-stu-id="f9edf-143">See [`[Remote]` attribute](#remote-attribute) for details about this attribute's behavior.</span></span>

<span data-ttu-id="f9edf-144">Doğrulama özniteliklerinin tüm listesi [System. ComponentModel. Dataaçıklamalarda](xref:System.ComponentModel.DataAnnotations) ad alanında bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="f9edf-144">A complete list of validation attributes can be found in the [System.ComponentModel.DataAnnotations](xref:System.ComponentModel.DataAnnotations) namespace.</span></span>

### <a name="error-messages"></a><span data-ttu-id="f9edf-145">Hata iletileri</span><span class="sxs-lookup"><span data-stu-id="f9edf-145">Error messages</span></span>

<span data-ttu-id="f9edf-146">Doğrulama öznitelikleri, geçersiz giriş için görüntülenecek hata iletisini belirtmenize izin verir.</span><span class="sxs-lookup"><span data-stu-id="f9edf-146">Validation attributes let you specify the error message to be displayed for invalid input.</span></span> <span data-ttu-id="f9edf-147">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="f9edf-147">For example:</span></span>

```csharp
[StringLength(8, ErrorMessage = "Name length can't be more than 8.")]
```

<span data-ttu-id="f9edf-148">Dahili olarak, öznitelikler, `String.Format` alan adı için bir yer tutucu ve bazen ek yer tutucular ile çağrı yapılır.</span><span class="sxs-lookup"><span data-stu-id="f9edf-148">Internally, the attributes call `String.Format` with a placeholder for the field name and sometimes additional placeholders.</span></span> <span data-ttu-id="f9edf-149">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="f9edf-149">For example:</span></span>

```csharp
[StringLength(8, ErrorMessage = "{0} length must be between {2} and {1}.", MinimumLength = 6)]
```

<span data-ttu-id="f9edf-150">Bir `Name` özelliğe uygulandığında, yukarıdaki kod tarafından oluşturulan hata iletisi "ad uzunluğu 6 ile 8 arasında olmalıdır." olacaktır.</span><span class="sxs-lookup"><span data-stu-id="f9edf-150">When applied to a `Name` property, the error message created by the preceding code would be "Name length must be between 6 and 8.".</span></span>

<span data-ttu-id="f9edf-151">`String.Format`Belirli bir özniteliğin hata iletisinde hangi parametrelerin geçtiğini öğrenmek için, bkz. [dataaçıklamalarda kaynak kodu](https://github.com/dotnet/runtime/tree/master/src/libraries/System.ComponentModel.Annotations/src/System/ComponentModel/DataAnnotations).</span><span class="sxs-lookup"><span data-stu-id="f9edf-151">To find out which parameters are passed to `String.Format` for a particular attribute's error message, see the [DataAnnotations source code](https://github.com/dotnet/runtime/tree/master/src/libraries/System.ComponentModel.Annotations/src/System/ComponentModel/DataAnnotations).</span></span>

## <a name="required-attribute"></a><span data-ttu-id="f9edf-152">[Zorunlu] özniteliği</span><span class="sxs-lookup"><span data-stu-id="f9edf-152">[Required] attribute</span></span>

<span data-ttu-id="f9edf-153">.NET Core 3,0 ve sonraki sürümlerde doğrulama sistemi, null olamayan parametrelere veya bir özniteliğe sahip oldukları gibi bağlantılı özelliklere sahiptir `[Required]` .</span><span class="sxs-lookup"><span data-stu-id="f9edf-153">The validation system in .NET Core 3.0 and later treats non-nullable parameters or bound properties as if they had a `[Required]` attribute.</span></span> <span data-ttu-id="f9edf-154">Ve gibi [değer türleri](/dotnet/csharp/language-reference/keywords/value-types) `decimal` `int` null atanamaz.</span><span class="sxs-lookup"><span data-stu-id="f9edf-154">[Value types](/dotnet/csharp/language-reference/keywords/value-types) such as `decimal` and `int` are non-nullable.</span></span> <span data-ttu-id="f9edf-155">Bu davranış, içinde yapılandırılarak devre dışı <xref:Microsoft.AspNetCore.Mvc.MvcOptions.SuppressImplicitRequiredAttributeForNonNullableReferenceTypes> bırakılabilir `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="f9edf-155">This behavior can be disabled by configuring <xref:Microsoft.AspNetCore.Mvc.MvcOptions.SuppressImplicitRequiredAttributeForNonNullableReferenceTypes> in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddControllers(options => options.SuppressImplicitRequiredAttributeForNonNullableReferenceTypes = true);
```

### <a name="required-validation-on-the-server"></a><span data-ttu-id="f9edf-156">[Zorunlu] sunucuda doğrulama</span><span class="sxs-lookup"><span data-stu-id="f9edf-156">[Required] validation on the server</span></span>

<span data-ttu-id="f9edf-157">Sunucuda, özelliği null ise gerekli bir değer eksik olarak kabul edilir.</span><span class="sxs-lookup"><span data-stu-id="f9edf-157">On the server, a required value is considered missing if the property is null.</span></span> <span data-ttu-id="f9edf-158">Null yapılamayan bir alan her zaman geçerlidir ve `[Required]` özniteliğin hata mesajı hiçbir zaman gösterilmez.</span><span class="sxs-lookup"><span data-stu-id="f9edf-158">A non-nullable field is always valid, and the `[Required]` attribute's error message is never displayed.</span></span>

<span data-ttu-id="f9edf-159">Ancak, null olamayan bir özellik için model bağlama başarısız olabilir ve gibi bir hata mesajı elde edilir `The value '' is invalid` .</span><span class="sxs-lookup"><span data-stu-id="f9edf-159">However, model binding for a non-nullable property may fail, resulting in an error message such as `The value '' is invalid`.</span></span> <span data-ttu-id="f9edf-160">Null yapılamayan türlerin sunucu tarafı doğrulaması için özel bir hata iletisi belirtmek üzere aşağıdaki seçenekleriniz vardır:</span><span class="sxs-lookup"><span data-stu-id="f9edf-160">To specify a custom error message for server-side validation of non-nullable types, you have the following options:</span></span>

* <span data-ttu-id="f9edf-161">Alanı null yapılabilir yapın (örneğin, `decimal?` yerine `decimal` ).</span><span class="sxs-lookup"><span data-stu-id="f9edf-161">Make the field nullable (for example, `decimal?` instead of `decimal`).</span></span> <span data-ttu-id="f9edf-162">[Null \<T> yapılabilir](/dotnet/csharp/programming-guide/nullable-types/) değer türleri standart null yapılabilir türler gibi değerlendirilir.</span><span class="sxs-lookup"><span data-stu-id="f9edf-162">[Nullable\<T>](/dotnet/csharp/programming-guide/nullable-types/) value types are treated like standard nullable types.</span></span>
* <span data-ttu-id="f9edf-163">Aşağıdaki örnekte gösterildiği gibi model bağlama tarafından kullanılacak varsayılan hata iletisini belirtin:</span><span class="sxs-lookup"><span data-stu-id="f9edf-163">Specify the default error message to be used by model binding, as shown in the following example:</span></span>

  [!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_Configuration&highlight=5-6)]

  <span data-ttu-id="f9edf-164">İçin varsayılan iletileri ayarlayabileceğiniz model bağlama hataları hakkında daha fazla bilgi için bkz <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DefaultModelBindingMessageProvider#methods> ..</span><span class="sxs-lookup"><span data-stu-id="f9edf-164">For more information about model binding errors that you can set default messages for, see <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DefaultModelBindingMessageProvider#methods>.</span></span>

### <a name="required-validation-on-the-client"></a><span data-ttu-id="f9edf-165">[Zorunlu] istemcide doğrulama</span><span class="sxs-lookup"><span data-stu-id="f9edf-165">[Required] validation on the client</span></span>

<span data-ttu-id="f9edf-166">Null yapılamayan türler ve dizeler, sunucu ile karşılaştırıldığında, istemci üzerinde farklı şekilde işlenir.</span><span class="sxs-lookup"><span data-stu-id="f9edf-166">Non-nullable types and strings are handled differently on the client compared to the server.</span></span> <span data-ttu-id="f9edf-167">İstemcide:</span><span class="sxs-lookup"><span data-stu-id="f9edf-167">On the client:</span></span>

* <span data-ttu-id="f9edf-168">Yalnızca girdi girildiğinde bir değer vardır.</span><span class="sxs-lookup"><span data-stu-id="f9edf-168">A value is considered present only if input is entered for it.</span></span> <span data-ttu-id="f9edf-169">Bu nedenle, istemci tarafı doğrulaması null yapılamayan türler, null yapılabilir türler ile aynı şekilde işler.</span><span class="sxs-lookup"><span data-stu-id="f9edf-169">Therefore, client-side validation handles non-nullable types the same as nullable types.</span></span>
* <span data-ttu-id="f9edf-170">Bir dize alanındaki boşluk, jQuery doğrulaması [gerekli](https://jqueryvalidation.org/required-method/) yöntemi tarafından geçerli bir girdi olarak kabul edilir.</span><span class="sxs-lookup"><span data-stu-id="f9edf-170">Whitespace in a string field is considered valid input by the jQuery Validation [required](https://jqueryvalidation.org/required-method/) method.</span></span> <span data-ttu-id="f9edf-171">Yalnızca boşluk girildiğinde, sunucu tarafı doğrulaması gerekli bir dize alanını geçersiz kabul eder.</span><span class="sxs-lookup"><span data-stu-id="f9edf-171">Server-side validation considers a required string field invalid if only whitespace is entered.</span></span>

<span data-ttu-id="f9edf-172">Daha önce belirtildiği gibi, null olamayan türler bir özniteliğe sahip olsa da kabul edilir `[Required]` .</span><span class="sxs-lookup"><span data-stu-id="f9edf-172">As noted earlier, non-nullable types are treated as though they had a `[Required]` attribute.</span></span> <span data-ttu-id="f9edf-173">Bu, özniteliğini uygulamasanız bile istemci tarafı doğrulamayı alacağınız anlamına gelir `[Required]` .</span><span class="sxs-lookup"><span data-stu-id="f9edf-173">That means you get client-side validation even if you don't apply the `[Required]` attribute.</span></span> <span data-ttu-id="f9edf-174">Ancak özniteliğini kullanmazsanız varsayılan bir hata iletisi alırsınız.</span><span class="sxs-lookup"><span data-stu-id="f9edf-174">But if you don't use the attribute, you get a default error message.</span></span> <span data-ttu-id="f9edf-175">Özel bir hata iletisi belirtmek için özniteliğini kullanın.</span><span class="sxs-lookup"><span data-stu-id="f9edf-175">To specify a custom error message, use the attribute.</span></span>

## <a name="remote-attribute"></a><span data-ttu-id="f9edf-176">[Uzak] özniteliği</span><span class="sxs-lookup"><span data-stu-id="f9edf-176">[Remote] attribute</span></span>

<span data-ttu-id="f9edf-177">`[Remote]`Özniteliği, alan girişinin geçerli olup olmadığını anlamak için sunucu üzerinde bir yöntem çağrılmasını gerektiren istemci tarafı doğrulaması uygular.</span><span class="sxs-lookup"><span data-stu-id="f9edf-177">The `[Remote]` attribute implements client-side validation that requires calling a method on the server to determine whether field input is valid.</span></span> <span data-ttu-id="f9edf-178">Örneğin, uygulamanın bir kullanıcı adının zaten kullanımda olup olmadığını doğrulaması gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="f9edf-178">For example, the app may need to verify whether a user name is already in use.</span></span>

<span data-ttu-id="f9edf-179">Uzaktan doğrulamayı uygulamak için:</span><span class="sxs-lookup"><span data-stu-id="f9edf-179">To implement remote validation:</span></span>

1. <span data-ttu-id="f9edf-180">JavaScript 'e çağırmak için bir eylem yöntemi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="f9edf-180">Create an action method for JavaScript to call.</span></span>  <span data-ttu-id="f9edf-181">JQuery doğrulaması [uzak](https://jqueryvalidation.org/remote-method/) YÖNTEMI bir JSON yanıtı bekliyor:</span><span class="sxs-lookup"><span data-stu-id="f9edf-181">The jQuery Validation [remote](https://jqueryvalidation.org/remote-method/) method expects a JSON response:</span></span>

   * <span data-ttu-id="f9edf-182">`true`giriş verilerinin geçerli olduğu anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="f9edf-182">`true` means the input data is valid.</span></span>
   * <span data-ttu-id="f9edf-183">`false`, `undefined` ya da `null` girişin geçersiz olduğu anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="f9edf-183">`false`, `undefined`, or `null` means the input is invalid.</span></span> <span data-ttu-id="f9edf-184">Varsayılan hata iletisini görüntüler.</span><span class="sxs-lookup"><span data-stu-id="f9edf-184">Display the default error message.</span></span>
   * <span data-ttu-id="f9edf-185">Diğer herhangi bir dize, girişin geçersiz olduğu anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="f9edf-185">Any other string means the input is invalid.</span></span> <span data-ttu-id="f9edf-186">Dizeyi özel bir hata iletisi olarak görüntüleyin.</span><span class="sxs-lookup"><span data-stu-id="f9edf-186">Display the string as a custom error message.</span></span>

   <span data-ttu-id="f9edf-187">Özel bir hata iletisi döndüren eylem yöntemine bir örnek aşağıda verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="f9edf-187">Here's an example of an action method that returns a custom error message:</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyEmail)]

1. <span data-ttu-id="f9edf-188">Model sınıfında, `[Remote]` Aşağıdaki örnekte gösterildiği gibi, doğrulama eylemi yöntemine işaret eden bir özniteliğe sahip özelliğe açıklama ekleyin:</span><span class="sxs-lookup"><span data-stu-id="f9edf-188">In the model class, annotate the property with a `[Remote]` attribute that points to the validation action method, as shown in the following example:</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Models/User.cs?name=snippet_Email)]
 
   <span data-ttu-id="f9edf-189">`[Remote]`Özniteliği `Microsoft.AspNetCore.Mvc` ad alanıdır.</span><span class="sxs-lookup"><span data-stu-id="f9edf-189">The `[Remote]` attribute is in the `Microsoft.AspNetCore.Mvc` namespace.</span></span>
   
### <a name="additional-fields"></a><span data-ttu-id="f9edf-190">Ek alanlar</span><span class="sxs-lookup"><span data-stu-id="f9edf-190">Additional fields</span></span>

<span data-ttu-id="f9edf-191">`AdditionalFields`Özniteliğinin özelliği, `[Remote]` sunucudaki verilere karşı alan birleşimlerini doğrulamanızı sağlar.</span><span class="sxs-lookup"><span data-stu-id="f9edf-191">The `AdditionalFields` property of the `[Remote]` attribute lets you validate combinations of fields against data on the server.</span></span> <span data-ttu-id="f9edf-192">Örneğin, `User` model `FirstName` ve `LastName` özellikleri varsa, var olan hiçbir kullanıcının bu ad çiftine sahip olmadığını doğrulamak isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="f9edf-192">For example, if the `User` model had `FirstName` and `LastName` properties, you might want to verify that no existing users already have that pair of names.</span></span> <span data-ttu-id="f9edf-193">Aşağıdaki örnek nasıl kullanılacağını gösterir `AdditionalFields` :</span><span class="sxs-lookup"><span data-stu-id="f9edf-193">The following example shows how to use `AdditionalFields`:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Models/User.cs?name=snippet_Name&highlight=1,5)]

<span data-ttu-id="f9edf-194">`AdditionalFields`açıkça "FirstName" ve "LastName" dizelerine ayarlanabilir, ancak [NameOf](/dotnet/csharp/language-reference/keywords/nameof) işlecinin kullanılması daha sonra yeniden düzenlemeyi basitleştirir.</span><span class="sxs-lookup"><span data-stu-id="f9edf-194">`AdditionalFields` could be set explicitly to the strings "FirstName" and "LastName", but using the [nameof](/dotnet/csharp/language-reference/keywords/nameof) operator simplifies later refactoring.</span></span> <span data-ttu-id="f9edf-195">Bu doğrulamanın eylem yöntemi hem hem de `firstName` `lastName` bağımsız değişkenlerini kabul etmelidir:</span><span class="sxs-lookup"><span data-stu-id="f9edf-195">The action method for this validation must accept both `firstName` and `lastName` arguments:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyName)]

<span data-ttu-id="f9edf-196">Kullanıcı adı veya soyadı girdiğinde JavaScript, bu ad çiftinin alındığını görmek için uzak bir çağrı yapar.</span><span class="sxs-lookup"><span data-stu-id="f9edf-196">When the user enters a first or last name, JavaScript makes a remote call to see if that pair of names has been taken.</span></span>

<span data-ttu-id="f9edf-197">İki veya daha fazla ek alanı doğrulamak için bunları virgülle ayrılmış bir liste olarak belirtin.</span><span class="sxs-lookup"><span data-stu-id="f9edf-197">To validate two or more additional fields, provide them as a comma-delimited list.</span></span> <span data-ttu-id="f9edf-198">Örneğin, modele bir özellik eklemek için `MiddleName` `[Remote]` Aşağıdaki örnekte gösterildiği gibi özniteliği ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="f9edf-198">For example, to add a `MiddleName` property to the model, set the `[Remote]` attribute as shown in the following example:</span></span>

```csharp
[Remote(action: "VerifyName", controller: "Users", AdditionalFields = nameof(FirstName) + "," + nameof(LastName))]
public string MiddleName { get; set; }
```

<span data-ttu-id="f9edf-199">`AdditionalFields`Tüm öznitelik bağımsız değişkenleri gibi, sabit bir ifade olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="f9edf-199">`AdditionalFields`, like all attribute arguments, must be a constant expression.</span></span> <span data-ttu-id="f9edf-200">Bu nedenle, bir ara [değerli dize](/dotnet/csharp/language-reference/keywords/interpolated-strings) veya <xref:System.String.Join*> başlatmak için çağrı kullanmayın `AdditionalFields` .</span><span class="sxs-lookup"><span data-stu-id="f9edf-200">Therefore, don't use an [interpolated string](/dotnet/csharp/language-reference/keywords/interpolated-strings) or call <xref:System.String.Join*> to initialize `AdditionalFields`.</span></span>

## <a name="alternatives-to-built-in-attributes"></a><span data-ttu-id="f9edf-201">Yerleşik özniteliklerin alternatifleri</span><span class="sxs-lookup"><span data-stu-id="f9edf-201">Alternatives to built-in attributes</span></span>

<span data-ttu-id="f9edf-202">Yerleşik öznitelikler tarafından sağlanmayan doğrulamaya ihtiyacınız varsa şunları yapabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="f9edf-202">If you need validation not provided by built-in attributes, you can:</span></span>

* <span data-ttu-id="f9edf-203">[Özel öznitelikler oluşturun](#custom-attributes).</span><span class="sxs-lookup"><span data-stu-id="f9edf-203">[Create custom attributes](#custom-attributes).</span></span>
* <span data-ttu-id="f9edf-204">[IValidatableObject uygulayın](#ivalidatableobject).</span><span class="sxs-lookup"><span data-stu-id="f9edf-204">[Implement IValidatableObject](#ivalidatableobject).</span></span>

## <a name="custom-attributes"></a><span data-ttu-id="f9edf-205">Özel öznitelikler</span><span class="sxs-lookup"><span data-stu-id="f9edf-205">Custom attributes</span></span>

<span data-ttu-id="f9edf-206">Yerleşik doğrulama özniteliklerinin işlemeyen senaryolar için özel doğrulama öznitelikleri oluşturabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="f9edf-206">For scenarios that the built-in validation attributes don't handle, you can create custom validation attributes.</span></span> <span data-ttu-id="f9edf-207">Öğesinden devralan bir sınıf oluşturun <xref:System.ComponentModel.DataAnnotations.ValidationAttribute> ve yöntemi geçersiz kılın <xref:System.ComponentModel.DataAnnotations.ValidationAttribute.IsValid*> .</span><span class="sxs-lookup"><span data-stu-id="f9edf-207">Create a class that inherits from <xref:System.ComponentModel.DataAnnotations.ValidationAttribute>, and override the <xref:System.ComponentModel.DataAnnotations.ValidationAttribute.IsValid*> method.</span></span>

<span data-ttu-id="f9edf-208">`IsValid`Yöntemi, doğrulanacak girdi olan *Value*adlı bir nesne kabul eder.</span><span class="sxs-lookup"><span data-stu-id="f9edf-208">The `IsValid` method accepts an object named *value*, which is the input to be validated.</span></span> <span data-ttu-id="f9edf-209">Aşırı yükleme, `ValidationContext` model bağlama tarafından oluşturulan model örneği gibi ek bilgiler sağlayan bir nesneyi de kabul eder.</span><span class="sxs-lookup"><span data-stu-id="f9edf-209">An overload also accepts a `ValidationContext` object, which provides additional information, such as the model instance created by model binding.</span></span>

<span data-ttu-id="f9edf-210">Aşağıdaki örnek, *Klasik* tarz bir filmin yayın tarihinin belirtilen yıldan daha sonra olmadığını doğrular.</span><span class="sxs-lookup"><span data-stu-id="f9edf-210">The following example validates that the release date for a movie in the *Classic* genre isn't later than a specified year.</span></span> <span data-ttu-id="f9edf-211">`[ClassicMovie]`Öznitelik:</span><span class="sxs-lookup"><span data-stu-id="f9edf-211">The `[ClassicMovie]` attribute:</span></span>

* <span data-ttu-id="f9edf-212">Yalnızca sunucuda çalışır.</span><span class="sxs-lookup"><span data-stu-id="f9edf-212">Is only run on the server.</span></span>
* <span data-ttu-id="f9edf-213">Klasik Filmler için, yayımlanma tarihini doğrular:</span><span class="sxs-lookup"><span data-stu-id="f9edf-213">For Classic movies, validates the release date:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Validation/ClassicMovieAttribute.cs?name=snippet_Class)]

<span data-ttu-id="f9edf-214">`movie`Yukarıdaki örnekteki değişken, `Movie` form gönderiminde verileri içeren bir nesneyi temsil eder.</span><span class="sxs-lookup"><span data-stu-id="f9edf-214">The `movie` variable in the preceding example represents a `Movie` object that contains the data from the form submission.</span></span> <span data-ttu-id="f9edf-215">Doğrulama başarısız olduğunda, bir `ValidationResult` hata iletisi döndürür.</span><span class="sxs-lookup"><span data-stu-id="f9edf-215">When validation fails, a `ValidationResult` with an error message is returned.</span></span>

## <a name="ivalidatableobject"></a><span data-ttu-id="f9edf-216">IValidatableObject</span><span class="sxs-lookup"><span data-stu-id="f9edf-216">IValidatableObject</span></span>

<span data-ttu-id="f9edf-217">Yukarıdaki örnek yalnızca türlerle birlikte kullanılabilir `Movie` .</span><span class="sxs-lookup"><span data-stu-id="f9edf-217">The preceding example works only with `Movie` types.</span></span> <span data-ttu-id="f9edf-218">Aşağıdaki örnekte gösterildiği gibi, sınıf düzeyi doğrulama için başka bir seçenek de `IValidatableObject` model sınıfında uygulanır:</span><span class="sxs-lookup"><span data-stu-id="f9edf-218">Another option for class-level validation is to implement `IValidatableObject` in the model class, as shown in the following example:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Models/ValidatableMovie.cs?name=snippet_Class&highlight=1,26-34)]

## <a name="top-level-node-validation"></a><span data-ttu-id="f9edf-219">Üst düzey düğüm doğrulaması</span><span class="sxs-lookup"><span data-stu-id="f9edf-219">Top-level node validation</span></span>

<span data-ttu-id="f9edf-220">Üst düzey düğümler şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="f9edf-220">Top-level nodes include:</span></span>

* <span data-ttu-id="f9edf-221">Eylem parametreleri</span><span class="sxs-lookup"><span data-stu-id="f9edf-221">Action parameters</span></span>
* <span data-ttu-id="f9edf-222">Denetleyici özellikleri</span><span class="sxs-lookup"><span data-stu-id="f9edf-222">Controller properties</span></span>
* <span data-ttu-id="f9edf-223">Sayfa işleyici parametreleri</span><span class="sxs-lookup"><span data-stu-id="f9edf-223">Page handler parameters</span></span>
* <span data-ttu-id="f9edf-224">Sayfa modeli özellikleri</span><span class="sxs-lookup"><span data-stu-id="f9edf-224">Page model properties</span></span>

<span data-ttu-id="f9edf-225">Model bağlantılı üst düzey düğümler, model özelliklerini doğrulamaya ek olarak onaylanır.</span><span class="sxs-lookup"><span data-stu-id="f9edf-225">Model-bound top-level nodes are validated in addition to validating model properties.</span></span> <span data-ttu-id="f9edf-226">Örnek uygulamadan aşağıdaki örnekte, `VerifyPhone` yöntemi <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute> eylem parametresini doğrulamak için öğesini kullanır `phone` :</span><span class="sxs-lookup"><span data-stu-id="f9edf-226">In the following example from the sample app, the `VerifyPhone` method uses the <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute> to validate the `phone` action parameter:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyPhone)]

<span data-ttu-id="f9edf-227">En üst düzey düğümler, <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute> doğrulama öznitelikleriyle birlikte kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="f9edf-227">Top-level nodes can use <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute> with validation attributes.</span></span> <span data-ttu-id="f9edf-228">Örnek uygulamadaki aşağıdaki örnekte, `CheckAge` yöntemi, `age` form gönderildiğinde parametrenin sorgu dizesinden bağlanması gerektiğini belirtir:</span><span class="sxs-lookup"><span data-stu-id="f9edf-228">In the following example from the sample app, the `CheckAge` method specifies that the `age` parameter must be bound from the query string when the form is submitted:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Controllers/UsersController.cs?name=snippet_CheckAgeSignature)]

<span data-ttu-id="f9edf-229">Denetim yaşı sayfasında (*Checkage. cshtml*) iki form vardır.</span><span class="sxs-lookup"><span data-stu-id="f9edf-229">In the Check Age page (*CheckAge.cshtml*), there are two forms.</span></span> <span data-ttu-id="f9edf-230">İlk form `Age` `99` bir değeri sorgu dizesi parametresi olarak gönderir: `https://localhost:5001/Users/CheckAge?Age=99` .</span><span class="sxs-lookup"><span data-stu-id="f9edf-230">The first form submits an `Age` value of `99` as a query string parameter: `https://localhost:5001/Users/CheckAge?Age=99`.</span></span>

<span data-ttu-id="f9edf-231">Sorgu dizesinden düzgün şekilde biçimlendirilen bir `age` parametre gönderildiğinde, form doğrular.</span><span class="sxs-lookup"><span data-stu-id="f9edf-231">When a properly formatted `age` parameter from the query string is submitted, the form validates.</span></span>

<span data-ttu-id="f9edf-232">Denetim yaşı sayfasındaki ikinci form, `Age` isteğin gövdesindeki değeri gönderir ve doğrulama başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="f9edf-232">The second form on the Check Age page submits the `Age` value in the body of the request, and validation fails.</span></span> <span data-ttu-id="f9edf-233">`age`Parametre bir sorgu dizesinden gelmesi gerektiğinden bağlama başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="f9edf-233">Binding fails because the `age` parameter must come from a query string.</span></span>

## <a name="maximum-errors"></a><span data-ttu-id="f9edf-234">En fazla hata sayısı</span><span class="sxs-lookup"><span data-stu-id="f9edf-234">Maximum errors</span></span>

<span data-ttu-id="f9edf-235">En fazla hata sayısına ulaşıldığında doğrulama durduruluyor (varsayılan olarak 200).</span><span class="sxs-lookup"><span data-stu-id="f9edf-235">Validation stops when the maximum number of errors is reached (200 by default).</span></span> <span data-ttu-id="f9edf-236">Bu numarayı içinde aşağıdaki kodla yapılandırabilirsiniz `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="f9edf-236">You can configure this number with the following code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_Configuration&highlight=4)]

## <a name="maximum-recursion"></a><span data-ttu-id="f9edf-237">En yüksek özyineleme</span><span class="sxs-lookup"><span data-stu-id="f9edf-237">Maximum recursion</span></span>

<span data-ttu-id="f9edf-238"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.Validation.ValidationVisitor>doğrulanan modelin nesne grafiğinin gezgeçer.</span><span class="sxs-lookup"><span data-stu-id="f9edf-238"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.Validation.ValidationVisitor> traverses the object graph of the model being validated.</span></span> <span data-ttu-id="f9edf-239">Derin olan veya sonsuz özyinelemeli olan modeller için doğrulama, yığın taşmasına neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="f9edf-239">For models that are deep or are infinitely recursive, validation may result in stack overflow.</span></span> <span data-ttu-id="f9edf-240">[Mvcoptions. MaxValidationDepth](xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxValidationDepth) , ziyaretçi özyineleme yapılandırılmış bir derinliği aşarsa doğrulamanın erken durdurulması için bir yol sağlar.</span><span class="sxs-lookup"><span data-stu-id="f9edf-240">[MvcOptions.MaxValidationDepth](xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxValidationDepth) provides a way to stop validation early if the visitor recursion exceeds a configured depth.</span></span> <span data-ttu-id="f9edf-241">Varsayılan değeri 32 ' `MvcOptions.MaxValidationDepth` dir.</span><span class="sxs-lookup"><span data-stu-id="f9edf-241">The default value of `MvcOptions.MaxValidationDepth` is 32.</span></span>

## <a name="automatic-short-circuit"></a><span data-ttu-id="f9edf-242">Otomatik kısa devre</span><span class="sxs-lookup"><span data-stu-id="f9edf-242">Automatic short-circuit</span></span>

<span data-ttu-id="f9edf-243">Model grafı doğrulama gerektirmiyorsa, doğrulama otomatik olarak kısa devre dışı (atlandı).</span><span class="sxs-lookup"><span data-stu-id="f9edf-243">Validation is automatically short-circuited (skipped) if the model graph doesn't require validation.</span></span> <span data-ttu-id="f9edf-244">Çalışma zamanının, hiçbir doğrulayıcıya sahip olmayan temel elemanlar koleksiyonları ( `byte[]` , `string[]` ,,,, ve gibi) için doğrulamayı atlayan nesneler `Dictionary<string, string>` .</span><span class="sxs-lookup"><span data-stu-id="f9edf-244">Objects that the runtime skips validation for include collections of primitives (such as `byte[]`, `string[]`, `Dictionary<string, string>`) and complex object graphs that don't have any validators.</span></span>

## <a name="disable-validation"></a><span data-ttu-id="f9edf-245">Doğrulamayı devre dışı bırak</span><span class="sxs-lookup"><span data-stu-id="f9edf-245">Disable validation</span></span>

<span data-ttu-id="f9edf-246">Doğrulamayı devre dışı bırakmak için:</span><span class="sxs-lookup"><span data-stu-id="f9edf-246">To disable validation:</span></span>

1. <span data-ttu-id="f9edf-247">`IObjectModelValidator`Hiçbir alanı geçersiz olarak işaretlememeyen bir uygulama oluşturun.</span><span class="sxs-lookup"><span data-stu-id="f9edf-247">Create an implementation of `IObjectModelValidator` that doesn't mark any fields as invalid.</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Validation/NullObjectModelValidator.cs?name=snippet_Class)]

1. <span data-ttu-id="f9edf-248">`Startup.ConfigureServices`Bağımlılık ekleme kapsayıcısında varsayılan uygulamayı değiştirmek için aşağıdaki kodu ekleyin `IObjectModelValidator` .</span><span class="sxs-lookup"><span data-stu-id="f9edf-248">Add the following code to `Startup.ConfigureServices` to replace the default `IObjectModelValidator` implementation in the dependency injection container.</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_DisableValidation)]

<span data-ttu-id="f9edf-249">Model bağlamalarından kaynaklanan model durumu hatalarını görmeye devam edebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="f9edf-249">You might still see model state errors that originate from model binding.</span></span>

## <a name="client-side-validation"></a><span data-ttu-id="f9edf-250">İstemci tarafı doğrulaması</span><span class="sxs-lookup"><span data-stu-id="f9edf-250">Client-side validation</span></span>

<span data-ttu-id="f9edf-251">İstemci tarafı doğrulama, form geçerli olana kadar gönderimi önler.</span><span class="sxs-lookup"><span data-stu-id="f9edf-251">Client-side validation prevents submission until the form is valid.</span></span> <span data-ttu-id="f9edf-252">Gönder düğmesi, formu gönderen veya hata iletilerini görüntüleyen JavaScript 'ı çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="f9edf-252">The Submit button runs JavaScript that either submits the form or displays error messages.</span></span>

<span data-ttu-id="f9edf-253">Bir form üzerinde giriş hataları olduğunda, istemci tarafı doğrulaması sunucuya gereksiz gidiş dönüş önler.</span><span class="sxs-lookup"><span data-stu-id="f9edf-253">Client-side validation avoids an unnecessary round trip to the server when there are input errors on a form.</span></span> <span data-ttu-id="f9edf-254">*_Layout. cshtml* ve *_ValidationScriptsPartial. cshtml* ' deki aşağıdaki betik başvuruları istemci tarafı doğrulamayı destekler:</span><span class="sxs-lookup"><span data-stu-id="f9edf-254">The following script references in *_Layout.cshtml* and *_ValidationScriptsPartial.cshtml* support client-side validation:</span></span>

[!code-cshtml[](validation/samples/3.x/ValidationSample/Views/Shared/_Layout.cshtml?name=snippet_Scripts)]

[!code-cshtml[](validation/samples/3.x/ValidationSample/Views/Shared/_ValidationScriptsPartial.cshtml?name=snippet_Scripts)]

<span data-ttu-id="f9edf-255">[JQuery unobtrusive doğrulama](https://github.com/aspnet/jquery-validation-unobtrusive) betiği, popüler [jQuery doğrulama](https://jqueryvalidation.org/) eklentisi üzerinde derleme yapan özel bir Microsoft ön uç kitaplığıdır.</span><span class="sxs-lookup"><span data-stu-id="f9edf-255">The [jQuery Unobtrusive Validation](https://github.com/aspnet/jquery-validation-unobtrusive) script is a custom Microsoft front-end library that builds on the popular [jQuery Validation](https://jqueryvalidation.org/) plugin.</span></span> <span data-ttu-id="f9edf-256">JQuery unobtrusive doğrulaması olmadan, iki yerde aynı doğrulama mantığını kodlamakta olmanız gerekir: model özelliklerindeki Sunucu tarafı doğrulama özniteliklerinde bir kez ve sonra istemci tarafı betiklerimizde.</span><span class="sxs-lookup"><span data-stu-id="f9edf-256">Without jQuery Unobtrusive Validation, you would have to code the same validation logic in two places: once in the server-side validation attributes on model properties, and then again in client-side scripts.</span></span> <span data-ttu-id="f9edf-257">Bunun yerine, [Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro) ve [HTML Yardımcıları](xref:mvc/views/overview) , doğrulama `data-` gerektiren form öğeleri için HTML 5 özniteliklerini işlemek üzere model özelliklerinden doğrulama özniteliklerini ve tür meta verilerini kullanır.</span><span class="sxs-lookup"><span data-stu-id="f9edf-257">Instead, [Tag Helpers](xref:mvc/views/tag-helpers/intro) and [HTML helpers](xref:mvc/views/overview) use the validation attributes and type metadata from model properties to render HTML 5 `data-` attributes for the form elements that need validation.</span></span> <span data-ttu-id="f9edf-258">jQuery unobtrusive doğrulaması öznitelikleri ayrıştırır `data-` ve mantığı jQuery doğrulamasına geçirir ve sunucu tarafı doğrulama mantığını istemciye etkin bir şekilde "kopyalıyor".</span><span class="sxs-lookup"><span data-stu-id="f9edf-258">jQuery Unobtrusive Validation parses the `data-` attributes and passes the logic to jQuery Validation, effectively "copying" the server-side validation logic to the client.</span></span> <span data-ttu-id="f9edf-259">Aşağıda gösterildiği gibi, etiket yardımcıları kullanarak istemcisinde doğrulama hatalarını görüntüleyebilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="f9edf-259">You can display validation errors on the client using tag helpers as shown here:</span></span>

[!code-cshtml[](validation/samples/3.x/ValidationSample/Pages/Movies/Create.cshtml?name=snippet_ReleaseDate&highlight=3-4)]

<span data-ttu-id="f9edf-260">Önceki etiket yardımcıları aşağıdaki HTML 'yi işler:</span><span class="sxs-lookup"><span data-stu-id="f9edf-260">The preceding tag helpers render the following HTML:</span></span>

```html
<div class="form-group">
    <label class="control-label" for="Movie_ReleaseDate">Release Date</label>
    <input class="form-control" type="date" data-val="true"
        data-val-required="The Release Date field is required."
        id="Movie_ReleaseDate" name="Movie.ReleaseDate" value="">
    <span class="text-danger field-validation-valid"
        data-valmsg-for="Movie.ReleaseDate" data-valmsg-replace="true"></span>
</div>
```

<span data-ttu-id="f9edf-261">`data-`HTML çıkışındaki özniteliklerin, özelliği için doğrulama özniteliklerine karşılık geldiğini unutmayın `Movie.ReleaseDate` .</span><span class="sxs-lookup"><span data-stu-id="f9edf-261">Notice that the `data-` attributes in the HTML output correspond to the validation attributes for the `Movie.ReleaseDate` property.</span></span> <span data-ttu-id="f9edf-262">`data-val-required`Öznitelik, Kullanıcı Yayın tarihi alanını doldurmazsa, görüntülenecek bir hata iletisi içerir.</span><span class="sxs-lookup"><span data-stu-id="f9edf-262">The `data-val-required` attribute contains an error message to display if the user doesn't fill in the release date field.</span></span> <span data-ttu-id="f9edf-263">jQuery unobtrusive doğrulaması bu değeri jQuery doğrulaması [Required ()](https://jqueryvalidation.org/required-method/) yöntemine geçirir ve sonra bu iletiyi eşlik eden **\<span>** öğede görüntüler.</span><span class="sxs-lookup"><span data-stu-id="f9edf-263">jQuery Unobtrusive Validation passes this value to the jQuery Validation [required()](https://jqueryvalidation.org/required-method/) method, which then displays that message in the accompanying **\<span>** element.</span></span>

<span data-ttu-id="f9edf-264">Veri türü doğrulama, bir öznitelik tarafından geçersiz kılınmadığı müddetçe, özelliğin .NET türünü temel alır `[DataType]` .</span><span class="sxs-lookup"><span data-stu-id="f9edf-264">Data type validation is based on the .NET type of a property, unless that is overridden by a `[DataType]` attribute.</span></span> <span data-ttu-id="f9edf-265">Tarayıcıların kendi varsayılan hata iletileri vardır ancak jQuery doğrulaması unobtrusive doğrulama paketi bu iletileri geçersiz kılabilir.</span><span class="sxs-lookup"><span data-stu-id="f9edf-265">Browsers have their own default error messages, but the jQuery Validation Unobtrusive Validation package can override those messages.</span></span> <span data-ttu-id="f9edf-266">`[DataType]`gibi öznitelikler ve alt sınıflar `[EmailAddress]` , hata iletisini belirtmenize izin verir.</span><span class="sxs-lookup"><span data-stu-id="f9edf-266">`[DataType]` attributes and subclasses such as `[EmailAddress]` let you specify the error message.</span></span>

## <a name="unobtrusive-validation"></a><span data-ttu-id="f9edf-267">Unobtrusive doğrulaması</span><span class="sxs-lookup"><span data-stu-id="f9edf-267">Unobtrusive validation</span></span>

<span data-ttu-id="f9edf-268">Obtrusive doğrulaması hakkında bilgi için [Bu GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/1111)bakın.</span><span class="sxs-lookup"><span data-stu-id="f9edf-268">For information on unobtrusive validation, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/1111).</span></span>

### <a name="add-validation-to-dynamic-forms"></a><span data-ttu-id="f9edf-269">Dinamik formlara doğrulama ekleme</span><span class="sxs-lookup"><span data-stu-id="f9edf-269">Add Validation to Dynamic Forms</span></span>

<span data-ttu-id="f9edf-270">jQuery unobtrusive doğrulaması, sayfa ilk kez yüklendiğinde, jQuery doğrulamasına yönelik doğrulama mantığını ve parametreleri geçirir.</span><span class="sxs-lookup"><span data-stu-id="f9edf-270">jQuery Unobtrusive Validation passes validation logic and parameters to jQuery Validation when the page first loads.</span></span> <span data-ttu-id="f9edf-271">Bu nedenle, doğrulama dinamik olarak üretilen formlarda otomatik olarak çalışmaz.</span><span class="sxs-lookup"><span data-stu-id="f9edf-271">Therefore, validation doesn't work automatically on dynamically generated forms.</span></span> <span data-ttu-id="f9edf-272">Doğrulamayı etkinleştirmek için, jQuery 'ten kaçınmaya yönelik doğrulamayı, dinamik formu oluşturduktan hemen sonra ayrıştırmaya söyleyin.</span><span class="sxs-lookup"><span data-stu-id="f9edf-272">To enable validation, tell jQuery Unobtrusive Validation to parse the dynamic form immediately after you create it.</span></span> <span data-ttu-id="f9edf-273">Örneğin, aşağıdaki kod, AJAX aracılığıyla eklenen bir formda istemci tarafı doğrulamayı ayarlar.</span><span class="sxs-lookup"><span data-stu-id="f9edf-273">For example, the following code sets up client-side validation on a form added via AJAX.</span></span>

```javascript
$.get({
    url: "https://url/that/returns/a/form",
    dataType: "html",
    error: function(jqXHR, textStatus, errorThrown) {
        alert(textStatus + ": Couldn't add form. " + errorThrown);
    },
    success: function(newFormHTML) {
        var container = document.getElementById("form-container");
        container.insertAdjacentHTML("beforeend", newFormHTML);
        var forms = container.getElementsByTagName("form");
        var newForm = forms[forms.length - 1];
        $.validator.unobtrusive.parse(newForm);
    }
})
```

<span data-ttu-id="f9edf-274">`$.validator.unobtrusive.parse()`Yöntemi, bir bağımsız değişkeni olarak bir jQuery seçiciyi kabul eder.</span><span class="sxs-lookup"><span data-stu-id="f9edf-274">The `$.validator.unobtrusive.parse()` method accepts a jQuery selector for its one argument.</span></span> <span data-ttu-id="f9edf-275">Bu yöntem, jQuery 'in bu `data-` seçicideki formların özniteliklerini ayrıştırmasına izin vermez.</span><span class="sxs-lookup"><span data-stu-id="f9edf-275">This method tells jQuery Unobtrusive Validation to parse the `data-` attributes of forms within that selector.</span></span> <span data-ttu-id="f9edf-276">Daha sonra bu özniteliklerin değerleri jQuery doğrulama eklentisine geçirilir.</span><span class="sxs-lookup"><span data-stu-id="f9edf-276">The values of those attributes are then passed to the jQuery Validation plugin.</span></span>

### <a name="add-validation-to-dynamic-controls"></a><span data-ttu-id="f9edf-277">Dinamik denetimlere doğrulama ekleme</span><span class="sxs-lookup"><span data-stu-id="f9edf-277">Add Validation to Dynamic Controls</span></span>

<span data-ttu-id="f9edf-278">`$.validator.unobtrusive.parse()`Yöntemi, ve gibi dinamik olarak üretilen denetimlerde değil, formun tamamında işe yarar `<input>` `<select/>` .</span><span class="sxs-lookup"><span data-stu-id="f9edf-278">The `$.validator.unobtrusive.parse()` method works on an entire form, not on individual dynamically generated controls, such as `<input>` and `<select/>`.</span></span> <span data-ttu-id="f9edf-279">Formu yeniden oluşturmak için, aşağıdaki örnekte gösterildiği gibi, form daha önce ayrıştırıldığında eklenen doğrulama verilerini kaldırın:</span><span class="sxs-lookup"><span data-stu-id="f9edf-279">To reparse the form, remove the validation data that was added when the form was parsed earlier, as shown in the following example:</span></span>

```javascript
$.get({
    url: "https://url/that/returns/a/control",
    dataType: "html",
    error: function(jqXHR, textStatus, errorThrown) {
        alert(textStatus + ": Couldn't add control. " + errorThrown);
    },
    success: function(newInputHTML) {
        var form = document.getElementById("my-form");
        form.insertAdjacentHTML("beforeend", newInputHTML);
        $(form).removeData("validator")    // Added by jQuery Validation
               .removeData("unobtrusiveValidation");   // Added by jQuery Unobtrusive Validation
        $.validator.unobtrusive.parse(form);
    }
})
```

## <a name="custom-client-side-validation"></a><span data-ttu-id="f9edf-280">Özel istemci tarafı doğrulaması</span><span class="sxs-lookup"><span data-stu-id="f9edf-280">Custom client-side validation</span></span>

<span data-ttu-id="f9edf-281">Özel istemci tarafı doğrulaması, `data-` özel bir jQuery doğrulama bağdaştırıcısıyla çalışan HTML öznitelikleri oluşturarak yapılır.</span><span class="sxs-lookup"><span data-stu-id="f9edf-281">Custom client-side validation is done by generating `data-` HTML attributes that work with a custom jQuery Validation adapter.</span></span> <span data-ttu-id="f9edf-282">Aşağıdaki örnek bağdaştırıcı kodu, `[ClassicMovie]` `[ClassicMovieWithClientValidator]` Bu makalede daha önce sunulan ve öznitelikleri için yazılmıştır:</span><span class="sxs-lookup"><span data-stu-id="f9edf-282">The following sample adapter code was written for the `[ClassicMovie]` and `[ClassicMovieWithClientValidator]` attributes that were introduced earlier in this article:</span></span>

[!code-javascript[](validation/samples/3.x/ValidationSample/wwwroot/js/classicMovieValidator.js)]

<span data-ttu-id="f9edf-283">Bağdaştırıcıların nasıl yazılacağı hakkında daha fazla bilgi için [jQuery doğrulama belgelerine](https://jqueryvalidation.org/documentation/)bakın.</span><span class="sxs-lookup"><span data-stu-id="f9edf-283">For information about how to write adapters, see the [jQuery Validation documentation](https://jqueryvalidation.org/documentation/).</span></span>

<span data-ttu-id="f9edf-284">Belirli bir alan için bir bağdaştırıcının kullanımı, şu öznitelikler tarafından tetiklenir `data-` :</span><span class="sxs-lookup"><span data-stu-id="f9edf-284">The use of an adapter for a given field is triggered by `data-` attributes that:</span></span>

* <span data-ttu-id="f9edf-285">Alana, doğrulamaya () tabi olacak şekilde bayrak ekleyin `data-val="true"` .</span><span class="sxs-lookup"><span data-stu-id="f9edf-285">Flag the field as being subject to validation (`data-val="true"`).</span></span>
* <span data-ttu-id="f9edf-286">Bir doğrulama kuralı adı ve hata iletisi metni (örneğin,) belirler `data-val-rulename="Error message."` .</span><span class="sxs-lookup"><span data-stu-id="f9edf-286">Identify a validation rule name and error message text (for example, `data-val-rulename="Error message."`).</span></span>
* <span data-ttu-id="f9edf-287">Doğrulayıcı ihtiyaçlarına ek parametreler sağlayın (örneğin, `data-val-rulename-param1="value"` ).</span><span class="sxs-lookup"><span data-stu-id="f9edf-287">Provide any additional parameters the validator needs (for example, `data-val-rulename-param1="value"`).</span></span>

<span data-ttu-id="f9edf-288">Aşağıdaki örnek, `data-` örnek uygulamanın özniteliği için öznitelikleri gösterir `ClassicMovie` :</span><span class="sxs-lookup"><span data-stu-id="f9edf-288">The following example shows the `data-` attributes for the sample app's `ClassicMovie` attribute:</span></span>

```html
<input class="form-control" type="date"
    data-val="true"
    data-val-classicmovie="Classic movies must have a release year no later than 1960."
    data-val-classicmovie-year="1960"
    data-val-required="The Release Date field is required."
    id="Movie_ReleaseDate" name="Movie.ReleaseDate" value="">
```

<span data-ttu-id="f9edf-289">Daha önce belirtildiği gibi, [Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro) ve [HTML Yardımcıları](xref:mvc/views/overview) , öznitelikleri işlemek için doğrulama özniteliklerinden bilgileri kullanır `data-` .</span><span class="sxs-lookup"><span data-stu-id="f9edf-289">As noted earlier, [Tag Helpers](xref:mvc/views/tag-helpers/intro) and [HTML helpers](xref:mvc/views/overview) use information from validation attributes to render `data-` attributes.</span></span> <span data-ttu-id="f9edf-290">Özel HTML özniteliklerinin oluşturulmasına neden olan kod yazmak için iki seçenek vardır `data-` :</span><span class="sxs-lookup"><span data-stu-id="f9edf-290">There are two options for writing code that results in the creation of custom `data-` HTML attributes:</span></span>

* <span data-ttu-id="f9edf-291">Öğesinden türeten bir sınıf oluşturun ve `AttributeAdapterBase<TAttribute>` `IValidationAttributeAdapterProvider` özniteliği ve kendı bağdaştırıcısını dı olarak kaydedin.</span><span class="sxs-lookup"><span data-stu-id="f9edf-291">Create a class that derives from `AttributeAdapterBase<TAttribute>` and a class that implements `IValidationAttributeAdapterProvider`, and register your attribute and its adapter in DI.</span></span> <span data-ttu-id="f9edf-292">Bu yöntem, sunucu ile ilgili ve istemciyle ilgili doğrulama kodundaki [tek sorumluluk sorumlusunu](https://wikipedia.org/wiki/Single_responsibility_principle) , ayrı sınıflarda izler.</span><span class="sxs-lookup"><span data-stu-id="f9edf-292">This method follows the [single responsibility principal](https://wikipedia.org/wiki/Single_responsibility_principle) in that server-related and client-related validation code is in separate classes.</span></span> <span data-ttu-id="f9edf-293">Ayrıca bağdaştırıcı, DI ' de kaydolduğundan bu yana de bunun avantajına sahiptir.</span><span class="sxs-lookup"><span data-stu-id="f9edf-293">The adapter also has the advantage that since it is registered in DI, other services in DI are available to it if needed.</span></span>
* <span data-ttu-id="f9edf-294">`IClientModelValidator` `ValidationAttribute` Sınıfınıza uygulayın.</span><span class="sxs-lookup"><span data-stu-id="f9edf-294">Implement `IClientModelValidator` in your `ValidationAttribute` class.</span></span> <span data-ttu-id="f9edf-295">Bu yöntem, öznitelik herhangi bir sunucu tarafı doğrulaması yapamazsa ve hiçbir hizmete gerek duymazsa uygun olabilir.</span><span class="sxs-lookup"><span data-stu-id="f9edf-295">This method might be appropriate if the attribute doesn't do any server-side validation and doesn't need any services from DI.</span></span>

### <a name="attributeadapter-for-client-side-validation"></a><span data-ttu-id="f9edf-296">İstemci tarafı doğrulaması için AttributeAdapter</span><span class="sxs-lookup"><span data-stu-id="f9edf-296">AttributeAdapter for client-side validation</span></span>

<span data-ttu-id="f9edf-297">`data-`HTML 'de öznitelikleri işleme yöntemi `ClassicMovie` örnek uygulamadaki özniteliği tarafından kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f9edf-297">This method of rendering `data-` attributes in HTML is used by the `ClassicMovie` attribute in the sample app.</span></span> <span data-ttu-id="f9edf-298">Bu yöntemi kullanarak istemci doğrulaması eklemek için:</span><span class="sxs-lookup"><span data-stu-id="f9edf-298">To add client validation by using this method:</span></span>

1. <span data-ttu-id="f9edf-299">Özel doğrulama özniteliği için bir öznitelik bağdaştırıcı sınıfı oluşturun.</span><span class="sxs-lookup"><span data-stu-id="f9edf-299">Create an attribute adapter class for the custom validation attribute.</span></span> <span data-ttu-id="f9edf-300">Özniteliği [Attributeadapterbase \<T> ](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.attributeadapterbase-1?view=aspnetcore-2.2)'den türet.</span><span class="sxs-lookup"><span data-stu-id="f9edf-300">Derive the class from [AttributeAdapterBase\<T>](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.attributeadapterbase-1?view=aspnetcore-2.2).</span></span> <span data-ttu-id="f9edf-301">`AddValidation` `data-` Aşağıdaki örnekte gösterildiği gibi, işlenen çıktıya öznitelikler ekleyen bir yöntem oluşturun:</span><span class="sxs-lookup"><span data-stu-id="f9edf-301">Create an `AddValidation` method that adds `data-` attributes to the rendered output, as shown in this example:</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Validation/ClassicMovieAttributeAdapter.cs?name=snippet_Class)]

1. <span data-ttu-id="f9edf-302">Uygulayan bir bağdaştırıcı sağlayıcısı sınıfı oluşturun <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.IValidationAttributeAdapterProvider> .</span><span class="sxs-lookup"><span data-stu-id="f9edf-302">Create an adapter provider class that implements <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.IValidationAttributeAdapterProvider>.</span></span> <span data-ttu-id="f9edf-303">Yöntemi içinde `GetAttributeAdapter` , aşağıdaki örnekte gösterildiği gibi, özel özniteliğini bağdaştırıcının oluşturucusuna geçirin:</span><span class="sxs-lookup"><span data-stu-id="f9edf-303">In the `GetAttributeAdapter` method pass in the custom attribute to the adapter's constructor, as shown in this example:</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Validation/CustomValidationAttributeAdapterProvider.cs?name=snippet_Class)]

1. <span data-ttu-id="f9edf-304">Dı için bağdaştırıcı sağlayıcısını Kaydet `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="f9edf-304">Register the adapter provider for DI in `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_Configuration&highlight=9-10)]

### <a name="iclientmodelvalidator-for-client-side-validation"></a><span data-ttu-id="f9edf-305">İstemci tarafı doğrulaması için ılientmodelvalidator</span><span class="sxs-lookup"><span data-stu-id="f9edf-305">IClientModelValidator for client-side validation</span></span>

<span data-ttu-id="f9edf-306">`data-`HTML 'de öznitelikleri işleme yöntemi `ClassicMovieWithClientValidator` örnek uygulamadaki özniteliği tarafından kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f9edf-306">This method of rendering `data-` attributes in HTML is used by the `ClassicMovieWithClientValidator` attribute in the sample app.</span></span> <span data-ttu-id="f9edf-307">Bu yöntemi kullanarak istemci doğrulaması eklemek için:</span><span class="sxs-lookup"><span data-stu-id="f9edf-307">To add client validation by using this method:</span></span>

* <span data-ttu-id="f9edf-308">Özel doğrulama özniteliğinde, `IClientModelValidator` arabirimini uygulayın ve bir `AddValidation` Yöntem oluşturun.</span><span class="sxs-lookup"><span data-stu-id="f9edf-308">In the custom validation attribute, implement the `IClientModelValidator` interface and create an `AddValidation` method.</span></span> <span data-ttu-id="f9edf-309">Yönteminde, `AddValidation` `data-` Aşağıdaki örnekte gösterildiği gibi, doğrulama için öznitelikler ekleyin:</span><span class="sxs-lookup"><span data-stu-id="f9edf-309">In the `AddValidation` method, add `data-` attributes for validation, as shown in the following example:</span></span>

  [!code-csharp[](validation/samples/3.x/ValidationSample/Validation/ClassicMovieWithClientValidatorAttribute.cs?name=snippet_Class)]

## <a name="disable-client-side-validation"></a><span data-ttu-id="f9edf-310">İstemci tarafı doğrulamayı devre dışı bırak</span><span class="sxs-lookup"><span data-stu-id="f9edf-310">Disable client-side validation</span></span>

<span data-ttu-id="f9edf-311">Aşağıdaki kod sayfalarda istemci doğrulamasını devre dışı bırakır Razor :</span><span class="sxs-lookup"><span data-stu-id="f9edf-311">The following code disables client validation in Razor Pages:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_DisableClientValidation&highlight=2-5)]

<span data-ttu-id="f9edf-312">İstemci tarafı doğrulamayı devre dışı bırakmak için diğer seçenekler:</span><span class="sxs-lookup"><span data-stu-id="f9edf-312">Other options to disable client-side validation:</span></span>

* <span data-ttu-id="f9edf-313">`_ValidationScriptsPartial`Tüm *. cshtml* dosyalarındaki başvuruyu not edin.</span><span class="sxs-lookup"><span data-stu-id="f9edf-313">Comment out the reference to `_ValidationScriptsPartial` in all the *.cshtml* files.</span></span>
* <span data-ttu-id="f9edf-314">*Pages\shared \_ validationscriptspartial. cshtml* dosyasının içeriğini kaldırın.</span><span class="sxs-lookup"><span data-stu-id="f9edf-314">Remove the contents of the *Pages\Shared\_ValidationScriptsPartial.cshtml* file.</span></span>

<span data-ttu-id="f9edf-315">Önceki yaklaşım ASP.NET Core sınıf kitaplığının istemci tarafında doğrulanmasını engellemez Identity Razor .</span><span class="sxs-lookup"><span data-stu-id="f9edf-315">The preceding approach won't prevent client side validation of ASP.NET Core Identity Razor Class Library.</span></span> <span data-ttu-id="f9edf-316">Daha fazla bilgi için bkz. <xref:security/authentication/scaffold-identity>.</span><span class="sxs-lookup"><span data-stu-id="f9edf-316">For more information, see <xref:security/authentication/scaffold-identity>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f9edf-317">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="f9edf-317">Additional resources</span></span>

* [<span data-ttu-id="f9edf-318">System. ComponentModel. Dataaçıklamalarda ad alanı</span><span class="sxs-lookup"><span data-stu-id="f9edf-318">System.ComponentModel.DataAnnotations namespace</span></span>](xref:System.ComponentModel.DataAnnotations)
* [<span data-ttu-id="f9edf-319">Model bağlama</span><span class="sxs-lookup"><span data-stu-id="f9edf-319">Model Binding</span></span>](model-binding.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f9edf-320">Bu makalede, ASP.NET Core MVC veya Pages uygulamasında Kullanıcı girişinin nasıl doğrulanacağı açıklanır Razor .</span><span class="sxs-lookup"><span data-stu-id="f9edf-320">This article explains how to validate user input in an ASP.NET Core MVC or Razor Pages app.</span></span>

<span data-ttu-id="f9edf-321">[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/sample) ([nasıl indirilir](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="f9edf-321">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="model-state"></a><span data-ttu-id="f9edf-322">Model durumu</span><span class="sxs-lookup"><span data-stu-id="f9edf-322">Model state</span></span>

<span data-ttu-id="f9edf-323">Model durumu iki alt sistemden gelen hataları temsil eder: model bağlama ve model doğrulama.</span><span class="sxs-lookup"><span data-stu-id="f9edf-323">Model state represents errors that come from two subsystems: model binding and model validation.</span></span> <span data-ttu-id="f9edf-324">[Model bağlamasından](model-binding.md) kaynaklanan hatalar genellikle veri dönüştürme hatalardır (örneğin, bir tamsayı bekleyen bir alana bir "x" girilir).</span><span class="sxs-lookup"><span data-stu-id="f9edf-324">Errors that originate from [model binding](model-binding.md) are generally data conversion errors (for example, an "x" is entered in a field that expects an integer).</span></span> <span data-ttu-id="f9edf-325">Model bağlama ve verilerin iş kurallarına uygun olmadığı rapor hataları (örneğin, 1 ile 5 arasında bir derecelendirme bekleyen bir alana bir 0 girildiğinde) oluşturulduktan sonra model doğrulaması oluşur.</span><span class="sxs-lookup"><span data-stu-id="f9edf-325">Model validation occurs after model binding and reports errors where the data doesn't conform to business rules (for example, a 0 is entered in a field that expects a rating between 1 and 5).</span></span>

<span data-ttu-id="f9edf-326">Hem model bağlama hem de doğrulama, bir denetleyici eyleminin veya bir sayfa işleyici yönteminin yürütülmesinden önce oluşur Razor .</span><span class="sxs-lookup"><span data-stu-id="f9edf-326">Both model binding and validation occur before the execution of a controller action or a Razor Pages handler method.</span></span> <span data-ttu-id="f9edf-327">Web uygulamaları için uygulama, `ModelState.IsValid` uygun şekilde inceleme ve tepki verme sorumluluğundadır.</span><span class="sxs-lookup"><span data-stu-id="f9edf-327">For web apps, it's the app's responsibility to inspect `ModelState.IsValid` and react appropriately.</span></span> <span data-ttu-id="f9edf-328">Web Apps genellikle sayfayı bir hata iletisiyle yeniden görüntülerdi:</span><span class="sxs-lookup"><span data-stu-id="f9edf-328">Web apps typically redisplay the page with an error message:</span></span>

[!code-csharp[](validation/samples_snapshot/2.x/Create.cshtml.cs?name=snippet&highlight=3-6)]

<span data-ttu-id="f9edf-329">Web API denetleyicilerinin `ModelState.IsValid` özniteliğe sahip olup olmadığını kontrol etmek zorunda değildir `[ApiController]` .</span><span class="sxs-lookup"><span data-stu-id="f9edf-329">Web API controllers don't have to check `ModelState.IsValid` if they have the `[ApiController]` attribute.</span></span> <span data-ttu-id="f9edf-330">Bu durumda, model durumu geçersiz olduğunda hata ayrıntılarını içeren bir otomatik HTTP 400 yanıtı döndürülür.</span><span class="sxs-lookup"><span data-stu-id="f9edf-330">In that case, an automatic HTTP 400 response containing error details is returned when model state is invalid.</span></span> <span data-ttu-id="f9edf-331">Daha fazla bilgi için bkz. [OTOMATIK HTTP 400 yanıtları](xref:web-api/index#automatic-http-400-responses).</span><span class="sxs-lookup"><span data-stu-id="f9edf-331">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span>

## <a name="rerun-validation"></a><span data-ttu-id="f9edf-332">Doğrulamayı yeniden çalıştır</span><span class="sxs-lookup"><span data-stu-id="f9edf-332">Rerun validation</span></span>

<span data-ttu-id="f9edf-333">Doğrulama otomatiktir, ancak el ile yinelemek isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="f9edf-333">Validation is automatic, but you might want to repeat it manually.</span></span> <span data-ttu-id="f9edf-334">Örneğin, bir özellik için bir değer hesaplamanıza ve özelliği hesaplanan değere ayarladıktan sonra doğrulamayı yeniden çalıştırmak isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="f9edf-334">For example, you might compute a value for a property and want to rerun validation after setting the property to the computed value.</span></span> <span data-ttu-id="f9edf-335">Doğrulamayı yeniden çalıştırmak için, `TryValidateModel` yöntemi aşağıda gösterildiği gibi çağırın:</span><span class="sxs-lookup"><span data-stu-id="f9edf-335">To rerun validation, call the `TryValidateModel` method, as shown here:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/MoviesController.cs?name=snippet_TryValidateModel&highlight=11)]

## <a name="validation-attributes"></a><span data-ttu-id="f9edf-336">Doğrulama öznitelikleri</span><span class="sxs-lookup"><span data-stu-id="f9edf-336">Validation attributes</span></span>

<span data-ttu-id="f9edf-337">Doğrulama öznitelikleri, model özellikleri için doğrulama kuralları belirtmenize olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="f9edf-337">Validation attributes let you specify validation rules for model properties.</span></span> <span data-ttu-id="f9edf-338">[Örnek uygulamadaki](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/sample) aşağıdaki örnek, doğrulama öznitelikleriyle açıklama eklenmiş bir model sınıfı gösterir.</span><span class="sxs-lookup"><span data-stu-id="f9edf-338">The following example from [the sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/sample) shows a model class that is annotated with validation attributes.</span></span> <span data-ttu-id="f9edf-339">`[ClassicMovie]`Özniteliği özel bir doğrulama özniteliğidir ve diğerleri yerleşik olarak bulunur.</span><span class="sxs-lookup"><span data-stu-id="f9edf-339">The `[ClassicMovie]` attribute is a custom validation attribute and the others are built-in.</span></span> <span data-ttu-id="f9edf-340">Gösterilmez `[ClassicMovie2]` , özel bir özniteliği uygulamak için alternatif bir yol gösterir.</span><span class="sxs-lookup"><span data-stu-id="f9edf-340">Not shown is `[ClassicMovie2]`, which shows an alternative way to implement a custom attribute.</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Models/Movie.cs?name=snippet_ModelClass)]

## <a name="built-in-attributes"></a><span data-ttu-id="f9edf-341">Yerleşik öznitelikler</span><span class="sxs-lookup"><span data-stu-id="f9edf-341">Built-in attributes</span></span>

<span data-ttu-id="f9edf-342">Yerleşik doğrulama öznitelikleri şunlardır:</span><span class="sxs-lookup"><span data-stu-id="f9edf-342">Built-in validation attributes include:</span></span>

* <span data-ttu-id="f9edf-343">`[CreditCard]`: Özelliğin kredi kartı biçimine sahip olduğunu doğrular.</span><span class="sxs-lookup"><span data-stu-id="f9edf-343">`[CreditCard]`: Validates that the property has a credit card format.</span></span>
* <span data-ttu-id="f9edf-344">`[Compare]`: Bir modeldeki iki özelliği eşleştiğini doğrular.</span><span class="sxs-lookup"><span data-stu-id="f9edf-344">`[Compare]`: Validates that two properties in a model match.</span></span> <span data-ttu-id="f9edf-345">Örneğin, *register.cshtml.cs* dosyası, `[Compare]` girilen iki parola eşleşmesini doğrulamak için kullanır.</span><span class="sxs-lookup"><span data-stu-id="f9edf-345">For example, the *Register.cshtml.cs* file uses `[Compare]` to validate the two entered passwords match.</span></span> <span data-ttu-id="f9edf-346">[Yapı Identity İskelesi](xref:security/authentication/scaffold-identity) Kayıt kodunu görmek için.</span><span class="sxs-lookup"><span data-stu-id="f9edf-346">[Scaffold Identity](xref:security/authentication/scaffold-identity) to see the Register code.</span></span>
* <span data-ttu-id="f9edf-347">`[EmailAddress]`: Özelliğin bir e-posta biçimine sahip olduğunu doğrular.</span><span class="sxs-lookup"><span data-stu-id="f9edf-347">`[EmailAddress]`: Validates that the property has an email format.</span></span>
* <span data-ttu-id="f9edf-348">`[Phone]`: Özelliğin bir telefon numarası biçimine sahip olduğunu doğrular.</span><span class="sxs-lookup"><span data-stu-id="f9edf-348">`[Phone]`: Validates that the property has a telephone number format.</span></span>
* <span data-ttu-id="f9edf-349">`[Range]`: Özellik değerinin belirtilen bir aralık dahilinde olduğunu doğrular.</span><span class="sxs-lookup"><span data-stu-id="f9edf-349">`[Range]`: Validates that the property value falls within a specified range.</span></span>
* <span data-ttu-id="f9edf-350">`[RegularExpression]`: Özellik değerinin belirtilen bir normal ifadeyle eşleştiğini doğrular.</span><span class="sxs-lookup"><span data-stu-id="f9edf-350">`[RegularExpression]`: Validates that the property value matches a specified regular expression.</span></span>
* <span data-ttu-id="f9edf-351">`[Required]`: Alanın null olduğunu doğrular.</span><span class="sxs-lookup"><span data-stu-id="f9edf-351">`[Required]`: Validates that the field is not null.</span></span> <span data-ttu-id="f9edf-352">Bu özniteliğin davranışı hakkındaki ayrıntılar için bkz. [ `[Required]` özniteliği](#required-attribute) .</span><span class="sxs-lookup"><span data-stu-id="f9edf-352">See [`[Required]` attribute](#required-attribute) for details about this attribute's behavior.</span></span>
* <span data-ttu-id="f9edf-353">`[StringLength]`: Dize özellik değerinin belirtilen uzunluk sınırını aşmadığını doğrular.</span><span class="sxs-lookup"><span data-stu-id="f9edf-353">`[StringLength]`: Validates that a string property value doesn't exceed a specified length limit.</span></span>
* <span data-ttu-id="f9edf-354">`[Url]`: Özelliğin bir URL biçimine sahip olduğunu doğrular.</span><span class="sxs-lookup"><span data-stu-id="f9edf-354">`[Url]`: Validates that the property has a URL format.</span></span>
* <span data-ttu-id="f9edf-355">`[Remote]`: Sunucuda bir eylem yöntemi çağırarak istemcide girişi doğrular.</span><span class="sxs-lookup"><span data-stu-id="f9edf-355">`[Remote]`: Validates input on the client by calling an action method on the server.</span></span> <span data-ttu-id="f9edf-356">Bu özniteliğin davranışı hakkındaki ayrıntılar için bkz. [ `[Remote]` özniteliği](#remote-attribute) .</span><span class="sxs-lookup"><span data-stu-id="f9edf-356">See [`[Remote]` attribute](#remote-attribute) for details about this attribute's behavior.</span></span>

<span data-ttu-id="f9edf-357">`[RegularExpression]`Özniteliği istemci tarafı doğrulama ile kullanırken, Regex Istemcide JavaScript 'te yürütülür.</span><span class="sxs-lookup"><span data-stu-id="f9edf-357">When using the `[RegularExpression]` attribute with client-side validation, the regex is executed in JavaScript on the client.</span></span> <span data-ttu-id="f9edf-358">Bu, [ECMAScript](/dotnet/standard/base-types/regular-expression-options#ecmascript-matching-behavior) eşleştirme davranışının kullanılacağı anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="f9edf-358">This means [ECMAScript](/dotnet/standard/base-types/regular-expression-options#ecmascript-matching-behavior) matching behavior will be used.</span></span> <span data-ttu-id="f9edf-359">Daha fazla bilgi için [Bu GitHub sorununa](https://github.com/dotnet/corefx/issues/42487)bakın.</span><span class="sxs-lookup"><span data-stu-id="f9edf-359">For more information, see [this GitHub issue](https://github.com/dotnet/corefx/issues/42487).</span></span>

<span data-ttu-id="f9edf-360">Doğrulama özniteliklerinin tüm listesi [System. ComponentModel. Dataaçıklamalarda](xref:System.ComponentModel.DataAnnotations) ad alanında bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="f9edf-360">A complete list of validation attributes can be found in the [System.ComponentModel.DataAnnotations](xref:System.ComponentModel.DataAnnotations) namespace.</span></span>

### <a name="error-messages"></a><span data-ttu-id="f9edf-361">Hata iletileri</span><span class="sxs-lookup"><span data-stu-id="f9edf-361">Error messages</span></span>

<span data-ttu-id="f9edf-362">Doğrulama öznitelikleri, geçersiz giriş için görüntülenecek hata iletisini belirtmenize izin verir.</span><span class="sxs-lookup"><span data-stu-id="f9edf-362">Validation attributes let you specify the error message to be displayed for invalid input.</span></span> <span data-ttu-id="f9edf-363">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="f9edf-363">For example:</span></span>

```csharp
[StringLength(8, ErrorMessage = "Name length can't be more than 8.")]
```

<span data-ttu-id="f9edf-364">Dahili olarak, öznitelikler, `String.Format` alan adı için bir yer tutucu ve bazen ek yer tutucular ile çağrı yapılır.</span><span class="sxs-lookup"><span data-stu-id="f9edf-364">Internally, the attributes call `String.Format` with a placeholder for the field name and sometimes additional placeholders.</span></span> <span data-ttu-id="f9edf-365">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="f9edf-365">For example:</span></span>

```csharp
[StringLength(8, ErrorMessage = "{0} length must be between {2} and {1}.", MinimumLength = 6)]
```

<span data-ttu-id="f9edf-366">Bir `Name` özelliğe uygulandığında, yukarıdaki kod tarafından oluşturulan hata iletisi "ad uzunluğu 6 ile 8 arasında olmalıdır." olacaktır.</span><span class="sxs-lookup"><span data-stu-id="f9edf-366">When applied to a `Name` property, the error message created by the preceding code would be "Name length must be between 6 and 8.".</span></span>

<span data-ttu-id="f9edf-367">`String.Format`Belirli bir özniteliğin hata iletisinde hangi parametrelerin geçtiğini öğrenmek için, bkz. [dataaçıklamalarda kaynak kodu](https://github.com/dotnet/corefx/tree/master/src/System.ComponentModel.Annotations/src/System/ComponentModel/DataAnnotations).</span><span class="sxs-lookup"><span data-stu-id="f9edf-367">To find out which parameters are passed to `String.Format` for a particular attribute's error message, see the [DataAnnotations source code](https://github.com/dotnet/corefx/tree/master/src/System.ComponentModel.Annotations/src/System/ComponentModel/DataAnnotations).</span></span>

## <a name="required-attribute"></a><span data-ttu-id="f9edf-368">[Zorunlu] özniteliği</span><span class="sxs-lookup"><span data-stu-id="f9edf-368">[Required] attribute</span></span>

<span data-ttu-id="f9edf-369">Varsayılan olarak, doğrulama sistemi, null olamayan parametreleri veya özellikleri bir özniteliğe sahip gibi davranır `[Required]` .</span><span class="sxs-lookup"><span data-stu-id="f9edf-369">By default, the validation system treats non-nullable parameters or properties as if they had a `[Required]` attribute.</span></span> <span data-ttu-id="f9edf-370">Ve gibi [değer türleri](/dotnet/csharp/language-reference/keywords/value-types) `decimal` `int` null atanamaz.</span><span class="sxs-lookup"><span data-stu-id="f9edf-370">[Value types](/dotnet/csharp/language-reference/keywords/value-types) such as `decimal` and `int` are non-nullable.</span></span>

### <a name="required-validation-on-the-server"></a><span data-ttu-id="f9edf-371">[Zorunlu] sunucuda doğrulama</span><span class="sxs-lookup"><span data-stu-id="f9edf-371">[Required] validation on the server</span></span>

<span data-ttu-id="f9edf-372">Sunucuda, özelliği null ise gerekli bir değer eksik olarak kabul edilir.</span><span class="sxs-lookup"><span data-stu-id="f9edf-372">On the server, a required value is considered missing if the property is null.</span></span> <span data-ttu-id="f9edf-373">Null yapılamayan bir alan her zaman geçerlidir ve [gerekli] özniteliğinin hata mesajı hiçbir zaman gösterilmez.</span><span class="sxs-lookup"><span data-stu-id="f9edf-373">A non-nullable field is always valid, and the [Required] attribute's error message is never displayed.</span></span>

<span data-ttu-id="f9edf-374">Ancak, null olamayan bir özellik için model bağlama başarısız olabilir ve gibi bir hata mesajı elde edilir `The value '' is invalid` .</span><span class="sxs-lookup"><span data-stu-id="f9edf-374">However, model binding for a non-nullable property may fail, resulting in an error message such as `The value '' is invalid`.</span></span> <span data-ttu-id="f9edf-375">Null yapılamayan türlerin sunucu tarafı doğrulaması için özel bir hata iletisi belirtmek üzere aşağıdaki seçenekleriniz vardır:</span><span class="sxs-lookup"><span data-stu-id="f9edf-375">To specify a custom error message for server-side validation of non-nullable types, you have the following options:</span></span>

* <span data-ttu-id="f9edf-376">Alanı null yapılabilir yapın (örneğin, `decimal?` yerine `decimal` ).</span><span class="sxs-lookup"><span data-stu-id="f9edf-376">Make the field nullable (for example, `decimal?` instead of `decimal`).</span></span> <span data-ttu-id="f9edf-377">[Null \<T> yapılabilir](/dotnet/csharp/programming-guide/nullable-types/) değer türleri standart null yapılabilir türler gibi değerlendirilir.</span><span class="sxs-lookup"><span data-stu-id="f9edf-377">[Nullable\<T>](/dotnet/csharp/programming-guide/nullable-types/) value types are treated like standard nullable types.</span></span>
* <span data-ttu-id="f9edf-378">Aşağıdaki örnekte gösterildiği gibi model bağlama tarafından kullanılacak varsayılan hata iletisini belirtin:</span><span class="sxs-lookup"><span data-stu-id="f9edf-378">Specify the default error message to be used by model binding, as shown in the following example:</span></span>

  [!code-csharp[](validation/samples/2.x/ValidationSample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=4-5)]

  <span data-ttu-id="f9edf-379">İçin varsayılan iletileri ayarlayabileceğiniz model bağlama hataları hakkında daha fazla bilgi için bkz <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DefaultModelBindingMessageProvider#methods> ..</span><span class="sxs-lookup"><span data-stu-id="f9edf-379">For more information about model binding errors that you can set default messages for, see <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DefaultModelBindingMessageProvider#methods>.</span></span>

### <a name="required-validation-on-the-client"></a><span data-ttu-id="f9edf-380">[Zorunlu] istemcide doğrulama</span><span class="sxs-lookup"><span data-stu-id="f9edf-380">[Required] validation on the client</span></span>

<span data-ttu-id="f9edf-381">Null yapılamayan türler ve dizeler, sunucu ile karşılaştırıldığında, istemci üzerinde farklı şekilde işlenir.</span><span class="sxs-lookup"><span data-stu-id="f9edf-381">Non-nullable types and strings are handled differently on the client compared to the server.</span></span> <span data-ttu-id="f9edf-382">İstemcide:</span><span class="sxs-lookup"><span data-stu-id="f9edf-382">On the client:</span></span>

* <span data-ttu-id="f9edf-383">Yalnızca girdi girildiğinde bir değer vardır.</span><span class="sxs-lookup"><span data-stu-id="f9edf-383">A value is considered present only if input is entered for it.</span></span> <span data-ttu-id="f9edf-384">Bu nedenle, istemci tarafı doğrulaması null yapılamayan türler, null yapılabilir türler ile aynı şekilde işler.</span><span class="sxs-lookup"><span data-stu-id="f9edf-384">Therefore, client-side validation handles non-nullable types the same as nullable types.</span></span>
* <span data-ttu-id="f9edf-385">Bir dize alanındaki boşluk, jQuery doğrulaması [gerekli](https://jqueryvalidation.org/required-method/) yöntemi tarafından geçerli bir girdi olarak kabul edilir.</span><span class="sxs-lookup"><span data-stu-id="f9edf-385">Whitespace in a string field is considered valid input by the jQuery Validation [required](https://jqueryvalidation.org/required-method/) method.</span></span> <span data-ttu-id="f9edf-386">Yalnızca boşluk girildiğinde, sunucu tarafı doğrulaması gerekli bir dize alanını geçersiz kabul eder.</span><span class="sxs-lookup"><span data-stu-id="f9edf-386">Server-side validation considers a required string field invalid if only whitespace is entered.</span></span>

<span data-ttu-id="f9edf-387">Daha önce belirtildiği gibi, null olamayan türler bir özniteliğe sahip olsa da kabul edilir `[Required]` .</span><span class="sxs-lookup"><span data-stu-id="f9edf-387">As noted earlier, non-nullable types are treated as though they had a `[Required]` attribute.</span></span> <span data-ttu-id="f9edf-388">Bu, özniteliğini uygulamasanız bile istemci tarafı doğrulamayı alacağınız anlamına gelir `[Required]` .</span><span class="sxs-lookup"><span data-stu-id="f9edf-388">That means you get client-side validation even if you don't apply the `[Required]` attribute.</span></span> <span data-ttu-id="f9edf-389">Ancak özniteliğini kullanmazsanız varsayılan bir hata iletisi alırsınız.</span><span class="sxs-lookup"><span data-stu-id="f9edf-389">But if you don't use the attribute, you get a default error message.</span></span> <span data-ttu-id="f9edf-390">Özel bir hata iletisi belirtmek için özniteliğini kullanın.</span><span class="sxs-lookup"><span data-stu-id="f9edf-390">To specify a custom error message, use the attribute.</span></span>

## <a name="remote-attribute"></a><span data-ttu-id="f9edf-391">[Uzak] özniteliği</span><span class="sxs-lookup"><span data-stu-id="f9edf-391">[Remote] attribute</span></span>

<span data-ttu-id="f9edf-392">`[Remote]`Özniteliği, alan girişinin geçerli olup olmadığını anlamak için sunucu üzerinde bir yöntem çağrılmasını gerektiren istemci tarafı doğrulaması uygular.</span><span class="sxs-lookup"><span data-stu-id="f9edf-392">The `[Remote]` attribute implements client-side validation that requires calling a method on the server to determine whether field input is valid.</span></span> <span data-ttu-id="f9edf-393">Örneğin, uygulamanın bir kullanıcı adının zaten kullanımda olup olmadığını doğrulaması gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="f9edf-393">For example, the app may need to verify whether a user name is already in use.</span></span>

<span data-ttu-id="f9edf-394">Uzaktan doğrulamayı uygulamak için:</span><span class="sxs-lookup"><span data-stu-id="f9edf-394">To implement remote validation:</span></span>

1. <span data-ttu-id="f9edf-395">JavaScript 'e çağırmak için bir eylem yöntemi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="f9edf-395">Create an action method for JavaScript to call.</span></span>  <span data-ttu-id="f9edf-396">JQuery Validate [uzak](https://jqueryvalidation.org/remote-method/) YÖNTEMI bir JSON yanıtı bekliyor:</span><span class="sxs-lookup"><span data-stu-id="f9edf-396">The jQuery Validate [remote](https://jqueryvalidation.org/remote-method/) method expects a JSON response:</span></span>

   * <span data-ttu-id="f9edf-397">`"true"`giriş verilerinin geçerli olduğu anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="f9edf-397">`"true"` means the input data is valid.</span></span>
   * <span data-ttu-id="f9edf-398">`"false"`, `undefined` ya da `null` girişin geçersiz olduğu anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="f9edf-398">`"false"`, `undefined`, or `null` means the input is invalid.</span></span>  <span data-ttu-id="f9edf-399">Varsayılan hata iletisini görüntüler.</span><span class="sxs-lookup"><span data-stu-id="f9edf-399">Display the default error message.</span></span>
   * <span data-ttu-id="f9edf-400">Diğer herhangi bir dize, girişin geçersiz olduğu anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="f9edf-400">Any other string means the input is invalid.</span></span> <span data-ttu-id="f9edf-401">Dizeyi özel bir hata iletisi olarak görüntüleyin.</span><span class="sxs-lookup"><span data-stu-id="f9edf-401">Display the string as a custom error message.</span></span>

   <span data-ttu-id="f9edf-402">Özel bir hata iletisi döndüren eylem yöntemine bir örnek aşağıda verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="f9edf-402">Here's an example of an action method that returns a custom error message:</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyEmail)]

1. <span data-ttu-id="f9edf-403">Model sınıfında, `[Remote]` Aşağıdaki örnekte gösterildiği gibi, doğrulama eylemi yöntemine işaret eden bir özniteliğe sahip özelliğe açıklama ekleyin:</span><span class="sxs-lookup"><span data-stu-id="f9edf-403">In the model class, annotate the property with a `[Remote]` attribute that points to the validation action method, as shown in the following example:</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Models/User.cs?name=snippet_UserEmailProperty)]
 
   <span data-ttu-id="f9edf-404">`[Remote]`Özniteliği `Microsoft.AspNetCore.Mvc` ad alanıdır.</span><span class="sxs-lookup"><span data-stu-id="f9edf-404">The `[Remote]` attribute is in the `Microsoft.AspNetCore.Mvc` namespace.</span></span> <span data-ttu-id="f9edf-405">Veya metapackage kullanmıyorsanız [Microsoft. AspNetCore. Mvc. ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures) NuGet paketini yükleyebilirsiniz `Microsoft.AspNetCore.App` `Microsoft.AspNetCore.All` .</span><span class="sxs-lookup"><span data-stu-id="f9edf-405">Install the [Microsoft.AspNetCore.Mvc.ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures) NuGet package if you're not using the `Microsoft.AspNetCore.App` or `Microsoft.AspNetCore.All` metapackage.</span></span>
   
### <a name="additional-fields"></a><span data-ttu-id="f9edf-406">Ek alanlar</span><span class="sxs-lookup"><span data-stu-id="f9edf-406">Additional fields</span></span>

<span data-ttu-id="f9edf-407">`AdditionalFields`Özniteliğinin özelliği, `[Remote]` sunucudaki verilere karşı alan birleşimlerini doğrulamanızı sağlar.</span><span class="sxs-lookup"><span data-stu-id="f9edf-407">The `AdditionalFields` property of the `[Remote]` attribute lets you validate combinations of fields against data on the server.</span></span> <span data-ttu-id="f9edf-408">Örneğin, `User` model `FirstName` ve `LastName` özellikleri varsa, var olan hiçbir kullanıcının bu ad çiftine sahip olmadığını doğrulamak isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="f9edf-408">For example, if the `User` model had `FirstName` and `LastName` properties, you might want to verify that no existing users already have that pair of names.</span></span> <span data-ttu-id="f9edf-409">Aşağıdaki örnek nasıl kullanılacağını gösterir `AdditionalFields` :</span><span class="sxs-lookup"><span data-stu-id="f9edf-409">The following example shows how to use `AdditionalFields`:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Models/User.cs?name=snippet_UserNameProperties)]

<span data-ttu-id="f9edf-410">`AdditionalFields`açıkça dizelere ayarlanabilir `"FirstName"` `"LastName"` , ancak [NameOf](/dotnet/csharp/language-reference/keywords/nameof) işlecinin kullanılması daha sonra yeniden düzenlemeyi basitleştirir.</span><span class="sxs-lookup"><span data-stu-id="f9edf-410">`AdditionalFields` could be set explicitly to the strings `"FirstName"` and `"LastName"`, but using the [nameof](/dotnet/csharp/language-reference/keywords/nameof) operator simplifies later refactoring.</span></span> <span data-ttu-id="f9edf-411">Bu doğrulama için eylem yöntemi hem adı hem de soyadı bağımsız değişkenlerini kabul etmelidir:</span><span class="sxs-lookup"><span data-stu-id="f9edf-411">The action method for this validation must accept both first name and last name arguments:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyName)]

<span data-ttu-id="f9edf-412">Kullanıcı adı veya soyadı girdiğinde JavaScript, bu ad çiftinin alındığını görmek için uzak bir çağrı yapar.</span><span class="sxs-lookup"><span data-stu-id="f9edf-412">When the user enters a first or last name, JavaScript makes a remote call to see if that pair of names has been taken.</span></span>

<span data-ttu-id="f9edf-413">İki veya daha fazla ek alanı doğrulamak için bunları virgülle ayrılmış bir liste olarak belirtin.</span><span class="sxs-lookup"><span data-stu-id="f9edf-413">To validate two or more additional fields, provide them as a comma-delimited list.</span></span> <span data-ttu-id="f9edf-414">Örneğin, modele bir özellik eklemek için `MiddleName` `[Remote]` Aşağıdaki örnekte gösterildiği gibi özniteliği ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="f9edf-414">For example, to add a `MiddleName` property to the model, set the `[Remote]` attribute as shown in the following example:</span></span>

```csharp
[Remote(action: "VerifyName", controller: "Users", AdditionalFields = nameof(FirstName) + "," + nameof(LastName))]
public string MiddleName { get; set; }
```

<span data-ttu-id="f9edf-415">`AdditionalFields`Tüm öznitelik bağımsız değişkenleri gibi, sabit bir ifade olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="f9edf-415">`AdditionalFields`, like all attribute arguments, must be a constant expression.</span></span> <span data-ttu-id="f9edf-416">Bu nedenle, bir ara [değerli dize](/dotnet/csharp/language-reference/keywords/interpolated-strings) veya <xref:System.String.Join*> başlatmak için çağrı kullanmayın `AdditionalFields` .</span><span class="sxs-lookup"><span data-stu-id="f9edf-416">Therefore, don't use an [interpolated string](/dotnet/csharp/language-reference/keywords/interpolated-strings) or call <xref:System.String.Join*> to initialize `AdditionalFields`.</span></span>

## <a name="alternatives-to-built-in-attributes"></a><span data-ttu-id="f9edf-417">Yerleşik özniteliklerin alternatifleri</span><span class="sxs-lookup"><span data-stu-id="f9edf-417">Alternatives to built-in attributes</span></span>

<span data-ttu-id="f9edf-418">Yerleşik öznitelikler tarafından sağlanmayan doğrulamaya ihtiyacınız varsa şunları yapabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="f9edf-418">If you need validation not provided by built-in attributes, you can:</span></span>

* <span data-ttu-id="f9edf-419">[Özel öznitelikler oluşturun](#custom-attributes).</span><span class="sxs-lookup"><span data-stu-id="f9edf-419">[Create custom attributes](#custom-attributes).</span></span>
* <span data-ttu-id="f9edf-420">[IValidatableObject uygulayın](#ivalidatableobject).</span><span class="sxs-lookup"><span data-stu-id="f9edf-420">[Implement IValidatableObject](#ivalidatableobject).</span></span>

## <a name="custom-attributes"></a><span data-ttu-id="f9edf-421">Özel öznitelikler</span><span class="sxs-lookup"><span data-stu-id="f9edf-421">Custom attributes</span></span>

<span data-ttu-id="f9edf-422">Yerleşik doğrulama özniteliklerinin işlemeyen senaryolar için özel doğrulama öznitelikleri oluşturabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="f9edf-422">For scenarios that the built-in validation attributes don't handle, you can create custom validation attributes.</span></span> <span data-ttu-id="f9edf-423">Öğesinden devralan bir sınıf oluşturun <xref:System.ComponentModel.DataAnnotations.ValidationAttribute> ve yöntemi geçersiz kılın <xref:System.ComponentModel.DataAnnotations.ValidationAttribute.IsValid*> .</span><span class="sxs-lookup"><span data-stu-id="f9edf-423">Create a class that inherits from <xref:System.ComponentModel.DataAnnotations.ValidationAttribute>, and override the <xref:System.ComponentModel.DataAnnotations.ValidationAttribute.IsValid*> method.</span></span>

<span data-ttu-id="f9edf-424">`IsValid`Yöntemi, doğrulanacak girdi olan *Value*adlı bir nesne kabul eder.</span><span class="sxs-lookup"><span data-stu-id="f9edf-424">The `IsValid` method accepts an object named *value*, which is the input to be validated.</span></span> <span data-ttu-id="f9edf-425">Aşırı yükleme, `ValidationContext` model bağlama tarafından oluşturulan model örneği gibi ek bilgiler sağlayan bir nesneyi de kabul eder.</span><span class="sxs-lookup"><span data-stu-id="f9edf-425">An overload also accepts a `ValidationContext` object, which provides additional information, such as the model instance created by model binding.</span></span>

<span data-ttu-id="f9edf-426">Aşağıdaki örnek, *Klasik* tarz bir filmin yayın tarihinin belirtilen yıldan daha sonra olmadığını doğrular.</span><span class="sxs-lookup"><span data-stu-id="f9edf-426">The following example validates that the release date for a movie in the *Classic* genre isn't later than a specified year.</span></span> <span data-ttu-id="f9edf-427">`[ClassicMovie2]`Öznitelik önce tarzı denetler ve yalnızca *Klasik*ise devam eder.</span><span class="sxs-lookup"><span data-stu-id="f9edf-427">The `[ClassicMovie2]` attribute checks the genre first and continues only if it's *Classic*.</span></span> <span data-ttu-id="f9edf-428">Classics olarak tanımlanan filmler için, öznitelik oluşturucusuna geçirilen sınırdan daha sonra olmadığından emin olmak için yayın tarihini denetler.)</span><span class="sxs-lookup"><span data-stu-id="f9edf-428">For movies identified as classics, it checks the release date to make sure it's not later than the limit passed to the attribute constructor.)</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/ClassicMovieAttribute.cs?name=snippet_ClassicMovieAttribute)]

<span data-ttu-id="f9edf-429">`movie`Yukarıdaki örnekteki değişken, `Movie` form gönderiminde verileri içeren bir nesneyi temsil eder.</span><span class="sxs-lookup"><span data-stu-id="f9edf-429">The `movie` variable in the preceding example represents a `Movie` object that contains the data from the form submission.</span></span> <span data-ttu-id="f9edf-430">`IsValid`Yöntemi, tarihi ve tarzı denetler.</span><span class="sxs-lookup"><span data-stu-id="f9edf-430">The `IsValid` method checks the date and genre.</span></span> <span data-ttu-id="f9edf-431">Doğrulama başarıyla tamamlandığında, `IsValid` bir `ValidationResult.Success` kod döndürür.</span><span class="sxs-lookup"><span data-stu-id="f9edf-431">Upon successful validation, `IsValid` returns a `ValidationResult.Success` code.</span></span> <span data-ttu-id="f9edf-432">Doğrulama başarısız olduğunda, bir `ValidationResult` hata iletisi döndürür.</span><span class="sxs-lookup"><span data-stu-id="f9edf-432">When validation fails, a `ValidationResult` with an error message is returned.</span></span>

## <a name="ivalidatableobject"></a><span data-ttu-id="f9edf-433">IValidatableObject</span><span class="sxs-lookup"><span data-stu-id="f9edf-433">IValidatableObject</span></span>

<span data-ttu-id="f9edf-434">Yukarıdaki örnek yalnızca türlerle birlikte kullanılabilir `Movie` .</span><span class="sxs-lookup"><span data-stu-id="f9edf-434">The preceding example works only with `Movie` types.</span></span> <span data-ttu-id="f9edf-435">Aşağıdaki örnekte gösterildiği gibi, sınıf düzeyi doğrulama için başka bir seçenek de `IValidatableObject` model sınıfında uygulanır:</span><span class="sxs-lookup"><span data-stu-id="f9edf-435">Another option for class-level validation is to implement `IValidatableObject` in the model class, as shown in the following example:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Models/MovieIValidatable.cs?name=snippet&highlight=1,26-34)]

## <a name="top-level-node-validation"></a><span data-ttu-id="f9edf-436">Üst düzey düğüm doğrulaması</span><span class="sxs-lookup"><span data-stu-id="f9edf-436">Top-level node validation</span></span>

<span data-ttu-id="f9edf-437">Üst düzey düğümler şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="f9edf-437">Top-level nodes include:</span></span>

* <span data-ttu-id="f9edf-438">Eylem parametreleri</span><span class="sxs-lookup"><span data-stu-id="f9edf-438">Action parameters</span></span>
* <span data-ttu-id="f9edf-439">Denetleyici özellikleri</span><span class="sxs-lookup"><span data-stu-id="f9edf-439">Controller properties</span></span>
* <span data-ttu-id="f9edf-440">Sayfa işleyici parametreleri</span><span class="sxs-lookup"><span data-stu-id="f9edf-440">Page handler parameters</span></span>
* <span data-ttu-id="f9edf-441">Sayfa modeli özellikleri</span><span class="sxs-lookup"><span data-stu-id="f9edf-441">Page model properties</span></span>

<span data-ttu-id="f9edf-442">Model bağlantılı üst düzey düğümler, model özelliklerini doğrulamaya ek olarak onaylanır.</span><span class="sxs-lookup"><span data-stu-id="f9edf-442">Model-bound top-level nodes are validated in addition to validating model properties.</span></span> <span data-ttu-id="f9edf-443">Örnek uygulamadan aşağıdaki örnekte, `VerifyPhone` yöntemi <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute> eylem parametresini doğrulamak için öğesini kullanır `phone` :</span><span class="sxs-lookup"><span data-stu-id="f9edf-443">In the following example from the sample app, the `VerifyPhone` method uses the <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute> to validate the `phone` action parameter:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyPhone)]

<span data-ttu-id="f9edf-444">En üst düzey düğümler, <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute> doğrulama öznitelikleriyle birlikte kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="f9edf-444">Top-level nodes can use <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute> with validation attributes.</span></span> <span data-ttu-id="f9edf-445">Örnek uygulamadaki aşağıdaki örnekte, `CheckAge` yöntemi, `age` form gönderildiğinde parametrenin sorgu dizesinden bağlanması gerektiğini belirtir:</span><span class="sxs-lookup"><span data-stu-id="f9edf-445">In the following example from the sample app, the `CheckAge` method specifies that the `age` parameter must be bound from the query string when the form is submitted:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/UsersController.cs?name=snippet_CheckAge)]

<span data-ttu-id="f9edf-446">Denetim yaşı sayfasında (*Checkage. cshtml*) iki form vardır.</span><span class="sxs-lookup"><span data-stu-id="f9edf-446">In the Check Age page (*CheckAge.cshtml*), there are two forms.</span></span> <span data-ttu-id="f9edf-447">İlk form `Age` `99` bir değeri sorgu dizesi olarak gönderir: `https://localhost:5001/Users/CheckAge?Age=99` .</span><span class="sxs-lookup"><span data-stu-id="f9edf-447">The first form submits an `Age` value of `99` as a query string: `https://localhost:5001/Users/CheckAge?Age=99`.</span></span>

<span data-ttu-id="f9edf-448">Sorgu dizesinden düzgün şekilde biçimlendirilen bir `age` parametre gönderildiğinde, form doğrular.</span><span class="sxs-lookup"><span data-stu-id="f9edf-448">When a properly formatted `age` parameter from the query string is submitted, the form validates.</span></span>

<span data-ttu-id="f9edf-449">Denetim yaşı sayfasındaki ikinci form, `Age` isteğin gövdesindeki değeri gönderir ve doğrulama başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="f9edf-449">The second form on the Check Age page submits the `Age` value in the body of the request, and validation fails.</span></span> <span data-ttu-id="f9edf-450">`age`Parametre bir sorgu dizesinden gelmesi gerektiğinden bağlama başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="f9edf-450">Binding fails because the `age` parameter must come from a query string.</span></span>

<span data-ttu-id="f9edf-451">`CompatibilityVersion.Version_2_1`Veya sonraki sürümleriyle çalışırken, en üst düzey düğüm doğrulama varsayılan olarak etkindir.</span><span class="sxs-lookup"><span data-stu-id="f9edf-451">When running with `CompatibilityVersion.Version_2_1` or later, top-level node validation is enabled by default.</span></span> <span data-ttu-id="f9edf-452">Aksi takdirde, üst düzey düğüm doğrulaması devre dışı bırakılır.</span><span class="sxs-lookup"><span data-stu-id="f9edf-452">Otherwise, top-level node validation is disabled.</span></span> <span data-ttu-id="f9edf-453">Varsayılan seçenek, <xref:Microsoft.AspNetCore.Mvc.MvcOptions.AllowValidatingTopLevelNodes*> () içindeki özelliği `Startup.ConfigureServices` burada gösterildiği gibi ayarlanarak geçersiz kılınabilir:</span><span class="sxs-lookup"><span data-stu-id="f9edf-453">The default option can be overridden by setting the <xref:Microsoft.AspNetCore.Mvc.MvcOptions.AllowValidatingTopLevelNodes*> property in (`Startup.ConfigureServices`), as shown here:</span></span>

[!code-csharp[](validation/samples_snapshot/2.x/Startup.cs?name=snippet_AddMvc&highlight=4)]

## <a name="maximum-errors"></a><span data-ttu-id="f9edf-454">En fazla hata sayısı</span><span class="sxs-lookup"><span data-stu-id="f9edf-454">Maximum errors</span></span>

<span data-ttu-id="f9edf-455">En fazla hata sayısına ulaşıldığında doğrulama durduruluyor (varsayılan olarak 200).</span><span class="sxs-lookup"><span data-stu-id="f9edf-455">Validation stops when the maximum number of errors is reached (200 by default).</span></span> <span data-ttu-id="f9edf-456">Bu numarayı içinde aşağıdaki kodla yapılandırabilirsiniz `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="f9edf-456">You can configure this number with the following code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=3)]

## <a name="maximum-recursion"></a><span data-ttu-id="f9edf-457">En yüksek özyineleme</span><span class="sxs-lookup"><span data-stu-id="f9edf-457">Maximum recursion</span></span>

<span data-ttu-id="f9edf-458"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.Validation.ValidationVisitor>doğrulanan modelin nesne grafiğinin gezgeçer.</span><span class="sxs-lookup"><span data-stu-id="f9edf-458"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.Validation.ValidationVisitor> traverses the object graph of the model being validated.</span></span> <span data-ttu-id="f9edf-459">Çok derin olan veya sonsuz özyinelemeli özyinelemeli modeller için, doğrulama yığın taşmasına neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="f9edf-459">For models that are very deep or are infinitely recursive, validation may result in stack overflow.</span></span> <span data-ttu-id="f9edf-460">[Mvcoptions. MaxValidationDepth](xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxValidationDepth) , ziyaretçi özyineleme yapılandırılmış bir derinliği aşarsa doğrulamanın erken durdurulması için bir yol sağlar.</span><span class="sxs-lookup"><span data-stu-id="f9edf-460">[MvcOptions.MaxValidationDepth](xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxValidationDepth) provides a way to stop validation early if the visitor recursion exceeds a configured depth.</span></span> <span data-ttu-id="f9edf-461">Varsayılan değeri, `MvcOptions.MaxValidationDepth` veya ile çalışırken 32 ' dir `CompatibilityVersion.Version_2_2` .</span><span class="sxs-lookup"><span data-stu-id="f9edf-461">The default value of `MvcOptions.MaxValidationDepth` is 32 when running with `CompatibilityVersion.Version_2_2` or later.</span></span> <span data-ttu-id="f9edf-462">Önceki sürümler için değer null, bu da derinlemesine bir kısıtlama kısıtlaması anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="f9edf-462">For earlier versions, the value is null, which means no depth constraint.</span></span>

## <a name="automatic-short-circuit"></a><span data-ttu-id="f9edf-463">Otomatik kısa devre</span><span class="sxs-lookup"><span data-stu-id="f9edf-463">Automatic short-circuit</span></span>

<span data-ttu-id="f9edf-464">Model grafı doğrulama gerektirmiyorsa, doğrulama otomatik olarak kısa devre dışı (atlandı).</span><span class="sxs-lookup"><span data-stu-id="f9edf-464">Validation is automatically short-circuited (skipped) if the model graph doesn't require validation.</span></span> <span data-ttu-id="f9edf-465">Çalışma zamanının, hiçbir doğrulayıcıya sahip olmayan temel elemanlar koleksiyonları ( `byte[]` , `string[]` ,,,, ve gibi) için doğrulamayı atlayan nesneler `Dictionary<string, string>` .</span><span class="sxs-lookup"><span data-stu-id="f9edf-465">Objects that the runtime skips validation for include collections of primitives (such as `byte[]`, `string[]`, `Dictionary<string, string>`) and complex object graphs that don't have any validators.</span></span>

## <a name="disable-validation"></a><span data-ttu-id="f9edf-466">Doğrulamayı devre dışı bırak</span><span class="sxs-lookup"><span data-stu-id="f9edf-466">Disable validation</span></span>

<span data-ttu-id="f9edf-467">Doğrulamayı devre dışı bırakmak için:</span><span class="sxs-lookup"><span data-stu-id="f9edf-467">To disable validation:</span></span>

1. <span data-ttu-id="f9edf-468">`IObjectModelValidator`Hiçbir alanı geçersiz olarak işaretlememeyen bir uygulama oluşturun.</span><span class="sxs-lookup"><span data-stu-id="f9edf-468">Create an implementation of `IObjectModelValidator` that doesn't mark any fields as invalid.</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/NullObjectModelValidator.cs?name=snippet_DisableValidation)]

1. <span data-ttu-id="f9edf-469">`Startup.ConfigureServices`Bağımlılık ekleme kapsayıcısında varsayılan uygulamayı değiştirmek için aşağıdaki kodu ekleyin `IObjectModelValidator` .</span><span class="sxs-lookup"><span data-stu-id="f9edf-469">Add the following code to `Startup.ConfigureServices` to replace the default `IObjectModelValidator` implementation in the dependency injection container.</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Startup.cs?name=snippet_DisableValidation)]

<span data-ttu-id="f9edf-470">Model bağlamalarından kaynaklanan model durumu hatalarını görmeye devam edebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="f9edf-470">You might still see model state errors that originate from model binding.</span></span>

## <a name="client-side-validation"></a><span data-ttu-id="f9edf-471">İstemci tarafı doğrulaması</span><span class="sxs-lookup"><span data-stu-id="f9edf-471">Client-side validation</span></span>

<span data-ttu-id="f9edf-472">İstemci tarafı doğrulama, form geçerli olana kadar gönderimi önler.</span><span class="sxs-lookup"><span data-stu-id="f9edf-472">Client-side validation prevents submission until the form is valid.</span></span> <span data-ttu-id="f9edf-473">Gönder düğmesi, formu gönderen veya hata iletilerini görüntüleyen JavaScript 'ı çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="f9edf-473">The Submit button runs JavaScript that either submits the form or displays error messages.</span></span>

<span data-ttu-id="f9edf-474">Bir form üzerinde giriş hataları olduğunda, istemci tarafı doğrulaması sunucuya gereksiz gidiş dönüş önler.</span><span class="sxs-lookup"><span data-stu-id="f9edf-474">Client-side validation avoids an unnecessary round trip to the server when there are input errors on a form.</span></span> <span data-ttu-id="f9edf-475">*_Layout. cshtml* ve *_ValidationScriptsPartial. cshtml* ' deki aşağıdaki betik başvuruları istemci tarafı doğrulamayı destekler:</span><span class="sxs-lookup"><span data-stu-id="f9edf-475">The following script references in *_Layout.cshtml* and *_ValidationScriptsPartial.cshtml* support client-side validation:</span></span>

[!code-cshtml[](validation/samples/2.x/ValidationSample/Views/Shared/_Layout.cshtml?name=snippet_ScriptTag)]

[!code-cshtml[](validation/samples/2.x/ValidationSample/Views/Shared/_ValidationScriptsPartial.cshtml?name=snippet_ScriptTags)]

<span data-ttu-id="f9edf-476">[JQuery unobtrusive doğrulama](https://github.com/aspnet/jquery-validation-unobtrusive) betiği, popüler [jQuery Validate](https://jqueryvalidation.org/) eklentisi üzerinde derleme yapan özel bir Microsoft ön uç kitaplığıdır.</span><span class="sxs-lookup"><span data-stu-id="f9edf-476">The [jQuery Unobtrusive Validation](https://github.com/aspnet/jquery-validation-unobtrusive) script is a custom Microsoft front-end library that builds on the popular [jQuery Validate](https://jqueryvalidation.org/) plugin.</span></span> <span data-ttu-id="f9edf-477">JQuery unobtrusive doğrulaması olmadan, iki yerde aynı doğrulama mantığını kodlamakta olmanız gerekir: model özelliklerindeki Sunucu tarafı doğrulama özniteliklerinde bir kez ve sonra istemci tarafı betiklerimizde.</span><span class="sxs-lookup"><span data-stu-id="f9edf-477">Without jQuery Unobtrusive Validation, you would have to code the same validation logic in two places: once in the server-side validation attributes on model properties, and then again in client-side scripts.</span></span> <span data-ttu-id="f9edf-478">Bunun yerine, [Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro) ve [HTML Yardımcıları](xref:mvc/views/overview) , doğrulama `data-` gerektiren form öğeleri için HTML 5 özniteliklerini işlemek üzere model özelliklerinden doğrulama özniteliklerini ve tür meta verilerini kullanır.</span><span class="sxs-lookup"><span data-stu-id="f9edf-478">Instead, [Tag Helpers](xref:mvc/views/tag-helpers/intro) and [HTML helpers](xref:mvc/views/overview) use the validation attributes and type metadata from model properties to render HTML 5 `data-` attributes for the form elements that need validation.</span></span> <span data-ttu-id="f9edf-479">jQuery unobtrusive doğrulaması öznitelikleri ayrıştırır `data-` ve mantığı, sunucu tarafı doğrulama mantığını istemciye, etkili bir şekilde "kopyalamak" amacıyla jQuery doğrulamasına geçirir.</span><span class="sxs-lookup"><span data-stu-id="f9edf-479">jQuery Unobtrusive Validation parses the `data-` attributes and passes the logic to jQuery Validate, effectively "copying" the server-side validation logic to the client.</span></span> <span data-ttu-id="f9edf-480">Aşağıda gösterildiği gibi, etiket yardımcıları kullanarak istemcisinde doğrulama hatalarını görüntüleyebilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="f9edf-480">You can display validation errors on the client using tag helpers as shown here:</span></span>

[!code-cshtml[](validation/samples/2.x/ValidationSample/Views/Movies/Create.cshtml?name=snippet_ReleaseDate&highlight=4-5)]

<span data-ttu-id="f9edf-481">Önceki etiket yardımcıları aşağıdaki HTML 'yi işler.</span><span class="sxs-lookup"><span data-stu-id="f9edf-481">The preceding tag helpers render the following HTML.</span></span>

```html
<form action="/Movies/Create" method="post">
    <div class="form-horizontal">
        <h4>Movie</h4>
        <div class="text-danger"></div>
        <div class="form-group">
            <label class="col-md-2 control-label" for="ReleaseDate">ReleaseDate</label>
            <div class="col-md-10">
                <input class="form-control" type="datetime"
                data-val="true" data-val-required="The ReleaseDate field is required."
                id="ReleaseDate" name="ReleaseDate" value="">
                <span class="text-danger field-validation-valid"
                data-valmsg-for="ReleaseDate" data-valmsg-replace="true"></span>
            </div>
        </div>
    </div>
</form>
```

<span data-ttu-id="f9edf-482">`data-`HTML çıkışındaki özniteliklerin, özelliği için doğrulama özniteliklerine karşılık geldiğini unutmayın `ReleaseDate` .</span><span class="sxs-lookup"><span data-stu-id="f9edf-482">Notice that the `data-` attributes in the HTML output correspond to the validation attributes for the `ReleaseDate` property.</span></span> <span data-ttu-id="f9edf-483">`data-val-required`Öznitelik, Kullanıcı Yayın tarihi alanını doldurmazsa, görüntülenecek bir hata iletisi içerir.</span><span class="sxs-lookup"><span data-stu-id="f9edf-483">The `data-val-required` attribute contains an error message to display if the user doesn't fill in the release date field.</span></span> <span data-ttu-id="f9edf-484">jQuery unobtrusive doğrulaması bu değeri jQuery Validate [Required ()](https://jqueryvalidation.org/required-method/) yöntemine geçirir, daha sonra bu iletiyi eşlik eden **\<span>** öğede görüntüler.</span><span class="sxs-lookup"><span data-stu-id="f9edf-484">jQuery Unobtrusive Validation passes this value to the jQuery Validate [required()](https://jqueryvalidation.org/required-method/) method, which then displays that message in the accompanying **\<span>** element.</span></span>

<span data-ttu-id="f9edf-485">Veri türü doğrulama, bir öznitelik tarafından geçersiz kılınmadığı müddetçe, özelliğin .NET türünü temel alır `[DataType]` .</span><span class="sxs-lookup"><span data-stu-id="f9edf-485">Data type validation is based on the .NET type of a property, unless that is overridden by a `[DataType]` attribute.</span></span> <span data-ttu-id="f9edf-486">Tarayıcıların kendi varsayılan hata iletileri vardır ancak jQuery doğrulaması unobtrusive doğrulama paketi bu iletileri geçersiz kılabilir.</span><span class="sxs-lookup"><span data-stu-id="f9edf-486">Browsers have their own default error messages, but the jQuery Validation Unobtrusive Validation package can override those messages.</span></span> <span data-ttu-id="f9edf-487">`[DataType]`gibi öznitelikler ve alt sınıflar `[EmailAddress]` , hata iletisini belirtmenize izin verir.</span><span class="sxs-lookup"><span data-stu-id="f9edf-487">`[DataType]` attributes and subclasses such as `[EmailAddress]` let you specify the error message.</span></span>

### <a name="add-validation-to-dynamic-forms"></a><span data-ttu-id="f9edf-488">Dinamik formlara doğrulama ekleme</span><span class="sxs-lookup"><span data-stu-id="f9edf-488">Add Validation to Dynamic Forms</span></span>

<span data-ttu-id="f9edf-489">jQuery unobtrusive doğrulaması, sayfa ilk yüklendiğinde jQuery doğrulaması için doğrulama mantığını ve parametreleri geçirir.</span><span class="sxs-lookup"><span data-stu-id="f9edf-489">jQuery Unobtrusive Validation passes validation logic and parameters to jQuery Validate when the page first loads.</span></span> <span data-ttu-id="f9edf-490">Bu nedenle, doğrulama dinamik olarak üretilen formlarda otomatik olarak çalışmaz.</span><span class="sxs-lookup"><span data-stu-id="f9edf-490">Therefore, validation doesn't work automatically on dynamically generated forms.</span></span> <span data-ttu-id="f9edf-491">Doğrulamayı etkinleştirmek için, jQuery 'ten kaçınmaya yönelik doğrulamayı, dinamik formu oluşturduktan hemen sonra ayrıştırmaya söyleyin.</span><span class="sxs-lookup"><span data-stu-id="f9edf-491">To enable validation, tell jQuery Unobtrusive Validation to parse the dynamic form immediately after you create it.</span></span> <span data-ttu-id="f9edf-492">Örneğin, aşağıdaki kod, AJAX aracılığıyla eklenen bir formda istemci tarafı doğrulamayı ayarlar.</span><span class="sxs-lookup"><span data-stu-id="f9edf-492">For example, the following code sets up client-side validation on a form added via AJAX.</span></span>

```javascript
$.get({
    url: "https://url/that/returns/a/form",
    dataType: "html",
    error: function(jqXHR, textStatus, errorThrown) {
        alert(textStatus + ": Couldn't add form. " + errorThrown);
    },
    success: function(newFormHTML) {
        var container = document.getElementById("form-container");
        container.insertAdjacentHTML("beforeend", newFormHTML);
        var forms = container.getElementsByTagName("form");
        var newForm = forms[forms.length - 1];
        $.validator.unobtrusive.parse(newForm);
    }
})
```

<span data-ttu-id="f9edf-493">`$.validator.unobtrusive.parse()`Yöntemi, bir bağımsız değişkeni olarak bir jQuery seçiciyi kabul eder.</span><span class="sxs-lookup"><span data-stu-id="f9edf-493">The `$.validator.unobtrusive.parse()` method accepts a jQuery selector for its one argument.</span></span> <span data-ttu-id="f9edf-494">Bu yöntem, jQuery 'in bu `data-` seçicideki formların özniteliklerini ayrıştırmasına izin vermez.</span><span class="sxs-lookup"><span data-stu-id="f9edf-494">This method tells jQuery Unobtrusive Validation to parse the `data-` attributes of forms within that selector.</span></span> <span data-ttu-id="f9edf-495">Daha sonra bu özniteliklerin değerleri jQuery Validate eklentisine geçirilir.</span><span class="sxs-lookup"><span data-stu-id="f9edf-495">The values of those attributes are then passed to the jQuery Validate plugin.</span></span>

### <a name="add-validation-to-dynamic-controls"></a><span data-ttu-id="f9edf-496">Dinamik denetimlere doğrulama ekleme</span><span class="sxs-lookup"><span data-stu-id="f9edf-496">Add Validation to Dynamic Controls</span></span>

<span data-ttu-id="f9edf-497">`$.validator.unobtrusive.parse()`Yöntemi, ve gibi dinamik olarak üretilen denetimlerde değil, formun tamamında işe yarar `<input>` `<select/>` .</span><span class="sxs-lookup"><span data-stu-id="f9edf-497">The `$.validator.unobtrusive.parse()` method works on an entire form, not on individual dynamically generated controls, such as `<input>` and `<select/>`.</span></span> <span data-ttu-id="f9edf-498">Formu yeniden oluşturmak için, aşağıdaki örnekte gösterildiği gibi, form daha önce ayrıştırıldığında eklenen doğrulama verilerini kaldırın:</span><span class="sxs-lookup"><span data-stu-id="f9edf-498">To reparse the form, remove the validation data that was added when the form was parsed earlier, as shown in the following example:</span></span>

```javascript
$.get({
    url: "https://url/that/returns/a/control",
    dataType: "html",
    error: function(jqXHR, textStatus, errorThrown) {
        alert(textStatus + ": Couldn't add control. " + errorThrown);
    },
    success: function(newInputHTML) {
        var form = document.getElementById("my-form");
        form.insertAdjacentHTML("beforeend", newInputHTML);
        $(form).removeData("validator")    // Added by jQuery Validate
               .removeData("unobtrusiveValidation");   // Added by jQuery Unobtrusive Validation
        $.validator.unobtrusive.parse(form);
    }
})
```

## <a name="custom-client-side-validation"></a><span data-ttu-id="f9edf-499">Özel istemci tarafı doğrulaması</span><span class="sxs-lookup"><span data-stu-id="f9edf-499">Custom client-side validation</span></span>

<span data-ttu-id="f9edf-500">Özel istemci tarafı doğrulama, `data-` özel bir jQuery Validate bağdaştırıcısıyla çalışan HTML öznitelikleri oluşturarak yapılır.</span><span class="sxs-lookup"><span data-stu-id="f9edf-500">Custom client-side validation is done by generating `data-` HTML attributes that work with a custom jQuery Validate adapter.</span></span> <span data-ttu-id="f9edf-501">Aşağıdaki örnek bağdaştırıcı kodu, `ClassicMovie` `ClassicMovie2` Bu makalede daha önce sunulan ve öznitelikleri için yazılmıştır:</span><span class="sxs-lookup"><span data-stu-id="f9edf-501">The following sample adapter code was written for the `ClassicMovie` and `ClassicMovie2` attributes that were introduced earlier in this article:</span></span>

[!code-javascript[](validation/samples/2.x/ValidationSample/wwwroot/js/classicMovieValidator.js?name=snippet_UnobtrusiveValidation)]

<span data-ttu-id="f9edf-502">Bağdaştırıcıların nasıl yazılacağı hakkında daha fazla bilgi için [jQuery Validate belgelerine](https://jqueryvalidation.org/documentation/)bakın.</span><span class="sxs-lookup"><span data-stu-id="f9edf-502">For information about how to write adapters, see the [jQuery Validate documentation](https://jqueryvalidation.org/documentation/).</span></span>

<span data-ttu-id="f9edf-503">Belirli bir alan için bir bağdaştırıcının kullanımı, şu öznitelikler tarafından tetiklenir `data-` :</span><span class="sxs-lookup"><span data-stu-id="f9edf-503">The use of an adapter for a given field is triggered by `data-` attributes that:</span></span>

* <span data-ttu-id="f9edf-504">Alana, doğrulamaya () tabi olacak şekilde bayrak ekleyin `data-val="true"` .</span><span class="sxs-lookup"><span data-stu-id="f9edf-504">Flag the field as being subject to validation (`data-val="true"`).</span></span>
* <span data-ttu-id="f9edf-505">Bir doğrulama kuralı adı ve hata iletisi metni (örneğin,) belirler `data-val-rulename="Error message."` .</span><span class="sxs-lookup"><span data-stu-id="f9edf-505">Identify a validation rule name and error message text (for example, `data-val-rulename="Error message."`).</span></span>
* <span data-ttu-id="f9edf-506">Doğrulayıcı ihtiyaçlarına ek parametreler sağlayın (örneğin, `data-val-rulename-parm1="value"` ).</span><span class="sxs-lookup"><span data-stu-id="f9edf-506">Provide any additional parameters the validator needs (for example, `data-val-rulename-parm1="value"`).</span></span>

<span data-ttu-id="f9edf-507">Aşağıdaki örnek, `data-` örnek uygulamanın özniteliği için öznitelikleri gösterir `ClassicMovie` :</span><span class="sxs-lookup"><span data-stu-id="f9edf-507">The following example shows the `data-` attributes for the sample app's `ClassicMovie` attribute:</span></span>

```html
<input class="form-control" type="datetime"
    data-val="true"
    data-val-classicmovie1="Classic movies must have a release year earlier than 1960."
    data-val-classicmovie1-year="1960"
    data-val-required="The ReleaseDate field is required."
    id="ReleaseDate" name="ReleaseDate" value="">
```

<span data-ttu-id="f9edf-508">Daha önce belirtildiği gibi, [Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro) ve [HTML Yardımcıları](xref:mvc/views/overview) , öznitelikleri işlemek için doğrulama özniteliklerinden bilgileri kullanır `data-` .</span><span class="sxs-lookup"><span data-stu-id="f9edf-508">As noted earlier, [Tag Helpers](xref:mvc/views/tag-helpers/intro) and [HTML helpers](xref:mvc/views/overview) use information from validation attributes to render `data-` attributes.</span></span> <span data-ttu-id="f9edf-509">Özel HTML özniteliklerinin oluşturulmasına neden olan kod yazmak için iki seçenek vardır `data-` :</span><span class="sxs-lookup"><span data-stu-id="f9edf-509">There are two options for writing code that results in the creation of custom `data-` HTML attributes:</span></span>

* <span data-ttu-id="f9edf-510">Öğesinden türeten bir sınıf oluşturun ve `AttributeAdapterBase<TAttribute>` `IValidationAttributeAdapterProvider` özniteliği ve kendı bağdaştırıcısını dı olarak kaydedin.</span><span class="sxs-lookup"><span data-stu-id="f9edf-510">Create a class that derives from `AttributeAdapterBase<TAttribute>` and a class that implements `IValidationAttributeAdapterProvider`, and register your attribute and its adapter in DI.</span></span> <span data-ttu-id="f9edf-511">Bu yöntem, sunucu ile ilgili ve istemciyle ilgili doğrulama kodundaki [tek sorumluluk sorumlusunu](https://wikipedia.org/wiki/Single_responsibility_principle) , ayrı sınıflarda izler.</span><span class="sxs-lookup"><span data-stu-id="f9edf-511">This method follows the [single responsibility principal](https://wikipedia.org/wiki/Single_responsibility_principle) in that server-related and client-related validation code is in separate classes.</span></span> <span data-ttu-id="f9edf-512">Ayrıca bağdaştırıcı, DI ' de kaydolduğundan bu yana de bunun avantajına sahiptir.</span><span class="sxs-lookup"><span data-stu-id="f9edf-512">The adapter also has the advantage that since it is registered in DI, other services in DI are available to it if needed.</span></span>
* <span data-ttu-id="f9edf-513">`IClientModelValidator` `ValidationAttribute` Sınıfınıza uygulayın.</span><span class="sxs-lookup"><span data-stu-id="f9edf-513">Implement `IClientModelValidator` in your `ValidationAttribute` class.</span></span> <span data-ttu-id="f9edf-514">Bu yöntem, öznitelik herhangi bir sunucu tarafı doğrulaması yapamazsa ve hiçbir hizmete gerek duymazsa uygun olabilir.</span><span class="sxs-lookup"><span data-stu-id="f9edf-514">This method might be appropriate if the attribute doesn't do any server-side validation and doesn't need any services from DI.</span></span>

### <a name="attributeadapter-for-client-side-validation"></a><span data-ttu-id="f9edf-515">İstemci tarafı doğrulaması için AttributeAdapter</span><span class="sxs-lookup"><span data-stu-id="f9edf-515">AttributeAdapter for client-side validation</span></span>

<span data-ttu-id="f9edf-516">`data-`HTML 'de öznitelikleri işleme yöntemi `ClassicMovie` örnek uygulamadaki özniteliği tarafından kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f9edf-516">This method of rendering `data-` attributes in HTML is used by the `ClassicMovie` attribute in the sample app.</span></span> <span data-ttu-id="f9edf-517">Bu yöntemi kullanarak istemci doğrulaması eklemek için:</span><span class="sxs-lookup"><span data-stu-id="f9edf-517">To add client validation by using this method:</span></span>

1. <span data-ttu-id="f9edf-518">Özel doğrulama özniteliği için bir öznitelik bağdaştırıcı sınıfı oluşturun.</span><span class="sxs-lookup"><span data-stu-id="f9edf-518">Create an attribute adapter class for the custom validation attribute.</span></span> <span data-ttu-id="f9edf-519">Özniteliği [Attributeadapterbase \<T> ](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.attributeadapterbase-1?view=aspnetcore-2.2)'den türet.</span><span class="sxs-lookup"><span data-stu-id="f9edf-519">Derive the class from [AttributeAdapterBase\<T>](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.attributeadapterbase-1?view=aspnetcore-2.2).</span></span> <span data-ttu-id="f9edf-520">`AddValidation` `data-` Aşağıdaki örnekte gösterildiği gibi, işlenen çıktıya öznitelikler ekleyen bir yöntem oluşturun:</span><span class="sxs-lookup"><span data-stu-id="f9edf-520">Create an `AddValidation` method that adds `data-` attributes to the rendered output, as shown in this example:</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/ClassicMovieAttributeAdapter.cs?name=snippet_ClassicMovieAttributeAdapter)]

1. <span data-ttu-id="f9edf-521">Uygulayan bir bağdaştırıcı sağlayıcısı sınıfı oluşturun <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.IValidationAttributeAdapterProvider> .</span><span class="sxs-lookup"><span data-stu-id="f9edf-521">Create an adapter provider class that implements <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.IValidationAttributeAdapterProvider>.</span></span> <span data-ttu-id="f9edf-522">Yöntemi içinde `GetAttributeAdapter` , aşağıdaki örnekte gösterildiği gibi, özel özniteliğini bağdaştırıcının oluşturucusuna geçirin:</span><span class="sxs-lookup"><span data-stu-id="f9edf-522">In the `GetAttributeAdapter` method pass in the custom attribute to the adapter's constructor, as shown in this example:</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/CustomValidationAttributeAdapterProvider.cs?name=snippet_CustomValidationAttributeAdapterProvider)]

1. <span data-ttu-id="f9edf-523">Dı için bağdaştırıcı sağlayıcısını Kaydet `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="f9edf-523">Register the adapter provider for DI in `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=8-10)]

### <a name="iclientmodelvalidator-for-client-side-validation"></a><span data-ttu-id="f9edf-524">İstemci tarafı doğrulaması için ılientmodelvalidator</span><span class="sxs-lookup"><span data-stu-id="f9edf-524">IClientModelValidator for client-side validation</span></span>

<span data-ttu-id="f9edf-525">`data-`HTML 'de öznitelikleri işleme yöntemi `ClassicMovie2` örnek uygulamadaki özniteliği tarafından kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f9edf-525">This method of rendering `data-` attributes in HTML is used by the `ClassicMovie2` attribute in the sample app.</span></span> <span data-ttu-id="f9edf-526">Bu yöntemi kullanarak istemci doğrulaması eklemek için:</span><span class="sxs-lookup"><span data-stu-id="f9edf-526">To add client validation by using this method:</span></span>

* <span data-ttu-id="f9edf-527">Özel doğrulama özniteliğinde, `IClientModelValidator` arabirimini uygulayın ve bir `AddValidation` Yöntem oluşturun.</span><span class="sxs-lookup"><span data-stu-id="f9edf-527">In the custom validation attribute, implement the `IClientModelValidator` interface and create an `AddValidation` method.</span></span> <span data-ttu-id="f9edf-528">Yönteminde, `AddValidation` `data-` Aşağıdaki örnekte gösterildiği gibi, doğrulama için öznitelikler ekleyin:</span><span class="sxs-lookup"><span data-stu-id="f9edf-528">In the `AddValidation` method, add `data-` attributes for validation, as shown in the following example:</span></span>

  [!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/ClassicMovie2Attribute.cs?name=snippet_ClassicMovie2Attribute)]

## <a name="disable-client-side-validation"></a><span data-ttu-id="f9edf-529">İstemci tarafı doğrulamayı devre dışı bırak</span><span class="sxs-lookup"><span data-stu-id="f9edf-529">Disable client-side validation</span></span>

<span data-ttu-id="f9edf-530">Aşağıdaki kod, MVC görünümlerinde istemci doğrulamasını devre dışı bırakır:</span><span class="sxs-lookup"><span data-stu-id="f9edf-530">The following code disables client validation in MVC views:</span></span>

[!code-csharp[](validation/samples_snapshot/2.x/Startup2.cs?name=snippet_DisableClientValidation)]

<span data-ttu-id="f9edf-531">Ve Razor sayfalarında:</span><span class="sxs-lookup"><span data-stu-id="f9edf-531">And in Razor Pages:</span></span>

[!code-csharp[](validation/samples_snapshot/2.x/Startup3.cs?name=snippet_DisableClientValidation)]

<span data-ttu-id="f9edf-532">İstemci doğrulamasını devre dışı bırakmaya yönelik başka bir seçenek `_ValidationScriptsPartial` de, *. cshtml* dosyanızdaki başvurusunu açıklama olarak yorumlamadır.</span><span class="sxs-lookup"><span data-stu-id="f9edf-532">Another option for disabling client validation is to comment out the reference to `_ValidationScriptsPartial` in your *.cshtml* file.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f9edf-533">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="f9edf-533">Additional resources</span></span>

* [<span data-ttu-id="f9edf-534">System. ComponentModel. Dataaçıklamalarda ad alanı</span><span class="sxs-lookup"><span data-stu-id="f9edf-534">System.ComponentModel.DataAnnotations namespace</span></span>](xref:System.ComponentModel.DataAnnotations)
* [<span data-ttu-id="f9edf-535">Model bağlama</span><span class="sxs-lookup"><span data-stu-id="f9edf-535">Model Binding</span></span>](model-binding.md)

::: moniker-end
