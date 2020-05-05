---
title: ASP.NET Core 'de test denetleyicisi mantığı
author: ardalis
description: Moq ve xUnit ile ASP.NET Core denetleyici mantığını test etme hakkında bilgi edinin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/07/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/controllers/testing
ms.openlocfilehash: 4deae7f7511e3ce94450bc06d5fc8dc77a94f212
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82767089"
---
# <a name="unit-test-controller-logic-in-aspnet-core"></a>ASP.NET Core 'de birim test denetleyicisi mantığı

[Steve Smith](https://ardalis.com/) tarafından

::: moniker range=">= aspnetcore-3.0"

[Birim testleri](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) , bir uygulamanın bir bölümünü altyapısından ve bağımlılıklarından yalıtımıyla test etmeyi içerir. Birim testi denetleyici mantığı olduğunda, yalnızca tek bir eylemin içerikleri test edilir, onun bağımlılıkları veya çerçevenin kendisi değildir.

## <a name="unit-testing-controllers"></a>Birim test denetleyicileri

Denetleyicinin davranışına odaklanmak için denetleyici eylemlerinin birim testlerini ayarlayın. Denetleyici birim testi [Filtreler](xref:mvc/controllers/filters), [yönlendirme](xref:fundamentals/routing)ve [model bağlama](xref:mvc/models/model-binding)gibi senaryoları önler. Bir isteğe topluca yanıt veren bileşenler arasındaki etkileşimleri kapsayan testler, *tümleştirme testleri*tarafından işlenir. Tümleştirme testleri hakkında daha fazla bilgi için bkz <xref:test/integration-tests>..

Özel filtreler ve rotalar yazıyorsanız, birim, belirli bir denetleyici eyleminde testlerin bir parçası olarak değil, yalıtımına göre test eder.

Denetleyici birim testlerini göstermek için örnek uygulamada aşağıdaki denetleyiciyi gözden geçirin. 

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/testing/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

Ana denetleyici bir beyin fırtınası oturumlarının listesini görüntüler ve yeni beyin fırtınası oturumlarının bir POST isteğiyle oluşturulmasına izin verir:

[!code-csharp[](testing/samples/3.x/TestingControllersSample/src/TestingControllersSample/Controllers/HomeController.cs?name=snippet_HomeController&highlight=1,5,10,31-32)]

Önceki denetleyici:

* [Açık bağımlılıklar ilkesini](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)izler.
* Bir örneği sağlamak için [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection) bekliyor `IBrainstormSessionRepository`.
* , [Moq](https://www.nuget.org/packages/Moq/)gibi bir sahte nesne çerçevesi `IBrainstormSessionRepository` kullanılarak bir moclenmiş hizmetle test edilebilir. Bir ilişkili *nesne* , test için kullanılan önceden tanımlanmış bir özellik ve Yöntem davranışları kümesine sahip bir fabricobject nesnesidir. Daha fazla bilgi için bkz. [tümleştirme testlerine giriş](xref:test/integration-tests#introduction-to-integration-tests).

`HTTP GET Index` Metodun döngüsü veya dallanmayı yoktur ve yalnızca bir yöntem çağırır. Bu eylemin birim testi:

* `GetTestSessions` Yöntemini kullanarak `IBrainstormSessionRepository` hizmeti gizler. `GetTestSessions`Tarih ve oturum adlarıyla iki adet sahte beyin fırtınası oturumu oluşturur.
* `Index` Yöntemini yürütür.
* Yöntemi tarafından döndürülen sonuç üzerinde onaylama işlemleri yapar:
  * Bir <xref:Microsoft.AspNetCore.Mvc.ViewResult> döndürülür.
  * [ViewDataDictionary. model](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary.Model*) bir `StormSessionViewModel`.
  * İçinde depolanan iki beyin fırtınası oturumu vardır `ViewDataDictionary.Model`.

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/HomeControllerTests.cs?name=snippet_Index_ReturnsAViewResult_WithAListOfBrainstormSessions&highlight=14-17)]

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/HomeControllerTests.cs?name=snippet_GetTestSessions)]

Ana denetleyicinin `HTTP POST Index` Yöntem sınamaları şunları doğrular:

* [ModelState. IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid*) olduğunda `false`, eylem yöntemi uygun verilerle *400 hatalı bir istek* <xref:Microsoft.AspNetCore.Mvc.ViewResult> döndürür.
* Ne `ModelState.IsValid` zaman `true`:
  * Depodaki `Add` yöntem çağrılır.
  * Doğru <xref:Microsoft.AspNetCore.Mvc.RedirectToActionResult> bağımsız değişkenlerle bir döndürülür.

