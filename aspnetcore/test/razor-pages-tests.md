---
title: Razor ASP.NET Core birim testleri sayfa testleri
author: rick-anderson
description: Sayfalar uygulamaları için birim testleri oluşturmayı öğrenin Razor .
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 7/22/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: test/razor-pages-tests
ms.openlocfilehash: 2486eb8c9fd0fc33ea77b0fedd99795218d7f4ca
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058044"
---
# <a name="no-locrazor-pages-unit-tests-in-aspnet-core"></a>Razor ASP.NET Core birim testleri sayfa testleri

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core, sayfaların uygulamalarının birim testlerini destekler Razor . Veri erişim katmanı (DAL) ve sayfa modellerinin testleri şunları sağlamaya yardımcı olur:

* Bir Razor Sayfalar uygulamasının parçaları, uygulama oluşturma sırasında bağımsız olarak ve bir birim olarak birlikte çalışır.
* Sınıfların ve yöntemlerin sınırlı sorumluluk kapsamları vardır.
* Uygulamanın nasıl davranması ile ilgili ek belgeler vardır.
* Kod güncelleştirmeleri tarafından ilgili hatalar olan gerileme, otomatik derleme ve dağıtım sırasında bulunur.

Bu konu başlığı altında, Razor uygulamalar ve birim testleri için temel olarak anlaşıldığınız varsayılmaktadır. RazorSayfalarla ilgili uygulamalar veya test kavramları hakkında bilgi sahibi değilseniz aşağıdaki konulara bakın:

