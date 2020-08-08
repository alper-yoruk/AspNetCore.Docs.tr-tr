---
title: ASP.NET Core MVC 'de model doğrulaması
author: rick-anderson
description: ASP.NET Core MVC ve sayfalarında model doğrulama hakkında bilgi edinin Razor .
ms.author: riande
ms.custom: mvc
ms.date: 12/15/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/models/validation
ms.openlocfilehash: c0edf56c966cb90c1c308f300a8944d392fdc0e7
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88020983"
---
# <a name="model-validation-in-aspnet-core-mvc-and-no-locrazor-pages"></a>ASP.NET Core MVC ve sayfalarda model doğrulaması Razor

::: moniker range=">= aspnetcore-3.0"

[Kirk Larkaya](https://github.com/serpent5) göre

Bu makalede, ASP.NET Core MVC veya Pages uygulamasında Kullanıcı girişinin nasıl doğrulanacağı açıklanır Razor .

[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/samples) ([nasıl indirilir](xref:index#how-to-download-a-sample)).

## <a name="model-state"></a>Model durumu

Model durumu iki alt sistemden gelen hataları temsil eder: model bağlama ve model doğrulama. [Model bağlamasından](model-binding.md) kaynaklanan hatalar genellikle veri dönüştürme hatalardır. Örneğin, bir tamsayı alanına bir "x" girilir. Model bağlama sonrasında model doğrulaması oluşur ve verilerin iş kurallarına uygun olmadığı rapor hataları raporlar. Örneğin, 1 ile 5 arasında bir derecelendirme bekleyen bir alana 0 girilir.

Hem model bağlama hem de model doğrulama, bir denetleyici eyleminin veya bir sayfa işleyici yönteminin yürütülmesinden önce oluşur Razor . Web uygulamaları için uygulama, `ModelState.IsValid` uygun şekilde inceleme ve tepki verme sorumluluğundadır. Web Apps genellikle sayfayı bir hata iletisiyle yeniden görüntülerdi:

[!code-csharp[](validation/samples/3.x/ValidationSample/Pages/Movies/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=3-6)]

Web API denetleyicilerinin `ModelState.IsValid` özniteliğe sahip olup olmadığını kontrol etmek zorunda değildir `[ApiController]` . Bu durumda, model durumu geçersiz olduğunda hata ayrıntılarını içeren bir otomatik HTTP 400 yanıtı döndürülür. Daha fazla bilgi için bkz. [OTOMATIK HTTP 400 yanıtları](xref:web-api/index#automatic-http-400-responses).

## <a name="rerun-validation"></a>Doğrulamayı yeniden çalıştır

Doğrulama otomatiktir, ancak el ile yinelemek isteyebilirsiniz. Örneğin, bir özellik için bir değer hesaplamanıza ve özelliği hesaplanan değere ayarladıktan sonra doğrulamayı yeniden çalıştırmak isteyebilirsiniz. Doğrulamayı yeniden çalıştırmak için, `TryValidateModel` yöntemi aşağıda gösterildiği gibi çağırın:

[!code-csharp[](validation/samples/3.x/ValidationSample/Pages/Movies/Create.cshtml.cs?name=snippet_TryValidate&highlight=3-6)]

## <a name="validation-attributes"></a>Doğrulama öznitelikleri

Doğrulama öznitelikleri, model özellikleri için doğrulama kuralları belirtmenize olanak tanır. Örnek uygulamadaki aşağıdaki örnek, doğrulama öznitelikleriyle açıklama eklenmiş bir model sınıfı gösterir. `[ClassicMovie]`Özniteliği özel bir doğrulama özniteliğidir ve diğerleri yerleşik olarak bulunur. Gösterilmez `[ClassicMovieWithClientValidator]` . `[ClassicMovieWithClientValidator]`özel bir öznitelik uygulamak için alternatif bir yol gösterir.

[!code-csharp[](validation/samples/3.x/ValidationSample/Models/Movie.cs?name=snippet_Class)]

## <a name="built-in-attributes"></a>Yerleşik öznitelikler

Yerleşik doğrulama özniteliklerinden bazıları şunlardır:

* `[CreditCard]`: Özelliğin kredi kartı biçimine sahip olduğunu doğrular. [JQuery doğrulaması ek yöntemleri](https://cdnjs.cloudflare.com/ajax/libs/jquery-validate/1.19.1/additional-methods.min.js)gerektirir.
* `[Compare]`: Bir modeldeki iki özelliği eşleştiğini doğrular.
* `[EmailAddress]`: Özelliğin bir e-posta biçimine sahip olduğunu doğrular.
* `[Phone]`: Özelliğin bir telefon numarası biçimine sahip olduğunu doğrular.
* `[Range]`: Özellik değerinin belirtilen bir aralık dahilinde olduğunu doğrular.
* `[RegularExpression]`: Özellik değerinin belirtilen bir normal ifadeyle eşleştiğini doğrular.
* `[Required]`: Alanın null olduğunu doğrular. Bu özniteliğin davranışı hakkındaki ayrıntılar için bkz. [ `[Required]` özniteliği](#required-attribute) .
* `[StringLength]`: Dize özellik değerinin belirtilen uzunluk sınırını aşmadığını doğrular.
* `[Url]`: Özelliğin bir URL biçimine sahip olduğunu doğrular.
* `[Remote]`: Sunucuda bir eylem yöntemi çağırarak istemcide girişi doğrular. Bu özniteliğin davranışı hakkındaki ayrıntılar için bkz. [ `[Remote]` özniteliği](#remote-attribute) .

Doğrulama özniteliklerinin tüm listesi [System. ComponentModel. Dataaçıklamalarda](xref:System.ComponentModel.DataAnnotations) ad alanında bulunabilir.

### <a name="error-messages"></a>Hata iletileri

Doğrulama öznitelikleri, geçersiz giriş için görüntülenecek hata iletisini belirtmenize izin verir. Örnek:

```csharp
[StringLength(8, ErrorMessage = "Name length can't be more than 8.")]
```

Dahili olarak, öznitelikler, `String.Format` alan adı için bir yer tutucu ve bazen ek yer tutucular ile çağrı yapılır. Örnek:

```csharp
[StringLength(8, ErrorMessage = "{0} length must be between {2} and {1}.", MinimumLength = 6)]
```

Bir `Name` özelliğe uygulandığında, yukarıdaki kod tarafından oluşturulan hata iletisi "ad uzunluğu 6 ile 8 arasında olmalıdır." olacaktır.

`String.Format`Belirli bir özniteliğin hata iletisinde hangi parametrelerin geçtiğini öğrenmek için, bkz. [dataaçıklamalarda kaynak kodu](https://github.com/dotnet/runtime/tree/master/src/libraries/System.ComponentModel.Annotations/src/System/ComponentModel/DataAnnotations).

## <a name="required-attribute"></a>[Zorunlu] özniteliği

.NET Core 3,0 ve sonraki sürümlerde doğrulama sistemi, null olamayan parametrelere veya bir özniteliğe sahip oldukları gibi bağlantılı özelliklere sahiptir `[Required]` . Ve gibi [değer türleri](/dotnet/csharp/language-reference/keywords/value-types) `decimal` `int` null atanamaz. Bu davranış, içinde yapılandırılarak devre dışı <xref:Microsoft.AspNetCore.Mvc.MvcOptions.SuppressImplicitRequiredAttributeForNonNullableReferenceTypes> bırakılabilir `Startup.ConfigureServices` :

```csharp
services.AddControllers(options => options.SuppressImplicitRequiredAttributeForNonNullableReferenceTypes = true);
```

### <a name="required-validation-on-the-server"></a>[Zorunlu] sunucuda doğrulama

Sunucuda, özelliği null ise gerekli bir değer eksik olarak kabul edilir. Null yapılamayan bir alan her zaman geçerlidir ve `[Required]` özniteliğin hata mesajı hiçbir zaman gösterilmez.

Ancak, null olamayan bir özellik için model bağlama başarısız olabilir ve gibi bir hata mesajı elde edilir `The value '' is invalid` . Null yapılamayan türlerin sunucu tarafı doğrulaması için özel bir hata iletisi belirtmek üzere aşağıdaki seçenekleriniz vardır:

* Alanı null yapılabilir yapın (örneğin, `decimal?` yerine `decimal` ). [Null \<T> yapılabilir](/dotnet/csharp/programming-guide/nullable-types/) değer türleri standart null yapılabilir türler gibi değerlendirilir.
* Aşağıdaki örnekte gösterildiği gibi model bağlama tarafından kullanılacak varsayılan hata iletisini belirtin:

  [!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_Configuration&highlight=5-6)]

  İçin varsayılan iletileri ayarlayabileceğiniz model bağlama hataları hakkında daha fazla bilgi için bkz <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DefaultModelBindingMessageProvider#methods> ..

### <a name="required-validation-on-the-client"></a>[Zorunlu] istemcide doğrulama

Null yapılamayan türler ve dizeler, sunucu ile karşılaştırıldığında, istemci üzerinde farklı şekilde işlenir. İstemcide:

* Yalnızca girdi girildiğinde bir değer vardır. Bu nedenle, istemci tarafı doğrulaması null yapılamayan türler, null yapılabilir türler ile aynı şekilde işler.
* Bir dize alanındaki boşluk, jQuery doğrulaması [gerekli](https://jqueryvalidation.org/required-method/) yöntemi tarafından geçerli bir girdi olarak kabul edilir. Yalnızca boşluk girildiğinde, sunucu tarafı doğrulaması gerekli bir dize alanını geçersiz kabul eder.

Daha önce belirtildiği gibi, null olamayan türler bir özniteliğe sahip olsa da kabul edilir `[Required]` . Bu, özniteliğini uygulamasanız bile istemci tarafı doğrulamayı alacağınız anlamına gelir `[Required]` . Ancak özniteliğini kullanmazsanız varsayılan bir hata iletisi alırsınız. Özel bir hata iletisi belirtmek için özniteliğini kullanın.

## <a name="remote-attribute"></a>[Uzak] özniteliği

`[Remote]`Özniteliği, alan girişinin geçerli olup olmadığını anlamak için sunucu üzerinde bir yöntem çağrılmasını gerektiren istemci tarafı doğrulaması uygular. Örneğin, uygulamanın bir kullanıcı adının zaten kullanımda olup olmadığını doğrulaması gerekebilir.

Uzaktan doğrulamayı uygulamak için:

1. JavaScript 'e çağırmak için bir eylem yöntemi oluşturun.  JQuery doğrulaması [uzak](https://jqueryvalidation.org/remote-method/) YÖNTEMI bir JSON yanıtı bekliyor:

   * `true`giriş verilerinin geçerli olduğu anlamına gelir.
   * `false`, `undefined` ya da `null` girişin geçersiz olduğu anlamına gelir. Varsayılan hata iletisini görüntüler.
   * Diğer herhangi bir dize, girişin geçersiz olduğu anlamına gelir. Dizeyi özel bir hata iletisi olarak görüntüleyin.

   Özel bir hata iletisi döndüren eylem yöntemine bir örnek aşağıda verilmiştir:

   [!code-csharp[](validation/samples/3.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyEmail)]

1. Model sınıfında, `[Remote]` Aşağıdaki örnekte gösterildiği gibi, doğrulama eylemi yöntemine işaret eden bir özniteliğe sahip özelliğe açıklama ekleyin:

   [!code-csharp[](validation/samples/3.x/ValidationSample/Models/User.cs?name=snippet_Email)]
 
   `[Remote]`Özniteliği `Microsoft.AspNetCore.Mvc` ad alanıdır.
   
### <a name="additional-fields"></a>Ek alanlar

`AdditionalFields`Özniteliğinin özelliği, `[Remote]` sunucudaki verilere karşı alan birleşimlerini doğrulamanızı sağlar. Örneğin, `User` model `FirstName` ve `LastName` özellikleri varsa, var olan hiçbir kullanıcının bu ad çiftine sahip olmadığını doğrulamak isteyebilirsiniz. Aşağıdaki örnek nasıl kullanılacağını gösterir `AdditionalFields` :

[!code-csharp[](validation/samples/3.x/ValidationSample/Models/User.cs?name=snippet_Name&highlight=1,5)]

`AdditionalFields`açıkça "FirstName" ve "LastName" dizelerine ayarlanabilir, ancak [NameOf](/dotnet/csharp/language-reference/keywords/nameof) işlecinin kullanılması daha sonra yeniden düzenlemeyi basitleştirir. Bu doğrulamanın eylem yöntemi hem hem de `firstName` `lastName` bağımsız değişkenlerini kabul etmelidir:

[!code-csharp[](validation/samples/3.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyName)]

Kullanıcı adı veya soyadı girdiğinde JavaScript, bu ad çiftinin alındığını görmek için uzak bir çağrı yapar.

İki veya daha fazla ek alanı doğrulamak için bunları virgülle ayrılmış bir liste olarak belirtin. Örneğin, modele bir özellik eklemek için `MiddleName` `[Remote]` Aşağıdaki örnekte gösterildiği gibi özniteliği ayarlayın:

```csharp
[Remote(action: "VerifyName", controller: "Users", AdditionalFields = nameof(FirstName) + "," + nameof(LastName))]
public string MiddleName { get; set; }
```

`AdditionalFields`Tüm öznitelik bağımsız değişkenleri gibi, sabit bir ifade olmalıdır. Bu nedenle, bir ara [değerli dize](/dotnet/csharp/language-reference/keywords/interpolated-strings) veya <xref:System.String.Join*> başlatmak için çağrı kullanmayın `AdditionalFields` .

## <a name="alternatives-to-built-in-attributes"></a>Yerleşik özniteliklerin alternatifleri

Yerleşik öznitelikler tarafından sağlanmayan doğrulamaya ihtiyacınız varsa şunları yapabilirsiniz:

* [Özel öznitelikler oluşturun](#custom-attributes).
* [IValidatableObject uygulayın](#ivalidatableobject).

## <a name="custom-attributes"></a>Özel öznitelikler

Yerleşik doğrulama özniteliklerinin işlemeyen senaryolar için özel doğrulama öznitelikleri oluşturabilirsiniz. Öğesinden devralan bir sınıf oluşturun <xref:System.ComponentModel.DataAnnotations.ValidationAttribute> ve yöntemi geçersiz kılın <xref:System.ComponentModel.DataAnnotations.ValidationAttribute.IsValid*> .

`IsValid`Yöntemi, doğrulanacak girdi olan *Value*adlı bir nesne kabul eder. Aşırı yükleme, `ValidationContext` model bağlama tarafından oluşturulan model örneği gibi ek bilgiler sağlayan bir nesneyi de kabul eder.

Aşağıdaki örnek, *Klasik* tarz bir filmin yayın tarihinin belirtilen yıldan daha sonra olmadığını doğrular. `[ClassicMovie]`Öznitelik:

* Yalnızca sunucuda çalışır.
* Klasik Filmler için, yayımlanma tarihini doğrular:

[!code-csharp[](validation/samples/3.x/ValidationSample/Validation/ClassicMovieAttribute.cs?name=snippet_Class)]

`movie`Yukarıdaki örnekteki değişken, `Movie` form gönderiminde verileri içeren bir nesneyi temsil eder. Doğrulama başarısız olduğunda, bir `ValidationResult` hata iletisi döndürür.

## <a name="ivalidatableobject"></a>IValidatableObject

Yukarıdaki örnek yalnızca türlerle birlikte kullanılabilir `Movie` . Aşağıdaki örnekte gösterildiği gibi, sınıf düzeyi doğrulama için başka bir seçenek de `IValidatableObject` model sınıfında uygulanır:

[!code-csharp[](validation/samples/3.x/ValidationSample/Models/ValidatableMovie.cs?name=snippet_Class&highlight=1,26-34)]

## <a name="top-level-node-validation"></a>Üst düzey düğüm doğrulaması

Üst düzey düğümler şunları içerir:

* Eylem parametreleri
* Denetleyici özellikleri
* Sayfa işleyici parametreleri
* Sayfa modeli özellikleri

Model bağlantılı üst düzey düğümler, model özelliklerini doğrulamaya ek olarak onaylanır. Örnek uygulamadan aşağıdaki örnekte, `VerifyPhone` yöntemi <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute> eylem parametresini doğrulamak için öğesini kullanır `phone` :

[!code-csharp[](validation/samples/3.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyPhone)]

En üst düzey düğümler, <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute> doğrulama öznitelikleriyle birlikte kullanılabilir. Örnek uygulamadaki aşağıdaki örnekte, `CheckAge` yöntemi, `age` form gönderildiğinde parametrenin sorgu dizesinden bağlanması gerektiğini belirtir:

[!code-csharp[](validation/samples/3.x/ValidationSample/Controllers/UsersController.cs?name=snippet_CheckAgeSignature)]

Denetim yaşı sayfasında (*Checkage. cshtml*) iki form vardır. İlk form `Age` `99` bir değeri sorgu dizesi parametresi olarak gönderir: `https://localhost:5001/Users/CheckAge?Age=99` .

Sorgu dizesinden düzgün şekilde biçimlendirilen bir `age` parametre gönderildiğinde, form doğrular.

Denetim yaşı sayfasındaki ikinci form, `Age` isteğin gövdesindeki değeri gönderir ve doğrulama başarısız olur. `age`Parametre bir sorgu dizesinden gelmesi gerektiğinden bağlama başarısız olur.

## <a name="maximum-errors"></a>En fazla hata sayısı

En fazla hata sayısına ulaşıldığında doğrulama durduruluyor (varsayılan olarak 200). Bu numarayı içinde aşağıdaki kodla yapılandırabilirsiniz `Startup.ConfigureServices` :

[!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_Configuration&highlight=4)]

## <a name="maximum-recursion"></a>En yüksek özyineleme

<xref:Microsoft.AspNetCore.Mvc.ModelBinding.Validation.ValidationVisitor>doğrulanan modelin nesne grafiğinin gezgeçer. Derin olan veya sonsuz özyinelemeli olan modeller için doğrulama, yığın taşmasına neden olabilir. [Mvcoptions. MaxValidationDepth](xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxValidationDepth) , ziyaretçi özyineleme yapılandırılmış bir derinliği aşarsa doğrulamanın erken durdurulması için bir yol sağlar. Varsayılan değeri 32 ' `MvcOptions.MaxValidationDepth` dir.

## <a name="automatic-short-circuit"></a>Otomatik kısa devre

Model grafı doğrulama gerektirmiyorsa, doğrulama otomatik olarak kısa devre dışı (atlandı). Çalışma zamanının, hiçbir doğrulayıcıya sahip olmayan temel elemanlar koleksiyonları ( `byte[]` , `string[]` ,,,, ve gibi) için doğrulamayı atlayan nesneler `Dictionary<string, string>` .

## <a name="disable-validation"></a>Doğrulamayı devre dışı bırak

Doğrulamayı devre dışı bırakmak için:

1. `IObjectModelValidator`Hiçbir alanı geçersiz olarak işaretlememeyen bir uygulama oluşturun.

   [!code-csharp[](validation/samples/3.x/ValidationSample/Validation/NullObjectModelValidator.cs?name=snippet_Class)]

1. `Startup.ConfigureServices`Bağımlılık ekleme kapsayıcısında varsayılan uygulamayı değiştirmek için aşağıdaki kodu ekleyin `IObjectModelValidator` .

   [!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_DisableValidation)]

Model bağlamalarından kaynaklanan model durumu hatalarını görmeye devam edebilirsiniz.

## <a name="client-side-validation"></a>İstemci tarafı doğrulaması

İstemci tarafı doğrulama, form geçerli olana kadar gönderimi önler. Gönder düğmesi, formu gönderen veya hata iletilerini görüntüleyen JavaScript 'ı çalıştırır.

Bir form üzerinde giriş hataları olduğunda, istemci tarafı doğrulaması sunucuya gereksiz gidiş dönüş önler. *_Layout. cshtml* ve *_ValidationScriptsPartial. cshtml* ' deki aşağıdaki betik başvuruları istemci tarafı doğrulamayı destekler:

[!code-cshtml[](validation/samples/3.x/ValidationSample/Views/Shared/_Layout.cshtml?name=snippet_Scripts)]

[!code-cshtml[](validation/samples/3.x/ValidationSample/Views/Shared/_ValidationScriptsPartial.cshtml?name=snippet_Scripts)]

[JQuery unobtrusive doğrulama](https://github.com/aspnet/jquery-validation-unobtrusive) betiği, popüler [jQuery doğrulama](https://jqueryvalidation.org/) eklentisi üzerinde derleme yapan özel bir Microsoft ön uç kitaplığıdır. JQuery unobtrusive doğrulaması olmadan, iki yerde aynı doğrulama mantığını kodlamakta olmanız gerekir: model özelliklerindeki Sunucu tarafı doğrulama özniteliklerinde bir kez ve sonra istemci tarafı betiklerimizde. Bunun yerine, [Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro) ve [HTML Yardımcıları](xref:mvc/views/overview) , doğrulama `data-` gerektiren form öğeleri için HTML 5 özniteliklerini işlemek üzere model özelliklerinden doğrulama özniteliklerini ve tür meta verilerini kullanır. jQuery unobtrusive doğrulaması öznitelikleri ayrıştırır `data-` ve mantığı jQuery doğrulamasına geçirir ve sunucu tarafı doğrulama mantığını istemciye etkin bir şekilde "kopyalıyor". Aşağıda gösterildiği gibi, etiket yardımcıları kullanarak istemcisinde doğrulama hatalarını görüntüleyebilirsiniz:

[!code-cshtml[](validation/samples/3.x/ValidationSample/Pages/Movies/Create.cshtml?name=snippet_ReleaseDate&highlight=3-4)]

Önceki etiket yardımcıları aşağıdaki HTML 'yi işler:

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

`data-`HTML çıkışındaki özniteliklerin, özelliği için doğrulama özniteliklerine karşılık geldiğini unutmayın `Movie.ReleaseDate` . `data-val-required`Öznitelik, Kullanıcı Yayın tarihi alanını doldurmazsa, görüntülenecek bir hata iletisi içerir. jQuery unobtrusive doğrulaması bu değeri jQuery doğrulaması [Required ()](https://jqueryvalidation.org/required-method/) yöntemine geçirir ve sonra bu iletiyi eşlik eden **\<span>** öğede görüntüler.

Veri türü doğrulama, bir öznitelik tarafından geçersiz kılınmadığı müddetçe, özelliğin .NET türünü temel alır `[DataType]` . Tarayıcıların kendi varsayılan hata iletileri vardır ancak jQuery doğrulaması unobtrusive doğrulama paketi bu iletileri geçersiz kılabilir. `[DataType]`gibi öznitelikler ve alt sınıflar `[EmailAddress]` , hata iletisini belirtmenize izin verir.

## <a name="unobtrusive-validation"></a>Unobtrusive doğrulaması

Obtrusive doğrulaması hakkında bilgi için [Bu GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/1111)bakın.

### <a name="add-validation-to-dynamic-forms"></a>Dinamik formlara doğrulama ekleme

jQuery unobtrusive doğrulaması, sayfa ilk kez yüklendiğinde, jQuery doğrulamasına yönelik doğrulama mantığını ve parametreleri geçirir. Bu nedenle, doğrulama dinamik olarak üretilen formlarda otomatik olarak çalışmaz. Doğrulamayı etkinleştirmek için, jQuery 'ten kaçınmaya yönelik doğrulamayı, dinamik formu oluşturduktan hemen sonra ayrıştırmaya söyleyin. Örneğin, aşağıdaki kod, AJAX aracılığıyla eklenen bir formda istemci tarafı doğrulamayı ayarlar.

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

`$.validator.unobtrusive.parse()`Yöntemi, bir bağımsız değişkeni olarak bir jQuery seçiciyi kabul eder. Bu yöntem, jQuery 'in bu `data-` seçicideki formların özniteliklerini ayrıştırmasına izin vermez. Daha sonra bu özniteliklerin değerleri jQuery doğrulama eklentisine geçirilir.

### <a name="add-validation-to-dynamic-controls"></a>Dinamik denetimlere doğrulama ekleme

`$.validator.unobtrusive.parse()`Yöntemi, ve gibi dinamik olarak üretilen denetimlerde değil, formun tamamında işe yarar `<input>` `<select/>` . Formu yeniden oluşturmak için, aşağıdaki örnekte gösterildiği gibi, form daha önce ayrıştırıldığında eklenen doğrulama verilerini kaldırın:

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

## <a name="custom-client-side-validation"></a>Özel istemci tarafı doğrulaması

Özel istemci tarafı doğrulaması, `data-` özel bir jQuery doğrulama bağdaştırıcısıyla çalışan HTML öznitelikleri oluşturarak yapılır. Aşağıdaki örnek bağdaştırıcı kodu, `[ClassicMovie]` `[ClassicMovieWithClientValidator]` Bu makalede daha önce sunulan ve öznitelikleri için yazılmıştır:

[!code-javascript[](validation/samples/3.x/ValidationSample/wwwroot/js/classicMovieValidator.js)]

Bağdaştırıcıların nasıl yazılacağı hakkında daha fazla bilgi için [jQuery doğrulama belgelerine](https://jqueryvalidation.org/documentation/)bakın.

Belirli bir alan için bir bağdaştırıcının kullanımı, şu öznitelikler tarafından tetiklenir `data-` :

* Alana, doğrulamaya () tabi olacak şekilde bayrak ekleyin `data-val="true"` .
* Bir doğrulama kuralı adı ve hata iletisi metni (örneğin,) belirler `data-val-rulename="Error message."` .
* Doğrulayıcı ihtiyaçlarına ek parametreler sağlayın (örneğin, `data-val-rulename-param1="value"` ).

Aşağıdaki örnek, `data-` örnek uygulamanın özniteliği için öznitelikleri gösterir `ClassicMovie` :

```html
<input class="form-control" type="date"
    data-val="true"
    data-val-classicmovie="Classic movies must have a release year no later than 1960."
    data-val-classicmovie-year="1960"
    data-val-required="The Release Date field is required."
    id="Movie_ReleaseDate" name="Movie.ReleaseDate" value="">
```

Daha önce belirtildiği gibi, [Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro) ve [HTML Yardımcıları](xref:mvc/views/overview) , öznitelikleri işlemek için doğrulama özniteliklerinden bilgileri kullanır `data-` . Özel HTML özniteliklerinin oluşturulmasına neden olan kod yazmak için iki seçenek vardır `data-` :

* Öğesinden türeten bir sınıf oluşturun ve `AttributeAdapterBase<TAttribute>` `IValidationAttributeAdapterProvider` özniteliği ve kendı bağdaştırıcısını dı olarak kaydedin. Bu yöntem, sunucu ile ilgili ve istemciyle ilgili doğrulama kodundaki [tek sorumluluk sorumlusunu](https://wikipedia.org/wiki/Single_responsibility_principle) , ayrı sınıflarda izler. Ayrıca bağdaştırıcı, DI ' de kaydolduğundan bu yana de bunun avantajına sahiptir.
* `IClientModelValidator` `ValidationAttribute` Sınıfınıza uygulayın. Bu yöntem, öznitelik herhangi bir sunucu tarafı doğrulaması yapamazsa ve hiçbir hizmete gerek duymazsa uygun olabilir.

### <a name="attributeadapter-for-client-side-validation"></a>İstemci tarafı doğrulaması için AttributeAdapter

`data-`HTML 'de öznitelikleri işleme yöntemi `ClassicMovie` örnek uygulamadaki özniteliği tarafından kullanılır. Bu yöntemi kullanarak istemci doğrulaması eklemek için:

1. Özel doğrulama özniteliği için bir öznitelik bağdaştırıcı sınıfı oluşturun. Özniteliği [Attributeadapterbase \<T> ](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.attributeadapterbase-1?view=aspnetcore-2.2)'den türet. `AddValidation` `data-` Aşağıdaki örnekte gösterildiği gibi, işlenen çıktıya öznitelikler ekleyen bir yöntem oluşturun:

   [!code-csharp[](validation/samples/3.x/ValidationSample/Validation/ClassicMovieAttributeAdapter.cs?name=snippet_Class)]

1. Uygulayan bir bağdaştırıcı sağlayıcısı sınıfı oluşturun <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.IValidationAttributeAdapterProvider> . Yöntemi içinde `GetAttributeAdapter` , aşağıdaki örnekte gösterildiği gibi, özel özniteliğini bağdaştırıcının oluşturucusuna geçirin:

   [!code-csharp[](validation/samples/3.x/ValidationSample/Validation/CustomValidationAttributeAdapterProvider.cs?name=snippet_Class)]

1. Dı için bağdaştırıcı sağlayıcısını Kaydet `Startup.ConfigureServices` :

   [!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_Configuration&highlight=9-10)]

### <a name="iclientmodelvalidator-for-client-side-validation"></a>İstemci tarafı doğrulaması için ılientmodelvalidator

`data-`HTML 'de öznitelikleri işleme yöntemi `ClassicMovieWithClientValidator` örnek uygulamadaki özniteliği tarafından kullanılır. Bu yöntemi kullanarak istemci doğrulaması eklemek için:

* Özel doğrulama özniteliğinde, `IClientModelValidator` arabirimini uygulayın ve bir `AddValidation` Yöntem oluşturun. Yönteminde, `AddValidation` `data-` Aşağıdaki örnekte gösterildiği gibi, doğrulama için öznitelikler ekleyin:

  [!code-csharp[](validation/samples/3.x/ValidationSample/Validation/ClassicMovieWithClientValidatorAttribute.cs?name=snippet_Class)]

## <a name="disable-client-side-validation"></a>İstemci tarafı doğrulamayı devre dışı bırak

Aşağıdaki kod sayfalarda istemci doğrulamasını devre dışı bırakır Razor :

[!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_DisableClientValidation&highlight=2-5)]

İstemci tarafı doğrulamayı devre dışı bırakmak için diğer seçenekler:

* `_ValidationScriptsPartial`Tüm *. cshtml* dosyalarındaki başvuruyu not edin.
* *Pages\shared \_ validationscriptspartial. cshtml* dosyasının içeriğini kaldırın.

Önceki yaklaşım ASP.NET Core sınıf kitaplığının istemci tarafında doğrulanmasını engellemez Identity Razor . Daha fazla bilgi için bkz. <xref:security/authentication/scaffold-identity>.

## <a name="additional-resources"></a>Ek kaynaklar

* [System. ComponentModel. Dataaçıklamalarda ad alanı](xref:System.ComponentModel.DataAnnotations)
* [Model bağlama](model-binding.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Bu makalede, ASP.NET Core MVC veya Pages uygulamasında Kullanıcı girişinin nasıl doğrulanacağı açıklanır Razor .

[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/sample) ([nasıl indirilir](xref:index#how-to-download-a-sample)).

## <a name="model-state"></a>Model durumu

Model durumu iki alt sistemden gelen hataları temsil eder: model bağlama ve model doğrulama. [Model bağlamasından](model-binding.md) kaynaklanan hatalar genellikle veri dönüştürme hatalardır (örneğin, bir tamsayı bekleyen bir alana bir "x" girilir). Model bağlama ve verilerin iş kurallarına uygun olmadığı rapor hataları (örneğin, 1 ile 5 arasında bir derecelendirme bekleyen bir alana bir 0 girildiğinde) oluşturulduktan sonra model doğrulaması oluşur.

Hem model bağlama hem de doğrulama, bir denetleyici eyleminin veya bir sayfa işleyici yönteminin yürütülmesinden önce oluşur Razor . Web uygulamaları için uygulama, `ModelState.IsValid` uygun şekilde inceleme ve tepki verme sorumluluğundadır. Web Apps genellikle sayfayı bir hata iletisiyle yeniden görüntülerdi:

[!code-csharp[](validation/samples_snapshot/2.x/Create.cshtml.cs?name=snippet&highlight=3-6)]

Web API denetleyicilerinin `ModelState.IsValid` özniteliğe sahip olup olmadığını kontrol etmek zorunda değildir `[ApiController]` . Bu durumda, model durumu geçersiz olduğunda hata ayrıntılarını içeren bir otomatik HTTP 400 yanıtı döndürülür. Daha fazla bilgi için bkz. [OTOMATIK HTTP 400 yanıtları](xref:web-api/index#automatic-http-400-responses).

## <a name="rerun-validation"></a>Doğrulamayı yeniden çalıştır

Doğrulama otomatiktir, ancak el ile yinelemek isteyebilirsiniz. Örneğin, bir özellik için bir değer hesaplamanıza ve özelliği hesaplanan değere ayarladıktan sonra doğrulamayı yeniden çalıştırmak isteyebilirsiniz. Doğrulamayı yeniden çalıştırmak için, `TryValidateModel` yöntemi aşağıda gösterildiği gibi çağırın:

[!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/MoviesController.cs?name=snippet_TryValidateModel&highlight=11)]

## <a name="validation-attributes"></a>Doğrulama öznitelikleri

Doğrulama öznitelikleri, model özellikleri için doğrulama kuralları belirtmenize olanak tanır. [Örnek uygulamadaki](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/sample) aşağıdaki örnek, doğrulama öznitelikleriyle açıklama eklenmiş bir model sınıfı gösterir. `[ClassicMovie]`Özniteliği özel bir doğrulama özniteliğidir ve diğerleri yerleşik olarak bulunur. Gösterilmez `[ClassicMovie2]` , özel bir özniteliği uygulamak için alternatif bir yol gösterir.

[!code-csharp[](validation/samples/2.x/ValidationSample/Models/Movie.cs?name=snippet_ModelClass)]

## <a name="built-in-attributes"></a>Yerleşik öznitelikler

Yerleşik doğrulama öznitelikleri şunlardır:

* `[CreditCard]`: Özelliğin kredi kartı biçimine sahip olduğunu doğrular.
* `[Compare]`: Bir modeldeki iki özelliği eşleştiğini doğrular. Örneğin, *register.cshtml.cs* dosyası, `[Compare]` girilen iki parola eşleşmesini doğrulamak için kullanır. [Yapı Identity İskelesi](xref:security/authentication/scaffold-identity) Kayıt kodunu görmek için.
* `[EmailAddress]`: Özelliğin bir e-posta biçimine sahip olduğunu doğrular.
* `[Phone]`: Özelliğin bir telefon numarası biçimine sahip olduğunu doğrular.
* `[Range]`: Özellik değerinin belirtilen bir aralık dahilinde olduğunu doğrular.
* `[RegularExpression]`: Özellik değerinin belirtilen bir normal ifadeyle eşleştiğini doğrular.
* `[Required]`: Alanın null olduğunu doğrular. Bu özniteliğin davranışı hakkındaki ayrıntılar için bkz. [ `[Required]` özniteliği](#required-attribute) .
* `[StringLength]`: Dize özellik değerinin belirtilen uzunluk sınırını aşmadığını doğrular.
* `[Url]`: Özelliğin bir URL biçimine sahip olduğunu doğrular.
* `[Remote]`: Sunucuda bir eylem yöntemi çağırarak istemcide girişi doğrular. Bu özniteliğin davranışı hakkındaki ayrıntılar için bkz. [ `[Remote]` özniteliği](#remote-attribute) .

`[RegularExpression]`Özniteliği istemci tarafı doğrulama ile kullanırken, Regex Istemcide JavaScript 'te yürütülür. Bu, [ECMAScript](/dotnet/standard/base-types/regular-expression-options#ecmascript-matching-behavior) eşleştirme davranışının kullanılacağı anlamına gelir. Daha fazla bilgi için [Bu GitHub sorununa](https://github.com/dotnet/corefx/issues/42487)bakın.

Doğrulama özniteliklerinin tüm listesi [System. ComponentModel. Dataaçıklamalarda](xref:System.ComponentModel.DataAnnotations) ad alanında bulunabilir.

### <a name="error-messages"></a>Hata iletileri

Doğrulama öznitelikleri, geçersiz giriş için görüntülenecek hata iletisini belirtmenize izin verir. Örnek:

```csharp
[StringLength(8, ErrorMessage = "Name length can't be more than 8.")]
```

Dahili olarak, öznitelikler, `String.Format` alan adı için bir yer tutucu ve bazen ek yer tutucular ile çağrı yapılır. Örnek:

```csharp
[StringLength(8, ErrorMessage = "{0} length must be between {2} and {1}.", MinimumLength = 6)]
```

Bir `Name` özelliğe uygulandığında, yukarıdaki kod tarafından oluşturulan hata iletisi "ad uzunluğu 6 ile 8 arasında olmalıdır." olacaktır.

`String.Format`Belirli bir özniteliğin hata iletisinde hangi parametrelerin geçtiğini öğrenmek için, bkz. [dataaçıklamalarda kaynak kodu](https://github.com/dotnet/corefx/tree/master/src/System.ComponentModel.Annotations/src/System/ComponentModel/DataAnnotations).

## <a name="required-attribute"></a>[Zorunlu] özniteliği

Varsayılan olarak, doğrulama sistemi, null olamayan parametreleri veya özellikleri bir özniteliğe sahip gibi davranır `[Required]` . Ve gibi [değer türleri](/dotnet/csharp/language-reference/keywords/value-types) `decimal` `int` null atanamaz.

### <a name="required-validation-on-the-server"></a>[Zorunlu] sunucuda doğrulama

Sunucuda, özelliği null ise gerekli bir değer eksik olarak kabul edilir. Null yapılamayan bir alan her zaman geçerlidir ve [gerekli] özniteliğinin hata mesajı hiçbir zaman gösterilmez.

Ancak, null olamayan bir özellik için model bağlama başarısız olabilir ve gibi bir hata mesajı elde edilir `The value '' is invalid` . Null yapılamayan türlerin sunucu tarafı doğrulaması için özel bir hata iletisi belirtmek üzere aşağıdaki seçenekleriniz vardır:

* Alanı null yapılabilir yapın (örneğin, `decimal?` yerine `decimal` ). [Null \<T> yapılabilir](/dotnet/csharp/programming-guide/nullable-types/) değer türleri standart null yapılabilir türler gibi değerlendirilir.
* Aşağıdaki örnekte gösterildiği gibi model bağlama tarafından kullanılacak varsayılan hata iletisini belirtin:

  [!code-csharp[](validation/samples/2.x/ValidationSample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=4-5)]

  İçin varsayılan iletileri ayarlayabileceğiniz model bağlama hataları hakkında daha fazla bilgi için bkz <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DefaultModelBindingMessageProvider#methods> ..

### <a name="required-validation-on-the-client"></a>[Zorunlu] istemcide doğrulama

Null yapılamayan türler ve dizeler, sunucu ile karşılaştırıldığında, istemci üzerinde farklı şekilde işlenir. İstemcide:

* Yalnızca girdi girildiğinde bir değer vardır. Bu nedenle, istemci tarafı doğrulaması null yapılamayan türler, null yapılabilir türler ile aynı şekilde işler.
* Bir dize alanındaki boşluk, jQuery doğrulaması [gerekli](https://jqueryvalidation.org/required-method/) yöntemi tarafından geçerli bir girdi olarak kabul edilir. Yalnızca boşluk girildiğinde, sunucu tarafı doğrulaması gerekli bir dize alanını geçersiz kabul eder.

Daha önce belirtildiği gibi, null olamayan türler bir özniteliğe sahip olsa da kabul edilir `[Required]` . Bu, özniteliğini uygulamasanız bile istemci tarafı doğrulamayı alacağınız anlamına gelir `[Required]` . Ancak özniteliğini kullanmazsanız varsayılan bir hata iletisi alırsınız. Özel bir hata iletisi belirtmek için özniteliğini kullanın.

## <a name="remote-attribute"></a>[Uzak] özniteliği

`[Remote]`Özniteliği, alan girişinin geçerli olup olmadığını anlamak için sunucu üzerinde bir yöntem çağrılmasını gerektiren istemci tarafı doğrulaması uygular. Örneğin, uygulamanın bir kullanıcı adının zaten kullanımda olup olmadığını doğrulaması gerekebilir.

Uzaktan doğrulamayı uygulamak için:

1. JavaScript 'e çağırmak için bir eylem yöntemi oluşturun.  JQuery Validate [uzak](https://jqueryvalidation.org/remote-method/) YÖNTEMI bir JSON yanıtı bekliyor:

   * `"true"`giriş verilerinin geçerli olduğu anlamına gelir.
   * `"false"`, `undefined` ya da `null` girişin geçersiz olduğu anlamına gelir.  Varsayılan hata iletisini görüntüler.
   * Diğer herhangi bir dize, girişin geçersiz olduğu anlamına gelir. Dizeyi özel bir hata iletisi olarak görüntüleyin.

   Özel bir hata iletisi döndüren eylem yöntemine bir örnek aşağıda verilmiştir:

   [!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyEmail)]

1. Model sınıfında, `[Remote]` Aşağıdaki örnekte gösterildiği gibi, doğrulama eylemi yöntemine işaret eden bir özniteliğe sahip özelliğe açıklama ekleyin:

   [!code-csharp[](validation/samples/2.x/ValidationSample/Models/User.cs?name=snippet_UserEmailProperty)]
 
   `[Remote]`Özniteliği `Microsoft.AspNetCore.Mvc` ad alanıdır. Veya metapackage kullanmıyorsanız [Microsoft. AspNetCore. Mvc. ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures) NuGet paketini yükleyebilirsiniz `Microsoft.AspNetCore.App` `Microsoft.AspNetCore.All` .
   
### <a name="additional-fields"></a>Ek alanlar

`AdditionalFields`Özniteliğinin özelliği, `[Remote]` sunucudaki verilere karşı alan birleşimlerini doğrulamanızı sağlar. Örneğin, `User` model `FirstName` ve `LastName` özellikleri varsa, var olan hiçbir kullanıcının bu ad çiftine sahip olmadığını doğrulamak isteyebilirsiniz. Aşağıdaki örnek nasıl kullanılacağını gösterir `AdditionalFields` :

[!code-csharp[](validation/samples/2.x/ValidationSample/Models/User.cs?name=snippet_UserNameProperties)]

`AdditionalFields`açıkça dizelere ayarlanabilir `"FirstName"` `"LastName"` , ancak [NameOf](/dotnet/csharp/language-reference/keywords/nameof) işlecinin kullanılması daha sonra yeniden düzenlemeyi basitleştirir. Bu doğrulama için eylem yöntemi hem adı hem de soyadı bağımsız değişkenlerini kabul etmelidir:

[!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyName)]

Kullanıcı adı veya soyadı girdiğinde JavaScript, bu ad çiftinin alındığını görmek için uzak bir çağrı yapar.

İki veya daha fazla ek alanı doğrulamak için bunları virgülle ayrılmış bir liste olarak belirtin. Örneğin, modele bir özellik eklemek için `MiddleName` `[Remote]` Aşağıdaki örnekte gösterildiği gibi özniteliği ayarlayın:

```csharp
[Remote(action: "VerifyName", controller: "Users", AdditionalFields = nameof(FirstName) + "," + nameof(LastName))]
public string MiddleName { get; set; }
```

`AdditionalFields`Tüm öznitelik bağımsız değişkenleri gibi, sabit bir ifade olmalıdır. Bu nedenle, bir ara [değerli dize](/dotnet/csharp/language-reference/keywords/interpolated-strings) veya <xref:System.String.Join*> başlatmak için çağrı kullanmayın `AdditionalFields` .

## <a name="alternatives-to-built-in-attributes"></a>Yerleşik özniteliklerin alternatifleri

Yerleşik öznitelikler tarafından sağlanmayan doğrulamaya ihtiyacınız varsa şunları yapabilirsiniz:

* [Özel öznitelikler oluşturun](#custom-attributes).
* [IValidatableObject uygulayın](#ivalidatableobject).

## <a name="custom-attributes"></a>Özel öznitelikler

Yerleşik doğrulama özniteliklerinin işlemeyen senaryolar için özel doğrulama öznitelikleri oluşturabilirsiniz. Öğesinden devralan bir sınıf oluşturun <xref:System.ComponentModel.DataAnnotations.ValidationAttribute> ve yöntemi geçersiz kılın <xref:System.ComponentModel.DataAnnotations.ValidationAttribute.IsValid*> .

`IsValid`Yöntemi, doğrulanacak girdi olan *Value*adlı bir nesne kabul eder. Aşırı yükleme, `ValidationContext` model bağlama tarafından oluşturulan model örneği gibi ek bilgiler sağlayan bir nesneyi de kabul eder.

Aşağıdaki örnek, *Klasik* tarz bir filmin yayın tarihinin belirtilen yıldan daha sonra olmadığını doğrular. `[ClassicMovie2]`Öznitelik önce tarzı denetler ve yalnızca *Klasik*ise devam eder. Classics olarak tanımlanan filmler için, öznitelik oluşturucusuna geçirilen sınırdan daha sonra olmadığından emin olmak için yayın tarihini denetler.)

[!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/ClassicMovieAttribute.cs?name=snippet_ClassicMovieAttribute)]

`movie`Yukarıdaki örnekteki değişken, `Movie` form gönderiminde verileri içeren bir nesneyi temsil eder. `IsValid`Yöntemi, tarihi ve tarzı denetler. Doğrulama başarıyla tamamlandığında, `IsValid` bir `ValidationResult.Success` kod döndürür. Doğrulama başarısız olduğunda, bir `ValidationResult` hata iletisi döndürür.

## <a name="ivalidatableobject"></a>IValidatableObject

Yukarıdaki örnek yalnızca türlerle birlikte kullanılabilir `Movie` . Aşağıdaki örnekte gösterildiği gibi, sınıf düzeyi doğrulama için başka bir seçenek de `IValidatableObject` model sınıfında uygulanır:

[!code-csharp[](validation/samples/2.x/ValidationSample/Models/MovieIValidatable.cs?name=snippet&highlight=1,26-34)]

## <a name="top-level-node-validation"></a>Üst düzey düğüm doğrulaması

Üst düzey düğümler şunları içerir:

* Eylem parametreleri
* Denetleyici özellikleri
* Sayfa işleyici parametreleri
* Sayfa modeli özellikleri

Model bağlantılı üst düzey düğümler, model özelliklerini doğrulamaya ek olarak onaylanır. Örnek uygulamadan aşağıdaki örnekte, `VerifyPhone` yöntemi <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute> eylem parametresini doğrulamak için öğesini kullanır `phone` :

[!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyPhone)]

En üst düzey düğümler, <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute> doğrulama öznitelikleriyle birlikte kullanılabilir. Örnek uygulamadaki aşağıdaki örnekte, `CheckAge` yöntemi, `age` form gönderildiğinde parametrenin sorgu dizesinden bağlanması gerektiğini belirtir:

[!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/UsersController.cs?name=snippet_CheckAge)]

Denetim yaşı sayfasında (*Checkage. cshtml*) iki form vardır. İlk form `Age` `99` bir değeri sorgu dizesi olarak gönderir: `https://localhost:5001/Users/CheckAge?Age=99` .

Sorgu dizesinden düzgün şekilde biçimlendirilen bir `age` parametre gönderildiğinde, form doğrular.

Denetim yaşı sayfasındaki ikinci form, `Age` isteğin gövdesindeki değeri gönderir ve doğrulama başarısız olur. `age`Parametre bir sorgu dizesinden gelmesi gerektiğinden bağlama başarısız olur.

`CompatibilityVersion.Version_2_1`Veya sonraki sürümleriyle çalışırken, en üst düzey düğüm doğrulama varsayılan olarak etkindir. Aksi takdirde, üst düzey düğüm doğrulaması devre dışı bırakılır. Varsayılan seçenek, <xref:Microsoft.AspNetCore.Mvc.MvcOptions.AllowValidatingTopLevelNodes*> () içindeki özelliği `Startup.ConfigureServices` burada gösterildiği gibi ayarlanarak geçersiz kılınabilir:

[!code-csharp[](validation/samples_snapshot/2.x/Startup.cs?name=snippet_AddMvc&highlight=4)]

## <a name="maximum-errors"></a>En fazla hata sayısı

En fazla hata sayısına ulaşıldığında doğrulama durduruluyor (varsayılan olarak 200). Bu numarayı içinde aşağıdaki kodla yapılandırabilirsiniz `Startup.ConfigureServices` :

[!code-csharp[](validation/samples/2.x/ValidationSample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=3)]

## <a name="maximum-recursion"></a>En yüksek özyineleme

<xref:Microsoft.AspNetCore.Mvc.ModelBinding.Validation.ValidationVisitor>doğrulanan modelin nesne grafiğinin gezgeçer. Çok derin olan veya sonsuz özyinelemeli özyinelemeli modeller için, doğrulama yığın taşmasına neden olabilir. [Mvcoptions. MaxValidationDepth](xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxValidationDepth) , ziyaretçi özyineleme yapılandırılmış bir derinliği aşarsa doğrulamanın erken durdurulması için bir yol sağlar. Varsayılan değeri, `MvcOptions.MaxValidationDepth` veya ile çalışırken 32 ' dir `CompatibilityVersion.Version_2_2` . Önceki sürümler için değer null, bu da derinlemesine bir kısıtlama kısıtlaması anlamına gelir.

## <a name="automatic-short-circuit"></a>Otomatik kısa devre

Model grafı doğrulama gerektirmiyorsa, doğrulama otomatik olarak kısa devre dışı (atlandı). Çalışma zamanının, hiçbir doğrulayıcıya sahip olmayan temel elemanlar koleksiyonları ( `byte[]` , `string[]` ,,,, ve gibi) için doğrulamayı atlayan nesneler `Dictionary<string, string>` .

## <a name="disable-validation"></a>Doğrulamayı devre dışı bırak

Doğrulamayı devre dışı bırakmak için:

1. `IObjectModelValidator`Hiçbir alanı geçersiz olarak işaretlememeyen bir uygulama oluşturun.

   [!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/NullObjectModelValidator.cs?name=snippet_DisableValidation)]

1. `Startup.ConfigureServices`Bağımlılık ekleme kapsayıcısında varsayılan uygulamayı değiştirmek için aşağıdaki kodu ekleyin `IObjectModelValidator` .

   [!code-csharp[](validation/samples/2.x/ValidationSample/Startup.cs?name=snippet_DisableValidation)]

Model bağlamalarından kaynaklanan model durumu hatalarını görmeye devam edebilirsiniz.

## <a name="client-side-validation"></a>İstemci tarafı doğrulaması

İstemci tarafı doğrulama, form geçerli olana kadar gönderimi önler. Gönder düğmesi, formu gönderen veya hata iletilerini görüntüleyen JavaScript 'ı çalıştırır.

Bir form üzerinde giriş hataları olduğunda, istemci tarafı doğrulaması sunucuya gereksiz gidiş dönüş önler. *_Layout. cshtml* ve *_ValidationScriptsPartial. cshtml* ' deki aşağıdaki betik başvuruları istemci tarafı doğrulamayı destekler:

[!code-cshtml[](validation/samples/2.x/ValidationSample/Views/Shared/_Layout.cshtml?name=snippet_ScriptTag)]

[!code-cshtml[](validation/samples/2.x/ValidationSample/Views/Shared/_ValidationScriptsPartial.cshtml?name=snippet_ScriptTags)]

[JQuery unobtrusive doğrulama](https://github.com/aspnet/jquery-validation-unobtrusive) betiği, popüler [jQuery Validate](https://jqueryvalidation.org/) eklentisi üzerinde derleme yapan özel bir Microsoft ön uç kitaplığıdır. JQuery unobtrusive doğrulaması olmadan, iki yerde aynı doğrulama mantığını kodlamakta olmanız gerekir: model özelliklerindeki Sunucu tarafı doğrulama özniteliklerinde bir kez ve sonra istemci tarafı betiklerimizde. Bunun yerine, [Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro) ve [HTML Yardımcıları](xref:mvc/views/overview) , doğrulama `data-` gerektiren form öğeleri için HTML 5 özniteliklerini işlemek üzere model özelliklerinden doğrulama özniteliklerini ve tür meta verilerini kullanır. jQuery unobtrusive doğrulaması öznitelikleri ayrıştırır `data-` ve mantığı, sunucu tarafı doğrulama mantığını istemciye, etkili bir şekilde "kopyalamak" amacıyla jQuery doğrulamasına geçirir. Aşağıda gösterildiği gibi, etiket yardımcıları kullanarak istemcisinde doğrulama hatalarını görüntüleyebilirsiniz:

[!code-cshtml[](validation/samples/2.x/ValidationSample/Views/Movies/Create.cshtml?name=snippet_ReleaseDate&highlight=4-5)]

Önceki etiket yardımcıları aşağıdaki HTML 'yi işler.

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

`data-`HTML çıkışındaki özniteliklerin, özelliği için doğrulama özniteliklerine karşılık geldiğini unutmayın `ReleaseDate` . `data-val-required`Öznitelik, Kullanıcı Yayın tarihi alanını doldurmazsa, görüntülenecek bir hata iletisi içerir. jQuery unobtrusive doğrulaması bu değeri jQuery Validate [Required ()](https://jqueryvalidation.org/required-method/) yöntemine geçirir, daha sonra bu iletiyi eşlik eden **\<span>** öğede görüntüler.

Veri türü doğrulama, bir öznitelik tarafından geçersiz kılınmadığı müddetçe, özelliğin .NET türünü temel alır `[DataType]` . Tarayıcıların kendi varsayılan hata iletileri vardır ancak jQuery doğrulaması unobtrusive doğrulama paketi bu iletileri geçersiz kılabilir. `[DataType]`gibi öznitelikler ve alt sınıflar `[EmailAddress]` , hata iletisini belirtmenize izin verir.

### <a name="add-validation-to-dynamic-forms"></a>Dinamik formlara doğrulama ekleme

jQuery unobtrusive doğrulaması, sayfa ilk yüklendiğinde jQuery doğrulaması için doğrulama mantığını ve parametreleri geçirir. Bu nedenle, doğrulama dinamik olarak üretilen formlarda otomatik olarak çalışmaz. Doğrulamayı etkinleştirmek için, jQuery 'ten kaçınmaya yönelik doğrulamayı, dinamik formu oluşturduktan hemen sonra ayrıştırmaya söyleyin. Örneğin, aşağıdaki kod, AJAX aracılığıyla eklenen bir formda istemci tarafı doğrulamayı ayarlar.

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

`$.validator.unobtrusive.parse()`Yöntemi, bir bağımsız değişkeni olarak bir jQuery seçiciyi kabul eder. Bu yöntem, jQuery 'in bu `data-` seçicideki formların özniteliklerini ayrıştırmasına izin vermez. Daha sonra bu özniteliklerin değerleri jQuery Validate eklentisine geçirilir.

### <a name="add-validation-to-dynamic-controls"></a>Dinamik denetimlere doğrulama ekleme

`$.validator.unobtrusive.parse()`Yöntemi, ve gibi dinamik olarak üretilen denetimlerde değil, formun tamamında işe yarar `<input>` `<select/>` . Formu yeniden oluşturmak için, aşağıdaki örnekte gösterildiği gibi, form daha önce ayrıştırıldığında eklenen doğrulama verilerini kaldırın:

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

## <a name="custom-client-side-validation"></a>Özel istemci tarafı doğrulaması

Özel istemci tarafı doğrulama, `data-` özel bir jQuery Validate bağdaştırıcısıyla çalışan HTML öznitelikleri oluşturarak yapılır. Aşağıdaki örnek bağdaştırıcı kodu, `ClassicMovie` `ClassicMovie2` Bu makalede daha önce sunulan ve öznitelikleri için yazılmıştır:

[!code-javascript[](validation/samples/2.x/ValidationSample/wwwroot/js/classicMovieValidator.js?name=snippet_UnobtrusiveValidation)]

Bağdaştırıcıların nasıl yazılacağı hakkında daha fazla bilgi için [jQuery Validate belgelerine](https://jqueryvalidation.org/documentation/)bakın.

Belirli bir alan için bir bağdaştırıcının kullanımı, şu öznitelikler tarafından tetiklenir `data-` :

* Alana, doğrulamaya () tabi olacak şekilde bayrak ekleyin `data-val="true"` .
* Bir doğrulama kuralı adı ve hata iletisi metni (örneğin,) belirler `data-val-rulename="Error message."` .
* Doğrulayıcı ihtiyaçlarına ek parametreler sağlayın (örneğin, `data-val-rulename-parm1="value"` ).

Aşağıdaki örnek, `data-` örnek uygulamanın özniteliği için öznitelikleri gösterir `ClassicMovie` :

```html
<input class="form-control" type="datetime"
    data-val="true"
    data-val-classicmovie1="Classic movies must have a release year earlier than 1960."
    data-val-classicmovie1-year="1960"
    data-val-required="The ReleaseDate field is required."
    id="ReleaseDate" name="ReleaseDate" value="">
```

Daha önce belirtildiği gibi, [Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro) ve [HTML Yardımcıları](xref:mvc/views/overview) , öznitelikleri işlemek için doğrulama özniteliklerinden bilgileri kullanır `data-` . Özel HTML özniteliklerinin oluşturulmasına neden olan kod yazmak için iki seçenek vardır `data-` :

* Öğesinden türeten bir sınıf oluşturun ve `AttributeAdapterBase<TAttribute>` `IValidationAttributeAdapterProvider` özniteliği ve kendı bağdaştırıcısını dı olarak kaydedin. Bu yöntem, sunucu ile ilgili ve istemciyle ilgili doğrulama kodundaki [tek sorumluluk sorumlusunu](https://wikipedia.org/wiki/Single_responsibility_principle) , ayrı sınıflarda izler. Ayrıca bağdaştırıcı, DI ' de kaydolduğundan bu yana de bunun avantajına sahiptir.
* `IClientModelValidator` `ValidationAttribute` Sınıfınıza uygulayın. Bu yöntem, öznitelik herhangi bir sunucu tarafı doğrulaması yapamazsa ve hiçbir hizmete gerek duymazsa uygun olabilir.

### <a name="attributeadapter-for-client-side-validation"></a>İstemci tarafı doğrulaması için AttributeAdapter

`data-`HTML 'de öznitelikleri işleme yöntemi `ClassicMovie` örnek uygulamadaki özniteliği tarafından kullanılır. Bu yöntemi kullanarak istemci doğrulaması eklemek için:

1. Özel doğrulama özniteliği için bir öznitelik bağdaştırıcı sınıfı oluşturun. Özniteliği [Attributeadapterbase \<T> ](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.attributeadapterbase-1?view=aspnetcore-2.2)'den türet. `AddValidation` `data-` Aşağıdaki örnekte gösterildiği gibi, işlenen çıktıya öznitelikler ekleyen bir yöntem oluşturun:

   [!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/ClassicMovieAttributeAdapter.cs?name=snippet_ClassicMovieAttributeAdapter)]

1. Uygulayan bir bağdaştırıcı sağlayıcısı sınıfı oluşturun <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.IValidationAttributeAdapterProvider> . Yöntemi içinde `GetAttributeAdapter` , aşağıdaki örnekte gösterildiği gibi, özel özniteliğini bağdaştırıcının oluşturucusuna geçirin:

   [!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/CustomValidationAttributeAdapterProvider.cs?name=snippet_CustomValidationAttributeAdapterProvider)]

1. Dı için bağdaştırıcı sağlayıcısını Kaydet `Startup.ConfigureServices` :

   [!code-csharp[](validation/samples/2.x/ValidationSample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=8-10)]

### <a name="iclientmodelvalidator-for-client-side-validation"></a>İstemci tarafı doğrulaması için ılientmodelvalidator

`data-`HTML 'de öznitelikleri işleme yöntemi `ClassicMovie2` örnek uygulamadaki özniteliği tarafından kullanılır. Bu yöntemi kullanarak istemci doğrulaması eklemek için:

* Özel doğrulama özniteliğinde, `IClientModelValidator` arabirimini uygulayın ve bir `AddValidation` Yöntem oluşturun. Yönteminde, `AddValidation` `data-` Aşağıdaki örnekte gösterildiği gibi, doğrulama için öznitelikler ekleyin:

  [!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/ClassicMovie2Attribute.cs?name=snippet_ClassicMovie2Attribute)]

## <a name="disable-client-side-validation"></a>İstemci tarafı doğrulamayı devre dışı bırak

Aşağıdaki kod, MVC görünümlerinde istemci doğrulamasını devre dışı bırakır:

[!code-csharp[](validation/samples_snapshot/2.x/Startup2.cs?name=snippet_DisableClientValidation)]

Ve Razor sayfalarında:

[!code-csharp[](validation/samples_snapshot/2.x/Startup3.cs?name=snippet_DisableClientValidation)]

İstemci doğrulamasını devre dışı bırakmaya yönelik başka bir seçenek `_ValidationScriptsPartial` de, *. cshtml* dosyanızdaki başvurusunu açıklama olarak yorumlamadır.

## <a name="additional-resources"></a>Ek kaynaklar

* [System. ComponentModel. Dataaçıklamalarda ad alanı](xref:System.ComponentModel.DataAnnotations)
* [Model bağlama](model-binding.md)

::: moniker-end
