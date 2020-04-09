---
title: ASP.NET Core'da Razor Pages ünite testleri
author: rick-anderson
description: Razor Pages uygulamaları için birim testleri nasıl oluşturabilirsiniz öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/14/2019
uid: test/razor-pages-tests
ms.openlocfilehash: 0e217b6b7f15519a3da44f5d074cf80fa96a3b3a
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78664411"
---
# <a name="razor-pages-unit-tests-in-aspnet-core"></a>ASP.NET Core'da Razor Pages ünite testleri

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core, Razor Pages uygulamalarının birim testlerini destekler. Veri erişim katmanının (DAL) ve sayfa modellerinin testleri aşağıdakileri sağlamaya yardımcı olur:

* Razor Pages uygulamasının parçaları, uygulama nın yapımı sırasında bağımsız ve birlikte bir birim olarak çalışır.
* Sınıfların ve yöntemlerin sorumluluk kapsamı sınırlıdır.
* Uygulamanın nasıl olması gerektiğine ilişkin ek belgeler vardır.
* Kod güncelleştirmelerinin getirdiği hatalar olan gerilemeler, otomatik oluşturma ve dağıtım sırasında bulunur.

Bu konu, Razor Pages uygulamaları ve birim testleri hakkında temel bir anlayışa sahip olduğunuzu varsayar. Razor Pages uygulamalarını veya test kavramlarını bilmiyorsanız aşağıdaki konulara bakın:

