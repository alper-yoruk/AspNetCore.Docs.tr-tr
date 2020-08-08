---
title: ASP.NET Core tümleştirme testleri
author: rick-anderson
description: Tümleştirme testlerinin, bir uygulamanın bileşenlerinin, veritabanı, dosya sistemi ve ağ dahil olmak üzere altyapı düzeyinde doğru şekilde çalışmasını nasıl sağladığını öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/14/2020
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
uid: test/integration-tests
ms.openlocfilehash: 508c2d2cb668f5dbf416d341c1d9a966f9d16fd4
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021048"
---
# <a name="integration-tests-in-aspnet-core"></a>ASP.NET Core tümleştirme testleri

, [Javier Calvarro Nelson](https://github.com/javiercn), [Steve Smith](https://ardalis.com/)ve [Jos van der til](https://jvandertil.nl)

::: moniker range=">= aspnetcore-3.0"

Tümleştirme sınamaları, uygulamanın bileşenlerinin veritabanı, dosya sistemi ve ağ gibi destekleyici altyapısını içeren bir düzeyde doğru şekilde çalışmasını güvence altına alır. ASP.NET Core, test Web ana bilgisayarı ve bellek içi test sunucusu olan bir birim testi çerçevesini kullanarak tümleştirme testlerini destekler.

Bu konuda, birim testlerinin temel bir şekilde anlaşıldığı varsayılır. Test kavramları hakkında bilgi sahibi değilseniz, [.NET Core 'Da birim testine ve .NET Standard](/dotnet/core/testing/) konusuna ve bağlı içeriğine bakın.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

Örnek uygulama bir Razor Sayfalar uygulamasıdır ve sayfaların temel olarak anlaşıldığını varsayar Razor . Sayfalarla alışkın değilseniz Razor , aşağıdaki konulara bakın:

* [Sayfalara giriş Razor](xref:razor-pages/index)
* [Sayfalarla çalışmaya başlama Razor](xref:tutorials/razor-pages/razor-pages-start)
* [RazorSayfalar birim testleri](xref:test/razor-pages-tests)

> [!NOTE]
> Maça 'Ları test etmek için, bir tarayıcıyı otomatikleştirebilen [Selenium](https://www.seleniumhq.org/)gibi bir araç öneririz.

## <a name="introduction-to-integration-tests"></a>Tümleştirme testlerine giriş

Tümleştirme testleri, bir uygulamanın bileşenlerini [birim testlerinden](/dotnet/core/testing/)daha geniş bir düzeyde değerlendirir. Birim testleri, ayrı sınıf yöntemleri gibi yalıtılmış yazılım bileşenlerini test etmek için kullanılır. Tümleştirme testleri iki veya daha fazla uygulama bileşeninin beklenen bir sonuç üretmek için birlikte çalıştığını ve muhtemelen bir isteği tam olarak işlemek için gereken her bileşeni de dahil olduğunu onaylar.

Bu geniş testler, uygulamanın altyapısını ve tüm çatısını test etmek için kullanılır, genellikle aşağıdaki bileşenler dahil:

* Veritabanı
* Dosya sistemi
* Ağ gereçleri
* İstek-yanıt işlem hattı

Birim testleri, altyapı bileşenlerinin yerine, *Fakes* veya *sahte nesneler*olarak bilinen fabriccomponents bileşenlerini kullanır.

Birim testlerinin aksine, tümleştirme testleri:

* Uygulamanın üretimde kullandığı gerçek bileşenleri kullanın.
* Daha fazla kod ve veri işleme gerektir.
* Çalıştırmak daha uzun sürer.

Bu nedenle, tümleştirme testlerinin kullanımını en önemli altyapı senaryolarıyla sınırlayın. Bir davranış, birim testi veya tümleştirme testi kullanılarak test edilebilir ise, birim testini seçin.

> [!TIP]
> Veritabanları ve dosya sistemleriyle ilgili her olası veri ve dosya erişimi için tümleştirme testleri yazma. Bir uygulamadaki kaç yerde veritabanlarıyla ve dosya sistemleriyle etkileşime girdiğinize bakılmaksızın, bir dizi Read, Write, Update ve DELETE tümleştirme testi, genellikle veritabanı ve dosya sistemi bileşenlerinin yeterli şekilde test edilmesine sahip olur. Bu bileşenlerle etkileşime geçen Yöntem mantığının rutin testleri için birim testleri kullanın. Birim testlerinde, altyapı kullanımı ve moizler 'in kullanılması daha hızlı test yürütmesiyle sonuçlanır.

> [!NOTE]
> Tümleştirme testlerinin tartışmalarında, test edilen proje genellikle *test altındaki sistem*veya kısa IÇIN "sut" olarak adlandırılır.
>
> *Bu konu başlığı altında, sınanan ASP.NET Core uygulamasına başvurmak için "SUT" kullanılır.*

## <a name="aspnet-core-integration-tests"></a>ASP.NET Core tümleştirme testleri

ASP.NET Core tümleştirme testleri şunları gerektirir:

* Testleri içermesi ve yürütmek için bir test projesi kullanılır. Test projesinin SUT 'a bir başvurusu vardır.
* Test projesi SUT için bir test Web Konağı oluşturur ve SUT ile istekleri ve yanıtları işlemek için bir test sunucusu istemcisi kullanır.
* Test Çalıştırıcısı, testleri yürütmek ve test sonuçlarını raporlamak için kullanılır.

Tümleştirme testleri, olağan *düzenleme*, *hareket*ve *onaylama* testi adımlarını içeren bir olay dizisini izler:

1. SUT 'un web ana bilgisayarı yapılandırıldı.
1. İstekleri uygulamaya göndermek için bir test sunucusu istemcisi oluşturulur.
1. Testi *Düzenle* adımı yürütülür: test uygulaması bir istek hazırlar.
1. *Davran* test adımı yürütülür: istemci, isteği gönderir ve yanıtını alır.
1. *Onaylama* testi adımı yürütülür: *Gerçek* yanıt, *beklenen* bir yanıta bağlı olarak *başarılı* veya *başarısız* olarak onaylanır.
1. İşlem, tüm testler yürütülene kadar devam eder.
1. Test sonuçları raporlanır.

Genellikle, test Web ana bilgisayarı, test çalıştırmaları için uygulamanın normal web ana bilgisayarında farklı şekilde yapılandırılır. Örneğin, testler için farklı bir veritabanı veya farklı uygulama ayarları kullanılıyor olabilir.

Test Web Konağı ve bellek içi test sunucusu ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)) gibi altyapı bileşenleri, [Microsoft. Aspnetcore. Mvc. Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) paketi tarafından sağlanır veya yönetilir. Bu paketin kullanımı, test oluşturma ve yürütmeyi kolaylaştırır.

`Microsoft.AspNetCore.Mvc.Testing`Paket aşağıdaki görevleri gerçekleştirir:

