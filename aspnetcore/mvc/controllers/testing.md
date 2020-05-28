---
title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

---
# <a name="unit-test-controller-logic-in-aspnet-core"></a>ASP.NET Core 'de birim test denetleyicisi mantığı

[Steve Smith](https://ardalis.com/) tarafından

::: moniker range=">= aspnetcore-3.0"

[Birim testleri](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) , bir uygulamanın bir bölümünü altyapısından ve bağımlılıklarından yalıtımıyla test etmeyi içerir. Birim testi denetleyici mantığı olduğunda, yalnızca tek bir eylemin içerikleri test edilir, onun bağımlılıkları veya çerçevenin kendisi değildir.

## <a name="unit-testing-controllers"></a>Birim test denetleyicileri

Denetleyicinin davranışına odaklanmak için denetleyici eylemlerinin birim testlerini ayarlayın. Denetleyici birim testi [Filtreler](xref:mvc/controllers/filters), [yönlendirme](xref:fundamentals/routing)ve [model bağlama](xref:mvc/models/model-binding)gibi senaryoları önler. Bir isteğe topluca yanıt veren bileşenler arasındaki etkileşimleri kapsayan testler, *tümleştirme testleri*tarafından işlenir. Tümleştirme testleri hakkında daha fazla bilgi için bkz <xref:test/integration-tests> ..

Özel filtreler ve rotalar yazıyorsanız, birim, belirli bir denetleyici eyleminde testlerin bir parçası olarak değil, yalıtımına göre test eder.

Denetleyici birim testlerini göstermek için örnek uygulamada aşağıdaki denetleyiciyi gözden geçirin. 

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/testing/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

Ana denetleyici bir beyin fırtınası oturumlarının listesini görüntüler ve yeni beyin fırtınası oturumlarının bir POST isteğiyle oluşturulmasına izin verir:

[!code-csharp[](testing/samples/3.x/TestingControllersSample/src/TestingControllersSample/Controllers/HomeController.cs?name=snippet_HomeController&highlight=1,5,10,31-32)]

Önceki denetleyici:

* [Açık bağımlılıklar ilkesini](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)izler.
* Bir örneği sağlamak için [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection) bekliyor `IBrainstormSessionRepository` .
* `IBrainstormSessionRepository`, [Moq](https://www.nuget.org/packages/Moq/)gibi bir sahte nesne çerçevesi kullanılarak bir moclenmiş hizmetle test edilebilir. Bir ilişkili *nesne* , test için kullanılan önceden tanımlanmış bir özellik ve Yöntem davranışları kümesine sahip bir fabricobject nesnesidir. Daha fazla bilgi için bkz. [tümleştirme testlerine giriş](xref:test/integration-tests#introduction-to-integration-tests).

`HTTP GET Index`Metodun döngüsü veya dallanmayı yoktur ve yalnızca bir yöntem çağırır. Bu eylemin birim testi:

* `IBrainstormSessionRepository`Yöntemini kullanarak hizmeti gizler `GetTestSessions` . `GetTestSessions`Tarih ve oturum adlarıyla iki adet sahte beyin fırtınası oturumu oluşturur.
* Yöntemini yürütür `Index` .
* Yöntemi tarafından döndürülen sonuç üzerinde onaylama işlemleri yapar:
  * Bir <xref:Microsoft.AspNetCore.Mvc.ViewResult> döndürülür.
  * [ViewDataDictionary. model](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary.Model*) bir `StormSessionViewModel` .
  * İçinde depolanan iki beyin fırtınası oturumu vardır `ViewDataDictionary.Model` .

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/HomeControllerTests.cs?name=snippet_Index_ReturnsAViewResult_WithAListOfBrainstormSessions&highlight=14-17)]

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/HomeControllerTests.cs?name=snippet_GetTestSessions)]

Ana denetleyicinin `HTTP POST Index` Yöntem sınamaları şunları doğrular:

* [ModelState. IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid*) olduğunda `false` , eylem yöntemi uygun verilerle *400 hatalı bir istek* döndürür <xref:Microsoft.AspNetCore.Mvc.ViewResult> .
* Ne `ModelState.IsValid` zaman `true` :
  * `Add`Depodaki yöntem çağrılır.
  * <xref:Microsoft.AspNetCore.Mvc.RedirectToActionResult>Doğru bağımsız değişkenlerle bir döndürülür.