* <xref:razor-pages/index>
* <xref:tutorials/razor-pages/razor-pages-start>
* [Dotnet testi ve xUnit kullanarak .NET Core'da C# testi](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/razor-pages-tests/samples) ( nasıl[indirilir](xref:index#how-to-download-a-sample))

Örnek proje iki uygulamadan oluşur:

| Uygulama         | Proje klasörü                     | Açıklama |
| ----------- | ---------------------------------- | ----------- |
| İleti uygulaması | *src/RazorPagesTestSample*         | Kullanıcının ileti eklemesine, bir iletiyi silmesine, tüm iletileri silmesine ve iletileri çözümlemesine izin verir (ileti başına ortalama sözcük sayısını bulun). |
| Test uygulaması    | *testler/RazorPagesTestSample.Tests* | İleti uygulamasının DAL ve Dizin sayfa modelini birim test etmek için kullanılır. |

Testler, Mac için [Visual Studio](/visualstudio/test/unit-test-your-code) veya Visual [Studio](/dotnet/core/tutorials/using-on-mac-vs-full-solution)gibi bir IDE'nin yerleşik test özellikleri kullanılarak çalıştırılabilir. Visual [Studio Code](https://code.visualstudio.com/) veya komut satırı kullanıyorsanız, *testler/RazorPagesTestSample.Tests* klasöründe aşağıdaki komut istemini uygulayın:

```dotnetcli
dotnet test
```

## <a name="message-app-organization"></a>İleti uygulaması organizasyonu

İleti uygulaması aşağıdaki özelliklere sahip bir Razor Pages mesaj sistemidir:

* Uygulamanın Dizin sayfası *(Pages/Index.cshtml* ve *Pages/Index.cshtml.css),* iletilerin eklenmesini, silinmesini ve çözümlemesini denetlemek için kullanıcı aracı ve sayfa modeli yöntemleri sağlar (ileti başına ortalama sözcük sayısını bulun).
* `Message` Bir ileti sınıfı *(Data/Message.cs)* tarafından iki `Id` özelliği `Text` olan açıklanır: (anahtar) ve (ileti). Özellik `Text` gereklidir ve 200 karakterle sınırlıdır.
* İletiler [Entity Framework'ün bellek veritabanı](/ef/core/providers/in-memory/)&#8224; kullanılarak depolanır.
* Uygulama, veritabanı bağlam sınıfında bir `AppDbContext` DAL içerir, (*Veri/AppDbContext.cs*). DAL yöntemleri, `virtual`testlerde kullanım yöntemlerinin alay etmesine olanak tanıyan işaretlenmiştir.
* Uygulama nın başlatılmasında veritabanı boşsa, ileti deposu üç iletiyle açılır. Bu *tohumlu iletiler* testlerde de kullanılır.

&#8224;EF konusu, [InMemory ile Test](/ef/core/miscellaneous/testing/in-memory), MSTest ile testler için bellek veritabanı nasıl kullanılacağını açıklar. Bu konu [xUnit](https://xunit.github.io/) test çerçevesini kullanır. Farklı test çerçeveleri arasındaki test kavramları ve test uygulamaları benzerdir, ancak aynı değildir.

Örnek uygulama depo deseni kullanmasa ve [Çalışma Birimi (UoW) deseninin](https://martinfowler.com/eaaCatalog/unitOfWork.html)etkili bir örneği olmasa da, Razor Pages bu geliştirme modellerini destekler. Daha fazla bilgi için bkz: Altyapı <xref:mvc/controllers/testing> [kalıcılık katmanını tasarlama](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) ve (örnek depo deseni uygular).

## <a name="test-app-organization"></a>Test uygulaması organizasyonu

Test *uygulaması, test/RazorPagesTestSample.Tests* klasöründe bir konsol uygulamasıdır.

| Test uygulaması klasörü | Açıklama |
| --------------- | ----------- |
| *ÜniteTestleri*     | <ul><li>*DataAccessLayerTest.cs* DAL için birim testleri içerir.</li><li>*IndexPageTests.cs* Dizin sayfası modeliiçin birim testleri içerir.</li></ul> |
| *Yardımcı Programlar*     | Veritabanının `TestDbContextOptions` her test için temel durumuna sıfırlanır, böylece her DAL birim testi için yeni veritabanı bağlam seçenekleri oluşturmak için kullanılan yöntemi içerir. |

Test çerçevesi [xUnit'tir.](https://xunit.github.io/) Nesne alay çerçeve [Moq](https://github.com/moq/moq4)olduğunu .

## <a name="unit-tests-of-the-data-access-layer-dal"></a>Veri erişim katmanının birim testleri (DAL)

İleti uygulaması `AppDbContext` sınıfında yer alan dört yöntem *(src/RazorPagesTestSample/Data/AppDbContext.cs)* içeren bir DAL'ye sahiptir. Her yöntemin test uygulamasında bir veya iki birim testi vardır.

| DAL yöntemi               | İşlev                                                                   |
| ------------------------ | -------------------------------------------------------------------------- |
| `GetMessagesAsync`       | Özellik tarafından sıralanan veritabanından bir alır. `List<Message>` `Text` |
| `AddMessageAsync`        | Veritabanına `Message` bir ekler.                                          |
| `DeleteAllMessagesAsync` | Veritabanından `Message` tüm girişleri siler.                           |
| `DeleteMessageAsync`     | Veritabanından bir `Message` tek i `Id`ler tarafından siler.                      |

DAL'nin birim <xref:Microsoft.EntityFrameworkCore.DbContextOptions> testleri, her `AppDbContext` test için yeni bir test oluştururken gerektirir. Her test `DbContextOptions` için oluşturmak için bir <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder>yaklaşım kullanmaktır:

```csharp
var optionsBuilder = new DbContextOptionsBuilder<AppDbContext>()
    .UseInMemoryDatabase("InMemoryDb");

using (var db = new AppDbContext(optionsBuilder.Options))
{
    // Use the db here in the unit test.
}
```

Bu yaklaşımla ilgili sorun, her testin veritabanını önceki testin bıraktığı durumda almasıdır. Bu, birbirine müdahale etmeyen atomik birim testleri yazmaya çalışırken sorunlu olabilir. Her test `AppDbContext` için yeni bir veritabanı bağlamı `DbContextOptions` kullanmaya zorlamak için, yeni bir hizmet sağlayıcısını temel alan bir örnek kaynağı olun. Test uygulaması sınıf `Utilities` yöntemini `TestDbContextOptions` kullanarak bunu nasıl yapacağını gösterir *(testler/RazorPagesTestSample.Tests/Utilities/Utilities.cs):*

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs?name=snippet1)]

DAL `DbContextOptions` birim testlerinde kullanılması, her testin yeni bir veritabanı örneğiyle atomik olarak çalışmasını sağlar:

```csharp
using (var db = new AppDbContext(Utilities.TestDbContextOptions()))
{
    // Use the db here in the unit test.
}
```

Sınıftaki `DataAccessLayerTest` her test yöntemi *(UnitTests/DataAccessLayerTest.cs)* benzer bir Düzenleme-Hareket-Assert deseni izler:

1. Düzenleme: Veritabanı test için yapılandırılır ve/veya beklenen sonuç tanımlanır.
1. Hareket: Test yürütülür.
1. Assert: İddialar, test sonucunun başarılı olup olmadığını belirlemek için yapılır.

Örneğin, `DeleteMessageAsync` yöntem onun `Id` *(src/RazorPagesTestSample/Data/AppDbContext.cs)* tarafından tanımlanan tek bir iletiyi kaldırmaktan sorumludur:

[!code-csharp[](razor-pages-tests/samples/3.x/src/RazorPagesTestSample/Data/AppDbContext.cs?name=snippet4)]

Bu yöntem için iki test vardır. Bir test, ileti veritabanında bulunduğunda yöntemin bir iletiyi siler. Silme iletisi `Id` yoksa diğer yöntem veritabanının değişmediğini sınar. Yöntem `DeleteMessageAsync_MessageIsDeleted_WhenMessageIsFound` aşağıda gösterilmiştir:

[!code-csharp[](razor-pages-tests/samples_snapshot/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

İlk olarak, yöntem, Kanun adımına hazırlık yapıldığı Düzenleme adımını gerçekleştirir. Tohumlama mesajları elde edilir ve `seedMessages`. Tohumlama iletileri veritabanına kaydedilir. Bir `Id` of `1` ile ileti silinmesi için ayarlanır. `DeleteMessageAsync` Yöntem yürütüldüğünde, beklenen iletiler bir `Id` `1`' nin ''si olan iletiler dışında tüm iletilere sahip olmalıdır. `expectedMessages` Değişken bu beklenen sonucu temsil eder.

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

Yöntem davranır: `DeleteMessageAsync` Yöntem aşağıdakilerden `recId` geçerek `1`yürütülür:

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet2)]

Son olarak, yöntem `Messages` bağlamından elde eder ve `expectedMessages` bu iki sinin eşit olduğunu iddia etmekle karşılaştırır:

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet3)]