* <xref:razor-pages/index>
* <xref:tutorials/razor-pages/razor-pages-start>
* [DotNet test ve xUnit kullanarak .NET Core 'Da birim testi C#](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/razor-pages-tests/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

Örnek proje iki uygulamalardan oluşur:

| Uygulama         | Proje klasörü                     | Açıklama |
| ----------- | ---------------------------------- | ----------- |
| İleti uygulaması | *src/ Razor pagestestsample*         | Kullanıcının ileti eklemesine, bir ileti silmesine, tüm iletileri silmesine ve iletileri çözümlemesine (ileti başına ortalama sözcük sayısını bulur) izin verir. |
| Test uygulaması    | *testler/ Razor pagestestsample. testler* | İleti uygulamasının DAL ve Dizin sayfa modelini test etmek için kullanılır. |

Testler, [Visual Studio](/visualstudio/test/unit-test-your-code) veya [Mac IÇIN VISUAL STUDIO](/dotnet/core/tutorials/using-on-mac-vs-full-solution)gibi bir IDE 'nin yerleşik test özellikleri kullanılarak çalıştırılabilir. [Visual Studio Code](https://code.visualstudio.com/) veya komut satırı kullanıyorsanız, *testler/ Razor pagestestsample. Tests* klasöründeki bir komut isteminde aşağıdaki komutu yürütün:

```dotnetcli
dotnet test
```

## <a name="message-app-organization"></a>İleti uygulama organizasyonu

İleti uygulaması, Razor aşağıdaki özelliklere sahip bir sayfalar ileti sistemidir:

* Uygulamanın ( *Pages/Index. cshtml* ve *Pages/index. cshtml. cs* ) dizin sayfası, iletilerin eklenmesi, silinmesini ve ANALIZINI denetlemek için bir UI ve sayfa modeli yöntemleri sağlar (ileti başına ortalama sözcük sayısını bulur).
* Bir ileti, `Message` sınıfı ( *Data/Message. cs* ) ile iki özelliği olan ( `Id` anahtar) ve `Text` (ileti) açıklanmaktadır. `Text`Özelliği gereklidir ve 200 karakterle sınırlıdır.
* İletiler, [Entity Framework bellek içi veritabanı](/ef/core/providers/in-memory/)&#8224; kullanılarak depolanır.
* Uygulama, veritabanı bağlamı sınıfında `AppDbContext` ( *Data/AppDbContext. cs* ) bir dal içerir. DAL Yöntemleri işaretlenir `virtual` ve bu yöntemler, testlerde kullanım için izin verir.
* Veritabanı uygulama başlangıcında boşsa, ileti deposu üç iletiyle başlatılır. Bu *sağlanan iletiler* , testlerde de kullanılır.

EF konusunda, [InMemory Ile Test](/ef/core/miscellaneous/testing/in-memory)&#8224;, MSTest ile testler için bellek içi bir veritabanının nasıl kullanılacağını açıklar. Bu konu [xUnit](https://xunit.github.io/) test çerçevesini kullanır. Farklı test çerçeveleri genelinde test kavramları ve test uygulamaları benzerdir ancak aynı değildir.

Örnek uygulama depo desenini kullanmaz ve [Iş birimi (UoW) düzeninin](https://martinfowler.com/eaaCatalog/unitOfWork.html)etkin bir örneği olmamasına karşın, Razor Sayfalar bu geliştirme düzenlerini destekler. Daha fazla bilgi için bkz. [altyapı Kalıcılık katmanını tasarlama](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) ve <xref:mvc/controllers/testing> (örnek depo modelini uygular).

## <a name="test-app-organization"></a>Test uygulaması kuruluşu

Test uygulaması, *testler/ Razor pagestestsample. Tests* klasörünün içindeki bir konsol uygulamasıdır.

| Test uygulaması klasörü | Açıklama |
| --------------- | ----------- |
| *UnitTests*     | <ul><li>*DataAccessLayerTest.cs* , dal için birim testlerini içerir.</li><li>*IndexPageTests.cs* , Dizin sayfası modelinin birim testlerini içerir.</li></ul> |
| *Yardımcı Programlar*     | Her bir `TestDbContextOptions` dal birim testi için yeni veritabanı bağlamı seçenekleri oluşturmak için kullanılan yöntemi içerir, böylece veritabanı her test için kendi temel koşuluna sıfırlanır. |

Test çerçevesi [xUnit](https://xunit.github.io/)' dir. Nesne sahte işlem çerçevesi [moq](https://github.com/moq/moq4)olur.

## <a name="unit-tests-of-the-data-access-layer-dal"></a>Veri erişim katmanının birim testleri (DAL)

İleti uygulamasında, sınıfında dört yöntem bulunan bir DAL vardır `AppDbContext` ( *src/ Razor Pagestestsample/Data/appdbcontext. cs* ). Her yöntemin test uygulamasında bir veya iki birim testi vardır.

| DAL yöntemi               | İşlev                                                                   |
| ------------------------ | -------------------------------------------------------------------------- |
| `GetMessagesAsync`       | `List<Message>`Özelliği tarafından sıralanan veritabanından bir alır `Text` . |
| `AddMessageAsync`        | Veritabanına bir ekler `Message` .                                          |
| `DeleteAllMessagesAsync` | Tüm `Message` girdileri veritabanından siler.                           |
| `DeleteMessageAsync`     | Veritabanından tek tek siler `Message` `Id` .                      |

DAL birim testleri <xref:Microsoft.EntityFrameworkCore.DbContextOptions> her bir test için yeni bir oluşturma `AppDbContext` için gereklidir. Her test için öğesini oluşturmaya yönelik bir yaklaşım, şunu `DbContextOptions` kullanmaktır <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder> :

```csharp
var optionsBuilder = new DbContextOptionsBuilder<AppDbContext>()
    .UseInMemoryDatabase("InMemoryDb");

using (var db = new AppDbContext(optionsBuilder.Options))
{
    // Use the db here in the unit test.
}
```

Bu yaklaşımla ilgili sorun, her testin, önceki testin bulunduğu herhangi bir durumda veritabanını aldığından emin olur. Bu, birbirleriyle karışmaz atomik birim testleri yazmaya çalışırken sorunlu olabilir. `AppDbContext`Her test için yeni bir veritabanı bağlamı kullanmaya zorlamak için, `DbContextOptions` Yeni bir hizmet sağlayıcısına dayalı bir örnek sağlayın. Test uygulaması, sınıfının sınıf yöntemi kullanılarak nasıl yapılacağını gösterir `Utilities` `TestDbContextOptions` ( *testler/ Razor Pagestestsample. testler/Utilities/Utilities. cs* ):

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs?name=snippet1)]

`DbContextOptions`Dal birim testlerinde kullanılması, her testin yeni bir veritabanı örneğiyle otomatik olarak çalıştırılmasına izin verir:

```csharp
using (var db = new AppDbContext(Utilities.TestDbContextOptions()))
{
    // Use the db here in the unit test.
}
```

Sınıftaki her bir test yöntemi `DataAccessLayerTest` ( *unittests/DataAccessLayerTest. cs* ) benzer bir düzenleme-işlem onaylama düzeni izler:

1. Düzenle: veritabanı test için yapılandırılmış ve/veya beklenen sonuç tanımlandı.
1. Davran: test yürütülür.
1. Onaylama: test sonucunun başarılı olup olmadığını belirleme onayları yapılır.

Örneğin, `DeleteMessageAsync` yöntemi `Id` ( *src/ Razor Pagestestsample/Data/appdbcontext. cs* ) tarafından tanımlanan tek bir iletinin kaldırılmasından sorumludur:

[!code-csharp[](razor-pages-tests/samples/3.x/src/RazorPagesTestSample/Data/AppDbContext.cs?name=snippet4)]

Bu yöntem için iki test vardır. Bir test, bir ileti veritabanında olduğunda yöntemin bir iletiyi sildiğini denetler. Diğer yöntem, silme iletisi yoksa veritabanının değişmediğini sınar `Id` . `DeleteMessageAsync_MessageIsDeleted_WhenMessageIsFound`Yöntemi aşağıda gösterilmiştir:

[!code-csharp[](razor-pages-tests/samples_snapshot/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

İlk olarak, yöntemi, hareket adımının hazırlanması gereken düzenleme adımını gerçekleştirir. Dengeli dağıtım iletileri ' de alınır ve tutulur `seedMessages` . Dengeli dağıtım iletileri veritabanına kaydedilir. İçeren ileti `Id` `1` silinmek üzere ayarlanmış. `DeleteMessageAsync`Yöntemi yürütüldüğünde, beklenen iletilerde, ' ın dışında bir ileti olmalıdır `Id` `1` . `expectedMessages`Değişken, beklenen bu sonucu temsil eder.

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

Yöntemi şu şekilde davranır: `DeleteMessageAsync` yöntemi ' ın içinde geçirme yürütülür `recId` `1` :

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet2)]

Son olarak, yöntemi bağlamını edinir `Messages` ve `expectedMessages` iki eşit olan asserile karşılaştırır:

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet3)]