Geçersiz bir model durumu, <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*> aşağıdaki ilk testte gösterildiği gibi kullanılarak hatalar eklenerek test edilir:

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/HomeControllerTests.cs?name=snippet_ModelState_ValidOrInvalid&highlight=9,16-17,38-41)]

[ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) geçerli olmadığında, `ViewResult` bir get isteği için de aynı döndürülür. Test geçersiz bir modeli geçirmeye çalışmıyor. Model bağlama çalışmadığından (bir [tümleştirme testi](xref:test/integration-tests) model bağlamayı kullansa da), geçersiz bir modelin geçirilmesi geçerli bir yaklaşım değildir. Bu durumda model bağlama sınanmamıştır. Bu birim testleri yalnızca eylem yöntemindeki kodu test eder.

İkinci test, geçerli olduğunda doğrular `ModelState` :

* Yeni bir `BrainstormSession` eklendi (depo aracılığıyla).
* Yöntemi, `RedirectToActionResult` beklenen özelliklerle bir döndürür.

Çağrılmayan hiçbir çağrı normalde yok sayılır, ancak `Verifiable` Kurulum çağrısının sonunda çağrılması testte sahte doğrulamaya izin verir. Bu, öğesine yapılan çağrısıyla gerçekleştirilir ve bu, `mockRepo.Verify` beklenen yöntemin çağrılmaması durumunda test başarısız olur.

> [!NOTE]
> Bu örnekte kullanılan moq kitaplığı, doğrulanabilir olmayan bir şekilde ("gevşek" bir veya saplamalar olarak da adlandırılır) doğrulanabilir veya "katı" olarak karışık bir şekilde karışık bir şekilde karıştırılamaz. [Moq Ile sahte davranışı özelleştirme](https://github.com/Moq/moq4/wiki/Quickstart#customizing-mock-behavior)hakkında daha fazla bilgi edinin.

Örnek uygulamadaki [Sessioncontroller](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/controllers/testing/samples/3.x/TestingControllersSample/src/TestingControllersSample/Controllers/SessionController.cs) , belirli bir beyin fırtınası oturumuyla ilgili bilgileri görüntüler. Denetleyici geçersiz `id` değerlerle ilgilenme mantığını içerir ( `return` Bu senaryoları kapsayan aşağıdaki örnekte iki senaryo vardır). Son `return` ifade, `StormSessionViewModel` görünüme (*Controllers/sessioncontroller. cs*) yeni bir döndürür:

[!code-csharp[](testing/samples/3.x/TestingControllersSample/src/TestingControllersSample/Controllers/SessionController.cs?name=snippet_SessionController&highlight=12-16,18-22,31)]

Birim testleri `return` , oturum denetleyicisi eyleminde her senaryo için bir test içerir `Index` :

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/SessionControllerTests.cs?name=snippet_SessionControllerTests&highlight=2,11-14,18,31-32,36,50-55)]

Uygulama, fikirler denetleyicisine geçiş yaparken, bir Web API 'SI olarak yol üzerinde işlevselliği kullanıma sunar `api/ideas` :

* `IdeaDTO`Bir beyin fırtınası oturumuyla ilişkili fikirler () listesi, yöntemi tarafından döndürülür `ForSession` .
* `Create`Yöntemi bir oturuma yeni fikirler ekler.

[!code-csharp[](testing/samples/3.x/TestingControllersSample/src/TestingControllersSample/Api/IdeasController.cs?name=snippet_ForSessionAndCreate&highlight=1-2,21-22)]

İş etki alanı varlıklarını doğrudan API çağrıları aracılığıyla döndürmekten kaçının. Etki alanı varlıkları:

* Genellikle istemcinin gerektirdiğinden daha fazla veri içerir.
* Genel olarak sunulan API ile uygulamanın iç etki alanı modelini gereksiz bir şekilde yapın.

Etki alanı varlıkları ve istemciye döndürülen türler arasında eşleme gerçekleştirilebilir:

* Örnek uygulamanın kullandığı şekilde bir LINQ ile el ile `Select` . Daha fazla bilgi için bkz. [LINQ (dil Ile tümleşik sorgu)](/dotnet/standard/using-linq).
* Otomatik olarak bir kitaplıkla (örneğin, [Automaber](https://github.com/AutoMapper/AutoMapper)).

Ardından, örnek uygulama, `Create` fikirler denetleyicisinin ve API yöntemlerine yönelik birim testlerini gösterir `ForSession` .

Örnek uygulama iki test içerir `ForSession` . İlk test, `ForSession` <xref:Microsoft.AspNetCore.Mvc.NotFoundObjectResult> geçersiz bir oturum için (http bulunamadı) döndürüp döndürmeyeceğini belirler:

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests4&highlight=5,7-8,15-16)]