Bu ikisi `List<Message>` nin aynı olduğunu karşılaştırmak için:

* İletiler `Id`tarafından sıralanır.
* İleti çiftleri `Text` özellik üzerinde karşılaştırılır.

Benzer bir sınama yöntemi, `DeleteMessageAsync_NoMessageIsDeleted_WhenMessageIsNotFound` var olmayan bir iletiyi silmeye çalışma sonucunu denetler. Bu durumda, veritabanında beklenen iletiler `DeleteMessageAsync` yöntem yürütüldükten sonra gerçek iletilere eşit olmalıdır. Veritabanının içeriğinde değişiklik olmamalıdır:

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet4)]

## <a name="unit-tests-of-the-page-model-methods"></a>Sayfa modeli yöntemlerinin birim testleri

Başka bir birim testi kümesi, sayfa modeli yöntemlerinin testlerinden sorumludur. İleti uygulamasında, Dizin sayfası modelleri `IndexModel` *src/RazorPagesTestSample/Pages/Index.cshtml.cs*sınıfında bulunur.

| Sayfa modeli yöntemi | İşlev |
| ----------------- | -------- |
| `OnGetAsync` | `GetMessagesAsync` Yöntemi kullanarak UI için DAL'den iletileri alır. |
| `OnPostAddMessageAsync` | [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) geçerliyse, veritabanına ileti eklemek için çağırır. `AddMessageAsync` |
| `OnPostDeleteAllMessagesAsync` | Veritabanındaki tüm iletileri silmek için çağrılar. `DeleteAllMessagesAsync` |
| `OnPostDeleteMessageAsync` | Belirtilen `DeleteMessageAsync` ile bir iletiyi silmek için yürütülür. `Id` |
| `OnPostAnalyzeMessagesAsync` | Veritabanında bir veya daha fazla ileti varsa, ileti başına ortalama sözcük sayısını hesaplar. |