* Bağımlılıklar dosyasını (*. Deps*) sut 'den test projesinin *bin* dizinine kopyalar.
* , Testler yürütüldüğünde statik dosya ve sayfa/görünümlerin bulunması için [içerik kökünü](xref:fundamentals/index#content-root) sut 'nin proje köküne ayarlar.
* , İle SUT önyükleyiciyi kolaylaştırmak için [Webapplicationfactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) sınıfını sağlar `TestServer` .

[Birim](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) testi belgeleri bir test projesi ve Test Çalıştırıcısı ayarlamayı ve testlerin ve test sınıflarının nasıl belirleneceğini gösteren testlerin ve önerilerin nasıl çalıştırılacağını gösteren ayrıntılı yönergelerle birlikte açıklanır.

> [!NOTE]
> Bir uygulama için test projesi oluştururken, birim testlerini tümleştirme testlerinden farklı projelere ayırın. Bu, altyapı testi bileşenlerinin birim testlerine yanlışlıkla dahil olmamasını sağlamaya yardımcı olur. Birim ve tümleştirme testlerinin ayrımı, hangi test kümesinin çalıştırılmasına da izin verir.

Uygulamaların ve MVC uygulamalarının testleri için yapılandırma arasında neredeyse fark yoktur Razor . Tek fark testlerin adlandırılmasınlardır. Bir Razor Sayfalar uygulamasında sayfa uç noktalarının testleri genellikle sayfa modeli sınıfından sonra (örneğin, `IndexPageTests` Dizin sayfasına yönelik bileşen tümleştirmesini test etmek için) adlandırılır. MVC uygulamasında, testler genellikle denetleyici sınıfları tarafından düzenlenir ve test ettikleri denetleyicilerde (örneğin, `HomeControllerTests` ana denetleyiciye yönelik bileşen tümleştirmesini test etmek için) adlandırılır.

## <a name="test-app-prerequisites"></a>Test uygulaması önkoşulları

Test projesi şunları vermelidir:

* [Microsoft. AspNetCore. Mvc. Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) paketine başvurun.
* () Proje dosyasında Web SDK 'sını belirtin `<Project Sdk="Microsoft.NET.Sdk.Web">` .

Bu Önkoşullar [örnek uygulamada](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/)görülebilir. *Testler/ Razor pagesproject. Tests/ Razor pagesproject. Tests. csproj* dosyasını inceleyin. Örnek uygulama, [xUnit](https://xunit.github.io/) test çerçevesini ve [anglesharp](https://anglesharp.github.io/) Parser kitaplığını kullanır, bu nedenle örnek uygulama de şu şekilde başvurur:

* [xUnit](https://www.nuget.org/packages/xunit)
* [xUnit. Çalıştırıcısı. VisualStudio](https://www.nuget.org/packages/xunit.runner.visualstudio)
* [AngleSharp](https://www.nuget.org/packages/AngleSharp)

Entity Framework Core, testlerde de kullanılır. Uygulama başvuru:

* [Microsoft. AspNetCore. Diagnostics. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore)
* [Microsoft. AspNetCore. Identity . EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity.EntityFrameworkCore)
* [Microsoft. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore)
* [Microsoft. EntityFrameworkCore. InMemory](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory)
* [Microsoft. EntityFrameworkCore. Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools)

## <a name="sut-environment"></a>SUT ortamı

SUT [ortamı](xref:fundamentals/environments) ayarlanmamışsa, ortam varsayılan olarak geliştirme olur.

## <a name="basic-tests-with-the-default-webapplicationfactory"></a>Varsayılan WebApplicationFactory ile temel testler

[Webapplicationfactory \<TEntryPoint> ](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) , tümleştirme testleri için bir [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) oluşturmak için kullanılır. `TEntryPoint`, genellikle sınıfının, SUT 'ın giriş noktası sınıfıdır `Startup` .

Test sınıfları sınıfı testlerin içerdiğini göstermek ve sınıftaki testler arasında paylaşılan nesne örnekleri sağlamak için bir *sınıf armatürü* arabirimi ([ıssfixture](https://xunit.github.io/docs/shared-context#class-fixture)) uygular.

Aşağıdaki test sınıfı, `BasicTests` SUT 'yi önyüklemek `WebApplicationFactory` ve bir test yöntemine [HttpClient](/dotnet/api/system.net.http.httpclient) sağlamak için öğesini kullanır `Get_EndpointsReturnSuccessAndCorrectContentType` . Yöntemi, yanıt durum kodunun başarılı olup olmadığını denetler (200-299 aralığındaki durum kodları) ve `Content-Type` üst bilgi `text/html; charset=utf-8` birçok uygulama sayfasına yöneliktir.

[Createclient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) , `HttpClient` otomatik olarak yeniden yönlendirmeleri ve tutamaçları izleyen bir örneği oluşturur cookie .

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet1)]

Varsayılan olarak, cookie [GDPR onay ilkesi](xref:security/gdpr) etkinleştirildiğinde, gerekli olmayan s istekler arasında korunmaz. cookieTempData sağlayıcısı tarafından kullanılanlar gibi, gerekli olmayan öğeleri korumak için bunları testlerinizde gerekli olarak işaretleyin. Gerekli olarak işaretlemek için gereken yönergeler için cookie bkz. [temel cookie ](xref:security/gdpr#essential-cookies).

## <a name="customize-webapplicationfactory"></a>WebApplicationFactory 'yi özelleştirme

Web ana bilgisayar yapılandırması, öğesinden devralarak `WebApplicationFactory` bir veya daha fazla özel fabrika oluşturmak için test sınıflarından bağımsız olarak oluşturulabilir:

1. `WebApplicationFactory` [Configurewebhost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost)'ten devralma ve geçersiz kılma. [Iwebhostbuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) , hizmet koleksiyonunun [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices)ile yapılandırılmasına izin verir:

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/CustomWebApplicationFactory.cs?name=snippet1)]

   [Örnek uygulamadaki](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) veritabanı dengeli dağıtımı, yöntemi tarafından gerçekleştirilir `InitializeDbForTests` . Yöntemi, [tümleştirme testleri örneği: test uygulaması kuruluşu](#test-app-organization) bölümünde açıklanmaktadır.

   SUT 'un veritabanı bağlamı `Startup.ConfigureServices` yöntemine kaydedilir. Test uygulamasının `builder.ConfigureServices` geri çağırması, uygulamanın kodu yürütüldükten *sonra* yürütülür `Startup.ConfigureServices` . Yürütme sırası, [genel ana bilgisayar](xref:fundamentals/host/generic-host) için ASP.NET Core 3,0 sürümüne sahip bir son değişiklikten oluşur. Uygulamanın veritabanından farklı testler için farklı bir veritabanı kullanmak istiyorsanız, uygulamanın veritabanı bağlamı içinde değiştirilmelidir `builder.ConfigureServices` .

   Hala [Web ana bilgisayarını](xref:fundamentals/host/web-host)kullanan sımlar için, test uygulamasının `builder.ConfigureServices` geri çağırması sut kodundan *önce* yürütülür `Startup.ConfigureServices` . Test uygulamasının `builder.ConfigureTestServices` geri çağırması *sonra*yürütülür.

   Örnek uygulama, veritabanı bağlamı için hizmet tanımlayıcısını bulur ve hizmet kaydını kaldırmak için tanımlayıcıyı kullanır. Ardından, fabrika, `ApplicationDbContext` testler için bellek içi veritabanı kullanan yeni bir ekler.

   Bellek içi veritabanından farklı bir veritabanına bağlanmak için, `UseInMemoryDatabase` bağlamı farklı bir veritabanına bağlama çağrısını değiştirin. SQL Server test veritabanı kullanmak için:

   * Proje dosyasındaki [Microsoft. EntityFrameworkCore. SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/) NuGet paketine başvurun.
   * `UseSqlServer`Veritabanına bir bağlantı dizesiyle çağrı yapın.

   ```csharp
   services.AddDbContext<ApplicationDbContext>((options, context) => 
   {
       context.UseSqlServer(
           Configuration.GetConnectionString("TestingDbConnectionString"));
   });
   ```

2. Özel `CustomWebApplicationFactory` Test sınıflarında kullanın. Aşağıdaki örnek, sınıfında fabrikası kullanır `IndexPageTests` :

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet1)]

   Örnek uygulamanın istemcisi, aşağıdaki yeniden yönlendirmeleri engelleyecek şekilde yapılandırılmıştır `HttpClient` . Daha sonra, [sahte kimlik doğrulama](#mock-authentication) bölümünde açıklandığı gibi, bu, testlerin uygulamanın ilk yanıtının sonucunu denetlemesini sağlar. İlk yanıt, bu testlerin çoğunda üst bilgiyle bir yeniden yönlendirmelidir `Location` .

3. Tipik bir test, `HttpClient` isteği ve yanıtı işlemek için ve yardımcı yöntemlerini kullanır:

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet2)]

SUT 'a yönelik herhangi bir POST isteği, uygulamanın [veri koruma antiforgery sistemi](xref:security/data-protection/introduction)tarafından otomatik olarak oluşturulan antiforgery denetimini karşılamalıdır. Bir testin POST isteğini düzenlemek için test uygulaması şunları kullanmalıdır:

1. Sayfa için bir istek oluşturun.
1. Timeforgery cookie ve istek doğrulama belirtecini ayrıştırın.
1. POST isteğini, antiforgery cookie ve istek doğrulama belirteciyle birlikte yapın.

`SendAsync`Örnek uygulamadaki yardımcı uzantı yöntemleri (*yardımcılar/Httpclienconversionsions. cs*) ve `GetDocumentAsync` yardımcı yöntemi (*yardımcılar/htmlyardımcıları. cs*), aşağıdaki [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) yöntemlerle Antiforgery denetimini işlemek için [anglesharp](https://anglesharp.github.io/) ayrıştırıcısını kullanır:

* `GetDocumentAsync`: [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) 'ı alır ve döndürür `IHtmlDocument` . `GetDocumentAsync`, orijinalin temelinde bir *sanal yanıt* hazırlayan bir fabrika kullanır `HttpResponseMessage` . Daha fazla bilgi için bkz. [Anglesharp belgeleri](https://github.com/AngleSharp/AngleSharp#documentation).
* `SendAsync``HttpClient`bir [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) oluşturun ve istekleri sut 'A göndermek Için [Sendadsync (HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) çağrısı yapın. `SendAsync`HTML biçimini kabul etmek için aşırı yüklemeler ( `IHtmlFormElement` ) ve şunları yapın:
  * Formun () Gönder düğmesi `IHtmlElement`
  * Form değerleri koleksiyonu ( `IEnumerable<KeyValuePair<string, string>>` )
  * Düğme ( `IHtmlElement` ) ve form değerlerini ( `IEnumerable<KeyValuePair<string, string>>` ) gönder

> [!NOTE]
> [Anglesharp](https://anglesharp.github.io/) , bu konuda ve örnek uygulamada Gösterim amacıyla kullanılan bir üçüncü taraf ayrıştırma kitaplığıdır. ASP.NET Core uygulamalarının tümleştirme testi için AngleSharp desteklenmez veya gerekli değildir. Diğer çözümleyiciler, [HTML çevikliği paketi (HAP)](https://html-agility-pack.net/)gibi kullanılabilir. Diğer bir yaklaşım ise, antiforgery sisteminin istek doğrulama belirtecini ve anorma doğrudan işlemesini işlemek için kod yazmaktır cookie .

## <a name="customize-the-client-with-withwebhostbuilder"></a>WithWebHostBuilder ile istemciyi özelleştirme

Bir test yönteminde ek yapılandırma gerektiğinde, [Withwebhostbuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) , `WebApplicationFactory` yapılandırmaya göre daha fazla özelleştirilmiş bir [ıwebhostbuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) ile yeni bir oluşturur.

`Post_DeleteMessageHandler_ReturnsRedirectToRoot` [Örnek uygulamanın](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) test yöntemi öğesinin kullanımını gösterir `WithWebHostBuilder` . Bu test, SUT 'da form gönderimini tetikleyerek veritabanında silme işlemini gerçekleştirir.

Sınıftaki başka bir test `IndexPageTests` veritabanındaki tüm kayıtları silen ve yönteminden önce çalışabilen bir işlem gerçekleştirdiğinden `Post_DeleteMessageHandler_ReturnsRedirectToRoot` , sut 'in silinmesine yönelik bir kaydın mevcut olduğundan emin olmak için veritabanı bu test yönteminde yeniden uygulanabilir. SUT içindeki formun ilk Sil düğmesini seçmek, `messages` sut isteğine göre benzetilir:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet3)]

## <a name="client-options"></a>İstemci seçenekleri

Aşağıdaki tabloda, örnek oluştururken kullanılabilen varsayılan [Webapplicationfactoryclientoptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) gösterilmektedir `HttpClient` .

| Seçenek | Açıklama | Varsayılan |
| ------ | ----------- | ------- |
| [Allowoto yeniden yönlendirme](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | `HttpClient`Örneklerin otomatik olarak yeniden yönlendirme yanıtlarını izleyip izmeyeceğini alır veya ayarlar. | `true` |
| [BaseAddress](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | Örneklerin temel adresini alır veya ayarlar `HttpClient` . | `http://localhost` |
| [Tanıtıcı Cookie s](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | Örneklerin tarafından işlenmesi gerekip gerekmediğini alır veya ayarlar `HttpClient` cookie . | `true` |
| [Maxautomaticyönlendirmeler](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | Örneklerin izlemesi gereken en fazla yeniden yönlendirme yanıtı sayısını alır veya ayarlar `HttpClient` . | 7 |

Sınıfını oluşturun `WebApplicationFactoryClientOptions` ve [createclient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) yöntemine geçirin (varsayılan değerler kod örneğinde gösterilir):

```csharp
// Default client option values are shown
var clientOptions = new WebApplicationFactoryClientOptions();
clientOptions.AllowAutoRedirect = true;
clientOptions.BaseAddress = new Uri("http://localhost");
clientOptions.HandleCookies = true;
clientOptions.MaxAutomaticRedirections = 7;

_client = _factory.CreateClient(clientOptions);
```

## <a name="inject-mock-services"></a>Sahte hizmetler ekleme

Hizmetler, ana bilgisayar tasarımcısında [Configuretestservices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) çağrısıyla bir testte geçersiz kılınabilir. **Sahte hizmetleri eklemek için SUT, `Startup` yöntemi olan bir sınıfa sahip olmalıdır `Startup.ConfigureServices` .**

Örnek SUT, bir teklif döndüren kapsamlı bir hizmet içerir. Dizin sayfası istendiğinde, teklif Dizin sayfasındaki gizli bir alana katıştırılır.

*Hizmetler/ıquoteservice. cs*:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Services/IQuoteService.cs?name=snippet1)]

*Hizmetler/QuoteService. cs*:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Services/QuoteService.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet2)]

*Pages/Index. cshtml. cs*:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml.cs?name=snippet1&highlight=4,9,20,26)]

*Sayfa/dizin. cs*:

[!code-cshtml[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml?name=snippet_Quote)]

Aşağıdaki biçimlendirme, SUT uygulaması çalıştırıldığında oluşturulur:

```html
<input id="quote" type="hidden" value="Come on, Sarah. We&#x27;ve an appointment in 
    London, and we&#x27;re already 30,000 years late.">
```

Bir tümleştirme testinde hizmet ve teklif ekleme işlemini test etmek için, test tarafından SUT 'ye bir sahte hizmet eklenir. Sahte hizmet, uygulamanın `QuoteService` adı verilen test uygulaması tarafından verilen bir hizmetle değiştirilir `TestQuoteService` :

*IntegrationTests.IndexPageTests.cs*:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet4)]