İkinci `ForSession` test, `ForSession` `<List<IdeaDTO>>` geçerli bir oturum için oturum fikirleri () listesini döndürüp döndürmeyeceğini belirler. Denetimler Ayrıca, özelliğinin doğru olduğunu onaylamak için ilk fikri de inceler `Name` :

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests5&highlight=5,7-8,15-18)]

Geçersiz olduğunda yönteminin davranışını test etmek için `Create` `ModelState` , örnek uygulama, testin bir parçası olarak denetleyiciye bir model hatası ekler. Birim testlerinde model doğrulamayı veya model bağlamayı sınamayı denemeyin, ancak &mdash; geçersiz kılma sırasında eylem yönteminin davranışını test edin `ModelState` :

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests1&highlight=7,13)]

İkinci testi, `Create` döndürülen depoya bağlıdır `null` , bu nedenle, sahte depo döndürecek şekilde yapılandırılır `null` . Bir test veritabanı (bellekte veya başka türlü) oluşturmanız gerekmez ve bu sonucu döndüren bir sorgu oluşturun. Örnek kodun gösterildiği gibi, test tek bir bildirimde gerçekleştirilebilir:

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests2&highlight=7-8,15)]

Üçüncü `Create` test, `Create_ReturnsNewlyCreatedIdeaForSession` deponun `UpdateAsync` yönteminin çağrıldığını doğrular. , İle birlikte çağrılır `Verifiable` ve `Verify` doğrulanabilen yöntemin yürütüldüğünü onaylamak için, moclenmiş deponun yöntemi çağırılır. `UpdateAsync`Yöntemin &mdash; bir tümleştirme testiyle gerçekleştirilebilecek verileri kaydettiğinizden emin olmak için birim testin sorumluluğu bu değildir.

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests3&highlight=20-22,28-33)]

## <a name="test-actionresultt"></a>Test ActionResult\<T>