İki ikisinin de aynı olduğunu karşılaştırmak için `List<Message>` :

* İletiler tarafından sıralanır `Id` .
* İleti çiftleri özelliği ile karşılaştırılır `Text` .

Benzer bir test yöntemi, `DeleteMessageAsync_NoMessageIsDeleted_WhenMessageIsNotFound` mevcut olmayan bir iletiyi silmeye çalışılması sonucunu denetler. Bu durumda, veritabanındaki beklenen iletiler, yöntem yürütüldükten sonra gerçek iletilere eşit olmalıdır `DeleteMessageAsync` . Veritabanının içeriğinde değişiklik olmamalıdır:

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet4)]

## <a name="unit-tests-of-the-page-model-methods"></a>Sayfa modeli yöntemlerinin birim testleri

Başka bir birim testi kümesi, sayfa modeli yöntemlerinin sınamalarından sorumludur. İleti uygulamasında Dizin sayfası modelleri `IndexModel` *src/ Razor pagestestsample/Pages/Index. cshtml. cs* içindeki sınıfta bulunur.

| Sayfa modeli yöntemi | İşlev |
| ----------------- | -------- |
| `OnGetAsync` | Yöntemini kullanarak, Kullanıcı arabirimi için DAL öğesinden gelen iletileri alır `GetMessagesAsync` . |
| `OnPostAddMessageAsync` | [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) geçerliyse, `AddMessageAsync` veritabanına bir ileti eklemek için çağırır. |
| `OnPostDeleteAllMessagesAsync` | `DeleteAllMessagesAsync`Veritabanındaki tüm iletileri silmek için çağırır. |
| `OnPostDeleteMessageAsync` | `DeleteMessageAsync`Belirtilen bir iletiyi silmek için yürütülür `Id` . |
| `OnPostAnalyzeMessagesAsync` | Veritabanında bir veya daha fazla ileti varsa, ileti başına ortalama sözcük sayısını hesaplar. |

