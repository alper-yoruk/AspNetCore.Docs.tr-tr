<!-- USED in RP and MVC tutorial -->

## <a name="add-validation-rules-to-the-movie-model"></a>Film modeline doğrulama kuralları ekleme

DataAnnotations ad alanı, bir sınıfa veya özelliğe bildirimsel olarak uygulanan yerleşik doğrulama öznitelikleri kümesi sağlar. DataAnnotations da biçimlendirme `DataType` ile yardımcı gibi biçimlendirme öznitelikleri içerir ve herhangi bir doğrulama sağlamaz.

Yerleşik `Movie` `Required`, , `StringLength`ve `RegularExpression` `Range` doğrulama özniteliklerinden yararlanmak için sınıfı güncelleştirin.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/MovieDateRatingDA.cs?name=snippet1)]

Doğrulama öznitelikleri, uygulandıkları model özellikleriüzerinde zorlamak istediğiniz davranışı belirtir:

* Ve `Required` `MinimumLength` öznitelikleri bir özelliğin bir değeri olması gerektiğini gösterir; ancak hiçbir şey, kullanıcının bu doğrulamayı karşılamak için beyaz alan girmesini engellemez.
* Öznitelik, `RegularExpression` hangi karakterlerin giriş olabileceğini sınırlamak için kullanılır. Önceki kodda, "Tür":

  * Sadece harfleri kullanmalısınız.
  * İlk harfin büyük harf olması gerekir. Beyaz boşluk, sayılar ve özel karakterlere izin verilmez.

* `RegularExpression` "Derecelendirme":

  * İlk karakterin büyük harf olmasını gerektirir.
  * Sonraki alanlarda özel karakterlere ve sayılara izin verir. "PG-13" bir derecelendirme için geçerlidir, ancak bir "Tür" için başarısız olur.

* `Range` özniteliği, bir değeri belirtilen bir aralık içinde kısıtlar.
* Öznitelik, `StringLength` dize özelliğinin en büyük uzunluğunu ve isteğe bağlı olarak en az uzunluğunu ayarlamanızı sağlar.
* Değer türleri (, `int` `float`, `DateTime`, , ) doğal `[Required]` `decimal`olarak gereklidir ve öznitelik gerekmez.

doğrulama kurallarının ASP.NET Core tarafından otomatik olarak uygulanması, uygulamanızın daha sağlam olmasına yardımcı olur. Ayrıca, bir şeyi doğrulamayı ve yanlışlıkla kötü verilerin veritabanına girmesine izin vermeyi unutmamanızı sağlar.