ASP.NET Core 2,1 veya sonraki sürümlerde [ \<T> ActionResult](xref:web-api/action-return-types#actionresultt-type) ( <xref:Microsoft.AspNetCore.Mvc.ActionResult%601> ), belirli bir türden türetilen veya döndürülen bir tür döndürmenizi sağlar `ActionResult` .

Örnek uygulama, belirli bir oturum için döndüren bir yöntemi içerir `List<IdeaDTO>` `id` . Oturum `id` yoksa, denetleyici şunu döndürür <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> :

[!code-csharp[](testing/samples/3.x/TestingControllersSample/src/TestingControllersSample/Api/IdeasController.cs?name=snippet_ForSessionActionResult&highlight=10,21)]

Denetleyicinin iki testi `ForSessionActionResult` uygulamasına dahil edilmiştir `ApiIdeasControllerTests` .

İlk test, denetleyicinin `ActionResult` varolmayan bir oturum için varolmayan bir fikirler listesi döndürdüğünü doğrular `id` :

* `ActionResult`Türü `ActionResult<List<IdeaDTO>>` .
* , <xref:Microsoft.AspNetCore.Mvc.ActionResult`1.Result*> Bir <xref:Microsoft.AspNetCore.Mvc.NotFoundObjectResult> .

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ForSessionActionResult_ReturnsNotFoundObjectResultForNonexistentSession&highlight=7,10,13-14)]

Geçerli bir oturum için `id` ikinci test, yöntemin döndürdüğünü onaylar:

* Bir `ActionResult` tür ile `List<IdeaDTO>` .
* [Eylem sonucu \<T> . Değer](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Value*) bir `List<IdeaDTO>` türdür.
* Listedeki ilk öğe, sahte oturumda saklanan fikrle eşleşen geçerli bir fikrdir (çağırarak elde edilir `GetTestSession` ).

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ForSessionActionResult_ReturnsIdeasForSession&highlight=7-8,15-18)]

Örnek uygulama, belirli bir oturum için yeni bir oluşturma yöntemi de içerir `Idea` . Denetleyici şunu döndürür:

* <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*>geçersiz bir model.
* <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*>oturum yoksa.
* <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>oturum yeni fikrle güncelleştirildiği zaman.

[!code-csharp[](testing/samples/3.x/TestingControllersSample/src/TestingControllersSample/Api/IdeasController.cs?name=snippet_CreateActionResult&highlight=9,16,29)]

`CreateActionResult`' Ye üç test dahildir `ApiIdeasControllerTests` .

İlk metin, bir <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*> geçersiz model için döndürüldüğünü onaylar.

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_CreateActionResult_ReturnsBadRequest_GivenInvalidModel&highlight=7,13-14)]

İkinci test, <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> oturum yoksa bir ' ın döndürülüp döndürülmediğini denetler.

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_CreateActionResult_ReturnsNotFoundObjectResultForNonexistentSession&highlight=5,15,22-23)]

Geçerli bir oturum için `id` son test şunları onaylar:

* Yöntemi bir `ActionResult` türü ile döndürür `BrainstormSession` .
* [Eylem sonucu \<T> . Sonuç](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Result*) bir <xref:Microsoft.AspNetCore.Mvc.CreatedAtActionResult> . `CreatedAtActionResult`, bir üst bilgiyle *201 oluşturulan* yanıta benzerdir `Location` .
* [Eylem sonucu \<T> . Değer](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Value*) bir `BrainstormSession` türdür.
* Oturumu güncelleştirmek için kullanılan sahte çağrı, `UpdateAsync(testSession)` çağrıldı. `Verifiable`Yöntem çağrısı `mockRepo.Verify()` onaylamalarda yürütülerek denetlenir.
* `Idea`Oturum için iki nesne döndürülür.
* Son öğe ( `Idea` sahte çağrının eklendiği `UpdateAsync` ), `newIdea` testteki oturuma eklenen ile eşleşir.

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_CreateActionResult_ReturnsNewlyCreatedIdeaForSession&highlight=20-22,28-34)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[Denetleyiciler](xref:mvc/controllers/actions) herhangi BIR ASP.NET Core MVC uygulamasında merkezi bir rol oynar. Bu nedenle, denetleyicilerin amaçlanan gibi davrandığına güvenmelisiniz. Otomatik testler, uygulama bir üretim ortamına dağıtılmadan önce hataları tespit edebilir.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/testing/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="unit-tests-of-controller-logic"></a>Denetleyici mantığının birim testleri

[Birim testleri](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) , bir uygulamanın bir bölümünü altyapısından ve bağımlılıklarından yalıtımıyla test etmeyi içerir. Birim testi denetleyici mantığı olduğunda, yalnızca tek bir eylemin içerikleri test edilir, onun bağımlılıkları veya çerçevenin kendisi değildir.

Denetleyicinin davranışına odaklanmak için denetleyici eylemlerinin birim testlerini ayarlayın. Denetleyici birim testi [Filtreler](xref:mvc/controllers/filters), [yönlendirme](xref:fundamentals/routing)ve [model bağlama](xref:mvc/models/model-binding)gibi senaryoları önler. Bir isteğe topluca yanıt veren bileşenler arasındaki etkileşimleri kapsayan testler, *tümleştirme testleri*tarafından işlenir. Tümleştirme testleri hakkında daha fazla bilgi için bkz <xref:test/integration-tests> ..

Özel filtreler ve rotalar yazıyorsanız, birim, belirli bir denetleyici eyleminde testlerin bir parçası olarak değil, yalıtımına göre test eder.

Denetleyici birim testlerini göstermek için örnek uygulamada aşağıdaki denetleyiciyi gözden geçirin. Ana denetleyici bir beyin fırtınası oturumlarının listesini görüntüler ve yeni beyin fırtınası oturumlarının bir POST isteğiyle oluşturulmasına izin verir:

[!code-csharp[](testing/samples/2.x/TestingControllersSample/src/TestingControllersSample/Controllers/HomeController.cs?name=snippet_HomeController&highlight=1,5,10,31-32)]

Önceki denetleyici:

* [Açık bağımlılıklar ilkesini](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)izler.
* Bir örneği sağlamak için [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection) bekliyor `IBrainstormSessionRepository` .
* `IBrainstormSessionRepository`, [Moq](https://www.nuget.org/packages/Moq/)gibi bir sahte nesne çerçevesi kullanılarak bir moclenmiş hizmetle test edilebilir. Bir ilişkili *nesne* , test için kullanılan önceden tanımlanmış bir özellik ve Yöntem davranışları kümesine sahip bir fabricobject nesnesidir. Daha fazla bilgi için bkz. [tümleştirme testlerine giriş](xref:test/integration-tests#introduction-to-integration-tests).

`HTTP GET Index`Metodun döngüsü veya dallanmayı yoktur ve yalnızca bir yöntem çağırır. Bu eylemin birim testi:

* `IBrainstormSessionRepository`Yöntemini kullanarak hizmeti gizler `GetTestSessions` . `GetTestSessions`Tarih ve oturum adlarıyla iki adet sahte beyin fırtınası oturumu oluşturur.
* Yöntemini yürütür `Index` .
* Yöntemi tarafından döndürülen sonuç üzerinde onaylama işlemleri yapar:
  * Bir <xref:Microsoft.AspNetCore.Mvc.ViewResult> döndürülür.
  * [ViewDataDictionary. model](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary.Model*) bir `StormSessionViewModel` .
  * İçinde depolanan iki beyin fırtınası oturumu vardır `ViewDataDictionary.Model` .

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/HomeControllerTests.cs?name=snippet_Index_ReturnsAViewResult_WithAListOfBrainstormSessions&highlight=14-17)]

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/HomeControllerTests.cs?name=snippet_GetTestSessions)]

Ana denetleyicinin `HTTP POST Index` Yöntem sınamaları şunları doğrular:

* [ModelState. IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid*) olduğunda `false` , eylem yöntemi uygun verilerle *400 hatalı bir istek* döndürür <xref:Microsoft.AspNetCore.Mvc.ViewResult> .
* Ne `ModelState.IsValid` zaman `true` :
  * `Add`Depodaki yöntem çağrılır.
  * <xref:Microsoft.AspNetCore.Mvc.RedirectToActionResult>Doğru bağımsız değişkenlerle bir döndürülür.

Geçersiz bir model durumu, <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*> aşağıdaki ilk testte gösterildiği gibi kullanılarak hatalar eklenerek test edilir:

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/HomeControllerTests.cs?name=snippet_ModelState_ValidOrInvalid&highlight=9,16-17,38-41)]

[ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) geçerli olmadığında, `ViewResult` bir get isteği için de aynı döndürülür. Test geçersiz bir modeli geçirmeye çalışmıyor. Model bağlama çalışmadığından (bir [tümleştirme testi](xref:test/integration-tests) model bağlamayı kullansa da), geçersiz bir modelin geçirilmesi geçerli bir yaklaşım değildir. Bu durumda model bağlama sınanmamıştır. Bu birim testleri yalnızca eylem yöntemindeki kodu test eder.

İkinci test, geçerli olduğunda doğrular `ModelState` :

* Yeni bir `BrainstormSession` eklendi (depo aracılığıyla).
* Yöntemi, `RedirectToActionResult` beklenen özelliklerle bir döndürür.

Çağrılmayan hiçbir çağrı normalde yok sayılır, ancak `Verifiable` Kurulum çağrısının sonunda çağrılması testte sahte doğrulamaya izin verir. Bu, öğesine yapılan çağrısıyla gerçekleştirilir ve bu, `mockRepo.Verify` beklenen yöntemin çağrılmaması durumunda test başarısız olur.

> [!NOTE]
> Bu örnekte kullanılan moq kitaplığı, doğrulanabilir olmayan bir şekilde ("gevşek" bir veya saplamalar olarak da adlandırılır) doğrulanabilir veya "katı" olarak karışık bir şekilde karışık bir şekilde karıştırılamaz. [Moq Ile sahte davranışı özelleştirme](https://github.com/Moq/moq4/wiki/Quickstart#customizing-mock-behavior)hakkında daha fazla bilgi edinin.

Örnek uygulamadaki [Sessioncontroller](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/controllers/testing/samples/2.x/TestingControllersSample/src/TestingControllersSample/Controllers/SessionController.cs) , belirli bir beyin fırtınası oturumuyla ilgili bilgileri görüntüler. Denetleyici geçersiz `id` değerlerle ilgilenme mantığını içerir ( `return` Bu senaryoları kapsayan aşağıdaki örnekte iki senaryo vardır). Son `return` ifade, `StormSessionViewModel` görünüme (*Controllers/sessioncontroller. cs*) yeni bir döndürür:

[!code-csharp[](testing/samples/2.x/TestingControllersSample/src/TestingControllersSample/Controllers/SessionController.cs?name=snippet_SessionController&highlight=12-16,18-22,31)]

Birim testleri `return` , oturum denetleyicisi eyleminde her senaryo için bir test içerir `Index` :

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/SessionControllerTests.cs?name=snippet_SessionControllerTests&highlight=2,11-14,18,31-32,36,50-55)]

Uygulama, fikirler denetleyicisine geçiş yaparken, bir Web API 'SI olarak yol üzerinde işlevselliği kullanıma sunar `api/ideas` :

* `IdeaDTO`Bir beyin fırtınası oturumuyla ilişkili fikirler () listesi, yöntemi tarafından döndürülür `ForSession` .
* `Create`Yöntemi bir oturuma yeni fikirler ekler.

[!code-csharp[](testing/samples/2.x/TestingControllersSample/src/TestingControllersSample/Api/IdeasController.cs?name=snippet_ForSessionAndCreate&highlight=1-2,21-22)]

İş etki alanı varlıklarını doğrudan API çağrıları aracılığıyla döndürmekten kaçının. Etki alanı varlıkları:

* Genellikle istemcinin gerektirdiğinden daha fazla veri içerir.
* Genel olarak sunulan API ile uygulamanın iç etki alanı modelini gereksiz bir şekilde yapın.

Etki alanı varlıkları ve istemciye döndürülen türler arasında eşleme gerçekleştirilebilir:

* Örnek uygulamanın kullandığı şekilde bir LINQ ile el ile `Select` . Daha fazla bilgi için bkz. [LINQ (dil Ile tümleşik sorgu)](/dotnet/standard/using-linq).
* Otomatik olarak bir kitaplıkla (örneğin, [Automaber](https://github.com/AutoMapper/AutoMapper)).

Ardından, örnek uygulama, `Create` fikirler denetleyicisinin ve API yöntemlerine yönelik birim testlerini gösterir `ForSession` .

Örnek uygulama iki test içerir `ForSession` . İlk test, `ForSession` <xref:Microsoft.AspNetCore.Mvc.NotFoundObjectResult> geçersiz bir oturum için (http bulunamadı) döndürüp döndürmeyeceğini belirler:

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests4&highlight=5,7-8,15-16)]

İkinci `ForSession` test, `ForSession` `<List<IdeaDTO>>` geçerli bir oturum için oturum fikirleri () listesini döndürüp döndürmeyeceğini belirler. Denetimler Ayrıca, özelliğinin doğru olduğunu onaylamak için ilk fikri de inceler `Name` :

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests5&highlight=5,7-8,15-18)]

Geçersiz olduğunda yönteminin davranışını test etmek için `Create` `ModelState` , örnek uygulama, testin bir parçası olarak denetleyiciye bir model hatası ekler. Birim testlerinde model doğrulamayı veya model bağlamayı sınamayı denemeyin, ancak &mdash; geçersiz kılma sırasında eylem yönteminin davranışını test edin `ModelState` :

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests1&highlight=7,13)]

İkinci testi, `Create` döndürülen depoya bağlıdır `null` , bu nedenle, sahte depo döndürecek şekilde yapılandırılır `null` . Bir test veritabanı (bellekte veya başka türlü) oluşturmanız gerekmez ve bu sonucu döndüren bir sorgu oluşturun. Örnek kodun gösterildiği gibi, test tek bir bildirimde gerçekleştirilebilir:

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests2&highlight=7-8,15)]

Üçüncü `Create` test, `Create_ReturnsNewlyCreatedIdeaForSession` deponun `UpdateAsync` yönteminin çağrıldığını doğrular. , İle birlikte çağrılır `Verifiable` ve `Verify` doğrulanabilen yöntemin yürütüldüğünü onaylamak için, moclenmiş deponun yöntemi çağırılır. `UpdateAsync`Yöntemin &mdash; bir tümleştirme testiyle gerçekleştirilebilecek verileri kaydettiğinizden emin olmak için birim testin sorumluluğu bu değildir.

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests3&highlight=20-22,28-33)]

## <a name="test-actionresultt"></a>Test ActionResult\<T>

ASP.NET Core 2,1 veya sonraki sürümlerde [ \<T> ActionResult](xref:web-api/action-return-types#actionresultt-type) ( <xref:Microsoft.AspNetCore.Mvc.ActionResult%601> ), belirli bir türden türetilen veya döndürülen bir tür döndürmenizi sağlar `ActionResult` .

Örnek uygulama, belirli bir oturum için döndüren bir yöntemi içerir `List<IdeaDTO>` `id` . Oturum `id` yoksa, denetleyici şunu döndürür <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> :

[!code-csharp[](testing/samples/2.x/TestingControllersSample/src/TestingControllersSample/Api/IdeasController.cs?name=snippet_ForSessionActionResult&highlight=10,21)]

Denetleyicinin iki testi `ForSessionActionResult` uygulamasına dahil edilmiştir `ApiIdeasControllerTests` .

İlk test, denetleyicinin `ActionResult` varolmayan bir oturum için varolmayan bir fikirler listesi döndürdüğünü doğrular `id` :

* `ActionResult`Türü `ActionResult<List<IdeaDTO>>` .
* , <xref:Microsoft.AspNetCore.Mvc.ActionResult`1.Result*> Bir <xref:Microsoft.AspNetCore.Mvc.NotFoundObjectResult> .

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ForSessionActionResult_ReturnsNotFoundObjectResultForNonexistentSession&highlight=7,10,13-14)]