Geçersiz bir model durumu, aşağıdaki ilk testte gösterildiği gibi <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*> kullanılarak hatalar eklenerek test edilir:

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/HomeControllerTests.cs?name=snippet_ModelState_ValidOrInvalid&highlight=9,16-17,38-41)]

[ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) geçerli olmadığında, bir get isteği `ViewResult` için de aynı döndürülür. Test geçersiz bir modeli geçirmeye çalışmıyor. Model bağlama çalışmadığından (bir [tümleştirme testi](xref:test/integration-tests) model bağlamayı kullansa da), geçersiz bir modelin geçirilmesi geçerli bir yaklaşım değildir. Bu durumda model bağlama sınanmamıştır. Bu birim testleri yalnızca eylem yöntemindeki kodu test eder.

İkinci test, `ModelState` geçerli olduğunda doğrular:

* Yeni `BrainstormSession` bir eklendi (depo aracılığıyla).
* Yöntemi, beklenen özelliklerle `RedirectToActionResult` bir döndürür.

Çağrılmayan hiçbir çağrı normalde yok sayılır, ancak kurulum çağrısının sonunda çağrılması `Verifiable` testte sahte doğrulamaya izin verir. Bu, öğesine `mockRepo.Verify`yapılan çağrısıyla gerçekleştirilir ve bu, beklenen yöntemin çağrılmaması durumunda test başarısız olur.

> [!NOTE]
> Bu örnekte kullanılan moq kitaplığı, doğrulanabilir olmayan bir şekilde ("gevşek" bir veya saplamalar olarak da adlandırılır) doğrulanabilir veya "katı" olarak karışık bir şekilde karışık bir şekilde karıştırılamaz. [Moq Ile sahte davranışı özelleştirme](https://github.com/Moq/moq4/wiki/Quickstart#customizing-mock-behavior)hakkında daha fazla bilgi edinin.

Örnek uygulamadaki [Sessioncontroller](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/controllers/testing/samples/3.x/TestingControllersSample/src/TestingControllersSample/Controllers/SessionController.cs) , belirli bir beyin fırtınası oturumuyla ilgili bilgileri görüntüler. Denetleyici geçersiz `id` değerlerle ilgilenme mantığını içerir (bu senaryoları kapsayan aşağıdaki örnekte `return` iki senaryo vardır). Son `return` ifade, görünüme ( `StormSessionViewModel` *Controllers/sessioncontroller. cs*) yeni bir döndürür:

[!code-csharp[](testing/samples/3.x/TestingControllersSample/src/TestingControllersSample/Controllers/SessionController.cs?name=snippet_SessionController&highlight=12-16,18-22,31)]

Birim testleri, oturum denetleyicisi `return` `Index` eyleminde her senaryo için bir test içerir:

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/SessionControllerTests.cs?name=snippet_SessionControllerTests&highlight=2,11-14,18,31-32,36,50-55)]

Uygulama, fikirler denetleyicisine geçiş yaparken, bir Web API 'SI olarak `api/ideas` yol üzerinde işlevselliği kullanıma sunar:

* Bir beyin fırtınası oturumuyla ilişkili`IdeaDTO`fikirler () listesi, `ForSession` yöntemi tarafından döndürülür.
* `Create` Yöntemi bir oturuma yeni fikirler ekler.

[!code-csharp[](testing/samples/3.x/TestingControllersSample/src/TestingControllersSample/Api/IdeasController.cs?name=snippet_ForSessionAndCreate&highlight=1-2,21-22)]

İş etki alanı varlıklarını doğrudan API çağrıları aracılığıyla döndürmekten kaçının. Etki alanı varlıkları:

* Genellikle istemcinin gerektirdiğinden daha fazla veri içerir.
* Genel olarak sunulan API ile uygulamanın iç etki alanı modelini gereksiz bir şekilde yapın.

Etki alanı varlıkları ve istemciye döndürülen türler arasında eşleme gerçekleştirilebilir:

* Örnek uygulamanın kullandığı şekilde `Select`bir LINQ ile el ile. Daha fazla bilgi için bkz. [LINQ (dil Ile tümleşik sorgu)](/dotnet/standard/using-linq).
* Otomatik olarak bir kitaplıkla (örneğin, [Automaber](https://github.com/AutoMapper/AutoMapper)).

Ardından, örnek uygulama, fikirler denetleyicisinin `Create` ve `ForSession` API yöntemlerine yönelik birim testlerini gösterir.

Örnek uygulama iki `ForSession` test içerir. İlk test, geçersiz bir `ForSession` <xref:Microsoft.AspNetCore.Mvc.NotFoundObjectResult> oturum için (http bulunamadı) döndürüp döndürmeyeceğini belirler:

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests4&highlight=5,7-8,15-16)]

İkinci `ForSession` test, geçerli bir `ForSession` oturum için oturum fikirleri (`<List<IdeaDTO>>`) listesini döndürüp döndürmeyeceğini belirler. Denetimler Ayrıca, `Name` özelliğinin doğru olduğunu onaylamak için ilk fikri de inceler:

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests5&highlight=5,7-8,15-18)]

Geçersiz olduğunda `Create` `ModelState` yönteminin davranışını test etmek için, örnek uygulama, testin bir parçası olarak denetleyiciye bir model hatası ekler. Birim testlerinde&mdash;model doğrulamayı veya model bağlamayı sınamayı denemeyin, ancak geçersiz `ModelState`kılma sırasında eylem yönteminin davranışını test edin:

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests1&highlight=7,13)]

İkinci testi, döndürülen `Create` depoya bağlıdır `null`, bu nedenle, sahte depo döndürecek `null`şekilde yapılandırılır. Bir test veritabanı (bellekte veya başka türlü) oluşturmanız gerekmez ve bu sonucu döndüren bir sorgu oluşturun. Örnek kodun gösterildiği gibi, test tek bir bildirimde gerçekleştirilebilir:

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests2&highlight=7-8,15)]

Üçüncü `Create` test `Create_ReturnsNewlyCreatedIdeaForSession`, deponun `UpdateAsync` yönteminin çağrıldığını doğrular. , İle birlikte `Verifiable`çağrılır ve doğrulanabilen yöntemin yürütüldüğünü onaylamak için, moclenmiş deponun `Verify` yöntemi çağırılır. `UpdateAsync` Yöntemin bir tümleştirme testiyle gerçekleştirilebilecek verileri&mdash;kaydettiğinizden emin olmak için birim testin sorumluluğu bu değildir.

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests3&highlight=20-22,28-33)]

## <a name="test-actionresultt"></a>Test ActionResult\<T>