`ConfigureTestServices`çağrılır ve kapsamlı hizmet kaydedilir:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet5&highlight=7-10,17,20-21)]

Testin yürütülmesi sırasında üretilen biçimlendirme tarafından sağlanan teklif metnini yansıtır `TestQuoteService` , bu nedenle onaylama işlemi geçer:

```html
<input id="quote" type="hidden" value="Something&#x27;s interfering with time, 
    Mr. Scarman, and time is my business.">
```

## <a name="mock-authentication"></a>Sahte kimlik doğrulaması

`AuthTests`Sınıftaki testler güvenli bir uç noktanın bulunup bulunmadığını denetler:

* Kimliği doğrulanmamış bir kullanıcıyı uygulamanın oturum açma sayfasına yönlendirir.
* Kimliği doğrulanmış bir kullanıcı için içerik döndürür.

SUT sayfasında, sayfada bir `/SecurePage` [Authorizefilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) uygulamak için Bu sayfa bir [authorizepage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) kuralı kullanır. Daha fazla bilgi için bkz. [ Razor Sayfalar yetkilendirme kuralları](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page).

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet1)]

`Get_SecurePageRedirectsAnUnauthenticatedUser`Testte, bir [Webapplicationfactoryclientoptions, Allowclıredirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) ' i ayarlayarak [yeniden](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) yönlendirmeye izin vermez olarak ayarlanır `false` :

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet2)]

İstemcinin yeniden yönlendirmeyi izlemeye izin vererek aşağıdaki denetimler yapılabilir:

* SUT tarafından döndürülen durum kodu, oturum açma sayfasına yeniden yönlendirmeden sonra, [HttpStatusCode. Tamam](/dotnet/api/system.net.httpstatuscode)olacak şekilde, son durum koduna değil beklenen [HttpStatusCode. Redirect](/dotnet/api/system.net.httpstatuscode) sonucuyla denetlenebilir.
* `Location`Yanıt üst bilgilerindeki üst bilgi değeri, `http://localhost/Identity/Account/Login` üst bilgi bulunmayan son oturum açma sayfası yanıtı değil, ile başladığı onaylanacak şekilde denetlenir `Location` .

Test uygulaması, <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> kimlik doğrulama ve yetkilendirme işlemlerini test etmek için bir [Configuretestservices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) içinde bir model oluşturabilir. En az bir senaryo, bir [kimlik doğrulayan Poesult. Success](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*)döndürür:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet4&highlight=11-18)]

, `TestAuthHandler` Kimlik doğrulama düzeni `Test` için kaydedildiği yere ayarlandığında kullanıcının kimliğini doğrulamak için çağrılır `AddAuthentication` `ConfigureTestServices` . `Test`Düzenin, uygulamanızın beklediği şemayla eşleşmesi önemlidir. Aksi takdirde kimlik doğrulaması çalışmaz.

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet3&highlight=7-12)]