Geçerli bir oturum için `id` ikinci test, yöntemin döndürdüğünü onaylar:

* Bir `ActionResult` tür ile `List<IdeaDTO>` .
* [Eylem sonucu \<T> . Değer](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Value*) bir `List<IdeaDTO>` türdür.
* Listedeki ilk öğe, sahte oturumda saklanan fikrle eşleşen geçerli bir fikrdir (çağırarak elde edilir `GetTestSession` ).

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ForSessionActionResult_ReturnsIdeasForSession&highlight=7-8,15-18)]

Örnek uygulama, belirli bir oturum için yeni bir oluşturma yöntemi de içerir `Idea` . Denetleyici şunu döndürür:

* <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*>geçersiz bir model.
* <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*>oturum yoksa.
* <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>oturum yeni fikrle güncelleştirildiği zaman.

[!code-csharp[](testing/samples/2.x/TestingControllersSample/src/TestingControllersSample/Api/IdeasController.cs?name=snippet_CreateActionResult&highlight=9,16,29)]

`CreateActionResult`' Ye üç test dahildir `ApiIdeasControllerTests` .

İlk metin, bir <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*> geçersiz model için döndürüldüğünü onaylar.

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_CreateActionResult_ReturnsBadRequest_GivenInvalidModel&highlight=7,13-14)]