ASP.NET Core 2,1 veya sonraki sürümlerde [ActionResult\<T>](xref:web-api/action-return-types#actionresultt-type) (<xref:Microsoft.AspNetCore.Mvc.ActionResult%601>), belirli bir türden türetilen `ActionResult` veya döndürülen bir tür döndürmenizi sağlar.

Örnek uygulama, belirli bir oturum `List<IdeaDTO>` `id`için döndüren bir yöntemi içerir. Oturum `id` yoksa, denetleyici şunu döndürür <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*>:

[!code-csharp[](testing/samples/3.x/TestingControllersSample/src/TestingControllersSample/Api/IdeasController.cs?name=snippet_ForSessionActionResult&highlight=10,21)]

`ForSessionActionResult` Denetleyicinin iki testi uygulamasına dahil edilmiştir `ApiIdeasControllerTests`.

İlk test, denetleyicinin varolmayan bir oturum `ActionResult` `id`için varolmayan bir fikirler listesi döndürdüğünü doğrular:

* `ActionResult` Türü `ActionResult<List<IdeaDTO>>`.
* , <xref:Microsoft.AspNetCore.Mvc.ActionResult`1.Result*> Bir <xref:Microsoft.AspNetCore.Mvc.NotFoundObjectResult>.

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ForSessionActionResult_ReturnsNotFoundObjectResultForNonexistentSession&highlight=7,10,13-14)]

Geçerli bir oturum `id`için ikinci test, yöntemin döndürdüğünü onaylar:

* Bir `ActionResult` `List<IdeaDTO>` tür ile.
* [ActionResult\<T>. Değer](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Value*) bir `List<IdeaDTO>` türdür.
* Listedeki ilk öğe, sahte oturumda saklanan fikrle eşleşen geçerli bir fikrdir (çağırarak `GetTestSession`elde edilir).

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ForSessionActionResult_ReturnsIdeasForSession&highlight=7-8,15-18)]

Örnek uygulama, belirli bir oturum için yeni `Idea` bir oluşturma yöntemi de içerir. Denetleyici şunu döndürür:

* <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*>geçersiz bir model.
* <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*>oturum yoksa.
* <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>oturum yeni fikrle güncelleştirildiği zaman.

[!code-csharp[](testing/samples/3.x/TestingControllersSample/src/TestingControllersSample/Api/IdeasController.cs?name=snippet_CreateActionResult&highlight=9,16,29)]

' Ye üç `CreateActionResult` test dahildir `ApiIdeasControllerTests`.

İlk metin, bir <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*> geçersiz model için döndürüldüğünü onaylar.

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_CreateActionResult_ReturnsBadRequest_GivenInvalidModel&highlight=7,13-14)]

İkinci test, oturum yoksa bir <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> ' ın döndürülüp döndürülmediğini denetler.

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_CreateActionResult_ReturnsNotFoundObjectResultForNonexistentSession&highlight=5,15,22-23)]

Geçerli bir oturum `id`için son test şunları onaylar:

* Yöntemi bir `ActionResult` `BrainstormSession` türü ile döndürür.
* [ActionResult\<T>. Sonuç](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Result*) bir <xref:Microsoft.AspNetCore.Mvc.CreatedAtActionResult>. `CreatedAtActionResult`, bir `Location` üst bilgiyle *201 oluşturulan* yanıta benzerdir.
* [ActionResult\<T>. Değer](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Value*) bir `BrainstormSession` türdür.
* Oturumu `UpdateAsync(testSession)`güncelleştirmek için kullanılan sahte çağrı, çağrıldı. `Verifiable` Yöntem çağrısı onaylamalarda yürütülerek `mockRepo.Verify()` denetlenir.
* Oturum `Idea` için iki nesne döndürülür.
* Son öğe (sahte çağrının `Idea` eklendiği `UpdateAsync`), `newIdea` testteki oturuma eklenen ile eşleşir.

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_CreateActionResult_ReturnsNewlyCreatedIdeaForSession&highlight=20-22,28-34)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[Denetleyiciler](xref:mvc/controllers/actions) herhangi BIR ASP.NET Core MVC uygulamasında merkezi bir rol oynar. Bu nedenle, denetleyicilerin amaçlanan gibi davrandığına güvenmelisiniz. Otomatik testler, uygulama bir üretim ortamına dağıtılmadan önce hataları tespit edebilir.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/testing/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="unit-tests-of-controller-logic"></a>Denetleyici mantığının birim testleri

[Birim testleri](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) , bir uygulamanın bir bölümünü altyapısından ve bağımlılıklarından yalıtımıyla test etmeyi içerir. Birim testi denetleyici mantığı olduğunda, yalnızca tek bir eylemin içerikleri test edilir, onun bağımlılıkları veya çerçevenin kendisi değildir.

Denetleyicinin davranışına odaklanmak için denetleyici eylemlerinin birim testlerini ayarlayın. Denetleyici birim testi [Filtreler](xref:mvc/controllers/filters), [yönlendirme](xref:fundamentals/routing)ve [model bağlama](xref:mvc/models/model-binding)gibi senaryoları önler. Bir isteğe topluca yanıt veren bileşenler arasındaki etkileşimleri kapsayan testler, *tümleştirme testleri*tarafından işlenir. Tümleştirme testleri hakkında daha fazla bilgi için bkz <xref:test/integration-tests>..

Özel filtreler ve rotalar yazıyorsanız, birim, belirli bir denetleyici eyleminde testlerin bir parçası olarak değil, yalıtımına göre test eder.

Denetleyici birim testlerini göstermek için örnek uygulamada aşağıdaki denetleyiciyi gözden geçirin. Ana denetleyici bir beyin fırtınası oturumlarının listesini görüntüler ve yeni beyin fırtınası oturumlarının bir POST isteğiyle oluşturulmasına izin verir:

[!code-csharp[](testing/samples/2.x/TestingControllersSample/src/TestingControllersSample/Controllers/HomeController.cs?name=snippet_HomeController&highlight=1,5,10,31-32)]

Önceki denetleyici:

* [Açık bağımlılıklar ilkesini](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)izler.
* Bir örneği sağlamak için [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection) bekliyor `IBrainstormSessionRepository`.
* , [Moq](https://www.nuget.org/packages/Moq/)gibi bir sahte nesne çerçevesi `IBrainstormSessionRepository` kullanılarak bir moclenmiş hizmetle test edilebilir. Bir ilişkili *nesne* , test için kullanılan önceden tanımlanmış bir özellik ve Yöntem davranışları kümesine sahip bir fabricobject nesnesidir. Daha fazla bilgi için bkz. [tümleştirme testlerine giriş](xref:test/integration-tests#introduction-to-integration-tests).

`HTTP GET Index` Metodun döngüsü veya dallanmayı yoktur ve yalnızca bir yöntem çağırır. Bu eylemin birim testi:

* `GetTestSessions` Yöntemini kullanarak `IBrainstormSessionRepository` hizmeti gizler. `GetTestSessions`Tarih ve oturum adlarıyla iki adet sahte beyin fırtınası oturumu oluşturur.
* `Index` Yöntemini yürütür.
* Yöntemi tarafından döndürülen sonuç üzerinde onaylama işlemleri yapar:
  * Bir <xref:Microsoft.AspNetCore.Mvc.ViewResult> döndürülür.
  * [ViewDataDictionary. model](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary.Model*) bir `StormSessionViewModel`.
  * İçinde depolanan iki beyin fırtınası oturumu vardır `ViewDataDictionary.Model`.

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/HomeControllerTests.cs?name=snippet_Index_ReturnsAViewResult_WithAListOfBrainstormSessions&highlight=14-17)]

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/HomeControllerTests.cs?name=snippet_GetTestSessions)]

Ana denetleyicinin `HTTP POST Index` Yöntem sınamaları şunları doğrular:

* [ModelState. IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid*) olduğunda `false`, eylem yöntemi uygun verilerle *400 hatalı bir istek* <xref:Microsoft.AspNetCore.Mvc.ViewResult> döndürür.
* Ne `ModelState.IsValid` zaman `true`:
  * Depodaki `Add` yöntem çağrılır.
  * Doğru <xref:Microsoft.AspNetCore.Mvc.RedirectToActionResult> bağımsız değişkenlerle bir döndürülür.

Geçersiz bir model durumu, aşağıdaki ilk testte gösterildiği gibi <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*> kullanılarak hatalar eklenerek test edilir:

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/HomeControllerTests.cs?name=snippet_ModelState_ValidOrInvalid&highlight=9,16-17,38-41)]

[ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) geçerli olmadığında, bir get isteği `ViewResult` için de aynı döndürülür. Test geçersiz bir modeli geçirmeye çalışmıyor. Model bağlama çalışmadığından (bir [tümleştirme testi](xref:test/integration-tests) model bağlamayı kullansa da), geçersiz bir modelin geçirilmesi geçerli bir yaklaşım değildir. Bu durumda model bağlama sınanmamıştır. Bu birim testleri yalnızca eylem yöntemindeki kodu test eder.

İkinci test, `ModelState` geçerli olduğunda doğrular:

* Yeni `BrainstormSession` bir eklendi (depo aracılığıyla).
* Yöntemi, beklenen özelliklerle `RedirectToActionResult` bir döndürür.

Çağrılmayan hiçbir çağrı normalde yok sayılır, ancak kurulum çağrısının sonunda çağrılması `Verifiable` testte sahte doğrulamaya izin verir. Bu, öğesine `mockRepo.Verify`yapılan çağrısıyla gerçekleştirilir ve bu, beklenen yöntemin çağrılmaması durumunda test başarısız olur.

> [!NOTE]
> Bu örnekte kullanılan moq kitaplığı, doğrulanabilir olmayan bir şekilde ("gevşek" bir veya saplamalar olarak da adlandırılır) doğrulanabilir veya "katı" olarak karışık bir şekilde karışık bir şekilde karıştırılamaz. [Moq Ile sahte davranışı özelleştirme](https://github.com/Moq/moq4/wiki/Quickstart#customizing-mock-behavior)hakkında daha fazla bilgi edinin.

Örnek uygulamadaki [Sessioncontroller](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/controllers/testing/samples/2.x/TestingControllersSample/src/TestingControllersSample/Controllers/SessionController.cs) , belirli bir beyin fırtınası oturumuyla ilgili bilgileri görüntüler. Denetleyici geçersiz `id` değerlerle ilgilenme mantığını içerir (bu senaryoları kapsayan aşağıdaki örnekte `return` iki senaryo vardır). Son `return` ifade, görünüme ( `StormSessionViewModel` *Controllers/sessioncontroller. cs*) yeni bir döndürür:

[!code-csharp[](testing/samples/2.x/TestingControllersSample/src/TestingControllersSample/Controllers/SessionController.cs?name=snippet_SessionController&highlight=12-16,18-22,31)]

Birim testleri, oturum denetleyicisi `return` `Index` eyleminde her senaryo için bir test içerir:

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/SessionControllerTests.cs?name=snippet_SessionControllerTests&highlight=2,11-14,18,31-32,36,50-55)]

Uygulama, fikirler denetleyicisine geçiş yaparken, bir Web API 'SI olarak `api/ideas` yol üzerinde işlevselliği kullanıma sunar:

* Bir beyin fırtınası oturumuyla ilişkili`IdeaDTO`fikirler () listesi, `ForSession` yöntemi tarafından döndürülür.
* `Create` Yöntemi bir oturuma yeni fikirler ekler.

[!code-csharp[](testing/samples/2.x/TestingControllersSample/src/TestingControllersSample/Api/IdeasController.cs?name=snippet_ForSessionAndCreate&highlight=1-2,21-22)]

İş etki alanı varlıklarını doğrudan API çağrıları aracılığıyla döndürmekten kaçının. Etki alanı varlıkları:

* Genellikle istemcinin gerektirdiğinden daha fazla veri içerir.
* Genel olarak sunulan API ile uygulamanın iç etki alanı modelini gereksiz bir şekilde yapın.

Etki alanı varlıkları ve istemciye döndürülen türler arasında eşleme gerçekleştirilebilir:

* Örnek uygulamanın kullandığı şekilde `Select`bir LINQ ile el ile. Daha fazla bilgi için bkz. [LINQ (dil Ile tümleşik sorgu)](/dotnet/standard/using-linq).
* Otomatik olarak bir kitaplıkla (örneğin, [Automaber](https://github.com/AutoMapper/AutoMapper)).

Ardından, örnek uygulama, fikirler denetleyicisinin `Create` ve `ForSession` API yöntemlerine yönelik birim testlerini gösterir.

Örnek uygulama iki `ForSession` test içerir. İlk test, geçersiz bir `ForSession` <xref:Microsoft.AspNetCore.Mvc.NotFoundObjectResult> oturum için (http bulunamadı) döndürüp döndürmeyeceğini belirler:

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests4&highlight=5,7-8,15-16)]

İkinci `ForSession` test, geçerli bir `ForSession` oturum için oturum fikirleri (`<List<IdeaDTO>>`) listesini döndürüp döndürmeyeceğini belirler. Denetimler Ayrıca, `Name` özelliğinin doğru olduğunu onaylamak için ilk fikri de inceler:

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests5&highlight=5,7-8,15-18)]

Geçersiz olduğunda `Create` `ModelState` yönteminin davranışını test etmek için, örnek uygulama, testin bir parçası olarak denetleyiciye bir model hatası ekler. Birim testlerinde&mdash;model doğrulamayı veya model bağlamayı sınamayı denemeyin, ancak geçersiz `ModelState`kılma sırasında eylem yönteminin davranışını test edin:

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests1&highlight=7,13)]

İkinci testi, döndürülen `Create` depoya bağlıdır `null`, bu nedenle, sahte depo döndürecek `null`şekilde yapılandırılır. Bir test veritabanı (bellekte veya başka türlü) oluşturmanız gerekmez ve bu sonucu döndüren bir sorgu oluşturun. Örnek kodun gösterildiği gibi, test tek bir bildirimde gerçekleştirilebilir:

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests2&highlight=7-8,15)]

Üçüncü `Create` test `Create_ReturnsNewlyCreatedIdeaForSession`, deponun `UpdateAsync` yönteminin çağrıldığını doğrular. , İle birlikte `Verifiable`çağrılır ve doğrulanabilen yöntemin yürütüldüğünü onaylamak için, moclenmiş deponun `Verify` yöntemi çağırılır. `UpdateAsync` Yöntemin bir tümleştirme testiyle gerçekleştirilebilecek verileri&mdash;kaydettiğinizden emin olmak için birim testin sorumluluğu bu değildir.

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests3&highlight=20-22,28-33)]

## <a name="test-actionresultt"></a>Test ActionResult\<T>

ASP.NET Core 2,1 veya sonraki sürümlerde [ActionResult\<T>](xref:web-api/action-return-types#actionresultt-type) (<xref:Microsoft.AspNetCore.Mvc.ActionResult%601>), belirli bir türden türetilen `ActionResult` veya döndürülen bir tür döndürmenizi sağlar.

Örnek uygulama, belirli bir oturum `List<IdeaDTO>` `id`için döndüren bir yöntemi içerir. Oturum `id` yoksa, denetleyici şunu döndürür <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*>:

[!code-csharp[](testing/samples/2.x/TestingControllersSample/src/TestingControllersSample/Api/IdeasController.cs?name=snippet_ForSessionActionResult&highlight=10,21)]

`ForSessionActionResult` Denetleyicinin iki testi uygulamasına dahil edilmiştir `ApiIdeasControllerTests`.

İlk test, denetleyicinin varolmayan bir oturum `ActionResult` `id`için varolmayan bir fikirler listesi döndürdüğünü doğrular:

* `ActionResult` Türü `ActionResult<List<IdeaDTO>>`.
* , <xref:Microsoft.AspNetCore.Mvc.ActionResult`1.Result*> Bir <xref:Microsoft.AspNetCore.Mvc.NotFoundObjectResult>.

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ForSessionActionResult_ReturnsNotFoundObjectResultForNonexistentSession&highlight=7,10,13-14)]