Hakkında daha fazla bilgi için `WebApplicationFactoryClientOptions` [istemci seçenekleri](#client-options) bölümüne bakın.

## <a name="set-the-environment"></a>Ortamı ayarlama

Varsayılan olarak, SUT 'nin ana bilgisayar ve uygulama ortamı geliştirme ortamını kullanacak şekilde yapılandırılmıştır. Kullanırken SUT ortamını geçersiz kılmak için `IHostBuilder` :

* `ASPNETCORE_ENVIRONMENT`Ortam değişkenini (örneğin,,, `Staging` veya gibi `Production` başka bir özel değeri `Testing` ) ayarlayın.
* `CreateHostBuilder`Ön eki olan ortam değişkenlerini okumak için test uygulamasında geçersiz kılın `ASPNETCORE` .

```csharp
protected override IHostBuilder CreateHostBuilder() =>
    base.CreateHostBuilder()
        .ConfigureHostConfiguration(
            config => config.AddEnvironmentVariables("ASPNETCORE"));
```

SUT Web konağını ( `IWebHostBuilder` ) kullanıyorsa, geçersiz kıl `CreateWebHostBuilder` :

```csharp
protected override IWebHostBuilder CreateWebHostBuilder() =>
    base.CreateWebHostBuilder().UseEnvironment("Testing");
```

## <a name="how-the-test-infrastructure-infers-the-app-content-root-path"></a>Test altyapısının uygulama içeriği kök yolunu nasıl öğrendiğini

`WebApplicationFactory`Oluşturucu, derleme üzerinde bir anahtarla eşit anahtar olan tümleştirme testlerini içeren bir [Webapplicationfactorycontentrootattribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) arayarak uygulama [içeriği kök](xref:fundamentals/index#content-root) yolunu alır `TEntryPoint` `System.Reflection.Assembly.FullName` . Doğru anahtara sahip bir özniteliğin bulunamaması durumunda, `WebApplicationFactory` bir çözüm dosyası (*. sln*) aramaya geri döner ve `TEntryPoint` derleme adını çözüm dizinine ekler. Uygulama kök dizini (içerik kök yolu) görünümleri ve içerik dosyalarını saptamak için kullanılır.

## <a name="disable-shadow-copying"></a>Gölge kopyalamayı devre dışı bırak

Gölge kopyalama, testlerin çıkış dizininden farklı bir dizinde yürütülmesine neden olur. Testlerin düzgün çalışması için gölge kopyalama devre dışı bırakılmalıdır. [Örnek uygulama](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) xUnit kullanır ve doğru yapılandırma ayarıyla bir *xunit.runner.js* dosyaya ekleyerek xUnit için gölge kopyalamayı devre dışı bırakır. Daha fazla bilgi için bkz. [JSON Ile xUnit yapılandırma](https://xunit.github.io/docs/configuring-with-json.html).

Aşağıdaki içeriğe sahip test projesinin köküne dosya *xunit.runner.js* ekleyin:

```json
{
  "shadowCopy": false
}
```

## <a name="disposal-of-objects"></a>Nesnelerin elden çıkarılması

`IClassFixture`Uygulamanın testleri yürütüldükten sonra, [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) ve [HttpClient](/dotnet/api/system.net.http.httpclient) , [webapplicationfactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1)'nin xUnit 'i çıkardığı zaman yürütülür. Geliştirici tarafından oluşturulan nesneler elden çıkarma gerektiriyorsa, bunları `IClassFixture` uygulamada atın. Daha fazla bilgi için bkz. [Dispose yöntemi uygulama](/dotnet/standard/garbage-collection/implementing-dispose).

## <a name="integration-tests-sample"></a>Tümleştirme Testleri örneği

[Örnek uygulama](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) iki uygulamalardan oluşur:

| Uygulama | Proje dizini | Açıklama |
| --- | ----------------- | ----------- |
| İleti uygulaması (SUT) | *src/ Razor pagesproject* | Bir kullanıcının, iletileri eklemesini, silmesini, silmesini ve analiz etmesini sağlar. |
| Test uygulaması | *testler/ Razor pagesproject. Tests* | SUT test tümleştirmesi için kullanılır. |

Testler, [Visual Studio](https://visualstudio.microsoft.com)gıbı bir IDE 'nin yerleşik test özellikleri kullanılarak çalıştırılabilir. [Visual Studio Code](https://code.visualstudio.com/) veya komut satırı kullanıyorsanız, *testler/ Razor pagesproject. Tests* dizinindeki bir komut isteminde aşağıdaki komutu yürütün:

```console
dotnet test
```

### <a name="message-app-sut-organization"></a>İleti uygulaması (SUT) kuruluşu

SUT, Razor aşağıdaki özelliklere sahip bir sayfalar ileti sistemidir:

* Uygulamanın (*Pages/Index. cshtml* ve *Pages/index. cshtml. cs*) dizin sayfası, iletilerin eklenmesi, silinmesini ve ANALIZINI denetlemek için bir UI ve sayfa modeli yöntemleri sağlar (ileti başına ortalama sözcük).
* Bir ileti, `Message` sınıfı (*Data/Message. cs*) ile iki özelliği olan ( `Id` anahtar) ve `Text` (ileti) açıklanmaktadır. `Text`Özelliği gereklidir ve 200 karakterle sınırlıdır.
* İletiler, [Entity Framework bellek içi veritabanı](/ef/core/providers/in-memory/)&#8224; kullanılarak depolanır.
* Uygulama, veritabanı bağlamı sınıfında ( `AppDbContext` *Data/AppDbContext. cs*) bir veri ERIŞIM katmanı (dal) içerir.
* Veritabanı uygulama başlangıcında boşsa, ileti deposu üç iletiyle başlatılır.
* Uygulama `/SecurePage` yalnızca kimliği doğrulanmış bir kullanıcı tarafından erişilebilen bir içerir.

EF konusunda, [InMemory Ile Test](/ef/core/miscellaneous/testing/in-memory)&#8224;, MSTest ile testler için bellek içi bir veritabanının nasıl kullanılacağını açıklar. Bu konu [xUnit](https://xunit.github.io/) test çerçevesini kullanır. Farklı test çerçeveleri genelinde test kavramları ve test uygulamaları benzerdir ancak aynı değildir.

Uygulama, depo desenini kullanmıyor ve [Iş birimi (UoW) düzeninin](https://martinfowler.com/eaaCatalog/unitOfWork.html)etkin bir örneği olmamasına karşın, Razor Sayfalar bu geliştirme düzenlerini destekler. Daha fazla bilgi için bkz. [altyapı Kalıcılık katmanını tasarlama](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) ve [Test denetleyicisi mantığı](/aspnet/core/mvc/controllers/testing) (örnek, depo modelini uygular).

### <a name="test-app-organization"></a>Test uygulaması kuruluşu

Test uygulaması, *testler/ Razor pagesproject. Tests* dizini içindeki bir konsol uygulamasıdır.

| Uygulama dizinini test et | Açıklama |
| ------------------ | ----------- |
| *AuthTests* | İçin test yöntemleri içerir:<ul><li>Güvenli bir sayfaya, kimliği doğrulanmamış bir kullanıcıyla erişme.</li><li>Bir sahte ile kimliği doğrulanmış bir kullanıcı tarafından güvenli bir sayfaya erişme <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> .</li><li>GitHub kullanıcı profilini alma ve profilin Kullanıcı oturum açma bilgilerini denetleme.</li></ul> |
| *BasicTests* | Yönlendirme ve içerik türü için bir test yöntemi içerir. |
| *Tümleştirme Testleri* | Özel sınıf kullanan dizin sayfası için tümleştirme testlerini içerir `WebApplicationFactory` . |
| *Yardımcılar/yardımcı programlar* | <ul><li>*Utilities.cs* , `InitializeDbForTests` veritabanını test verileriyle tohum için kullanılan yöntemi içerir.</li><li>*HtmlHelpers.cs* `IHtmlDocument` , test yöntemleri tarafından kullanılmak üzere anglesharp döndüren bir yöntem sağlar.</li><li>*HttpClientExtensions.cs* `SendAsync` , istekleri sut 'a göndermek için için aşırı yüklemeler sağlar.</li></ul> |

Test çerçevesi [xUnit](https://xunit.github.io/)' dir. Tümleştirme testleri, [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)içeren [Microsoft. Aspnetcore. testhost](/dotnet/api/microsoft.aspnetcore.testhost)kullanılarak yürütülür. Test ana bilgisayarı ve test sunucusunu yapılandırmak için [Microsoft. AspNetCore. Mvc. Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) paketi kullanıldığından, ve paketleri test uygulamasındaki `TestHost` `TestServer` Proje dosyasında veya geliştirici yapılandırmasında doğrudan paket başvuruları gerektirmez.

**Test için veritabanının temelini sağlama**

Tümleştirme testleri genellikle veritabanında test yürütmeden önce küçük bir veri kümesi gerektirir. Örneğin, veritabanı kaydı silme için bir silme testi çağrılarında, silme isteğinin başarılı olması için veritabanının en az bir kaydı olmalıdır.

Örnek uygulama, *Utilities.cs* içinde testlerin, yürütme sırasında kullanabileceği üç iletiyle birlikte veritabanını kullanır:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/Helpers/Utilities.cs?name=snippet1)]

SUT 'un veritabanı bağlamı `Startup.ConfigureServices` yöntemine kaydedilir. Test uygulamasının `builder.ConfigureServices` geri çağırması, uygulamanın kodu yürütüldükten *sonra* yürütülür `Startup.ConfigureServices` . Testler için farklı bir veritabanı kullanmak istiyorsanız, uygulamanın veritabanı bağlamı içinde değiştirilmelidir `builder.ConfigureServices` . Daha fazla bilgi için, [WebApplicationFactory 'Yi özelleştirme](#customize-webapplicationfactory) bölümüne bakın.

Hala [Web ana bilgisayarını](xref:fundamentals/host/web-host)kullanan sımlar için, test uygulamasının `builder.ConfigureServices` geri çağırması sut kodundan *önce* yürütülür `Startup.ConfigureServices` . Test uygulamasının `builder.ConfigureTestServices` geri çağırması *sonra*yürütülür.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Tümleştirme sınamaları, uygulamanın bileşenlerinin veritabanı, dosya sistemi ve ağ gibi destekleyici altyapısını içeren bir düzeyde doğru şekilde çalışmasını güvence altına alır. ASP.NET Core, test Web ana bilgisayarı ve bellek içi test sunucusu olan bir birim testi çerçevesini kullanarak tümleştirme testlerini destekler.

Bu konuda, birim testlerinin temel bir şekilde anlaşıldığı varsayılır. Test kavramları hakkında bilgi sahibi değilseniz, [.NET Core 'Da birim testine ve .NET Standard](/dotnet/core/testing/) konusuna ve bağlı içeriğine bakın.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

Örnek uygulama bir Razor Sayfalar uygulamasıdır ve sayfaların temel olarak anlaşıldığını varsayar Razor . Sayfalarla alışkın değilseniz Razor , aşağıdaki konulara bakın:

* [Sayfalara giriş Razor](xref:razor-pages/index)
* [Sayfalarla çalışmaya başlama Razor](xref:tutorials/razor-pages/razor-pages-start)
* [RazorSayfalar birim testleri](xref:test/razor-pages-tests)

> [!NOTE]
> Maça 'Ları test etmek için, bir tarayıcıyı otomatikleştirebilen [Selenium](https://www.seleniumhq.org/)gibi bir araç öneririz.

## <a name="introduction-to-integration-tests"></a>Tümleştirme testlerine giriş

Tümleştirme testleri, bir uygulamanın bileşenlerini [birim testlerinden](/dotnet/core/testing/)daha geniş bir düzeyde değerlendirir. Birim testleri, ayrı sınıf yöntemleri gibi yalıtılmış yazılım bileşenlerini test etmek için kullanılır. Tümleştirme testleri iki veya daha fazla uygulama bileşeninin beklenen bir sonuç üretmek için birlikte çalıştığını ve muhtemelen bir isteği tam olarak işlemek için gereken her bileşeni de dahil olduğunu onaylar.

Bu geniş testler, uygulamanın altyapısını ve tüm çatısını test etmek için kullanılır, genellikle aşağıdaki bileşenler dahil:

* Veritabanı
* Dosya sistemi
* Ağ gereçleri
* İstek-yanıt işlem hattı

Birim testleri, altyapı bileşenlerinin yerine, *Fakes* veya *sahte nesneler*olarak bilinen fabriccomponents bileşenlerini kullanır.

Birim testlerinin aksine, tümleştirme testleri:

* Uygulamanın üretimde kullandığı gerçek bileşenleri kullanın.
* Daha fazla kod ve veri işleme gerektir.
* Çalıştırmak daha uzun sürer.

Bu nedenle, tümleştirme testlerinin kullanımını en önemli altyapı senaryolarıyla sınırlayın. Bir davranış, birim testi veya tümleştirme testi kullanılarak test edilebilir ise, birim testini seçin.

> [!TIP]
> Veritabanları ve dosya sistemleriyle ilgili her olası veri ve dosya erişimi için tümleştirme testleri yazma. Bir uygulamadaki kaç yerde veritabanlarıyla ve dosya sistemleriyle etkileşime girdiğinize bakılmaksızın, bir dizi Read, Write, Update ve DELETE tümleştirme testi, genellikle veritabanı ve dosya sistemi bileşenlerinin yeterli şekilde test edilmesine sahip olur. Bu bileşenlerle etkileşime geçen Yöntem mantığının rutin testleri için birim testleri kullanın. Birim testlerinde, altyapı kullanımı ve moizler 'in kullanılması daha hızlı test yürütmesiyle sonuçlanır.

> [!NOTE]
> Tümleştirme testlerinin tartışmalarında, test edilen proje genellikle *test altındaki sistem*veya kısa IÇIN "sut" olarak adlandırılır.
>
> *Bu konu başlığı altında, sınanan ASP.NET Core uygulamasına başvurmak için "SUT" kullanılır.*

## <a name="aspnet-core-integration-tests"></a>ASP.NET Core tümleştirme testleri

ASP.NET Core tümleştirme testleri şunları gerektirir:

* Testleri içermesi ve yürütmek için bir test projesi kullanılır. Test projesinin SUT 'a bir başvurusu vardır.
* Test projesi SUT için bir test Web Konağı oluşturur ve SUT ile istekleri ve yanıtları işlemek için bir test sunucusu istemcisi kullanır.
* Test Çalıştırıcısı, testleri yürütmek ve test sonuçlarını raporlamak için kullanılır.

Tümleştirme testleri, olağan *düzenleme*, *hareket*ve *onaylama* testi adımlarını içeren bir olay dizisini izler:

1. SUT 'un web ana bilgisayarı yapılandırıldı.
1. İstekleri uygulamaya göndermek için bir test sunucusu istemcisi oluşturulur.
1. Testi *Düzenle* adımı yürütülür: test uygulaması bir istek hazırlar.
1. *Davran* test adımı yürütülür: istemci, isteği gönderir ve yanıtını alır.
1. *Onaylama* testi adımı yürütülür: *Gerçek* yanıt, *beklenen* bir yanıta bağlı olarak *başarılı* veya *başarısız* olarak onaylanır.
1. İşlem, tüm testler yürütülene kadar devam eder.
1. Test sonuçları raporlanır.

Genellikle, test Web ana bilgisayarı, test çalıştırmaları için uygulamanın normal web ana bilgisayarında farklı şekilde yapılandırılır. Örneğin, testler için farklı bir veritabanı veya farklı uygulama ayarları kullanılıyor olabilir.

Test Web Konağı ve bellek içi test sunucusu ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)) gibi altyapı bileşenleri, [Microsoft. Aspnetcore. Mvc. Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) paketi tarafından sağlanır veya yönetilir. Bu paketin kullanımı, test oluşturma ve yürütmeyi kolaylaştırır.

`Microsoft.AspNetCore.Mvc.Testing`Paket aşağıdaki görevleri gerçekleştirir:

* Bağımlılıklar dosyasını (*. Deps*) sut 'den test projesinin *bin* dizinine kopyalar.
* , Testler yürütüldüğünde statik dosya ve sayfa/görünümlerin bulunması için [içerik kökünü](xref:fundamentals/index#content-root) sut 'nin proje köküne ayarlar.
* , İle SUT önyükleyiciyi kolaylaştırmak için [Webapplicationfactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) sınıfını sağlar `TestServer` .

[Birim](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) testi belgeleri bir test projesi ve Test Çalıştırıcısı ayarlamayı ve testlerin ve test sınıflarının nasıl belirleneceğini gösteren testlerin ve önerilerin nasıl çalıştırılacağını gösteren ayrıntılı yönergelerle birlikte açıklanır.

> [!NOTE]
> Bir uygulama için test projesi oluştururken, birim testlerini tümleştirme testlerinden farklı projelere ayırın. Bu, altyapı testi bileşenlerinin birim testlerine yanlışlıkla dahil olmamasını sağlamaya yardımcı olur. Birim ve tümleştirme testlerinin ayrımı, hangi test kümesinin çalıştırılmasına da izin verir.

Uygulamaların ve MVC uygulamalarının testleri için yapılandırma arasında neredeyse fark yoktur Razor . Tek fark testlerin adlandırılmasınlardır. Bir Razor Sayfalar uygulamasında sayfa uç noktalarının testleri genellikle sayfa modeli sınıfından sonra (örneğin, `IndexPageTests` Dizin sayfasına yönelik bileşen tümleştirmesini test etmek için) adlandırılır. MVC uygulamasında, testler genellikle denetleyici sınıfları tarafından düzenlenir ve test ettikleri denetleyicilerde (örneğin, `HomeControllerTests` ana denetleyiciye yönelik bileşen tümleştirmesini test etmek için) adlandırılır.

## <a name="test-app-prerequisites"></a>Test uygulaması önkoşulları

Test projesi şunları vermelidir:

* Aşağıdaki paketlere başvurun:
  * [Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)
  * [Microsoft. AspNetCore. Mvc. test](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing/)
* () Proje dosyasında Web SDK 'sını belirtin `<Project Sdk="Microsoft.NET.Sdk.Web">` . [Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app)'e başvururken Web SDK 'sı gereklidir.

Bu Önkoşullar [örnek uygulamada](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/)görülebilir. *Testler/ Razor pagesproject. Tests/ Razor pagesproject. Tests. csproj* dosyasını inceleyin. Örnek uygulama, [xUnit](https://xunit.github.io/) test çerçevesini ve [anglesharp](https://anglesharp.github.io/) Parser kitaplığını kullanır, bu nedenle örnek uygulama de şu şekilde başvurur:

* [xUnit](https://www.nuget.org/packages/xunit/)
* [xUnit. Çalıştırıcısı. VisualStudio](https://www.nuget.org/packages/xunit.runner.visualstudio/)
* [AngleSharp](https://www.nuget.org/packages/AngleSharp/)

## <a name="sut-environment"></a>SUT ortamı

SUT [ortamı](xref:fundamentals/environments) ayarlanmamışsa, ortam varsayılan olarak geliştirme olur.

## <a name="basic-tests-with-the-default-webapplicationfactory"></a>Varsayılan WebApplicationFactory ile temel testler

[Webapplicationfactory \<TEntryPoint> ](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) , tümleştirme testleri için bir [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) oluşturmak için kullanılır. `TEntryPoint`, genellikle sınıfının, SUT 'ın giriş noktası sınıfıdır `Startup` .

Test sınıfları sınıfı testlerin içerdiğini göstermek ve sınıftaki testler arasında paylaşılan nesne örnekleri sağlamak için bir *sınıf armatürü* arabirimi ([ıssfixture](https://xunit.github.io/docs/shared-context#class-fixture)) uygular.

Aşağıdaki test sınıfı, `BasicTests` SUT 'yi önyüklemek `WebApplicationFactory` ve bir test yöntemine [HttpClient](/dotnet/api/system.net.http.httpclient) sağlamak için öğesini kullanır `Get_EndpointsReturnSuccessAndCorrectContentType` . Yöntemi, yanıt durum kodunun başarılı olup olmadığını denetler (200-299 aralığındaki durum kodları) ve `Content-Type` üst bilgi `text/html; charset=utf-8` birçok uygulama sayfasına yöneliktir.

[Createclient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) , `HttpClient` otomatik olarak yeniden yönlendirmeleri ve tutamaçları izleyen bir örneği oluşturur cookie .

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet1)]

Varsayılan olarak, cookie [GDPR onay ilkesi](xref:security/gdpr) etkinleştirildiğinde, gerekli olmayan s istekler arasında korunmaz. cookieTempData sağlayıcısı tarafından kullanılanlar gibi, gerekli olmayan öğeleri korumak için bunları testlerinizde gerekli olarak işaretleyin. Gerekli olarak işaretlemek için gereken yönergeler için cookie bkz. [temel cookie ](xref:security/gdpr#essential-cookies).

## <a name="customize-webapplicationfactory"></a>WebApplicationFactory 'yi özelleştirme

Web ana bilgisayar yapılandırması, öğesinden devralarak `WebApplicationFactory` bir veya daha fazla özel fabrika oluşturmak için test sınıflarından bağımsız olarak oluşturulabilir:

1. `WebApplicationFactory` [Configurewebhost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost)'ten devralma ve geçersiz kılma. [Iwebhostbuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) , uygulama öncesinde yürütülen [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices)ile hizmet koleksiyonu yapılandırmasına izin verir `Startup.ConfigureServices` . [Iwebhostbuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) , [configuretestservices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices)ile hizmet koleksiyonundaki kayıtlı hizmetlerin üzerine yazma ve değiştirme olanağı sağlar:

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/CustomWebApplicationFactory.cs?name=snippet1)]

   [Örnek uygulamadaki](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) veritabanı dengeli dağıtımı, yöntemi tarafından gerçekleştirilir `InitializeDbForTests` . Yöntemi, [tümleştirme testleri örneği: test uygulaması kuruluşu](#test-app-organization) bölümünde açıklanmaktadır.

2. Özel `CustomWebApplicationFactory` Test sınıflarında kullanın. Aşağıdaki örnek, sınıfında fabrikası kullanır `IndexPageTests` :

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet1)]

   Örnek uygulamanın istemcisi, aşağıdaki yeniden yönlendirmeleri engelleyecek şekilde yapılandırılmıştır `HttpClient` . Daha sonra, [sahte kimlik doğrulama](#mock-authentication) bölümünde açıklandığı gibi, bu, testlerin uygulamanın ilk yanıtının sonucunu denetlemesini sağlar. İlk yanıt, bu testlerin çoğunda üst bilgiyle bir yeniden yönlendirmelidir `Location` .

3. Tipik bir test, `HttpClient` isteği ve yanıtı işlemek için ve yardımcı yöntemlerini kullanır:

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet2)]

SUT 'a yönelik herhangi bir POST isteği, uygulamanın [veri koruma antiforgery sistemi](xref:security/data-protection/introduction)tarafından otomatik olarak oluşturulan antiforgery denetimini karşılamalıdır. Bir testin POST isteğini düzenlemek için test uygulaması şunları kullanmalıdır:

1. Sayfa için bir istek oluşturun.
1. Timeforgery cookie ve istek doğrulama belirtecini ayrıştırın.
1. POST isteğini, antiforgery cookie ve istek doğrulama belirteciyle birlikte yapın.

`SendAsync`Örnek uygulamadaki yardımcı uzantı yöntemleri (*yardımcılar/Httpclienconversionsions. cs*) ve `GetDocumentAsync` yardımcı yöntemi (*yardımcılar/htmlyardımcıları. cs*), aşağıdaki [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) yöntemlerle Antiforgery denetimini işlemek için [anglesharp](https://anglesharp.github.io/) ayrıştırıcısını kullanır:

* `GetDocumentAsync`: [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) 'ı alır ve döndürür `IHtmlDocument` . `GetDocumentAsync`, orijinalin temelinde bir *sanal yanıt* hazırlayan bir fabrika kullanır `HttpResponseMessage` . Daha fazla bilgi için bkz. [Anglesharp belgeleri](https://github.com/AngleSharp/AngleSharp#documentation).
* `SendAsync``HttpClient`bir [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) oluşturun ve istekleri sut 'A göndermek Için [Sendadsync (HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) çağrısı yapın. `SendAsync`HTML biçimini kabul etmek için aşırı yüklemeler ( `IHtmlFormElement` ) ve şunları yapın:
  * Formun () Gönder düğmesi `IHtmlElement`
  * Form değerleri koleksiyonu ( `IEnumerable<KeyValuePair<string, string>>` )
  * Düğme ( `IHtmlElement` ) ve form değerlerini ( `IEnumerable<KeyValuePair<string, string>>` ) gönder

> [!NOTE]
> [Anglesharp](https://anglesharp.github.io/) , bu konuda ve örnek uygulamada Gösterim amacıyla kullanılan bir üçüncü taraf ayrıştırma kitaplığıdır. ASP.NET Core uygulamalarının tümleştirme testi için AngleSharp desteklenmez veya gerekli değildir. Diğer çözümleyiciler, [HTML çevikliği paketi (HAP)](https://html-agility-pack.net/)gibi kullanılabilir. Diğer bir yaklaşım ise, antiforgery sisteminin istek doğrulama belirtecini ve anorma doğrudan işlemesini işlemek için kod yazmaktır cookie .

## <a name="customize-the-client-with-withwebhostbuilder"></a>WithWebHostBuilder ile istemciyi özelleştirme

Bir test yönteminde ek yapılandırma gerektiğinde, [Withwebhostbuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) , `WebApplicationFactory` yapılandırmaya göre daha fazla özelleştirilmiş bir [ıwebhostbuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) ile yeni bir oluşturur.

`Post_DeleteMessageHandler_ReturnsRedirectToRoot` [Örnek uygulamanın](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) test yöntemi öğesinin kullanımını gösterir `WithWebHostBuilder` . Bu test, SUT 'da form gönderimini tetikleyerek veritabanında silme işlemini gerçekleştirir.

Sınıftaki başka bir test `IndexPageTests` veritabanındaki tüm kayıtları silen ve yönteminden önce çalışabilen bir işlem gerçekleştirdiğinden `Post_DeleteMessageHandler_ReturnsRedirectToRoot` , sut 'in silinmesine yönelik bir kaydın mevcut olduğundan emin olmak için veritabanı bu test yönteminde yeniden uygulanabilir. SUT içindeki formun ilk Sil düğmesini seçmek, `messages` sut isteğine göre benzetilir:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet3)]

## <a name="client-options"></a>İstemci seçenekleri

Aşağıdaki tabloda, örnek oluştururken kullanılabilen varsayılan [Webapplicationfactoryclientoptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) gösterilmektedir `HttpClient` .

| Seçenek | Açıklama | Varsayılan |
| ------ | ----------- | ------- |
| [Allowoto yeniden yönlendirme](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | `HttpClient`Örneklerin otomatik olarak yeniden yönlendirme yanıtlarını izleyip izmeyeceğini alır veya ayarlar. | `true` |
| [BaseAddress](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | Örneklerin temel adresini alır veya ayarlar `HttpClient` . | `http://localhost` |
| [Tanıtıcı Cookie s](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | Örneklerin tarafından işlenmesi gerekip gerekmediğini alır veya ayarlar `HttpClient` cookie . | `true` |
| [Maxautomaticyönlendirmeler](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | Örneklerin izlemesi gereken en fazla yeniden yönlendirme yanıtı sayısını alır veya ayarlar `HttpClient` . | 7 |

Sınıfını oluşturun `WebApplicationFactoryClientOptions` ve [createclient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) yöntemine geçirin (varsayılan değerler kod örneğinde gösterilir):

```csharp
// Default client option values are shown
var clientOptions = new WebApplicationFactoryClientOptions();
clientOptions.AllowAutoRedirect = true;
clientOptions.BaseAddress = new Uri("http://localhost");
clientOptions.HandleCookies = true;
clientOptions.MaxAutomaticRedirections = 7;

_client = _factory.CreateClient(clientOptions);
```

## <a name="inject-mock-services"></a>Sahte hizmetler ekleme

Hizmetler, ana bilgisayar tasarımcısında [Configuretestservices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) çağrısıyla bir testte geçersiz kılınabilir. **Sahte hizmetleri eklemek için SUT, `Startup` yöntemi olan bir sınıfa sahip olmalıdır `Startup.ConfigureServices` .**

Örnek SUT, bir teklif döndüren kapsamlı bir hizmet içerir. Dizin sayfası istendiğinde, teklif Dizin sayfasındaki gizli bir alana katıştırılır.

*Hizmetler/ıquoteservice. cs*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/IQuoteService.cs?name=snippet1)]

*Hizmetler/QuoteService. cs*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/QuoteService.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet2)]

*Pages/Index. cshtml. cs*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml.cs?name=snippet1&highlight=4,9,20,26)]

*Sayfa/dizin. cs*:

[!code-cshtml[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml?name=snippet_Quote)]

Aşağıdaki biçimlendirme, SUT uygulaması çalıştırıldığında oluşturulur:

```html
<input id="quote" type="hidden" value="Come on, Sarah. We&#x27;ve an appointment in 
    London, and we&#x27;re already 30,000 years late.">
```

Bir tümleştirme testinde hizmet ve teklif ekleme işlemini test etmek için, test tarafından SUT 'ye bir sahte hizmet eklenir. Sahte hizmet, uygulamanın `QuoteService` adı verilen test uygulaması tarafından verilen bir hizmetle değiştirilir `TestQuoteService` :

*IntegrationTests.IndexPageTests.cs*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet4)]

`ConfigureTestServices`çağrılır ve kapsamlı hizmet kaydedilir:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet5&highlight=7-10,17,20-21)]

Testin yürütülmesi sırasında üretilen biçimlendirme tarafından sağlanan teklif metnini yansıtır `TestQuoteService` , bu nedenle onaylama işlemi geçer:

```html
<input id="quote" type="hidden" value="Something&#x27;s interfering with time, 
    Mr. Scarman, and time is my business.">
```

## <a name="mock-authentication"></a>Sahte kimlik doğrulaması

`AuthTests`Sınıftaki testler güvenli bir uç noktanın bulunup bulunmadığını denetler:

* Kimliği doğrulanmamış bir kullanıcıyı uygulamanın oturum açma sayfasına yönlendirir.
* Kimliği doğrulanmış bir kullanıcı için içerik döndürür.

SUT sayfasında, sayfada bir `/SecurePage` [Authorizefilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) uygulamak için Bu sayfa bir [authorizepage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) kuralı kullanır. Daha fazla bilgi için bkz. [ Razor Sayfalar yetkilendirme kuralları](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page).

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet1)]