Sayfa modeli yöntemleri, sınıfında yedi test kullanılarak test edilir `IndexPageTests` ( *testler/ Razor Pagestestsample. testler/unittests/ındexpagetests. cs* ). Testler tanıdık düzenleme-onaylama-Işlem düzeni kullanır. Bu sınamalar üzerinde odaklanılmıştır:

* [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) geçersiz olduğunda yöntemlerin doğru davranışı izleyip izlemediğini belirleme.
* Yöntemlerin doğru bir şekilde ürettiği doğrulanıyor <xref:Microsoft.AspNetCore.Mvc.IActionResult> .
* Özellik değeri atamalarının doğru şekilde yapıldığından emin olup olmadığı denetleniyor.

Bu test grubu genellikle, bir sayfa modeli yönteminin yürütüldüğü Işlem adımı için beklenen verileri oluşturmak üzere DAL yöntemlerini oluşturuyor. Örneğin, `GetMessagesAsync` öğesinin yöntemi `AppDbContext` Çıkış oluşturmak için kullanılır. Bir sayfa modeli yöntemi bu yöntemi yürüttüğünde, sahte sonuç döndürür. Veriler veritabanından gelmiyor. Bu, sayfa modeli testlerinde DAL kullanımı için öngörülebilir, güvenilir bir test koşulları oluşturur.

`OnGetAsync_PopulatesThePageModel_WithAListOfMessages`Test, `GetMessagesAsync` yöntemin sayfa modeli için nasıl kullanılacağını gösterir:

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet1&highlight=3-4)]

Yöntem, `OnGetAsync` işlem adımında yürütüldüğünde, sayfa modelinin `GetMessagesAsync` yöntemini çağırır.

Birim testi Işlem adımı ( *testler/ Razor pagestestsample. testler/unittests/ındexpagetests. cs* ):

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet2)]

`IndexPage` sayfa modelinin `OnGetAsync` Yöntemi ( *src/ Razor pagestestsample/Pages/Index. cshtml. cs* ):

[!code-csharp[](razor-pages-tests/samples/3.x/src/RazorPagesTestSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3)]

`GetMessagesAsync`Dal içindeki yöntemi bu yöntem çağrısının sonucunu döndürmez. Metodun moclenmiş sürümü sonucu döndürür.

`Assert`Adımda, gerçek iletiler ( `actualMessages` ) `Messages` sayfa modelinin özelliğinden atanır. İletiler atandığında bir tür denetimi de gerçekleştirilir. Beklenen ve gerçek iletiler `Text` özellikleriyle karşılaştırılır. Test, iki `List<Message>` örneğinin aynı iletileri içerdiğini onaylar.

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet3)]

Bu gruptaki diğer testler,,, ve ' ı <xref:Microsoft.AspNetCore.Http.DefaultHttpContext> <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary> <xref:Microsoft.AspNetCore.Mvc.ActionContext> kurmak için, `PageContext` `ViewDataDictionary` , bir içeren sayfa modeli nesneleri oluşturur `PageContext` . Bunlar, testleri yürütmek için faydalıdır. Örneğin, ileti uygulaması `ModelState` <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*> yürütüldüğünde geçerli bir <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult> döndürülüp döndürüldüğünden emin olmak için bir hata oluşturur `OnPostAddMessageAsync` :

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