Sayfa modeli yöntemleri `IndexPageTests` sınıfta yedi test kullanılarak test edilir *(testler/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs).* Testler tanıdık Düzenle-İddia-Hareket modelini kullanır. Bu testler şu na odaklanır:

* [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) geçersiz olduğunda yöntemlerin doğru davranışı izp'lep'leyetmeme mi belirlendir.
* Yöntemleri onaylamak doğru <xref:Microsoft.AspNetCore.Mvc.IActionResult>üretir.
* Özellik değeri atamalarının doğru yapıldığını denetleme.

Bu test grubu genellikle, bir sayfa modeli yönteminin yürütüldüğü Act adımı için beklenen verileri üretmek için DAL'Nin yöntemleriyle alay konusu olur. Örneğin, `GetMessagesAsync` çıkış üretmek `AppDbContext` için yöntem alay edilir. Bir sayfa modeli yöntemi bu yöntemi yürütürse, sahte sonucu döndürür. Veriler veritabanından gelmiyor. Bu, sayfa modeli testlerinde DAL'yi kullanmak için öngörülebilir ve güvenilir test koşulları oluşturur.

Test, `OnGetAsync_PopulatesThePageModel_WithAListOfMessages` yöntemin `GetMessagesAsync` sayfa modeli için nasıl alay edildiğini gösterir:

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet1&highlight=3-4)]

`OnGetAsync` Yöntem Act adımında yürütüldüğünde, sayfa modelinin `GetMessagesAsync` yöntemini çağırır.

Ünite test Act adım (*testler/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs):*

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet2)]

`IndexPage`sayfa modeli `OnGetAsync` nin yöntemi (*src/RazorPagesTestSample/Pages/Index.cshtml.cs):*

[!code-csharp[](razor-pages-tests/samples/3.x/src/RazorPagesTestSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3)]

DAL'deki `GetMessagesAsync` yöntem, bu yöntem çağrısının sonucunu döndürmez. Yöntemin alay edilen sürümü sonucu döndürür.

`Assert` Adımda, gerçek iletiler`actualMessages`( ) sayfa `Messages` modelinin özelliğinden atanır. İletiler atandığında bir tür denetimi de gerçekleştirilir. Beklenen ve gerçek iletiler özellikleriyle `Text` karşılaştırılır. Test, iki `List<Message>` örneğin aynı iletileri içerdiğini ileri sürmelidir.

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet3)]

Bu gruptaki <xref:Microsoft.AspNetCore.Http.DefaultHttpContext> <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary>diğer testler, bir , a <xref:Microsoft.AspNetCore.Mvc.ActionContext> `PageContext` `ViewDataDictionary`, ve bir `PageContext`. Bunlar testlerin yürütülmesinde yararlıdır. Örneğin, ileti uygulaması `ModelState` yürütüldüğünde <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*> geçerli <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult> bir döndürülünden `OnPostAddMessageAsync` denetlemede bir hata belirler:

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet4&highlight=11,26,29,32)]

## <a name="additional-resources"></a>Ek kaynaklar

