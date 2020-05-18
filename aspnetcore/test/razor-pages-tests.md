---
Başlık: Razor sayfa testleri ASP.NET Core Yazar: Rick-Anderson Description: uygulamalar için Razor birim testleri oluşturmayı öğrenin.
monikerRange: ' >= aspnetcore-2,1 ' MS. Author: Riande MS. Custom: MVC MS. Date: 08/14/2019 No-loc: [Blazor, "Identity", "şifrelemem", Razor, SignalR] uid: test/RAZOR-sayfalar-testler
---
# <a name="razor-pages-unit-tests-in-aspnet-core"></a>ASP.NET Core birim testlerini Razor Pages

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core, Razor Pages uygulamaların birim testlerini destekler. Veri erişim katmanı (DAL) ve sayfa modellerinin testleri şunları sağlamaya yardımcı olur:

* Razor Pages uygulamasının parçaları, uygulama oluşturma sırasında bağımsız olarak ve bir birim olarak birlikte çalışır.
* Sınıfların ve yöntemlerin sınırlı sorumluluk kapsamları vardır.
* Uygulamanın nasıl davranması ile ilgili ek belgeler vardır.
* Kod güncelleştirmeleri tarafından ilgili hatalar olan gerileme, otomatik derleme ve dağıtım sırasında bulunur.

Bu konu, Razor Pages uygulamalar ve birim testleri hakkında temel bir anladığınızı varsayar. Razor Pages uygulamalar veya test kavramları hakkında bilgi sahibi değilseniz aşağıdaki konulara bakın:

* <xref:razor-pages/index>
* <xref:tutorials/razor-pages/razor-pages-start>
* [DotNet test ve xUnit kullanarak .NET Core 'Da birim testi C#](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/razor-pages-tests/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

Örnek proje iki uygulamalardan oluşur:

| Uygulama         | Proje klasörü                     | Açıklama |
| ----------- | ---------------------------------- | ----------- |
| İleti uygulaması | *src/RazorPagesTestSample*         | Kullanıcının ileti eklemesine, bir ileti silmesine, tüm iletileri silmesine ve iletileri çözümlemesine (ileti başına ortalama sözcük sayısını bulur) izin verir. |
| Test uygulaması    | *testler/RazorPagesTestSample. testler* | İleti uygulamasının DAL ve Dizin sayfa modelini test etmek için kullanılır. |

Testler, [Visual Studio](/visualstudio/test/unit-test-your-code) veya [Mac IÇIN VISUAL STUDIO](/dotnet/core/tutorials/using-on-mac-vs-full-solution)gibi bir IDE 'nin yerleşik test özellikleri kullanılarak çalıştırılabilir. [Visual Studio Code](https://code.visualstudio.com/) veya komut satırı kullanıyorsanız, *testler/RazorPagesTestSample. Tests* klasöründeki bir komut isteminde aşağıdaki komutu yürütün:

```dotnetcli
dotnet test
```

## <a name="message-app-organization"></a>İleti uygulama organizasyonu

İleti uygulaması, aşağıdaki özelliklere sahip bir Razor Pages ileti sistemidir:

* Uygulamanın (*Pages/Index. cshtml* ve *Pages/index. cshtml. cs*) dizin sayfası, iletilerin eklenmesi, silinmesini ve ANALIZINI denetlemek için bir UI ve sayfa modeli yöntemleri sağlar (ileti başına ortalama sözcük sayısını bulur).
* Bir ileti, `Message` sınıfı (*Data/Message. cs*) ile iki özelliği `Id` olan (anahtar) ve `Text` (ileti) açıklanmaktadır. `Text` Özelliği gereklidir ve 200 karakterle sınırlıdır.
* İletiler, [Entity Framework bellek içi veritabanı](/ef/core/providers/in-memory/)&#8224; kullanılarak depolanır.
* Uygulama, `AppDbContext` veritabanı bağlamı sınıfında (*Data/appdbcontext. cs*) bir dal içerir. DAL Yöntemleri işaretlenir `virtual`ve bu yöntemler, testlerde kullanım için izin verir.
* Veritabanı uygulama başlangıcında boşsa, ileti deposu üç iletiyle başlatılır. Bu *sağlanan iletiler* , testlerde de kullanılır.

EF konusunda, [InMemory Ile Test](/ef/core/miscellaneous/testing/in-memory)&#8224;, MSTest ile testler için bellek içi bir veritabanının nasıl kullanılacağını açıklar. Bu konu [xUnit](https://xunit.github.io/) test çerçevesini kullanır. Farklı test çerçeveleri genelinde test kavramları ve test uygulamaları benzerdir ancak aynı değildir.

Örnek uygulama depo desenini kullanmaz ve [Iş birimi (UoW) düzeninin](https://martinfowler.com/eaaCatalog/unitOfWork.html)etkin bir örneği olmasa da Razor Pages bu geliştirme düzenlerini destekler. Daha fazla bilgi için bkz. [altyapı Kalıcılık katmanını tasarlama](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) ve <xref:mvc/controllers/testing> (örnek depo modelini uygular).

## <a name="test-app-organization"></a>Test uygulaması kuruluşu

Test uygulaması, *testler/RazorPagesTestSample. Tests* klasörünün içindeki bir konsol uygulamasıdır.

| Test uygulaması klasörü | Açıklama |
| --------------- | ----------- |
| *UnitTests*     | <ul><li>*DataAccessLayerTest.cs* , dal için birim testlerini içerir.</li><li>*IndexPageTests.cs* , Dizin sayfası modelinin birim testlerini içerir.</li></ul> |
| *Yardımcı Programlar*     | Her bir `TestDbContextOptions` dal birim testi için yeni veritabanı bağlamı seçenekleri oluşturmak için kullanılan yöntemi içerir, böylece veritabanı her test için kendi temel koşuluna sıfırlanır. |

Test çerçevesi [xUnit](https://xunit.github.io/)' dir. Nesne sahte işlem çerçevesi [moq](https://github.com/moq/moq4)olur.

## <a name="unit-tests-of-the-data-access-layer-dal"></a>Veri erişim katmanının birim testleri (DAL)

İleti uygulamasında, `AppDbContext` sınıfında dört yöntem bulunan bir dal vardır (*src/RazorPagesTestSample/Data/appdbcontext. cs*). Her yöntemin test uygulamasında bir veya iki birim testi vardır.

| DAL yöntemi               | İşlev                                                                   |
| ------------------------ | -------------------------------------------------------------------------- |
| `GetMessagesAsync`       | Özelliği tarafından `List<Message>` sıralanan veritabanından bir alır. `Text` |
| `AddMessageAsync`        | Veritabanına bir `Message` ekler.                                          |
| `DeleteAllMessagesAsync` | Tüm `Message` girdileri veritabanından siler.                           |
| `DeleteMessageAsync`     | Veritabanından tek tek `Message` siler `Id`.                      |

DAL birim testleri her bir test <xref:Microsoft.EntityFrameworkCore.DbContextOptions> için yeni `AppDbContext` bir oluşturma için gereklidir. Her test için öğesini oluşturmaya `DbContextOptions` yönelik bir yaklaşım, şunu kullanmaktır <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder>:

```csharp
var optionsBuilder = new DbContextOptionsBuilder<AppDbContext>()
    .UseInMemoryDatabase("InMemoryDb");

using (var db = new AppDbContext(optionsBuilder.Options))
{
    // Use the db here in the unit test.
}
```

Bu yaklaşımla ilgili sorun, her testin, önceki testin bulunduğu herhangi bir durumda veritabanını aldığından emin olur. Bu, birbirleriyle karışmaz atomik birim testleri yazmaya çalışırken sorunlu olabilir. Her test için `AppDbContext` yeni bir veritabanı bağlamı kullanmaya zorlamak için, yeni bir hizmet sağlayıcısına `DbContextOptions` dayalı bir örnek sağlayın. Test uygulaması, `Utilities` sınıfının sınıf yöntemi `TestDbContextOptions` kullanılarak nasıl yapılacağını gösterir (testler/*RazorPagesTestSample. testler/Utilities/Utilities. cs*):

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs?name=snippet1)]

DAL birim `DbContextOptions` testlerinde kullanılması, her testin yeni bir veritabanı örneğiyle otomatik olarak çalıştırılmasına izin verir:

```csharp
using (var db = new AppDbContext(Utilities.TestDbContextOptions()))
{
    // Use the db here in the unit test.
}
```

`DataAccessLayerTest` Sınıftaki her bir test yöntemi (*unittests/DataAccessLayerTest. cs*) benzer bir düzenleme-işlem onaylama düzeni izler:

1. Düzenle: veritabanı test için yapılandırılmış ve/veya beklenen sonuç tanımlandı.
1. Davran: test yürütülür.
1. Onaylama: test sonucunun başarılı olup olmadığını belirleme onayları yapılır.

Örneğin, `DeleteMessageAsync` yöntemi `Id` (*src/RazorPagesTestSample/Data/appdbcontext. cs*) tarafından tanımlanan tek bir iletinin kaldırılmasından sorumludur:

[!code-csharp[](razor-pages-tests/samples/3.x/src/RazorPagesTestSample/Data/AppDbContext.cs?name=snippet4)]

Bu yöntem için iki test vardır. Bir test, bir ileti veritabanında olduğunda yöntemin bir iletiyi sildiğini denetler. Diğer yöntem, silme iletisi `Id` yoksa veritabanının değişmediğini sınar. `DeleteMessageAsync_MessageIsDeleted_WhenMessageIsFound` Yöntemi aşağıda gösterilmiştir:

[!code-csharp[](razor-pages-tests/samples_snapshot/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

İlk olarak, yöntemi, hareket adımının hazırlanması gereken düzenleme adımını gerçekleştirir. Dengeli dağıtım iletileri ' de `seedMessages`alınır ve tutulur. Dengeli dağıtım iletileri veritabanına kaydedilir. İçeren `Id` `1` ileti silinmek üzere ayarlanmış. `DeleteMessageAsync` Yöntemi yürütüldüğünde, beklenen iletilerde, ' `Id` ın `1`dışında bir ileti olmalıdır. Değişken `expectedMessages` , beklenen bu sonucu temsil eder.

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

Yöntemi şu şekilde davranır: `DeleteMessageAsync` yöntemi ' ın içinde `recId` geçirme yürütülür: `1`

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet2)]

Son olarak, yöntemi bağlamını edinir `Messages` ve iki eşit olan `expectedMessages` asserile karşılaştırır:

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet3)]

İki ikisinin `List<Message>` de aynı olduğunu karşılaştırmak için:

* İletiler tarafından `Id`sıralanır.
* İleti çiftleri `Text` özelliği ile karşılaştırılır.

Benzer bir test yöntemi, `DeleteMessageAsync_NoMessageIsDeleted_WhenMessageIsNotFound` mevcut olmayan bir iletiyi silmeye çalışılması sonucunu denetler. Bu durumda, veritabanındaki beklenen iletiler, `DeleteMessageAsync` Yöntem yürütüldükten sonra gerçek iletilere eşit olmalıdır. Veritabanının içeriğinde değişiklik olmamalıdır:

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet4)]

## <a name="unit-tests-of-the-page-model-methods"></a>Sayfa modeli yöntemlerinin birim testleri

Başka bir birim testi kümesi, sayfa modeli yöntemlerinin sınamalarından sorumludur. İleti uygulamasında Dizin sayfası modelleri `IndexModel` *src/RazorPagesTestSample/Pages/Index. cshtml. cs*içindeki sınıfta bulunur.

| Sayfa modeli yöntemi | İşlev |
| ----------------- | -------- |
| `OnGetAsync` | `GetMessagesAsync` Yöntemini kullanarak, kullanıcı ARABIRIMI için dal öğesinden gelen iletileri alır. |
| `OnPostAddMessageAsync` | [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) geçerliyse, veritabanına bir ileti eklemek `AddMessageAsync` için çağırır. |
| `OnPostDeleteAllMessagesAsync` | Veritabanındaki `DeleteAllMessagesAsync` tüm iletileri silmek için çağırır. |
| `OnPostDeleteMessageAsync` | Belirtilen bir iletiyi silmek için yürütülür `DeleteMessageAsync` `Id` |
| `OnPostAnalyzeMessagesAsync` | Veritabanında bir veya daha fazla ileti varsa, ileti başına ortalama sözcük sayısını hesaplar. |

Sayfa modeli yöntemleri, `IndexPageTests` sınıfında yedi test kullanılarak test edilir (*testler/RazorPagesTestSample. testler/unittests/ındexpagetests. cs*). Testler tanıdık düzenleme-onaylama-Işlem düzeni kullanır. Bu sınamalar üzerinde odaklanılmıştır:

* [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) geçersiz olduğunda yöntemlerin doğru davranışı izleyip izlemediğini belirleme.
* Yöntemlerin doğru <xref:Microsoft.AspNetCore.Mvc.IActionResult>bir şekilde ürettiği doğrulanıyor.
* Özellik değeri atamalarının doğru şekilde yapıldığından emin olup olmadığı denetleniyor.

Bu test grubu genellikle, bir sayfa modeli yönteminin yürütüldüğü Işlem adımı için beklenen verileri oluşturmak üzere DAL yöntemlerini oluşturuyor. Örneğin, öğesinin `GetMessagesAsync` yöntemi çıkış oluşturmak için `AppDbContext` kullanılır. Bir sayfa modeli yöntemi bu yöntemi yürüttüğünde, sahte sonuç döndürür. Veriler veritabanından gelmiyor. Bu, sayfa modeli testlerinde DAL kullanımı için öngörülebilir, güvenilir bir test koşulları oluşturur.

`OnGetAsync_PopulatesThePageModel_WithAListOfMessages` Test, `GetMessagesAsync` yöntemin sayfa modeli için nasıl kullanılacağını gösterir:

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet1&highlight=3-4)]

`OnGetAsync` Yöntem, işlem adımında yürütüldüğünde, sayfa modelinin `GetMessagesAsync` yöntemini çağırır.

Birim testi Işlem adımı (*testler/RazorPagesTestSample. testler/UnitTests/ındexpagetests. cs*):

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet2)]

`IndexPage`sayfa modelinin `OnGetAsync` yöntemi (*src/RazorPagesTestSample/Pages/Index. cshtml. cs*):

[!code-csharp[](razor-pages-tests/samples/3.x/src/RazorPagesTestSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3)]

DAL `GetMessagesAsync` içindeki yöntemi bu yöntem çağrısının sonucunu döndürmez. Metodun moclenmiş sürümü sonucu döndürür.

`Assert` Adımda, gerçek iletiler (`actualMessages`) sayfa modelinin `Messages` özelliğinden atanır. İletiler atandığında bir tür denetimi de gerçekleştirilir. Beklenen ve gerçek iletiler `Text` özellikleriyle karşılaştırılır. Test, iki `List<Message>` örneğinin aynı iletileri içerdiğini onaylar.

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet3)]

Bu gruptaki diğer <xref:Microsoft.AspNetCore.Http.DefaultHttpContext>testler,,, ve ' ı kurmak <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary> <xref:Microsoft.AspNetCore.Mvc.ActionContext> `PageContext` `ViewDataDictionary`için,, bir içeren sayfa modeli nesneleri oluşturur. `PageContext` Bunlar, testleri yürütmek için faydalıdır. Örneğin, ileti `ModelState` uygulaması yürütüldüğünde geçerli <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*> <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult> bir döndürülüp döndürüldüğünden `OnPostAddMessageAsync` emin olmak için bir hata oluşturur:

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet4&highlight=11,26,29,32)]

## <a name="additional-resources"></a>Ek kaynaklar

* [DotNet test ve xUnit kullanarak .NET Core 'Da birim testi C#](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* <xref:mvc/controllers/testing>
* [Kodunuzun birim testi](/visualstudio/test/unit-test-your-code) (Visual Studio)
* <xref:test/integration-tests>
* [xUnit.net](https://xunit.github.io/)
* [Mac için Visual Studio kullanarak macOS’ta eksiksiz bir .NET Core çözümü derleme](/dotnet/core/tutorials/using-on-mac-vs-full-solution)
* [XUnit.net kullanmaya başlama: .NET SDK komut satırı ile .NET Core kullanma](https://xunit.github.io/docs/getting-started-dotnet-core)
* [Moq dili](https://github.com/moq/moq4)
* [Moq hızlı başlangıç](https://github.com/Moq/moq4/wiki/Quickstart)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core, Razor Pages uygulamaların birim testlerini destekler. Veri erişim katmanı (DAL) ve sayfa modellerinin testleri şunları sağlamaya yardımcı olur:

* Razor Pages uygulamasının parçaları, uygulama oluşturma sırasında bağımsız olarak ve bir birim olarak birlikte çalışır.
* Sınıfların ve yöntemlerin sınırlı sorumluluk kapsamları vardır.
* Uygulamanın nasıl davranması ile ilgili ek belgeler vardır.
* Kod güncelleştirmeleri tarafından ilgili hatalar olan gerileme, otomatik derleme ve dağıtım sırasında bulunur.

Bu konu, Razor Pages uygulamalar ve birim testleri hakkında temel bir anladığınızı varsayar. Razor Pages uygulamalar veya test kavramları hakkında bilgi sahibi değilseniz aşağıdaki konulara bakın:

* <xref:razor-pages/index>
* <xref:tutorials/razor-pages/razor-pages-start>
* [DotNet test ve xUnit kullanarak .NET Core 'Da birim testi C#](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/razor-pages-tests/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

Örnek proje iki uygulamalardan oluşur:

| Uygulama         | Proje klasörü                     | Açıklama |
| ----------- | ---------------------------------- | ----------- |
| İleti uygulaması | *src/RazorPagesTestSample*         | Kullanıcının ileti eklemesine, bir ileti silmesine, tüm iletileri silmesine ve iletileri çözümlemesine (ileti başına ortalama sözcük sayısını bulur) izin verir. |
| Test uygulaması    | *testler/RazorPagesTestSample. testler* | İleti uygulamasının DAL ve Dizin sayfa modelini test etmek için kullanılır. |

Testler, [Visual Studio](/visualstudio/test/unit-test-your-code) veya [Mac IÇIN VISUAL STUDIO](/dotnet/core/tutorials/using-on-mac-vs-full-solution)gibi bir IDE 'nin yerleşik test özellikleri kullanılarak çalıştırılabilir. [Visual Studio Code](https://code.visualstudio.com/) veya komut satırı kullanıyorsanız, *testler/RazorPagesTestSample. Tests* klasöründeki bir komut isteminde aşağıdaki komutu yürütün:

```dotnetcli
dotnet test
```

## <a name="message-app-organization"></a>İleti uygulama organizasyonu

İleti uygulaması, aşağıdaki özelliklere sahip bir Razor Pages ileti sistemidir:

* Uygulamanın (*Pages/Index. cshtml* ve *Pages/index. cshtml. cs*) dizin sayfası, iletilerin eklenmesi, silinmesini ve ANALIZINI denetlemek için bir UI ve sayfa modeli yöntemleri sağlar (ileti başına ortalama sözcük sayısını bulur).
* Bir ileti, `Message` sınıfı (*Data/Message. cs*) ile iki özelliği `Id` olan (anahtar) ve `Text` (ileti) açıklanmaktadır. `Text` Özelliği gereklidir ve 200 karakterle sınırlıdır.
* İletiler, [Entity Framework bellek içi veritabanı](/ef/core/providers/in-memory/)&#8224; kullanılarak depolanır.
* Uygulama, `AppDbContext` veritabanı bağlamı sınıfında (*Data/appdbcontext. cs*) bir dal içerir. DAL Yöntemleri işaretlenir `virtual`ve bu yöntemler, testlerde kullanım için izin verir.
* Veritabanı uygulama başlangıcında boşsa, ileti deposu üç iletiyle başlatılır. Bu *sağlanan iletiler* , testlerde de kullanılır.

EF konusunda, [InMemory Ile Test](/ef/core/miscellaneous/testing/in-memory)&#8224;, MSTest ile testler için bellek içi bir veritabanının nasıl kullanılacağını açıklar. Bu konu [xUnit](https://xunit.github.io/) test çerçevesini kullanır. Farklı test çerçeveleri genelinde test kavramları ve test uygulamaları benzerdir ancak aynı değildir.

Örnek uygulama depo desenini kullanmaz ve [Iş birimi (UoW) düzeninin](https://martinfowler.com/eaaCatalog/unitOfWork.html)etkin bir örneği olmasa da Razor Pages bu geliştirme düzenlerini destekler. Daha fazla bilgi için bkz. [altyapı Kalıcılık katmanını tasarlama](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) ve <xref:mvc/controllers/testing> (örnek depo modelini uygular).

## <a name="test-app-organization"></a>Test uygulaması kuruluşu

Test uygulaması, *testler/RazorPagesTestSample. Tests* klasörünün içindeki bir konsol uygulamasıdır.

| Test uygulaması klasörü | Açıklama |
| --------------- | ----------- |
| *UnitTests*     | <ul><li>*DataAccessLayerTest.cs* , dal için birim testlerini içerir.</li><li>*IndexPageTests.cs* , Dizin sayfası modelinin birim testlerini içerir.</li></ul> |
| *Yardımcı Programlar*     | Her bir `TestDbContextOptions` dal birim testi için yeni veritabanı bağlamı seçenekleri oluşturmak için kullanılan yöntemi içerir, böylece veritabanı her test için kendi temel koşuluna sıfırlanır. |

Test çerçevesi [xUnit](https://xunit.github.io/)' dir. Nesne sahte işlem çerçevesi [moq](https://github.com/moq/moq4)olur.

## <a name="unit-tests-of-the-data-access-layer-dal"></a>Veri erişim katmanının birim testleri (DAL)

İleti uygulamasında, `AppDbContext` sınıfında dört yöntem bulunan bir dal vardır (*src/RazorPagesTestSample/Data/appdbcontext. cs*). Her yöntemin test uygulamasında bir veya iki birim testi vardır.

| DAL yöntemi               | İşlev                                                                   |
| ------------------------ | -------------------------------------------------------------------------- |
| `GetMessagesAsync`       | Özelliği tarafından `List<Message>` sıralanan veritabanından bir alır. `Text` |
| `AddMessageAsync`        | Veritabanına bir `Message` ekler.                                          |
| `DeleteAllMessagesAsync` | Tüm `Message` girdileri veritabanından siler.                           |
| `DeleteMessageAsync`     | Veritabanından tek tek `Message` siler `Id`.                      |

DAL birim testleri her bir test <xref:Microsoft.EntityFrameworkCore.DbContextOptions> için yeni `AppDbContext` bir oluşturma için gereklidir. Her test için öğesini oluşturmaya `DbContextOptions` yönelik bir yaklaşım, şunu kullanmaktır <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder>:

```csharp
var optionsBuilder = new DbContextOptionsBuilder<AppDbContext>()
    .UseInMemoryDatabase("InMemoryDb");

using (var db = new AppDbContext(optionsBuilder.Options))
{
    // Use the db here in the unit test.
}
```

Bu yaklaşımla ilgili sorun, her testin, önceki testin bulunduğu herhangi bir durumda veritabanını aldığından emin olur. Bu, birbirleriyle karışmaz atomik birim testleri yazmaya çalışırken sorunlu olabilir. Her test için `AppDbContext` yeni bir veritabanı bağlamı kullanmaya zorlamak için, yeni bir hizmet sağlayıcısına `DbContextOptions` dayalı bir örnek sağlayın. Test uygulaması, `Utilities` sınıfının sınıf yöntemi `TestDbContextOptions` kullanılarak nasıl yapılacağını gösterir (testler/*RazorPagesTestSample. testler/Utilities/Utilities. cs*):

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs?name=snippet1)]

DAL birim `DbContextOptions` testlerinde kullanılması, her testin yeni bir veritabanı örneğiyle otomatik olarak çalıştırılmasına izin verir:

```csharp
using (var db = new AppDbContext(Utilities.TestDbContextOptions()))
{
    // Use the db here in the unit test.
}
```

`DataAccessLayerTest` Sınıftaki her bir test yöntemi (*unittests/DataAccessLayerTest. cs*) benzer bir düzenleme-işlem onaylama düzeni izler:

1. Düzenle: veritabanı test için yapılandırılmış ve/veya beklenen sonuç tanımlandı.
1. Davran: test yürütülür.
1. Onaylama: test sonucunun başarılı olup olmadığını belirleme onayları yapılır.

Örneğin, `DeleteMessageAsync` yöntemi `Id` (*src/RazorPagesTestSample/Data/appdbcontext. cs*) tarafından tanımlanan tek bir iletinin kaldırılmasından sorumludur:

[!code-csharp[](razor-pages-tests/samples/2.x/src/RazorPagesTestSample/Data/AppDbContext.cs?name=snippet4)]

Bu yöntem için iki test vardır. Bir test, bir ileti veritabanında olduğunda yöntemin bir iletiyi sildiğini denetler. Diğer yöntem, silme iletisi `Id` yoksa veritabanının değişmediğini sınar. `DeleteMessageAsync_MessageIsDeleted_WhenMessageIsFound` Yöntemi aşağıda gösterilmiştir:

[!code-csharp[](razor-pages-tests/samples_snapshot/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

İlk olarak, yöntemi, hareket adımının hazırlanması gereken düzenleme adımını gerçekleştirir. Dengeli dağıtım iletileri ' de `seedMessages`alınır ve tutulur. Dengeli dağıtım iletileri veritabanına kaydedilir. İçeren `Id` `1` ileti silinmek üzere ayarlanmış. `DeleteMessageAsync` Yöntemi yürütüldüğünde, beklenen iletilerde, ' `Id` ın `1`dışında bir ileti olmalıdır. Değişken `expectedMessages` , beklenen bu sonucu temsil eder.

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

Yöntemi şu şekilde davranır: `DeleteMessageAsync` yöntemi ' ın içinde `recId` geçirme yürütülür: `1`

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet2)]

Son olarak, yöntemi bağlamını edinir `Messages` ve iki eşit olan `expectedMessages` asserile karşılaştırır:

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet3)]

İki ikisinin `List<Message>` de aynı olduğunu karşılaştırmak için:

* İletiler tarafından `Id`sıralanır.
* İleti çiftleri `Text` özelliği ile karşılaştırılır.

Benzer bir test yöntemi, `DeleteMessageAsync_NoMessageIsDeleted_WhenMessageIsNotFound` mevcut olmayan bir iletiyi silmeye çalışılması sonucunu denetler. Bu durumda, veritabanındaki beklenen iletiler, `DeleteMessageAsync` Yöntem yürütüldükten sonra gerçek iletilere eşit olmalıdır. Veritabanının içeriğinde değişiklik olmamalıdır:

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet4)]

## <a name="unit-tests-of-the-page-model-methods"></a>Sayfa modeli yöntemlerinin birim testleri

Başka bir birim testi kümesi, sayfa modeli yöntemlerinin sınamalarından sorumludur. İleti uygulamasında Dizin sayfası modelleri `IndexModel` *src/RazorPagesTestSample/Pages/Index. cshtml. cs*içindeki sınıfta bulunur.

| Sayfa modeli yöntemi | İşlev |
| ----------------- | -------- |
| `OnGetAsync` | `GetMessagesAsync` Yöntemini kullanarak, kullanıcı ARABIRIMI için dal öğesinden gelen iletileri alır. |
| `OnPostAddMessageAsync` | [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) geçerliyse, veritabanına bir ileti eklemek `AddMessageAsync` için çağırır. |
| `OnPostDeleteAllMessagesAsync` | Veritabanındaki `DeleteAllMessagesAsync` tüm iletileri silmek için çağırır. |
| `OnPostDeleteMessageAsync` | Belirtilen bir iletiyi silmek için yürütülür `DeleteMessageAsync` `Id` |
| `OnPostAnalyzeMessagesAsync` | Veritabanında bir veya daha fazla ileti varsa, ileti başına ortalama sözcük sayısını hesaplar. |

Sayfa modeli yöntemleri, `IndexPageTests` sınıfında yedi test kullanılarak test edilir (*testler/RazorPagesTestSample. testler/unittests/ındexpagetests. cs*). Testler tanıdık düzenleme-onaylama-Işlem düzeni kullanır. Bu sınamalar üzerinde odaklanılmıştır:

* [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) geçersiz olduğunda yöntemlerin doğru davranışı izleyip izlemediğini belirleme.
* Yöntemlerin doğru <xref:Microsoft.AspNetCore.Mvc.IActionResult>bir şekilde ürettiği doğrulanıyor.
* Özellik değeri atamalarının doğru şekilde yapıldığından emin olup olmadığı denetleniyor.

Bu test grubu genellikle, bir sayfa modeli yönteminin yürütüldüğü Işlem adımı için beklenen verileri oluşturmak üzere DAL yöntemlerini oluşturuyor. Örneğin, öğesinin `GetMessagesAsync` yöntemi çıkış oluşturmak için `AppDbContext` kullanılır. Bir sayfa modeli yöntemi bu yöntemi yürüttüğünde, sahte sonuç döndürür. Veriler veritabanından gelmiyor. Bu, sayfa modeli testlerinde DAL kullanımı için öngörülebilir, güvenilir bir test koşulları oluşturur.

`OnGetAsync_PopulatesThePageModel_WithAListOfMessages` Test, `GetMessagesAsync` yöntemin sayfa modeli için nasıl kullanılacağını gösterir:

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet1&highlight=3-4)]

`OnGetAsync` Yöntem, işlem adımında yürütüldüğünde, sayfa modelinin `GetMessagesAsync` yöntemini çağırır.

Birim testi Işlem adımı (*testler/RazorPagesTestSample. testler/UnitTests/ındexpagetests. cs*):

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet2)]

`IndexPage`sayfa modelinin `OnGetAsync` yöntemi (*src/RazorPagesTestSample/Pages/Index. cshtml. cs*):

[!code-csharp[](razor-pages-tests/samples/2.x/src/RazorPagesTestSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3)]

DAL `GetMessagesAsync` içindeki yöntemi bu yöntem çağrısının sonucunu döndürmez. Metodun moclenmiş sürümü sonucu döndürür.

`Assert` Adımda, gerçek iletiler (`actualMessages`) sayfa modelinin `Messages` özelliğinden atanır. İletiler atandığında bir tür denetimi de gerçekleştirilir. Beklenen ve gerçek iletiler `Text` özellikleriyle karşılaştırılır. Test, iki `List<Message>` örneğinin aynı iletileri içerdiğini onaylar.

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet3)]

Bu gruptaki diğer <xref:Microsoft.AspNetCore.Http.DefaultHttpContext>testler,,, ve ' ı kurmak <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary> <xref:Microsoft.AspNetCore.Mvc.ActionContext> `PageContext` `ViewDataDictionary`için,, bir içeren sayfa modeli nesneleri oluşturur. `PageContext` Bunlar, testleri yürütmek için faydalıdır. Örneğin, ileti `ModelState` uygulaması yürütüldüğünde geçerli <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*> <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult> bir döndürülüp döndürüldüğünden `OnPostAddMessageAsync` emin olmak için bir hata oluşturur:

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet4&highlight=11,26,29,32)]

## <a name="additional-resources"></a>Ek kaynaklar

* [DotNet test ve xUnit kullanarak .NET Core 'Da birim testi C#](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* <xref:mvc/controllers/testing>
* [Kodunuzun birim testi](/visualstudio/test/unit-test-your-code) (Visual Studio)
* <xref:test/integration-tests>
* [xUnit.net](https://xunit.github.io/)
* [Mac için Visual Studio kullanarak macOS’ta eksiksiz bir .NET Core çözümü derleme](/dotnet/core/tutorials/using-on-mac-vs-full-solution)
* [XUnit.net kullanmaya başlama: .NET SDK komut satırı ile .NET Core kullanma](https://xunit.github.io/docs/getting-started-dotnet-core)
* [Moq dili](https://github.com/moq/moq4)
* [Moq hızlı başlangıç](https://github.com/Moq/moq4/wiki/Quickstart)

::: moniker-end