ASP.NET Core, sayfaların uygulamalarının birim testlerini destekler Razor . Veri erişim katmanı (DAL) ve sayfa modellerinin testleri şunları sağlamaya yardımcı olur:

* Bir Razor Sayfalar uygulamasının parçaları, uygulama oluşturma sırasında bağımsız olarak ve bir birim olarak birlikte çalışır.
* Sınıfların ve yöntemlerin sınırlı sorumluluk kapsamları vardır.
* Uygulamanın nasıl davranması ile ilgili ek belgeler vardır.
* Kod güncelleştirmeleri tarafından ilgili hatalar olan gerileme, otomatik derleme ve dağıtım sırasında bulunur.

Bu konu başlığı altında, Razor uygulamalar ve birim testleri için temel olarak anlaşıldığınız varsayılmaktadır. RazorSayfalarla ilgili uygulamalar veya test kavramları hakkında bilgi sahibi değilseniz aşağıdaki konulara bakın:

* <xref:razor-pages/index>
* <xref:tutorials/razor-pages/razor-pages-start>
* [DotNet test ve xUnit kullanarak .NET Core 'Da birim testi C#](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/razor-pages-tests/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

Örnek proje iki uygulamalardan oluşur:

| Uygulama         | Proje klasörü                     | Açıklama |
| ----------- | ---------------------------------- | ----------- |
| İleti uygulaması | *src/ Razor pagestestsample*         | Kullanıcının ileti eklemesine, bir ileti silmesine, tüm iletileri silmesine ve iletileri çözümlemesine (ileti başına ortalama sözcük sayısını bulur) izin verir. |
| Test uygulaması    | *testler/ Razor pagestestsample. testler* | İleti uygulamasının DAL ve Dizin sayfa modelini test etmek için kullanılır. |

Testler, [Visual Studio](/visualstudio/test/unit-test-your-code) veya [Mac IÇIN VISUAL STUDIO](/dotnet/core/tutorials/using-on-mac-vs-full-solution)gibi bir IDE 'nin yerleşik test özellikleri kullanılarak çalıştırılabilir. [Visual Studio Code](https://code.visualstudio.com/) veya komut satırı kullanıyorsanız, *testler/ Razor pagestestsample. Tests* klasöründeki bir komut isteminde aşağıdaki komutu yürütün:

```dotnetcli
dotnet test
```

## <a name="message-app-organization"></a>İleti uygulama organizasyonu

İleti uygulaması, Razor aşağıdaki özelliklere sahip bir sayfalar ileti sistemidir:

* Uygulamanın ( *Pages/Index. cshtml* ve *Pages/index. cshtml. cs* ) dizin sayfası, iletilerin eklenmesi, silinmesini ve ANALIZINI denetlemek için bir UI ve sayfa modeli yöntemleri sağlar (ileti başına ortalama sözcük sayısını bulur).
* Bir ileti, `Message` sınıfı ( *Data/Message. cs* ) ile iki özelliği olan ( `Id` anahtar) ve `Text` (ileti) açıklanmaktadır. `Text`Özelliği gereklidir ve 200 karakterle sınırlıdır.
* İletiler, [Entity Framework bellek içi veritabanı](/ef/core/providers/in-memory/)&#8224; kullanılarak depolanır.
* Uygulama, veritabanı bağlamı sınıfında `AppDbContext` ( *Data/AppDbContext. cs* ) bir dal içerir. DAL Yöntemleri işaretlenir `virtual` ve bu yöntemler, testlerde kullanım için izin verir.
* Veritabanı uygulama başlangıcında boşsa, ileti deposu üç iletiyle başlatılır. Bu *sağlanan iletiler* , testlerde de kullanılır.

EF konusunda, [InMemory Ile Test](/ef/core/miscellaneous/testing/in-memory)&#8224;, MSTest ile testler için bellek içi bir veritabanının nasıl kullanılacağını açıklar. Bu konu [xUnit](https://xunit.github.io/) test çerçevesini kullanır. Farklı test çerçeveleri genelinde test kavramları ve test uygulamaları benzerdir ancak aynı değildir.

Örnek uygulama depo desenini kullanmaz ve [Iş birimi (UoW) düzeninin](https://martinfowler.com/eaaCatalog/unitOfWork.html)etkin bir örneği olmamasına karşın, Razor Sayfalar bu geliştirme düzenlerini destekler. Daha fazla bilgi için bkz. [altyapı Kalıcılık katmanını tasarlama](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) ve <xref:mvc/controllers/testing> (örnek depo modelini uygular).

## <a name="test-app-organization"></a>Test uygulaması kuruluşu

Test uygulaması, *testler/ Razor pagestestsample. Tests* klasörünün içindeki bir konsol uygulamasıdır.

| Test uygulaması klasörü | Açıklama |
| --------------- | ----------- |
| *UnitTests*     | <ul><li>*DataAccessLayerTest.cs* , dal için birim testlerini içerir.</li><li>*IndexPageTests.cs* , Dizin sayfası modelinin birim testlerini içerir.</li></ul> |
| *Yardımcı Programlar*     | Her bir `TestDbContextOptions` dal birim testi için yeni veritabanı bağlamı seçenekleri oluşturmak için kullanılan yöntemi içerir, böylece veritabanı her test için kendi temel koşuluna sıfırlanır. |

Test çerçevesi [xUnit](https://xunit.github.io/)' dir. Nesne sahte işlem çerçevesi [moq](https://github.com/moq/moq4)olur.

## <a name="unit-tests-of-the-data-access-layer-dal"></a>Veri erişim katmanının birim testleri (DAL)

İleti uygulamasında, sınıfında dört yöntem bulunan bir DAL vardır `AppDbContext` ( *src/ Razor Pagestestsample/Data/appdbcontext. cs* ). Her yöntemin test uygulamasında bir veya iki birim testi vardır.

| DAL yöntemi               | İşlev                                                                   |
| ------------------------ | -------------------------------------------------------------------------- |
| `GetMessagesAsync`       | `List<Message>`Özelliği tarafından sıralanan veritabanından bir alır `Text` . |
| `AddMessageAsync`        | Veritabanına bir ekler `Message` .                                          |
| `DeleteAllMessagesAsync` | Tüm `Message` girdileri veritabanından siler.                           |
| `DeleteMessageAsync`     | Veritabanından tek tek siler `Message` `Id` .                      |

DAL birim testleri <xref:Microsoft.EntityFrameworkCore.DbContextOptions> her bir test için yeni bir oluşturma `AppDbContext` için gereklidir. Her test için öğesini oluşturmaya yönelik bir yaklaşım, şunu `DbContextOptions` kullanmaktır <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder> :

```csharp
var optionsBuilder = new DbContextOptionsBuilder<AppDbContext>()
    .UseInMemoryDatabase("InMemoryDb");

using (var db = new AppDbContext(optionsBuilder.Options))
{
    // Use the db here in the unit test.
}
```

Bu yaklaşımla ilgili sorun, her testin, önceki testin bulunduğu herhangi bir durumda veritabanını aldığından emin olur. Bu, birbirleriyle karışmaz atomik birim testleri yazmaya çalışırken sorunlu olabilir. `AppDbContext`Her test için yeni bir veritabanı bağlamı kullanmaya zorlamak için, `DbContextOptions` Yeni bir hizmet sağlayıcısına dayalı bir örnek sağlayın. Test uygulaması, sınıfının sınıf yöntemi kullanılarak nasıl yapılacağını gösterir `Utilities` `TestDbContextOptions` ( *testler/ Razor Pagestestsample. testler/Utilities/Utilities. cs* ):

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs?name=snippet1)]

`DbContextOptions`Dal birim testlerinde kullanılması, her testin yeni bir veritabanı örneğiyle otomatik olarak çalıştırılmasına izin verir:

```csharp
using (var db = new AppDbContext(Utilities.TestDbContextOptions()))
{
    // Use the db here in the unit test.
}
```

Sınıftaki her bir test yöntemi `DataAccessLayerTest` ( *unittests/DataAccessLayerTest. cs* ) benzer bir düzenleme-işlem onaylama düzeni izler:

1. Düzenle: veritabanı test için yapılandırılmış ve/veya beklenen sonuç tanımlandı.
1. Davran: test yürütülür.
1. Onaylama: test sonucunun başarılı olup olmadığını belirleme onayları yapılır.

Örneğin, `DeleteMessageAsync` yöntemi `Id` ( *src/ Razor Pagestestsample/Data/appdbcontext. cs* ) tarafından tanımlanan tek bir iletinin kaldırılmasından sorumludur:

[!code-csharp[](razor-pages-tests/samples/2.x/src/RazorPagesTestSample/Data/AppDbContext.cs?name=snippet4)]

Bu yöntem için iki test vardır. Bir test, bir ileti veritabanında olduğunda yöntemin bir iletiyi sildiğini denetler. Diğer yöntem, silme iletisi yoksa veritabanının değişmediğini sınar `Id` . `DeleteMessageAsync_MessageIsDeleted_WhenMessageIsFound`Yöntemi aşağıda gösterilmiştir:

[!code-csharp[](razor-pages-tests/samples_snapshot/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

İlk olarak, yöntemi, hareket adımının hazırlanması gereken düzenleme adımını gerçekleştirir. Dengeli dağıtım iletileri ' de alınır ve tutulur `seedMessages` . Dengeli dağıtım iletileri veritabanına kaydedilir. İçeren ileti `Id` `1` silinmek üzere ayarlanmış. `DeleteMessageAsync`Yöntemi yürütüldüğünde, beklenen iletilerde, ' ın dışında bir ileti olmalıdır `Id` `1` . `expectedMessages`Değişken, beklenen bu sonucu temsil eder.

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

Yöntemi şu şekilde davranır: `DeleteMessageAsync` yöntemi ' ın içinde geçirme yürütülür `recId` `1` :

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet2)]

Son olarak, yöntemi bağlamını edinir `Messages` ve `expectedMessages` iki eşit olan asserile karşılaştırır:

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet3)]