Geçerli bir oturum `id`için ikinci test, yöntemin döndürdüğünü onaylar:

* Bir `ActionResult` `List<IdeaDTO>` tür ile.
* [ActionResult\<T>. Değer](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Value*) bir `List<IdeaDTO>` türdür.
* Listedeki ilk öğe, sahte oturumda saklanan fikrle eşleşen geçerli bir fikrdir (çağırarak `GetTestSession`elde edilir).

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ForSessionActionResult_ReturnsIdeasForSession&highlight=7-8,15-18)]

Örnek uygulama, belirli bir oturum için yeni `Idea` bir oluşturma yöntemi de içerir. Denetleyici şunu döndürür:

* <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*>geçersiz bir model.
* <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*>oturum yoksa.
* <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>oturum yeni fikrle güncelleştirildiği zaman.

[!code-csharp[](testing/samples/2.x/TestingControllersSample/src/TestingControllersSample/Api/IdeasController.cs?name=snippet_CreateActionResult&highlight=9,16,29)]

' Ye üç `CreateActionResult` test dahildir `ApiIdeasControllerTests`.

İlk metin, bir <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*> geçersiz model için döndürüldüğünü onaylar.

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_CreateActionResult_ReturnsBadRequest_GivenInvalidModel&highlight=7,13-14)]