* [Dotnet testi ve xUnit kullanarak .NET Core'da C# testi](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* <xref:mvc/controllers/testing>
* [Birim Kodutest](/visualstudio/test/unit-test-your-code) (Visual Studio)
* <xref:test/integration-tests>
* [xUnit.net](https://xunit.github.io/)
* [Mac için Visual Studio kullanarak macOS’ta eksiksiz bir .NET Core çözümü derleme](/dotnet/core/tutorials/using-on-mac-vs-full-solution)
* [xUnit.net ile başlarken: .NET SDK komut satırı ile .NET Core kullanma](https://xunit.github.io/docs/getting-started-dotnet-core)
* [Moq](https://github.com/moq/moq4)
* [Moq Quickstart](https://github.com/Moq/moq4/wiki/Quickstart)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core, Razor Pages uygulamalarının birim testlerini destekler. Veri erişim katmanının (DAL) ve sayfa modellerinin testleri aşağıdakileri sağlamaya yardımcı olur:

* Razor Pages uygulamasının parçaları, uygulama nın yapımı sırasında bağımsız ve birlikte bir birim olarak çalışır.
* Sınıfların ve yöntemlerin sorumluluk kapsamı sınırlıdır.
* Uygulamanın nasıl olması gerektiğine ilişkin ek belgeler vardır.
* Kod güncelleştirmelerinin getirdiği hatalar olan gerilemeler, otomatik oluşturma ve dağıtım sırasında bulunur.

Bu konu, Razor Pages uygulamaları ve birim testleri hakkında temel bir anlayışa sahip olduğunuzu varsayar. Razor Pages uygulamalarını veya test kavramlarını bilmiyorsanız aşağıdaki konulara bakın:

* <xref:razor-pages/index>
* <xref:tutorials/razor-pages/razor-pages-start>
* [Dotnet testi ve xUnit kullanarak .NET Core'da C# testi](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/razor-pages-tests/samples) ( nasıl[indirilir](xref:index#how-to-download-a-sample))

Örnek proje iki uygulamadan oluşur:

| Uygulama         | Proje klasörü                     | Açıklama |
| ----------- | ---------------------------------- | ----------- |
| İleti uygulaması | *src/RazorPagesTestSample*         | Kullanıcının ileti eklemesine, bir iletiyi silmesine, tüm iletileri silmesine ve iletileri çözümlemesine izin verir (ileti başına ortalama sözcük sayısını bulun). |
| Test uygulaması    | *testler/RazorPagesTestSample.Tests* | İleti uygulamasının DAL ve Dizin sayfa modelini birim test etmek için kullanılır. |

Testler, Mac için [Visual Studio](/visualstudio/test/unit-test-your-code) veya Visual [Studio](/dotnet/core/tutorials/using-on-mac-vs-full-solution)gibi bir IDE'nin yerleşik test özellikleri kullanılarak çalıştırılabilir. Visual [Studio Code](https://code.visualstudio.com/) veya komut satırı kullanıyorsanız, *testler/RazorPagesTestSample.Tests* klasöründe aşağıdaki komut istemini uygulayın:

```dotnetcli
dotnet test
```

## <a name="message-app-organization"></a>İleti uygulaması organizasyonu

İleti uygulaması aşağıdaki özelliklere sahip bir Razor Pages mesaj sistemidir:

* Uygulamanın Dizin sayfası *(Pages/Index.cshtml* ve *Pages/Index.cshtml.css),* iletilerin eklenmesini, silinmesini ve çözümlemesini denetlemek için kullanıcı aracı ve sayfa modeli yöntemleri sağlar (ileti başına ortalama sözcük sayısını bulun).
* `Message` Bir ileti sınıfı *(Data/Message.cs)* tarafından iki `Id` özelliği `Text` olan açıklanır: (anahtar) ve (ileti). Özellik `Text` gereklidir ve 200 karakterle sınırlıdır.
* İletiler [Entity Framework'ün bellek veritabanı](/ef/core/providers/in-memory/)&#8224; kullanılarak depolanır.
* Uygulama, veritabanı bağlam sınıfında bir `AppDbContext` DAL içerir, (*Veri/AppDbContext.cs*). DAL yöntemleri, `virtual`testlerde kullanım yöntemlerinin alay etmesine olanak tanıyan işaretlenmiştir.
* Uygulama nın başlatılmasında veritabanı boşsa, ileti deposu üç iletiyle açılır. Bu *tohumlu iletiler* testlerde de kullanılır.

&#8224;EF konusu, [InMemory ile Test](/ef/core/miscellaneous/testing/in-memory), MSTest ile testler için bellek veritabanı nasıl kullanılacağını açıklar. Bu konu [xUnit](https://xunit.github.io/) test çerçevesini kullanır. Farklı test çerçeveleri arasındaki test kavramları ve test uygulamaları benzerdir, ancak aynı değildir.

Örnek uygulama depo deseni kullanmasa ve [Çalışma Birimi (UoW) deseninin](https://martinfowler.com/eaaCatalog/unitOfWork.html)etkili bir örneği olmasa da, Razor Pages bu geliştirme modellerini destekler. Daha fazla bilgi için bkz: Altyapı <xref:mvc/controllers/testing> [kalıcılık katmanını tasarlama](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) ve (örnek depo deseni uygular).

## <a name="test-app-organization"></a>Test uygulaması organizasyonu

Test *uygulaması, test/RazorPagesTestSample.Tests* klasöründe bir konsol uygulamasıdır.

| Test uygulaması klasörü | Açıklama |
| --------------- | ----------- |
| *ÜniteTestleri*     | <ul><li>*DataAccessLayerTest.cs* DAL için birim testleri içerir.</li><li>*IndexPageTests.cs* Dizin sayfası modeliiçin birim testleri içerir.</li></ul> |
| *Yardımcı Programlar*     | Veritabanının `TestDbContextOptions` her test için temel durumuna sıfırlanır, böylece her DAL birim testi için yeni veritabanı bağlam seçenekleri oluşturmak için kullanılan yöntemi içerir. |

Test çerçevesi [xUnit'tir.](https://xunit.github.io/) Nesne alay çerçeve [Moq](https://github.com/moq/moq4)olduğunu .

## <a name="unit-tests-of-the-data-access-layer-dal"></a>Veri erişim katmanının birim testleri (DAL)

İleti uygulaması `AppDbContext` sınıfında yer alan dört yöntem *(src/RazorPagesTestSample/Data/AppDbContext.cs)* içeren bir DAL'ye sahiptir. Her yöntemin test uygulamasında bir veya iki birim testi vardır.

| DAL yöntemi               | İşlev                                                                   |
| ------------------------ | -------------------------------------------------------------------------- |
| `GetMessagesAsync`       | Özellik tarafından sıralanan veritabanından bir alır. `List<Message>` `Text` |
| `AddMessageAsync`        | Veritabanına `Message` bir ekler.                                          |
| `DeleteAllMessagesAsync` | Veritabanından `Message` tüm girişleri siler.                           |
| `DeleteMessageAsync`     | Veritabanından bir `Message` tek i `Id`ler tarafından siler.                      |

DAL'nin birim <xref:Microsoft.EntityFrameworkCore.DbContextOptions> testleri, her `AppDbContext` test için yeni bir test oluştururken gerektirir. Her test `DbContextOptions` için oluşturmak için bir <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder>yaklaşım kullanmaktır:

```csharp
var optionsBuilder = new DbContextOptionsBuilder<AppDbContext>()
    .UseInMemoryDatabase("InMemoryDb");

using (var db = new AppDbContext(optionsBuilder.Options))
{
    // Use the db here in the unit test.
}
```

Bu yaklaşımla ilgili sorun, her testin veritabanını önceki testin bıraktığı durumda almasıdır. Bu, birbirine müdahale etmeyen atomik birim testleri yazmaya çalışırken sorunlu olabilir. Her test `AppDbContext` için yeni bir veritabanı bağlamı `DbContextOptions` kullanmaya zorlamak için, yeni bir hizmet sağlayıcısını temel alan bir örnek kaynağı olun. Test uygulaması sınıf `Utilities` yöntemini `TestDbContextOptions` kullanarak bunu nasıl yapacağını gösterir *(testler/RazorPagesTestSample.Tests/Utilities/Utilities.cs):*

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs?name=snippet1)]

DAL `DbContextOptions` birim testlerinde kullanılması, her testin yeni bir veritabanı örneğiyle atomik olarak çalışmasını sağlar:

```csharp
using (var db = new AppDbContext(Utilities.TestDbContextOptions()))
{
    // Use the db here in the unit test.
}
```

Sınıftaki `DataAccessLayerTest` her test yöntemi *(UnitTests/DataAccessLayerTest.cs)* benzer bir Düzenleme-Hareket-Assert deseni izler:

1. Düzenleme: Veritabanı test için yapılandırılır ve/veya beklenen sonuç tanımlanır.
1. Hareket: Test yürütülür.
1. Assert: İddialar, test sonucunun başarılı olup olmadığını belirlemek için yapılır.

Örneğin, `DeleteMessageAsync` yöntem onun `Id` *(src/RazorPagesTestSample/Data/AppDbContext.cs)* tarafından tanımlanan tek bir iletiyi kaldırmaktan sorumludur:

[!code-csharp[](razor-pages-tests/samples/2.x/src/RazorPagesTestSample/Data/AppDbContext.cs?name=snippet4)]

Bu yöntem için iki test vardır. Bir test, ileti veritabanında bulunduğunda yöntemin bir iletiyi siler. Silme iletisi `Id` yoksa diğer yöntem veritabanının değişmediğini sınar. Yöntem `DeleteMessageAsync_MessageIsDeleted_WhenMessageIsFound` aşağıda gösterilmiştir:

[!code-csharp[](razor-pages-tests/samples_snapshot/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

İlk olarak, yöntem, Kanun adımına hazırlık yapıldığı Düzenleme adımını gerçekleştirir. Tohumlama mesajları elde edilir ve `seedMessages`. Tohumlama iletileri veritabanına kaydedilir. Bir `Id` of `1` ile ileti silinmesi için ayarlanır. `DeleteMessageAsync` Yöntem yürütüldüğünde, beklenen iletiler bir `Id` `1`' nin ''si olan iletiler dışında tüm iletilere sahip olmalıdır. `expectedMessages` Değişken bu beklenen sonucu temsil eder.

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

Yöntem davranır: `DeleteMessageAsync` Yöntem aşağıdakilerden `recId` geçerek `1`yürütülür:

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet2)]

Son olarak, yöntem `Messages` bağlamından elde eder ve `expectedMessages` bu iki sinin eşit olduğunu iddia etmekle karşılaştırır:

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet3)]

Bu ikisi `List<Message>` nin aynı olduğunu karşılaştırmak için:

* İletiler `Id`tarafından sıralanır.
* İleti çiftleri `Text` özellik üzerinde karşılaştırılır.

Benzer bir sınama yöntemi, `DeleteMessageAsync_NoMessageIsDeleted_WhenMessageIsNotFound` var olmayan bir iletiyi silmeye çalışma sonucunu denetler. Bu durumda, veritabanında beklenen iletiler `DeleteMessageAsync` yöntem yürütüldükten sonra gerçek iletilere eşit olmalıdır. Veritabanının içeriğinde değişiklik olmamalıdır:

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet4)]

## <a name="unit-tests-of-the-page-model-methods"></a>Sayfa modeli yöntemlerinin birim testleri

Başka bir birim testi kümesi, sayfa modeli yöntemlerinin testlerinden sorumludur. İleti uygulamasında, Dizin sayfası modelleri `IndexModel` *src/RazorPagesTestSample/Pages/Index.cshtml.cs*sınıfında bulunur.

| Sayfa modeli yöntemi | İşlev |
| ----------------- | -------- |
| `OnGetAsync` | `GetMessagesAsync` Yöntemi kullanarak UI için DAL'den iletileri alır. |
| `OnPostAddMessageAsync` | [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) geçerliyse, veritabanına ileti eklemek için çağırır. `AddMessageAsync` |
| `OnPostDeleteAllMessagesAsync` | Veritabanındaki tüm iletileri silmek için çağrılar. `DeleteAllMessagesAsync` |
| `OnPostDeleteMessageAsync` | Belirtilen `DeleteMessageAsync` ile bir iletiyi silmek için yürütülür. `Id` |
| `OnPostAnalyzeMessagesAsync` | Veritabanında bir veya daha fazla ileti varsa, ileti başına ortalama sözcük sayısını hesaplar. |

Sayfa modeli yöntemleri `IndexPageTests` sınıfta yedi test kullanılarak test edilir *(testler/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs).* Testler tanıdık Düzenle-İddia-Hareket modelini kullanır. Bu testler şu na odaklanır:

* [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) geçersiz olduğunda yöntemlerin doğru davranışı izp'lep'leyetmeme mi belirlendir.
* Yöntemleri onaylamak doğru <xref:Microsoft.AspNetCore.Mvc.IActionResult>üretir.
* Özellik değeri atamalarının doğru yapıldığını denetleme.

Bu test grubu genellikle, bir sayfa modeli yönteminin yürütüldüğü Act adımı için beklenen verileri üretmek için DAL'Nin yöntemleriyle alay konusu olur. Örneğin, `GetMessagesAsync` çıkış üretmek `AppDbContext` için yöntem alay edilir. Bir sayfa modeli yöntemi bu yöntemi yürütürse, sahte sonucu döndürür. Veriler veritabanından gelmiyor. Bu, sayfa modeli testlerinde DAL'yi kullanmak için öngörülebilir ve güvenilir test koşulları oluşturur.

Test, `OnGetAsync_PopulatesThePageModel_WithAListOfMessages` yöntemin `GetMessagesAsync` sayfa modeli için nasıl alay edildiğini gösterir:

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet1&highlight=3-4)]

`OnGetAsync` Yöntem Act adımında yürütüldüğünde, sayfa modelinin `GetMessagesAsync` yöntemini çağırır.

Ünite test Act adım (*testler/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs):*

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet2)]