İkinci test, <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> oturum yoksa bir ' ın döndürülüp döndürülmediğini denetler.

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_CreateActionResult_ReturnsNotFoundObjectResultForNonexistentSession&highlight=5,15,22-23)]

Geçerli bir oturum için `id` son test şunları onaylar:

* Yöntemi bir `ActionResult` türü ile döndürür `BrainstormSession` .
* [Eylem sonucu \<T> . Sonuç](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Result*) bir <xref:Microsoft.AspNetCore.Mvc.CreatedAtActionResult> . `CreatedAtActionResult`, bir üst bilgiyle *201 oluşturulan* yanıta benzerdir `Location` .
* [Eylem sonucu \<T> . Değer](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Value*) bir `BrainstormSession` türdür.
* Oturumu güncelleştirmek için kullanılan sahte çağrı, `UpdateAsync(testSession)` çağrıldı. `Verifiable`Yöntem çağrısı `mockRepo.Verify()` onaylamalarda yürütülerek denetlenir.
* `Idea`Oturum için iki nesne döndürülür.
* Son öğe ( `Idea` sahte çağrının eklendiği `UpdateAsync` ), `newIdea` testteki oturuma eklenen ile eşleşir.

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_CreateActionResult_ReturnsNewlyCreatedIdeaForSession&highlight=20-22,28-34)]

::: moniker-end

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:test/integration-tests>
* [Visual Studio ile birim testleri oluşturma ve çalıştırma](/visualstudio/test/unit-test-your-code)
* [ASP.NET Core MVC Için Mysınanan. AspNetCore. Mvc-Floent test Kitaplığı](https://github.com/ivaylokenov/MyTested.AspNetCore.Mvc): MVC ve Web API uygulamalarını test etmek için akıcı bir arabirim sağlayan, türü kesin belirlenmiş birim testi kitaplığı. (*Microsoft tarafından korunmaz veya desteklenmez.*)