`Get_SecurePageRedirectsAnUnauthenticatedUser`Testte, bir [Webapplicationfactoryclientoptions, Allowclıredirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) ' i ayarlayarak [yeniden](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) yönlendirmeye izin vermez olarak ayarlanır `false` :

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet2)]

İstemcinin yeniden yönlendirmeyi izlemeye izin vererek aşağıdaki denetimler yapılabilir:

* SUT tarafından döndürülen durum kodu, oturum açma sayfasına yeniden yönlendirmeden sonra, [HttpStatusCode. Tamam](/dotnet/api/system.net.httpstatuscode)olacak şekilde, son durum koduna değil beklenen [HttpStatusCode. Redirect](/dotnet/api/system.net.httpstatuscode) sonucuyla denetlenebilir.
* `Location`Yanıt üst bilgilerindeki üst bilgi değeri, `http://localhost/Identity/Account/Login` üst bilgi bulunmayan son oturum açma sayfası yanıtı değil, ile başladığı onaylanacak şekilde denetlenir `Location` .

Test uygulaması, <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> kimlik doğrulama ve yetkilendirme işlemlerini test etmek için bir [Configuretestservices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) içinde bir model oluşturabilir. En az bir senaryo, bir [kimlik doğrulayan Poesult. Success](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*)döndürür:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet4&highlight=11-18)]