İkinci test, oturum yoksa bir <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> ' ın döndürülüp döndürülmediğini denetler.

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_CreateActionResult_ReturnsNotFoundObjectResultForNonexistentSession&highlight=5,15,22-23)]

Geçerli bir oturum `id`için son test şunları onaylar:

* Yöntemi bir `ActionResult` `BrainstormSession` türü ile döndürür.
* [ActionResult\<T>. Sonuç](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Result*) bir <xref:Microsoft.AspNetCore.Mvc.CreatedAtActionResult>. `CreatedAtActionResult`, bir `Location` üst bilgiyle *201 oluşturulan* yanıta benzerdir.
* [ActionResult\<T>. Değer](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Value*) bir `BrainstormSession` türdür.
* Oturumu `UpdateAsync(testSession)`güncelleştirmek için kullanılan sahte çağrı, çağrıldı. `Verifiable` Yöntem çağrısı onaylamalarda yürütülerek `mockRepo.Verify()` denetlenir.
* Oturum `Idea` için iki nesne döndürülür.
* Son öğe (sahte çağrının `Idea` eklendiği `UpdateAsync`), `newIdea` testteki oturuma eklenen ile eşleşir.

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_CreateActionResult_ReturnsNewlyCreatedIdeaForSession&highlight=20-22,28-34)]

::: moniker-end

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:test/integration-tests>
* [Visual Studio ile birim testleri oluşturma ve çalıştırma](/visualstudio/test/unit-test-your-code)
* [Mytesting. aspnetcore. Mvc-floent test kitaplığı ASP.NET Core MVC](https://github.com/ivaylokenov/MyTested.AspNetCore.Mvc) &ndash; 'nin kesin türü belirtilmiş birim testi kitaplığı, MVC ve Web API uygulamalarını test etmek için akıcı bir arabirim sağlar. (*Microsoft tarafından korunmaz veya desteklenmez.*)