İki ikisinin de aynı olduğunu karşılaştırmak için `List<Message>` :

* İletiler tarafından sıralanır `Id` .
* İleti çiftleri özelliği ile karşılaştırılır `Text` .

Benzer bir test yöntemi, `DeleteMessageAsync_NoMessageIsDeleted_WhenMessageIsNotFound` mevcut olmayan bir iletiyi silmeye çalışılması sonucunu denetler. Bu durumda, veritabanındaki beklenen iletiler, yöntem yürütüldükten sonra gerçek iletilere eşit olmalıdır `DeleteMessageAsync` . Veritabanının içeriğinde değişiklik olmamalıdır:

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet4)]

## <a name="unit-tests-of-the-page-model-methods"></a>Sayfa modeli yöntemlerinin birim testleri

Başka bir birim testi kümesi, sayfa modeli yöntemlerinin sınamalarından sorumludur. İleti uygulamasında Dizin sayfası modelleri `IndexModel` *src/ Razor pagestestsample/Pages/Index. cshtml. cs* içindeki sınıfta bulunur.

| Sayfa modeli yöntemi | İşlev |
| ----------------- | -------- |
| `OnGetAsync` | Yöntemini kullanarak, Kullanıcı arabirimi için DAL öğesinden gelen iletileri alır `GetMessagesAsync` . |
| `OnPostAddMessageAsync` | [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) geçerliyse, `AddMessageAsync` veritabanına bir ileti eklemek için çağırır. |
| `OnPostDeleteAllMessagesAsync` | `DeleteAllMessagesAsync`Veritabanındaki tüm iletileri silmek için çağırır. |
| `OnPostDeleteMessageAsync` | `DeleteMessageAsync`Belirtilen bir iletiyi silmek için yürütülür `Id` . |
| `OnPostAnalyzeMessagesAsync` | Veritabanında bir veya daha fazla ileti varsa, ileti başına ortalama sözcük sayısını hesaplar. |