, `TestAuthHandler` Kimlik doğrulama şeması, için kaydedildiği yere ayarlandığında bir kullanıcının kimliğini doğrulamak için `Test` çağrılır `AddAuthentication` `ConfigureTestServices` :

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet3&highlight=7-12)]

Hakkında daha fazla bilgi için `WebApplicationFactoryClientOptions` [istemci seçenekleri](#client-options) bölümüne bakın.

## <a name="set-the-environment"></a>Ortamı ayarlama

Varsayılan olarak, SUT 'nin ana bilgisayar ve uygulama ortamı geliştirme ortamını kullanacak şekilde yapılandırılmıştır. SUT ortamını geçersiz kılmak için:

* `ASPNETCORE_ENVIRONMENT`Ortam değişkenini (örneğin,,, `Staging` veya gibi `Production` başka bir özel değeri `Testing` ) ayarlayın.
* `CreateWebHostBuilder`Ortam değişkenini okumak için test uygulaması içinde geçersiz kılın `ASPNETCORE_ENVIRONMENT` .

```csharp
public class CustomWebApplicationFactory<TStartup> 
    : WebApplicationFactory<TStartup> where TStartup: class
{
    protected override IWebHostBuilder CreateWebHostBuilder()
    {
        return base.CreateWebHostBuilder()
            .UseEnvironment(
                Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT"));
    }

    ...
}
```

Ortam ayrıca doğrudan konak Oluşturucu üzerinde özel olarak ayarlanabilir <xref:Microsoft.AspNetCore.Mvc.Testing.WebApplicationFactory%601> :

```csharp
public class CustomWebApplicationFactory<TStartup> 
    : WebApplicationFactory<TStartup> where TStartup: class
{
    protected override void ConfigureWebHost(IWebHostBuilder builder)
    {
        builder.UseEnvironment(
            Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT"));
    
        ...
    }

    ...
```

## <a name="how-the-test-infrastructure-infers-the-app-content-root-path"></a>Test altyapısının uygulama içeriği kök yolunu nasıl öğrendiğini

`WebApplicationFactory`Oluşturucu, derleme üzerinde bir anahtarla eşit anahtar olan tümleştirme testlerini içeren bir [Webapplicationfactorycontentrootattribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) arayarak uygulama [içeriği kök](xref:fundamentals/index#content-root) yolunu alır `TEntryPoint` `System.Reflection.Assembly.FullName` . Doğru anahtara sahip bir özniteliğin bulunamaması durumunda, `WebApplicationFactory` bir çözüm dosyası (*. sln*) aramaya geri döner ve `TEntryPoint` derleme adını çözüm dizinine ekler. Uygulama kök dizini (içerik kök yolu) görünümleri ve içerik dosyalarını saptamak için kullanılır.

## <a name="disable-shadow-copying"></a>Gölge kopyalamayı devre dışı bırak

Gölge kopyalama, testlerin çıkış dizininden farklı bir dizinde yürütülmesine neden olur. Testlerin düzgün çalışması için gölge kopyalama devre dışı bırakılmalıdır. [Örnek uygulama](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) xUnit kullanır ve doğru yapılandırma ayarıyla bir *xunit.runner.js* dosyaya ekleyerek xUnit için gölge kopyalamayı devre dışı bırakır. Daha fazla bilgi için bkz. [JSON Ile xUnit yapılandırma](https://xunit.github.io/docs/configuring-with-json.html).

Aşağıdaki içeriğe sahip test projesinin köküne dosya *xunit.runner.js* ekleyin:

```json
{
  "shadowCopy": false
}
```

Visual Studio kullanıyorsanız, dosyanın **Çıkış Dizinine Kopyala** özelliğini **her zaman Kopyala**olarak ayarlayın. Visual Studio kullanmıyorsanız, `Content` Test uygulamasının proje dosyasına bir hedef ekleyin:

```xml
<ItemGroup>
  <Content Update="xunit.runner.json">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
  </Content>
</ItemGroup>
```

## <a name="disposal-of-objects"></a>Nesnelerin elden çıkarılması

`IClassFixture`Uygulamanın testleri yürütüldükten sonra, [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) ve [HttpClient](/dotnet/api/system.net.http.httpclient) , [webapplicationfactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1)'nin xUnit 'i çıkardığı zaman yürütülür. Geliştirici tarafından oluşturulan nesneler elden çıkarma gerektiriyorsa, bunları `IClassFixture` uygulamada atın. Daha fazla bilgi için bkz. [Dispose yöntemi uygulama](/dotnet/standard/garbage-collection/implementing-dispose).

## <a name="integration-tests-sample"></a>Tümleştirme Testleri örneği

[Örnek uygulama](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) iki uygulamalardan oluşur:

| Uygulama | Proje dizini | Açıklama |
| --- | ----------------- | ----------- |
| İleti uygulaması (SUT) | *src/ Razor pagesproject* | Bir kullanıcının, iletileri eklemesini, silmesini, silmesini ve analiz etmesini sağlar. |
| Test uygulaması | *testler/ Razor pagesproject. Tests* | SUT test tümleştirmesi için kullanılır. |

Testler, [Visual Studio](https://visualstudio.microsoft.com)gıbı bir IDE 'nin yerleşik test özellikleri kullanılarak çalıştırılabilir. [Visual Studio Code](https://code.visualstudio.com/) veya komut satırı kullanıyorsanız, *testler/ Razor pagesproject. Tests* dizinindeki bir komut isteminde aşağıdaki komutu yürütün:

```dotnetcli
dotnet test
```

### <a name="message-app-sut-organization"></a>İleti uygulaması (SUT) kuruluşu

SUT, Razor aşağıdaki özelliklere sahip bir sayfalar ileti sistemidir:

* Uygulamanın (*Pages/Index. cshtml* ve *Pages/index. cshtml. cs*) dizin sayfası, iletilerin eklenmesi, silinmesini ve ANALIZINI denetlemek için bir UI ve sayfa modeli yöntemleri sağlar (ileti başına ortalama sözcük).
* Bir ileti, `Message` sınıfı (*Data/Message. cs*) ile iki özelliği olan ( `Id` anahtar) ve `Text` (ileti) açıklanmaktadır. `Text`Özelliği gereklidir ve 200 karakterle sınırlıdır.
* İletiler, [Entity Framework bellek içi veritabanı](/ef/core/providers/in-memory/)&#8224; kullanılarak depolanır.
* Uygulama, veritabanı bağlamı sınıfında ( `AppDbContext` *Data/AppDbContext. cs*) bir veri ERIŞIM katmanı (dal) içerir.
* Veritabanı uygulama başlangıcında boşsa, ileti deposu üç iletiyle başlatılır.
* Uygulama `/SecurePage` yalnızca kimliği doğrulanmış bir kullanıcı tarafından erişilebilen bir içerir.

EF konusunda, [InMemory Ile Test](/ef/core/miscellaneous/testing/in-memory)&#8224;, MSTest ile testler için bellek içi bir veritabanının nasıl kullanılacağını açıklar. Bu konu [xUnit](https://xunit.github.io/) test çerçevesini kullanır. Farklı test çerçeveleri genelinde test kavramları ve test uygulamaları benzerdir ancak aynı değildir.

Uygulama, depo desenini kullanmıyor ve [Iş birimi (UoW) düzeninin](https://martinfowler.com/eaaCatalog/unitOfWork.html)etkin bir örneği olmamasına karşın, Razor Sayfalar bu geliştirme düzenlerini destekler. Daha fazla bilgi için bkz. [altyapı Kalıcılık katmanını tasarlama](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) ve [Test denetleyicisi mantığı](/aspnet/core/mvc/controllers/testing) (örnek, depo modelini uygular).

### <a name="test-app-organization"></a>Test uygulaması kuruluşu

Test uygulaması, *testler/ Razor pagesproject. Tests* dizini içindeki bir konsol uygulamasıdır.

| Uygulama dizinini test et | Açıklama |
| ------------------ | ----------- |
| *AuthTests* | İçin test yöntemleri içerir:<ul><li>Güvenli bir sayfaya, kimliği doğrulanmamış bir kullanıcıyla erişme.</li><li>Bir sahte ile kimliği doğrulanmış bir kullanıcı tarafından güvenli bir sayfaya erişme <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> .</li><li>GitHub kullanıcı profilini alma ve profilin Kullanıcı oturum açma bilgilerini denetleme.</li></ul> |
| *BasicTests* | Yönlendirme ve içerik türü için bir test yöntemi içerir. |
| *Tümleştirme Testleri* | Özel sınıf kullanan dizin sayfası için tümleştirme testlerini içerir `WebApplicationFactory` . |
| *Yardımcılar/yardımcı programlar* | <ul><li>*Utilities.cs* , `InitializeDbForTests` veritabanını test verileriyle tohum için kullanılan yöntemi içerir.</li><li>*HtmlHelpers.cs* `IHtmlDocument` , test yöntemleri tarafından kullanılmak üzere anglesharp döndüren bir yöntem sağlar.</li><li>*HttpClientExtensions.cs* `SendAsync` , istekleri sut 'a göndermek için için aşırı yüklemeler sağlar.</li></ul> |

Test çerçevesi [xUnit](https://xunit.github.io/)' dir. Tümleştirme testleri, [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)içeren [Microsoft. Aspnetcore. testhost](/dotnet/api/microsoft.aspnetcore.testhost)kullanılarak yürütülür. Test ana bilgisayarı ve test sunucusunu yapılandırmak için [Microsoft. AspNetCore. Mvc. Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) paketi kullanıldığından, ve paketleri test uygulamasındaki `TestHost` `TestServer` Proje dosyasında veya geliştirici yapılandırmasında doğrudan paket başvuruları gerektirmez.

**Test için veritabanının temelini sağlama**

Tümleştirme testleri genellikle veritabanında test yürütmeden önce küçük bir veri kümesi gerektirir. Örneğin, veritabanı kaydı silme için bir silme testi çağrılarında, silme isteğinin başarılı olması için veritabanının en az bir kaydı olmalıdır.

Örnek uygulama, *Utilities.cs* içinde testlerin, yürütme sırasında kullanabileceği üç iletiyle birlikte veritabanını kullanır:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/Helpers/Utilities.cs?name=snippet1)]

::: moniker-end

## <a name="additional-resources"></a>Ek kaynaklar

* [Birim testleri](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* <xref:test/razor-pages-tests>
* <xref:fundamentals/middleware/index>
* <xref:mvc/controllers/testing>