`IndexPage`sayfa modeli `OnGetAsync` nin yöntemi (*src/RazorPagesTestSample/Pages/Index.cshtml.cs):*

[!code-csharp[](razor-pages-tests/samples/2.x/src/RazorPagesTestSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3)]

DAL'deki `GetMessagesAsync` yöntem, bu yöntem çağrısının sonucunu döndürmez. Yöntemin alay edilen sürümü sonucu döndürür.

`Assert` Adımda, gerçek iletiler`actualMessages`( ) sayfa `Messages` modelinin özelliğinden atanır. İletiler atandığında bir tür denetimi de gerçekleştirilir. Beklenen ve gerçek iletiler özellikleriyle `Text` karşılaştırılır. Test, iki `List<Message>` örneğin aynı iletileri içerdiğini ileri sürmelidir.

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet3)]

Bu gruptaki <xref:Microsoft.AspNetCore.Http.DefaultHttpContext> <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary>diğer testler, bir , a <xref:Microsoft.AspNetCore.Mvc.ActionContext> `PageContext` `ViewDataDictionary`, ve bir `PageContext`. Bunlar testlerin yürütülmesinde yararlıdır. Örneğin, ileti uygulaması `ModelState` yürütüldüğünde <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*> geçerli <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult> bir döndürülünden `OnPostAddMessageAsync` denetlemede bir hata belirler:

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet4&highlight=11,26,29,32)]

## <a name="additional-resources"></a>Ek kaynaklar

* [Dotnet testi ve xUnit kullanarak .NET Core'da C# testi](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* <xref:mvc/controllers/testing>
* [Birim Kodutest](/visualstudio/test/unit-test-your-code) (Visual Studio)
* <xref:test/integration-tests>
* [xUnit.net](https://xunit.github.io/)
* [Mac için Visual Studio kullanarak macOS’ta eksiksiz bir .NET Core çözümü derleme](/dotnet/core/tutorials/using-on-mac-vs-full-solution)
* [xUnit.net ile başlarken: .NET SDK komut satırı ile .NET Core kullanma](https://xunit.github.io/docs/getting-started-dotnet-core)
* [Moq](https://github.com/moq/moq4)
* [Moq Quickstart](https://github.com/Moq/moq4/wiki/Quickstart)

::: moniker-end