Sayfa modeli yöntemleri, sınıfında yedi test kullanılarak test edilir `IndexPageTests` ( *testler/ Razor Pagestestsample. testler/unittests/ındexpagetests. cs* ). Testler tanıdık düzenleme-onaylama-Işlem düzeni kullanır. Bu sınamalar üzerinde odaklanılmıştır:

* [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) geçersiz olduğunda yöntemlerin doğru davranışı izleyip izlemediğini belirleme.
* Yöntemlerin doğru bir şekilde ürettiği doğrulanıyor <xref:Microsoft.AspNetCore.Mvc.IActionResult> .
* Özellik değeri atamalarının doğru şekilde yapıldığından emin olup olmadığı denetleniyor.

Bu test grubu genellikle, bir sayfa modeli yönteminin yürütüldüğü Işlem adımı için beklenen verileri oluşturmak üzere DAL yöntemlerini oluşturuyor. Örneğin, `GetMessagesAsync` öğesinin yöntemi `AppDbContext` Çıkış oluşturmak için kullanılır. Bir sayfa modeli yöntemi bu yöntemi yürüttüğünde, sahte sonuç döndürür. Veriler veritabanından gelmiyor. Bu, sayfa modeli testlerinde DAL kullanımı için öngörülebilir, güvenilir bir test koşulları oluşturur.

`OnGetAsync_PopulatesThePageModel_WithAListOfMessages`Test, `GetMessagesAsync` yöntemin sayfa modeli için nasıl kullanılacağını gösterir:

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet1&highlight=3-4)]

Yöntem, `OnGetAsync` işlem adımında yürütüldüğünde, sayfa modelinin `GetMessagesAsync` yöntemini çağırır.

Birim testi Işlem adımı ( *testler/ Razor pagestestsample. testler/unittests/ındexpagetests. cs* ):

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet2)]

`IndexPage` sayfa modelinin `OnGetAsync` Yöntemi ( *src/ Razor pagestestsample/Pages/Index. cshtml. cs* ):

[!code-csharp[](razor-pages-tests/samples/2.x/src/RazorPagesTestSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3)]

`GetMessagesAsync`Dal içindeki yöntemi bu yöntem çağrısının sonucunu döndürmez. Metodun moclenmiş sürümü sonucu döndürür.

`Assert`Adımda, gerçek iletiler ( `actualMessages` ) `Messages` sayfa modelinin özelliğinden atanır. İletiler atandığında bir tür denetimi de gerçekleştirilir. Beklenen ve gerçek iletiler `Text` özellikleriyle karşılaştırılır. Test, iki `List<Message>` örneğinin aynı iletileri içerdiğini onaylar.

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet3)]

Bu gruptaki diğer testler,,, ve ' ı <xref:Microsoft.AspNetCore.Http.DefaultHttpContext> <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary> <xref:Microsoft.AspNetCore.Mvc.ActionContext> kurmak için, `PageContext` `ViewDataDictionary` , bir içeren sayfa modeli nesneleri oluşturur `PageContext` . Bunlar, testleri yürütmek için faydalıdır. Örneğin, ileti uygulaması `ModelState` <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*> yürütüldüğünde geçerli bir <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult> döndürülüp döndürüldüğünden emin olmak için bir hata oluşturur `OnPostAddMessageAsync` :

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
* [JustMockLite](https://github.com/telerik/JustMockLite): .NET geliştiricileri için bir sahte işlem çerçevesi. ( *Microsoft tarafından korunmaz veya desteklenmez.* )

::: moniker-end
