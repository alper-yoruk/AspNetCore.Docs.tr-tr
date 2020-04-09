---
title: ASP.NET Core'da entegrasyon testleri
author: rick-anderson
description: Tümleştirme testlerinin, veritabanı, dosya sistemi ve ağ da dahil olmak üzere bir uygulamanın bileşenlerinin altyapı düzeyinde doğru çalışmasını nasıl sağlayacağını öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/06/2019
uid: test/integration-tests
ms.openlocfilehash: 414e47b9c5a1c843755bd79d313f503b554945db
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78664698"
---
# <a name="integration-tests-in-aspnet-core"></a>ASP.NET Core'da entegrasyon testleri

Javier [Calvarro Nelson](https://github.com/javiercn), [Steve Smith](https://ardalis.com/)ve [Jos van der Til](https://jvandertil.nl) tarafından

::: moniker range=">= aspnetcore-3.0"

Tümleştirme testleri, uygulamanın bileşenlerinin veritabanı, dosya sistemi ve ağ gibi uygulamanın destekleyici altyapısını içeren bir düzeyde düzgün çalışmasını sağlar. ASP.NET Core, bir test web ana bilgisayarı ve bellek içi test sunucusu içeren bir birim test çerçevesi kullanarak tümleştirme testlerini destekler.

Bu konu, birim testleri temel bir anlayış varsayar. Test kavramlarını bilmiyorsanız, [.NET Core ve .NET Standart](/dotnet/core/testing/) konusundaki Birim Testi'ne ve bağlantılı içeriğine bakın.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ( nasıl[indirilir](xref:index#how-to-download-a-sample))

Örnek uygulama bir Razor Pages uygulamasıdır ve Razor Pages'in temel bir anlayışını varsayar. Razor Pages'e aşina değilseniz aşağıdaki konulara bakın:

* [Razor Pages’e giriş](xref:razor-pages/index)
* [Razor Sayfaları kullanmaya başlama](xref:tutorials/razor-pages/razor-pages-start)
* [Razor Pages birim testleri](xref:test/razor-pages-tests)

> [!NOTE]
> SP'leri test etmek için, bir tarayıcıyı otomatikleştirebilen [Selenyum](https://www.seleniumhq.org/)gibi bir araç önerdik.

## <a name="introduction-to-integration-tests"></a>Entegrasyon testlerine giriş

Tümleştirme testleri, bir uygulamanın bileşenlerini [birim testlerinden](/dotnet/core/testing/)daha geniş bir düzeyde değerlendirir. Birim testleri, tek tek sınıf yöntemleri gibi yalıtılmış yazılım bileşenlerini sınamak için kullanılır. Tümleştirme testleri, iki veya daha fazla uygulama bileşeninin beklenen sonucu elde etmek için birlikte çalıştığını ve muhtemelen bir isteği tam olarak işlemek için gereken her bileşeni de içeren bir sonucu onaylar.

Bu daha geniş testler, genellikle aşağıdaki bileşenler de dahil olmak üzere uygulamanın altyapısını ve tüm çerçevesini test etmek için kullanılır:

* Veritabanı
* Dosya sistemi
* Ağ gereçleri
* İstek-yanıt ardışık

Birim testleri, altyapı bileşenleri yerine *sahte* veya *sahte nesneler*olarak bilinen fabrikasyon bileşenler kullanır.

Birim testlerinin aksine, tümleştirme testleri:

* Uygulamanın üretimde kullandığı gerçek bileşenleri kullanın.
* Daha fazla kod ve veri işleme gerektirir.
* Koşması daha uzun sürer.

Bu nedenle, tümleştirme testlerinin kullanımını en önemli altyapı senaryoları ile sınırlandırın. Bir davranış birim testi veya tümleştirme testi kullanılarak test edilebilirse, birim testini seçin.

> [!TIP]
> Veritabanları ve dosya sistemleri ile veri ve dosya erişimi olası her permütasyon için entegrasyon testleri yazmayın. Bir uygulamanın veritabanları ve dosya sistemleriyle etkileşimde olduğu yerden kaç yerde olursa olsun, okuma, yazma, güncelleme ve silme tümleştirme testlerinin odaklanmış bir kümesi genellikle veritabanı ve dosya sistemi bileşenlerini yeterince test etme yeteneğine sahiptir. Bu bileşenlerle etkileşimedebilen yöntem mantığının rutin testleri için birim testleri kullanın. Birim testlerinde, altyapı sahtelerinin/alaylarının kullanımı daha hızlı test yürütülmesine neden olabilir.

> [!NOTE]
> Tümleştirme testlerinde, test edilen proje sık sık *Test Altında Sistem*veya kısaca "SUT" olarak adlandırılır.
>
> *"SUT" test ASP.NET Core uygulamasına başvurmak için bu konu boyunca kullanılır.*

## <a name="aspnet-core-integration-tests"></a>ASP.NET Çekirdek entegrasyon testleri

ASP.NET Core'daki tümleştirme testleri aşağıdakileri gerektirir:

* Testleri içeren ve yürütmek için bir test projesi kullanılır. Test projesinin SUT'ye bir başvurusu vardır.
* Test projesi SUT için bir test web ana bilgisayarı oluşturur ve SUT ile istek ve yanıtları işlemek için bir test sunucusu istemcisi kullanır.
* Testleri yürütmek ve test sonuçlarını bildirmek için bir test koşucusu kullanılır.

Tümleştirme testleri, olağan *Düzenleme,* *Hareket*ve Test adımlarını *öne* süriçeren bir dizi olay izleyin:

1. SUT'nin web barındırma alanı yapılandırılır.
1. Uygulamaya istek göndermek için bir test sunucusu istemcisi oluşturulur.
1. *Test düzenle* adımı yürütülür: Test uygulaması bir istek hazırlar.
1. *Act* test adımı yürütülür: İstemci isteği gönderir ve yanıtı alır.
1. *Assert* test adımı yürütülür: *Gerçek* yanıt *beklenen* yanıta bağlı olarak *bir geçiş* veya *başarısız* olarak doğrulanır.
1. Tüm testler yürütülene kadar işlem devam ediyor.
1. Test sonuçları bildirilir.

Genellikle, test web ana bilgisayarı, test çalışır için uygulamanın normal web ana bilgisayardan farklı şekilde yapılandırılır. Örneğin, testler için farklı bir veritabanı veya farklı uygulama ayarları kullanılabilir.

Test web ana bilgisayarı ve bellek içi test sunucusu[(TestServer)](/dotnet/api/microsoft.aspnetcore.testhost.testserver)gibi altyapı bileşenleri [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) paketi tarafından sağlanır veya yönetilir. Bu paketin kullanımı test oluşturma ve yürütme kolaylaştırır.

`Microsoft.AspNetCore.Mvc.Testing` Paket aşağıdaki görevleri işler:

* Bağımlılıklar dosyasını (*.deps)* SUT'dan test projesinin *depo* gözü dizinine kopyalar.
* İçerik [kökünü](xref:fundamentals/index#content-root) SUT'un proje köküne ayarlar, böylece testler yürütüldüğünde statik dosyaların ve sayfaların/görünümlerin bulunur.
* SUT ile bootstrapping kolaylaştırmak için [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) sınıfı `TestServer`sağlar.

[Birim test](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) belgeleri, bir test projesinin ve test koşucununun nasıl ayarlayacağıyla birlikte, testlerin nasıl adlandırılacacağına ve sınıfları nasıl adlandıracağınızla ilgili ayrıntılı yönergelerin yanı sıra açıklar.

> [!NOTE]
> Bir uygulama için test projesi oluştururken, birim testlerini tümleştirme testlerinden farklı projelere ayırın. Bu, altyapı sınama bileşenlerinin yanlışlıkla birim testlerine dahil olmamasını sağlamaya yardımcı olur. Birim ve tümleştirme testlerinin ayrılması, hangi test kümesinin çalıştırıldığı üzerinde denetim sağlar.

Razor Pages uygulamalarının testleri için yapılandırma ile MVC uygulamaları arasında neredeyse hiçbir fark yoktur. Tek fark, testlerin nasıl adlandırıldığının fark edilir. Bir Razor Pages uygulamasında, sayfa bitiş noktalarının testleri genellikle sayfa modeli `IndexPageTests` sınıfından (örneğin, Dizin sayfası için bileşen tümleştirmesini test etmek için) adlarını verilmiştir. Bir MVC uygulamasında, testler genellikle denetleyici sınıfları tarafından düzenlenir ve test `HomeControllerTests` ettikleri denetleyicilerden (örneğin, Ev denetleyicisi için bileşen tümleştirmesini test etmek için) adlandırılmıştır.

## <a name="test-app-prerequisites"></a>Ön koşulları test edin

Test projesi şunları yapmalıdır:

* [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) paketine başvurun.
* Proje dosyasında Web SDK'yı belirtin (`<Project Sdk="Microsoft.NET.Sdk.Web">`).

Bu ön koşullar [örnek uygulamada](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/)görülebilir. Testleri *inceleyin/RazorPagesProject.Tests/RazorPagesProject.Tests.csproj* dosyası. Örnek uygulama [xUnit](https://xunit.github.io/) test çerçevesini ve [AngleSharp](https://anglesharp.github.io/) parser kitaplığını kullanır, bu nedenle örnek uygulama da başvurur:

* [xunit](https://www.nuget.org/packages/xunit)
* [xunit.runner.visualstudio](https://www.nuget.org/packages/xunit.runner.visualstudio)
* [Açı Keskin](https://www.nuget.org/packages/AngleSharp)

Varlık Framework Core da testlerde kullanılır. Uygulama referansları:

* [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore)
* [Microsoft.AspNetCore.Identity.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity.EntityFrameworkCore)
* [Microsoft.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore)
* [Microsoft.EntityFrameworkCore.InMemory](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory)
* [Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools)

## <a name="sut-environment"></a>SUT ortamı

SUT'nin [ortamı](xref:fundamentals/environments) ayarlı değilse, ortam Geliştirme için varsayılan dır.

## <a name="basic-tests-with-the-default-webapplicationfactory"></a>Varsayılan WebApplicationFactory ile temel testler

[WebApplicationFactory\<TEntryPoint>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) tümleştirme testleri için bir [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) oluşturmak için kullanılır. `TEntryPoint`SUT'nin giriş noktası sınıfıdır, `Startup` genellikle sınıftır.

Test sınıfları, sınıfın testler içerdiğini belirtmek ve sınıftaki testler arasında paylaşılan nesne örnekleri sağlamak için bir *sınıf fikstür* arabirimi[(IClassFixture)](https://xunit.github.io/docs/shared-context#class-fixture)uygular.

Aşağıdaki test sınıfı, `BasicTests`SUT bootstrap ve bir test yöntemi için bir [HttpClient](/dotnet/api/system.net.http.httpclient) sağlamak için kullanır. `Get_EndpointsReturnSuccessAndCorrectContentType` `WebApplicationFactory` Yöntem, yanıt durum kodunun başarılı olup olmadığını (200-299 aralığındaki durum kodları) denetler ve `Content-Type` üstbilgi birkaç uygulama sayfası içindir. `text/html; charset=utf-8`

[CreateClient,](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) otomatik olarak `HttpClient` yeniden yönlendirmeleri izleyen ve tanımlama bilgilerini işleyen bir örnek oluşturur.

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet1)]

Varsayılan olarak, [GDPR onay ilkesi](xref:security/gdpr) etkinleştirildiğinde, gerekli olmayan tanımlama bilgileri istekler arasında korunmaz. TempData sağlayıcısı tarafından kullanılanlar gibi gerekli olmayan tanımlama bilgilerini korumak için, bunları testlerinizde gerekli olarak işaretleyin. Bir çerezi temel olarak işaretleme yönergeleri için [Temel tanımlama bilgilerine](xref:security/gdpr#essential-cookies)bakın.

## <a name="customize-webapplicationfactory"></a>WebApplicationFactory'yi Özelleştir

Web ana bilgisayar yapılandırması, bir veya daha `WebApplicationFactory` fazla özel fabrika oluşturmak için devralınarak test sınıflarından bağımsız olarak oluşturulabilir:

1. `WebApplicationFactory` [ConfigureWebHost'tan](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost)devralma ve geçersiz kılma. [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices)ile hizmet toplama yapılandırma sağlar:

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/CustomWebApplicationFactory.cs?name=snippet1)]

   [Örnek uygulamadaki](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) veritabanı tohumlama `InitializeDbForTests` yöntemi ile gerçekleştirilir. Yöntem, [Tümleştirme testleri örneğinde açıklanmıştır: Test uygulaması organizasyonu](#test-app-organization) bölümü.

   SUT'nin veritabanı bağlamı kendi `Startup.ConfigureServices` yönteminde kayıtlıdır. Test uygulamasının `builder.ConfigureServices` geri araması, uygulamanın `Startup.ConfigureServices` kodu yürütüldükten *sonra* yürütülür. Yürütme emri, ASP.NET Core 3.0 sürümü ile [Genel Ana Bilgisayar](xref:fundamentals/host/generic-host) için bir kesme değişikliğidir. Testler için uygulamanın veritabanından farklı bir veritabanı kullanmak için uygulamanın veritabanı bağlamı 'nda `builder.ConfigureServices`değiştirilmesi gerekir.

   Örnek uygulama veritabanı bağlamı için hizmet tanımlayıcısını bulur ve hizmet kaydını kaldırmak için tanımlayıcıyı kullanır. Ardından, fabrika testler `ApplicationDbContext` için bellek içi bir veritabanı kullanan yeni bir ekle.

   Bellek veritabanından farklı bir veritabanına bağlanmak için, bağlamı farklı bir veritabanına bağlamak için çağrıyı `UseInMemoryDatabase` değiştirin. SQL Server test veritabanı kullanmak için:

   * Proje dosyasındaki [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/) NuGet paketine başvurun.
   * Veritabanına bağlantı dizesi ile arayın. `UseSqlServer`

   ```csharp
   services.AddDbContext<ApplicationDbContext>((options, context) => 
   {
       context.UseSqlServer(
           Configuration.GetConnectionString("TestingDbConnectionString"));
   });
   ```

2. Test sınıflarında özel `CustomWebApplicationFactory` kullanın. Aşağıdaki örnek, sınıftaki `IndexPageTests` fabrikayı kullanır:

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet1)]

   Örnek uygulamanın istemcisi aşağıdaki yönlendirmeleri önlemek `HttpClient` için yapılandırılır. Daha sonra [Mock kimlik doğrulama](#mock-authentication) bölümünde açıklandığı gibi, bu testlerin uygulamanın ilk yanıtının sonucunu kontrol etmelerine izin verir. İlk yanıt, bu testlerin çoğunda üstbilgi `Location` yle yeniden yönlendirmedir.

3. Tipik bir test, isteği `HttpClient` ve yanıtı işlemek için yardımcı ve yardımcı yöntemleri kullanır:

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet2)]

SUT herhangi bir POST isteği otomatik olarak uygulamanın [veri koruma antiforgery sistemi](xref:security/data-protection/introduction)tarafından yapılan antiforgery kontrol karşılamak gerekir. Bir testin POST isteğini ayarlamak için test uygulamasının aşağıdakileri yapması gerekir:

1. Sayfa için bir istekte bulunun.
1. Antiforgery çerezini ayrıştırın ve yanıttan doğrulama belirteci isteyin.
1. Antiforgery çerezi ile POST isteğinde bulunun ve yerinde doğrulama belirteci isteyin.

`SendAsync` Yardımcı uzantı yöntemleri *(Yardımcıları / HttpClientExtensions.cs*) ve `GetDocumentAsync` yardımcı yöntem (*Yardımcılar / HtmlHelpers.cs*) [örnek uygulama](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) aşağıdaki yöntemlerle antiforgery kontrol işlemek için [AngleSharp](https://anglesharp.github.io/) parser kullanın:

* `GetDocumentAsync`&ndash; [HttpResponseMessage'ı](/dotnet/api/system.net.http.httpresponsemessage) alır `IHtmlDocument`ve bir . `GetDocumentAsync`orijinaline `HttpResponseMessage`dayalı sanal bir *yanıt* hazırlayan bir fabrika kullanır. Daha fazla bilgi için [AngleSharp belgelerine](https://github.com/AngleSharp/AngleSharp#documentation)bakın.
* `SendAsync`bir `HttpClient` [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) oluşturmak için uzantı yöntemleri ve SUT isteklerini göndermek için [SendAsync (HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) arayın. HTML formunu `SendAsync` kabul etmek`IHtmlFormElement`için aşırı yüklemeler ( ) ve aşağıdakiler:
  * Formun gönder düğmesi`IHtmlElement`( )
  * Form değerleri`IEnumerable<KeyValuePair<string, string>>`toplama ( )
  * Gönder düğmesi`IHtmlElement`( )`IEnumerable<KeyValuePair<string, string>>`ve form değerleri ( )

> [!NOTE]
> [AngleSharp,](https://anglesharp.github.io/) bu konu ve örnek uygulamada gösteri amacıyla kullanılan üçüncü taraf ayrıştırma kitaplığıdır. AngleSharp, ASP.NET Core uygulamalarının entegrasyon testleri için desteklenmez veya gerekli değildir. [Html Çeviklik Paketi (HAP)](https://html-agility-pack.net/)gibi diğer ayrıştırıcılar kullanılabilir. Başka bir yaklaşım doğrudan antiforgery sisteminin istek doğrulama belirteci ve antiforgery çerez işlemek için kod yazmaktır.

## <a name="customize-the-client-with-withwebhostbuilder"></a>WithWebHostBuilder ile istemciyi özelleştirin

Ek yapılandırma bir test yöntemi içinde gerekli olduğunda, [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) daha fazla yapılandırma tarafından özelleştirilmiş bir `WebApplicationFactory` [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) ile yeni bir oluşturur.

Örnek `Post_DeleteMessageHandler_ReturnsRedirectToRoot` [uygulamanın](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) test yöntemi. `WithWebHostBuilder` Bu test, SUT'da bir form göndermesini tetikleyerek veritabanında bir kayıt silme gerçekleştirir.

Sınıftaki başka `IndexPageTests` bir sınama veritabanındaki tüm kayıtları silen ve `Post_DeleteMessageHandler_ReturnsRedirectToRoot` yöntemden önce çalıştırılabilen bir işlem gerçekleştirdiğinden, SUT'nin silmesi için bir kaydın bulunduğundan emin olmak için veritabanı bu sınama yönteminde yeniden düzenlenir. SUT'deki formun `messages` ilk silme düğmesini seçmek SUT'a istekte simüle edilir:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet3)]

## <a name="client-options"></a>İstemci seçenekleri

Aşağıdaki tablo, örnek oluştururken `HttpClient` kullanılabilen varsayılan [WebApplicationFactoryClientOptions'ı](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) gösterir.

| Seçenek | Açıklama | Varsayılan |
| ------ | ----------- | ------- |
| [İzin AutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | Örneklerin yönlendirme yanıtlarını otomatik olarak izlemesi gerekip gerekmediğini `HttpClient` alır veya ayarlar. | `true` |
| [Baseaddress](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | Örneklerin `HttpClient` temel adresini alır veya ayarlar. | `http://localhost` |
| [Kulp kurabiyeleri](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | Örneklerin tanımlama `HttpClient` bilgilerini işlemesi gerekip gerekmediğini alır veya ayarlar. | `true` |
| [MaxAutomaticRedirections](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | `HttpClient` Örneklerin izlemesi gereken en fazla yönlendirme yanıtı sayısını alır veya ayarlar. | 7 |

`WebApplicationFactoryClientOptions` Sınıfı oluşturun ve [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) yöntemine geçirin (varsayılan değerler kod örneğinde gösterilir):

```csharp
// Default client option values are shown
var clientOptions = new WebApplicationFactoryClientOptions();
clientOptions.AllowAutoRedirect = true;
clientOptions.BaseAddress = new Uri("http://localhost");
clientOptions.HandleCookies = true;
clientOptions.MaxAutomaticRedirections = 7;

_client = _factory.CreateClient(clientOptions);
```

## <a name="inject-mock-services"></a>Sahte hizmetleri enjekte edin

Hizmetler, ana bilgisayar oluşturucuda [ConfigureTestServices'e](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) yapılandırılan bir çağrıyla bir testte geçersiz kılınabilir. **Sahte hizmetleri enjekte etmek için, `Startup` SUT'nin bir `Startup.ConfigureServices` yönteme sahip bir sınıfı olmalıdır.**

Örnek SUT, bir teklifi döndüren kapsamlı bir hizmet içerir. Teklif, Dizin sayfasında dizin sayfası istendiğinde Dizin sayfasındaki gizli bir alana gömülür.

*Hizmetler/IQuoteService.cs*:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Services/IQuoteService.cs?name=snippet1)]

*Hizmetler/QuoteService.cs*:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Services/QuoteService.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet2)]

*Sayfalar/Index.cshtml.cs*:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml.cs?name=snippet1&highlight=4,9,20,26)]

*Sayfalar/Index.cs*:

[!code-cshtml[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml?name=snippet_Quote)]

SUT uygulaması çalıştırıldığında aşağıdaki biçimlendirme oluşturulur:

```html
<input id="quote" type="hidden" value="Come on, Sarah. We&#x27;ve an appointment in 
    London, and we&#x27;re already 30,000 years late.">
```

Bir entegrasyon testinde hizmeti test etmek ve enjeksiyondan alıntı yapmak için, test tarafından SUT'ye sahte bir hizmet enjekte edilir. Sahte hizmet, uygulamanın `QuoteService` yerine test uygulaması tarafından sağlanan bir `TestQuoteService`hizmetin yerini alır:

*IntegrationTests.IndexPageTests.cs*:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet4)]

`ConfigureTestServices`denir ve kapsamlı hizmet kaydedilir:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet5&highlight=7-10,17,20-21)]

Testin yürütülmesi sırasında üretilen biçimlendirme, tarafından `TestQuoteService`sağlanan teklif metnini yansıtır, böylece iddia geçer:

```html
<input id="quote" type="hidden" value="Something&#x27;s interfering with time, 
    Mr. Scarman, and time is my business.">
```

## <a name="mock-authentication"></a>Sahte kimlik doğrulama

Sınıftaki `AuthTests` testler güvenli bir bitiş noktası olup olmadığını denetler:

* Kimliği doğrulanmamış bir kullanıcıyı uygulamanın Giriş sayfasına yönlendirir.
* Kimliği doğrulanmış bir kullanıcı için içerik verir.

SUT'de, sayfa, sayfaya bir [AuthorizeFilter](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) uygulamak için bir [AuthorizePage](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) kuralı kullanır. `/SecurePage` Daha fazla bilgi için [Bkz. Razor Pages yetkilendirme kuralları.](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page)

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet1)]

Testte, `Get_SecurePageRedirectsAnUnauthenticatedUser` [Bir WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) ayarlayarak yeniden yönlendirmeleri izin `false`vermeyecek şekilde ayarlanır:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet2)]

İstemcinin yönlendirmeyi izlemesine izin verilerek aşağıdaki denetimler yapılabilir:

* SUT tarafından döndürülen durum kodu, [HttpStatusCode.OK](/dotnet/api/system.net.httpstatuscode)olacak Login sayfasına yönlendirildikten sonra son durum kodu na göre değil, beklenen [HttpStatusCode.Redirect](/dotnet/api/system.net.httpstatuscode) sonucuyla karşıkontrol edilebilir.
* Yanıt `Location` üstbilgisindeki üstbilgi değeri, `http://localhost/Identity/Account/Login` `Location` üstbilginin bulunmadığı son Giriş sayfası yanıtıyla değil, ile başladığını doğrulamak için denetlenir.

Test uygulaması, kimlik <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> doğrulama ve yetkilendirme yönlerini test etmek için [ConfigureTestServices'teki](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) bir uygulamayla alay edebilir. En az senaryo [Bir AuthenticateResult.Success](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*)döndürür:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet4&highlight=11-18)]

Kimlik `TestAuthHandler` doğrulama düzeni şu şekilde kayıtlı olduğu `Test` yere `AddAuthentication` ayarlandığında kullanıcının `ConfigureTestServices`kimliğini doğrulamak için çağrılır:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet3&highlight=7-12)]

Hakkında daha `WebApplicationFactoryClientOptions`fazla bilgi için [İstemci seçenekleri](#client-options) bölümüne bakın.

## <a name="set-the-environment"></a>Ortamı ayarlama

Varsayılan olarak, SUT'nin ana bilgisayar ve uygulama ortamı Geliştirme ortamını kullanacak şekilde yapılandırılır. SUT'nin ortamını geçersiz kılmak için:

* Ortam `ASPNETCORE_ENVIRONMENT` değişkenini `Staging`(örneğin, `Production`, , veya diğer `Testing`özel değer gibi) ayarlayın.
* Önceden `CreateHostBuilder` belirlenmiş `ASPNETCORE`ortam değişkenlerini okumak için test uygulamasında geçersiz kılma

```csharp
protected override IHostBuilder CreateHostBuilder() => 
    base.CreateHostBuilder()
        .ConfigureHostConfiguration(
            config => config.AddEnvironmentVariables("ASPNETCORE"));
```

## <a name="how-the-test-infrastructure-infers-the-app-content-root-path"></a>Test altyapısı uygulama içeriği kök yolunu nasıl çıkar

Oluşturucu, `WebApplicationFactory` `TEntryPoint` derlemeye `System.Reflection.Assembly.FullName`eşit bir anahtarla tümleştirme testlerini içeren derlemede bir [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) arayarak uygulama [içeriği kök](xref:fundamentals/index#content-root) yolunu çıkar. Doğru anahtara sahip bir öznitelik bulunamazsa, `WebApplicationFactory` çözüm dosyası *(.sln)* aramaya geri `TEntryPoint` döner ve derleme adını çözüm dizinine ekler. Uygulama kök dizini (içerik kökü yolu) görünümleri ve içerik dosyalarını keşfetmek için kullanılır.

## <a name="disable-shadow-copying"></a>Gölge kopyalamayı devre dışı bırak

Gölge kopyalama, testlerin çıktı dizininden farklı bir dizinde yürütülmesine neden olur. Testlerin düzgün çalışması için gölge kopyalamadevre dışı bırakılmalıdır. [Örnek uygulama](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) xUnit kullanır ve doğru yapılandırma ayarına sahip bir *xunit.runner.json* dosyası ekleyerek xUnit için gölge kopyalamadevre dışı bırakır. Daha fazla bilgi için bkz: [XUnit'i JSON ile yapılandırma.](https://xunit.github.io/docs/configuring-with-json.html)

*Xunit.runner.json* dosyasını aşağıdaki içerikle test projesinin köküne ekleyin:

```json
{
  "shadowCopy": false
}
```

## <a name="disposal-of-objects"></a>Nesnelerin atılması

`IClassFixture` Uygulama testleri uygulandıktan sonra, [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) ve [HttpClient](/dotnet/api/system.net.http.httpclient) xUnit [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1)elden zaman bertaraf edilir. Geliştirici tarafından anında verilen nesneler elden çıkarılması gerekiyorsa, bunları `IClassFixture` uygulamada atın. Daha fazla bilgi için [bkz.](/dotnet/standard/garbage-collection/implementing-dispose)

## <a name="integration-tests-sample"></a>Entegrasyon testleri örneği

[Örnek uygulama](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) iki uygulamadan oluşur:

| Uygulama | Proje dizini | Açıklama |
| --- | ----------------- | ----------- |
| İleti uygulaması (SUT) | *src/RazorPagesProject* | Kullanıcının ileti eklemesine, silmesine, tümünü silmesine ve çözümlemesine olanak tanır. |
| Test uygulaması | *testler/RazorPagesProject.Tests* | SUT'yi tümleştirme testinde kullanılır. |

Testler Visual [Studio](https://visualstudio.microsoft.com)gibi bir IDE'nin yerleşik test özellikleri kullanılarak çalıştırılabilir. Visual [Studio Code](https://code.visualstudio.com/) veya komut satırı kullanıyorsanız, *testler/RazorPagesProject.Tests* dizininde aşağıdaki komut istemini uygulayın:

```console
dotnet test
```

### <a name="message-app-sut-organization"></a>İleti uygulaması (SUT) organizasyonu

SUT, aşağıdaki özelliklere sahip bir Razor Pages mesaj sistemidir:

* Uygulamanın Dizin sayfası *(Pages/Index.cshtml* ve *Pages/Index.cshtml.cs)* iletilerin eklenmesini, silinmesini ve çözümlemesini (ileti başına ortalama sözcük) denetlemek için bir kullanıcı aracı ve sayfa modeli yöntemleri sağlar.
* `Message` Bir ileti sınıfı *(Data/Message.cs)* tarafından iki `Id` özelliği `Text` olan açıklanır: (anahtar) ve (ileti). Özellik `Text` gereklidir ve 200 karakterle sınırlıdır.
* İletiler [Entity Framework'ün bellek veritabanı](/ef/core/providers/in-memory/)&#8224; kullanılarak depolanır.
* Uygulama, veritabanı bağlam sınıfında bir veri erişim katmanı `AppDbContext` (DAL) içerir(*Data/AppDbContext.cs*).
* Uygulama nın başlatılmasında veritabanı boşsa, ileti deposu üç iletiyle açılır.
* Uygulama, yalnızca `/SecurePage` kimliği doğrulanmış bir kullanıcı tarafından erişilebilen bir uygulama içerir.

&#8224;EF konusu, [InMemory ile Test](/ef/core/miscellaneous/testing/in-memory), MSTest ile testler için bellek veritabanı nasıl kullanılacağını açıklar. Bu konu [xUnit](https://xunit.github.io/) test çerçevesini kullanır. Farklı test çerçeveleri arasındaki test kavramları ve test uygulamaları benzerdir, ancak aynı değildir.

Uygulama depo deseni kullanmasa ve [Çalışma Birimi (UoW) deseninin](https://martinfowler.com/eaaCatalog/unitOfWork.html)etkili bir örneği olmasa da, Razor Pages bu geliştirme modellerini destekler. Daha fazla bilgi için bkz: [Altyapı kalıcılığı katmanını](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) ve [Test denetleyicisi mantığını](/aspnet/core/mvc/controllers/testing) tasarlama (örnek depo deseni uygular).

### <a name="test-app-organization"></a>Test uygulaması organizasyonu

Test *uygulaması, testler/RazorPagesProject.Tests* dizininin içindeki bir konsol uygulamasıdır.

| Test uygulaması dizini | Açıklama |
| ------------------ | ----------- |
| *AuthTests* | Aşağıdakiler için test yöntemleri içerir:<ul><li>Kimliği doğrulanmamış bir kullanıcı tarafından güvenli bir sayfaya erişme.</li><li>Sahte kimlik doğrulaması yapılan bir kullanıcı tarafından <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>güvenli bir sayfaya erişme.</li><li>GitHub kullanıcı profili edinme ve profilin kullanıcı oturum açma denetimini.</li></ul> |
| *Temel Testler* | Yönlendirme ve içerik türü için bir test yöntemi içerir. |
| *Entegrasyon Testleri* | Özel `WebApplicationFactory` sınıf kullanarak Dizin sayfası için tümleştirme testlerini içerir. |
| *Yardımcılar/Yardımcı Programlar* | <ul><li>*Utilities.cs,* `InitializeDbForTests` veritabanını test verileriyle tohumlamak için kullanılan yöntemi içerir.</li><li>*HtmlHelpers.cs* test yöntemleri tarafından kullanılmak `IHtmlDocument` üzere bir AngleSharp döndürmek için bir yöntem sağlar.</li><li>*HttpClientExtensions.cs* SUT'ye istek göndermek için `SendAsync` aşırı yükleme sağlar.</li></ul> |

Test çerçevesi [xUnit'tir.](https://xunit.github.io/) Tümleştirme testleri [Microsoft.AspNetCore.TestHost](/dotnet/api/microsoft.aspnetcore.testhost)kullanılarak yapılır , [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)içerir. [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) paketi test ana bilgisayarını ve test sunucusunu `TestHost` `TestServer` yapılandırmak için kullanıldığından, ve paketler test uygulamasının proje dosyasında veya test uygulamasındaki geliştirici yapılandırmasında doğrudan paket başvuruları gerektirmez.

**Test için veritabanıtohumlama**

Tümleştirme testleri genellikle test yürütmeönce veritabanında küçük bir veri kümesi gerektirir. Örneğin, bir silme testi veritabanı kaydısilme yitirmesini gerektirdiğinden, veritabanının silme isteğinin başarılı olması için en az bir kaydı olmalıdır.

Örnek uygulama *veritabanını,* testlerin yürütüldüğünde kullanabileceği Utilities.cs üç iletiyle tohumlar:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/Helpers/Utilities.cs?name=snippet1)]

SUT'nin veritabanı bağlamı kendi `Startup.ConfigureServices` yönteminde kayıtlıdır. Test uygulamasının `builder.ConfigureServices` geri araması, uygulamanın `Startup.ConfigureServices` kodu yürütüldükten *sonra* yürütülür. Testler için farklı bir veritabanı kullanmak için uygulamanın veritabanı bağlamı ' nda `builder.ConfigureServices`değiştirilmelidir. Daha fazla bilgi için [WebApplicationFactory'i Özelleştir](#customize-webapplicationfactory) bölümüne bakın.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Tümleştirme testleri, uygulamanın bileşenlerinin veritabanı, dosya sistemi ve ağ gibi uygulamanın destekleyici altyapısını içeren bir düzeyde düzgün çalışmasını sağlar. ASP.NET Core, bir test web ana bilgisayarı ve bellek içi test sunucusu içeren bir birim test çerçevesi kullanarak tümleştirme testlerini destekler.

Bu konu, birim testleri temel bir anlayış varsayar. Test kavramlarını bilmiyorsanız, [.NET Core ve .NET Standart](/dotnet/core/testing/) konusundaki Birim Testi'ne ve bağlantılı içeriğine bakın.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ( nasıl[indirilir](xref:index#how-to-download-a-sample))

Örnek uygulama bir Razor Pages uygulamasıdır ve Razor Pages'in temel bir anlayışını varsayar. Razor Pages'e aşina değilseniz aşağıdaki konulara bakın:

* [Razor Pages’e giriş](xref:razor-pages/index)
* [Razor Sayfaları kullanmaya başlama](xref:tutorials/razor-pages/razor-pages-start)
* [Razor Pages birim testleri](xref:test/razor-pages-tests)

> [!NOTE]
> SP'leri test etmek için, bir tarayıcıyı otomatikleştirebilen [Selenyum](https://www.seleniumhq.org/)gibi bir araç önerdik.

## <a name="introduction-to-integration-tests"></a>Entegrasyon testlerine giriş

Tümleştirme testleri, bir uygulamanın bileşenlerini [birim testlerinden](/dotnet/core/testing/)daha geniş bir düzeyde değerlendirir. Birim testleri, tek tek sınıf yöntemleri gibi yalıtılmış yazılım bileşenlerini sınamak için kullanılır. Tümleştirme testleri, iki veya daha fazla uygulama bileşeninin beklenen sonucu elde etmek için birlikte çalıştığını ve muhtemelen bir isteği tam olarak işlemek için gereken her bileşeni de içeren bir sonucu onaylar.

Bu daha geniş testler, genellikle aşağıdaki bileşenler de dahil olmak üzere uygulamanın altyapısını ve tüm çerçevesini test etmek için kullanılır:

* Veritabanı
* Dosya sistemi
* Ağ gereçleri
* İstek-yanıt ardışık

Birim testleri, altyapı bileşenleri yerine *sahte* veya *sahte nesneler*olarak bilinen fabrikasyon bileşenler kullanır.

Birim testlerinin aksine, tümleştirme testleri:

* Uygulamanın üretimde kullandığı gerçek bileşenleri kullanın.
* Daha fazla kod ve veri işleme gerektirir.
* Koşması daha uzun sürer.

Bu nedenle, tümleştirme testlerinin kullanımını en önemli altyapı senaryoları ile sınırlandırın. Bir davranış birim testi veya tümleştirme testi kullanılarak test edilebilirse, birim testini seçin.

> [!TIP]
> Veritabanları ve dosya sistemleri ile veri ve dosya erişimi olası her permütasyon için entegrasyon testleri yazmayın. Bir uygulamanın veritabanları ve dosya sistemleriyle etkileşimde olduğu yerden kaç yerde olursa olsun, okuma, yazma, güncelleme ve silme tümleştirme testlerinin odaklanmış bir kümesi genellikle veritabanı ve dosya sistemi bileşenlerini yeterince test etme yeteneğine sahiptir. Bu bileşenlerle etkileşimedebilen yöntem mantığının rutin testleri için birim testleri kullanın. Birim testlerinde, altyapı sahtelerinin/alaylarının kullanımı daha hızlı test yürütülmesine neden olabilir.

> [!NOTE]
> Tümleştirme testlerinde, test edilen proje sık sık *Test Altında Sistem*veya kısaca "SUT" olarak adlandırılır.
>
> *"SUT" test ASP.NET Core uygulamasına başvurmak için bu konu boyunca kullanılır.*

## <a name="aspnet-core-integration-tests"></a>ASP.NET Çekirdek entegrasyon testleri

ASP.NET Core'daki tümleştirme testleri aşağıdakileri gerektirir:

* Testleri içeren ve yürütmek için bir test projesi kullanılır. Test projesinin SUT'ye bir başvurusu vardır.
* Test projesi SUT için bir test web ana bilgisayarı oluşturur ve SUT ile istek ve yanıtları işlemek için bir test sunucusu istemcisi kullanır.
* Testleri yürütmek ve test sonuçlarını bildirmek için bir test koşucusu kullanılır.

Tümleştirme testleri, olağan *Düzenleme,* *Hareket*ve Test adımlarını *öne* süriçeren bir dizi olay izleyin:

1. SUT'nin web barındırma alanı yapılandırılır.
1. Uygulamaya istek göndermek için bir test sunucusu istemcisi oluşturulur.
1. *Test düzenle* adımı yürütülür: Test uygulaması bir istek hazırlar.
1. *Act* test adımı yürütülür: İstemci isteği gönderir ve yanıtı alır.
1. *Assert* test adımı yürütülür: *Gerçek* yanıt *beklenen* yanıta bağlı olarak *bir geçiş* veya *başarısız* olarak doğrulanır.
1. Tüm testler yürütülene kadar işlem devam ediyor.
1. Test sonuçları bildirilir.

Genellikle, test web ana bilgisayarı, test çalışır için uygulamanın normal web ana bilgisayardan farklı şekilde yapılandırılır. Örneğin, testler için farklı bir veritabanı veya farklı uygulama ayarları kullanılabilir.

Test web ana bilgisayarı ve bellek içi test sunucusu[(TestServer)](/dotnet/api/microsoft.aspnetcore.testhost.testserver)gibi altyapı bileşenleri [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) paketi tarafından sağlanır veya yönetilir. Bu paketin kullanımı test oluşturma ve yürütme kolaylaştırır.

`Microsoft.AspNetCore.Mvc.Testing` Paket aşağıdaki görevleri işler:

* Bağımlılıklar dosyasını (*.deps)* SUT'dan test projesinin *depo* gözü dizinine kopyalar.
* İçerik [kökünü](xref:fundamentals/index#content-root) SUT'un proje köküne ayarlar, böylece testler yürütüldüğünde statik dosyaların ve sayfaların/görünümlerin bulunur.
* SUT ile bootstrapping kolaylaştırmak için [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) sınıfı `TestServer`sağlar.

[Birim test](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) belgeleri, bir test projesinin ve test koşucununun nasıl ayarlayacağıyla birlikte, testlerin nasıl adlandırılacacağına ve sınıfları nasıl adlandıracağınızla ilgili ayrıntılı yönergelerin yanı sıra açıklar.

> [!NOTE]
> Bir uygulama için test projesi oluştururken, birim testlerini tümleştirme testlerinden farklı projelere ayırın. Bu, altyapı sınama bileşenlerinin yanlışlıkla birim testlerine dahil olmamasını sağlamaya yardımcı olur. Birim ve tümleştirme testlerinin ayrılması, hangi test kümesinin çalıştırıldığı üzerinde denetim sağlar.

Razor Pages uygulamalarının testleri için yapılandırma ile MVC uygulamaları arasında neredeyse hiçbir fark yoktur. Tek fark, testlerin nasıl adlandırıldığının fark edilir. Bir Razor Pages uygulamasında, sayfa bitiş noktalarının testleri genellikle sayfa modeli `IndexPageTests` sınıfından (örneğin, Dizin sayfası için bileşen tümleştirmesini test etmek için) adlarını verilmiştir. Bir MVC uygulamasında, testler genellikle denetleyici sınıfları tarafından düzenlenir ve test `HomeControllerTests` ettikleri denetleyicilerden (örneğin, Ev denetleyicisi için bileşen tümleştirmesini test etmek için) adlandırılmıştır.

## <a name="test-app-prerequisites"></a>Ön koşulları test edin

Test projesi şunları yapmalıdır:

* Aşağıdaki paketlere başvurun:
  * [Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)
  * [Microsoft.AspNetCore.Mvc.Test](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing/)
* Proje dosyasında Web SDK'yı belirtin (`<Project Sdk="Microsoft.NET.Sdk.Web">`). Web SDK [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app)başvururken gereklidir.

Bu ön koşullar [örnek uygulamada](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/)görülebilir. Testleri *inceleyin/RazorPagesProject.Tests/RazorPagesProject.Tests.csproj* dosyası. Örnek uygulama [xUnit](https://xunit.github.io/) test çerçevesini ve [AngleSharp](https://anglesharp.github.io/) parser kitaplığını kullanır, bu nedenle örnek uygulama da başvurur:

* [xunit](https://www.nuget.org/packages/xunit/)
* [xunit.runner.visualstudio](https://www.nuget.org/packages/xunit.runner.visualstudio/)
* [Açı Keskin](https://www.nuget.org/packages/AngleSharp/)

## <a name="sut-environment"></a>SUT ortamı

SUT'nin [ortamı](xref:fundamentals/environments) ayarlı değilse, ortam Geliştirme için varsayılan dır.

## <a name="basic-tests-with-the-default-webapplicationfactory"></a>Varsayılan WebApplicationFactory ile temel testler

[WebApplicationFactory\<TEntryPoint>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) tümleştirme testleri için bir [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) oluşturmak için kullanılır. `TEntryPoint`SUT'nin giriş noktası sınıfıdır, `Startup` genellikle sınıftır.

Test sınıfları, sınıfın testler içerdiğini belirtmek ve sınıftaki testler arasında paylaşılan nesne örnekleri sağlamak için bir *sınıf fikstür* arabirimi[(IClassFixture)](https://xunit.github.io/docs/shared-context#class-fixture)uygular.

Aşağıdaki test sınıfı, `BasicTests`SUT bootstrap ve bir test yöntemi için bir [HttpClient](/dotnet/api/system.net.http.httpclient) sağlamak için kullanır. `Get_EndpointsReturnSuccessAndCorrectContentType` `WebApplicationFactory` Yöntem, yanıt durum kodunun başarılı olup olmadığını (200-299 aralığındaki durum kodları) denetler ve `Content-Type` üstbilgi birkaç uygulama sayfası içindir. `text/html; charset=utf-8`

[CreateClient,](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) otomatik olarak `HttpClient` yeniden yönlendirmeleri izleyen ve tanımlama bilgilerini işleyen bir örnek oluşturur.

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet1)]

Varsayılan olarak, [GDPR onay ilkesi](xref:security/gdpr) etkinleştirildiğinde, gerekli olmayan tanımlama bilgileri istekler arasında korunmaz. TempData sağlayıcısı tarafından kullanılanlar gibi gerekli olmayan tanımlama bilgilerini korumak için, bunları testlerinizde gerekli olarak işaretleyin. Bir çerezi temel olarak işaretleme yönergeleri için [Temel tanımlama bilgilerine](xref:security/gdpr#essential-cookies)bakın.

## <a name="customize-webapplicationfactory"></a>WebApplicationFactory'yi Özelleştir

Web ana bilgisayar yapılandırması, bir veya daha `WebApplicationFactory` fazla özel fabrika oluşturmak için devralınarak test sınıflarından bağımsız olarak oluşturulabilir:

1. `WebApplicationFactory` [ConfigureWebHost'tan](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost)devralma ve geçersiz kılma. [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices)ile hizmet toplama yapılandırma sağlar:

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/CustomWebApplicationFactory.cs?name=snippet1)]

   [Örnek uygulamadaki](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) veritabanı tohumlama `InitializeDbForTests` yöntemi ile gerçekleştirilir. Yöntem, [Tümleştirme testleri örneğinde açıklanmıştır: Test uygulaması organizasyonu](#test-app-organization) bölümü.

2. Test sınıflarında özel `CustomWebApplicationFactory` kullanın. Aşağıdaki örnek, sınıftaki `IndexPageTests` fabrikayı kullanır:

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet1)]

   Örnek uygulamanın istemcisi aşağıdaki yönlendirmeleri önlemek `HttpClient` için yapılandırılır. Daha sonra [Mock kimlik doğrulama](#mock-authentication) bölümünde açıklandığı gibi, bu testlerin uygulamanın ilk yanıtının sonucunu kontrol etmelerine izin verir. İlk yanıt, bu testlerin çoğunda üstbilgi `Location` yle yeniden yönlendirmedir.

3. Tipik bir test, isteği `HttpClient` ve yanıtı işlemek için yardımcı ve yardımcı yöntemleri kullanır:

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet2)]

SUT herhangi bir POST isteği otomatik olarak uygulamanın [veri koruma antiforgery sistemi](xref:security/data-protection/introduction)tarafından yapılan antiforgery kontrol karşılamak gerekir. Bir testin POST isteğini ayarlamak için test uygulamasının aşağıdakileri yapması gerekir:

1. Sayfa için bir istekte bulunun.
1. Antiforgery çerezini ayrıştırın ve yanıttan doğrulama belirteci isteyin.
1. Antiforgery çerezi ile POST isteğinde bulunun ve yerinde doğrulama belirteci isteyin.

`SendAsync` Yardımcı uzantı yöntemleri *(Yardımcıları / HttpClientExtensions.cs*) ve `GetDocumentAsync` yardımcı yöntem (*Yardımcılar / HtmlHelpers.cs*) [örnek uygulama](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) aşağıdaki yöntemlerle antiforgery kontrol işlemek için [AngleSharp](https://anglesharp.github.io/) parser kullanın:

* `GetDocumentAsync`&ndash; [HttpResponseMessage'ı](/dotnet/api/system.net.http.httpresponsemessage) alır `IHtmlDocument`ve bir . `GetDocumentAsync`orijinaline `HttpResponseMessage`dayalı sanal bir *yanıt* hazırlayan bir fabrika kullanır. Daha fazla bilgi için [AngleSharp belgelerine](https://github.com/AngleSharp/AngleSharp#documentation)bakın.
* `SendAsync`bir `HttpClient` [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) oluşturmak için uzantı yöntemleri ve SUT isteklerini göndermek için [SendAsync (HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) arayın. HTML formunu `SendAsync` kabul etmek`IHtmlFormElement`için aşırı yüklemeler ( ) ve aşağıdakiler:
  * Formun gönder düğmesi`IHtmlElement`( )
  * Form değerleri`IEnumerable<KeyValuePair<string, string>>`toplama ( )
  * Gönder düğmesi`IHtmlElement`( )`IEnumerable<KeyValuePair<string, string>>`ve form değerleri ( )

> [!NOTE]
> [AngleSharp,](https://anglesharp.github.io/) bu konu ve örnek uygulamada gösteri amacıyla kullanılan üçüncü taraf ayrıştırma kitaplığıdır. AngleSharp, ASP.NET Core uygulamalarının entegrasyon testleri için desteklenmez veya gerekli değildir. [Html Çeviklik Paketi (HAP)](https://html-agility-pack.net/)gibi diğer ayrıştırıcılar kullanılabilir. Başka bir yaklaşım doğrudan antiforgery sisteminin istek doğrulama belirteci ve antiforgery çerez işlemek için kod yazmaktır.

## <a name="customize-the-client-with-withwebhostbuilder"></a>WithWebHostBuilder ile istemciyi özelleştirin

Ek yapılandırma bir test yöntemi içinde gerekli olduğunda, [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) daha fazla yapılandırma tarafından özelleştirilmiş bir `WebApplicationFactory` [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) ile yeni bir oluşturur.

Örnek `Post_DeleteMessageHandler_ReturnsRedirectToRoot` [uygulamanın](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) test yöntemi. `WithWebHostBuilder` Bu test, SUT'da bir form göndermesini tetikleyerek veritabanında bir kayıt silme gerçekleştirir.

Sınıftaki başka `IndexPageTests` bir sınama veritabanındaki tüm kayıtları silen ve `Post_DeleteMessageHandler_ReturnsRedirectToRoot` yöntemden önce çalıştırılabilen bir işlem gerçekleştirdiğinden, SUT'nin silmesi için bir kaydın bulunduğundan emin olmak için veritabanı bu sınama yönteminde yeniden düzenlenir. SUT'deki formun `messages` ilk silme düğmesini seçmek SUT'a istekte simüle edilir:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet3)]

## <a name="client-options"></a>İstemci seçenekleri

Aşağıdaki tablo, örnek oluştururken `HttpClient` kullanılabilen varsayılan [WebApplicationFactoryClientOptions'ı](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) gösterir.

| Seçenek | Açıklama | Varsayılan |
| ------ | ----------- | ------- |
| [İzin AutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | Örneklerin yönlendirme yanıtlarını otomatik olarak izlemesi gerekip gerekmediğini `HttpClient` alır veya ayarlar. | `true` |
| [Baseaddress](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | Örneklerin `HttpClient` temel adresini alır veya ayarlar. | `http://localhost` |
| [Kulp kurabiyeleri](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | Örneklerin tanımlama `HttpClient` bilgilerini işlemesi gerekip gerekmediğini alır veya ayarlar. | `true` |
| [MaxAutomaticRedirections](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | `HttpClient` Örneklerin izlemesi gereken en fazla yönlendirme yanıtı sayısını alır veya ayarlar. | 7 |

`WebApplicationFactoryClientOptions` Sınıfı oluşturun ve [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) yöntemine geçirin (varsayılan değerler kod örneğinde gösterilir):

```csharp
// Default client option values are shown
var clientOptions = new WebApplicationFactoryClientOptions();
clientOptions.AllowAutoRedirect = true;
clientOptions.BaseAddress = new Uri("http://localhost");
clientOptions.HandleCookies = true;
clientOptions.MaxAutomaticRedirections = 7;

_client = _factory.CreateClient(clientOptions);
```

## <a name="inject-mock-services"></a>Sahte hizmetleri enjekte edin

Hizmetler, ana bilgisayar oluşturucuda [ConfigureTestServices'e](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) yapılandırılan bir çağrıyla bir testte geçersiz kılınabilir. **Sahte hizmetleri enjekte etmek için, `Startup` SUT'nin bir `Startup.ConfigureServices` yönteme sahip bir sınıfı olmalıdır.**

Örnek SUT, bir teklifi döndüren kapsamlı bir hizmet içerir. Teklif, Dizin sayfasında dizin sayfası istendiğinde Dizin sayfasındaki gizli bir alana gömülür.

*Hizmetler/IQuoteService.cs*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/IQuoteService.cs?name=snippet1)]

*Hizmetler/QuoteService.cs*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/QuoteService.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet2)]

*Sayfalar/Index.cshtml.cs*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml.cs?name=snippet1&highlight=4,9,20,26)]

*Sayfalar/Index.cs*:

[!code-cshtml[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml?name=snippet_Quote)]

SUT uygulaması çalıştırıldığında aşağıdaki biçimlendirme oluşturulur:

```html
<input id="quote" type="hidden" value="Come on, Sarah. We&#x27;ve an appointment in 
    London, and we&#x27;re already 30,000 years late.">
```

Bir entegrasyon testinde hizmeti test etmek ve enjeksiyondan alıntı yapmak için, test tarafından SUT'ye sahte bir hizmet enjekte edilir. Sahte hizmet, uygulamanın `QuoteService` yerine test uygulaması tarafından sağlanan bir `TestQuoteService`hizmetin yerini alır:

*IntegrationTests.IndexPageTests.cs*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet4)]

`ConfigureTestServices`denir ve kapsamlı hizmet kaydedilir:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet5&highlight=7-10,17,20-21)]

Testin yürütülmesi sırasında üretilen biçimlendirme, tarafından `TestQuoteService`sağlanan teklif metnini yansıtır, böylece iddia geçer:

```html
<input id="quote" type="hidden" value="Something&#x27;s interfering with time, 
    Mr. Scarman, and time is my business.">
```

## <a name="mock-authentication"></a>Sahte kimlik doğrulama

Sınıftaki `AuthTests` testler güvenli bir bitiş noktası olup olmadığını denetler:

* Kimliği doğrulanmamış bir kullanıcıyı uygulamanın Giriş sayfasına yönlendirir.
* Kimliği doğrulanmış bir kullanıcı için içerik verir.

SUT'de, sayfa, sayfaya bir [AuthorizeFilter](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) uygulamak için bir [AuthorizePage](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) kuralı kullanır. `/SecurePage` Daha fazla bilgi için [Bkz. Razor Pages yetkilendirme kuralları.](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page)

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet1)]

Testte, `Get_SecurePageRedirectsAnUnauthenticatedUser` [Bir WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) ayarlayarak yeniden yönlendirmeleri izin `false`vermeyecek şekilde ayarlanır:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet2)]

İstemcinin yönlendirmeyi izlemesine izin verilerek aşağıdaki denetimler yapılabilir:

* SUT tarafından döndürülen durum kodu, [HttpStatusCode.OK](/dotnet/api/system.net.httpstatuscode)olacak Login sayfasına yönlendirildikten sonra son durum kodu na göre değil, beklenen [HttpStatusCode.Redirect](/dotnet/api/system.net.httpstatuscode) sonucuyla karşıkontrol edilebilir.
* Yanıt `Location` üstbilgisindeki üstbilgi değeri, `http://localhost/Identity/Account/Login` `Location` üstbilginin bulunmadığı son Giriş sayfası yanıtıyla değil, ile başladığını doğrulamak için denetlenir.

Test uygulaması, kimlik <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> doğrulama ve yetkilendirme yönlerini test etmek için [ConfigureTestServices'teki](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) bir uygulamayla alay edebilir. En az senaryo [Bir AuthenticateResult.Success](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*)döndürür:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet4&highlight=11-18)]

Kimlik `TestAuthHandler` doğrulama düzeni şu şekilde kayıtlı olduğu `Test` yere `AddAuthentication` ayarlandığında kullanıcının `ConfigureTestServices`kimliğini doğrulamak için çağrılır:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet3&highlight=7-12)]

Hakkında daha `WebApplicationFactoryClientOptions`fazla bilgi için [İstemci seçenekleri](#client-options) bölümüne bakın.

## <a name="set-the-environment"></a>Ortamı ayarlama

Varsayılan olarak, SUT'nin ana bilgisayar ve uygulama ortamı Geliştirme ortamını kullanacak şekilde yapılandırılır. SUT'nin ortamını geçersiz kılmak için:

* Ortam `ASPNETCORE_ENVIRONMENT` değişkenini `Staging`(örneğin, `Production`, , veya diğer `Testing`özel değer gibi) ayarlayın.
* Önceden `CreateHostBuilder` belirlenmiş `ASPNETCORE`ortam değişkenlerini okumak için test uygulamasında geçersiz kılma

```csharp
protected override IHostBuilder CreateHostBuilder() => 
    base.CreateHostBuilder()
        .ConfigureHostConfiguration(
            config => config.AddEnvironmentVariables("ASPNETCORE"));
```

## <a name="how-the-test-infrastructure-infers-the-app-content-root-path"></a>Test altyapısı uygulama içeriği kök yolunu nasıl çıkar

Oluşturucu, `WebApplicationFactory` `TEntryPoint` derlemeye `System.Reflection.Assembly.FullName`eşit bir anahtarla tümleştirme testlerini içeren derlemede bir [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) arayarak uygulama [içeriği kök](xref:fundamentals/index#content-root) yolunu çıkar. Doğru anahtara sahip bir öznitelik bulunamazsa, `WebApplicationFactory` çözüm dosyası *(.sln)* aramaya geri `TEntryPoint` döner ve derleme adını çözüm dizinine ekler. Uygulama kök dizini (içerik kökü yolu) görünümleri ve içerik dosyalarını keşfetmek için kullanılır.

## <a name="disable-shadow-copying"></a>Gölge kopyalamayı devre dışı bırak

Gölge kopyalama, testlerin çıktı dizininden farklı bir dizinde yürütülmesine neden olur. Testlerin düzgün çalışması için gölge kopyalamadevre dışı bırakılmalıdır. [Örnek uygulama](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) xUnit kullanır ve doğru yapılandırma ayarına sahip bir *xunit.runner.json* dosyası ekleyerek xUnit için gölge kopyalamadevre dışı bırakır. Daha fazla bilgi için bkz: [XUnit'i JSON ile yapılandırma.](https://xunit.github.io/docs/configuring-with-json.html)

*Xunit.runner.json* dosyasını aşağıdaki içerikle test projesinin köküne ekleyin:

```json
{
  "shadowCopy": false
}
```

Visual Studio kullanıyorsanız, dosyanın **Kopyala'yı Çıkış Dizini** özelliğine **her zaman kopyalamak**için ayarlayın. Visual Studio kullanmıyorsanız, `Content` test uygulamasının proje dosyasına bir hedef ekleyin:

```xml
<ItemGroup>
  <Content Update="xunit.runner.json">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
  </Content>
</ItemGroup>
```

## <a name="disposal-of-objects"></a>Nesnelerin atılması

`IClassFixture` Uygulama testleri uygulandıktan sonra, [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) ve [HttpClient](/dotnet/api/system.net.http.httpclient) xUnit [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1)elden zaman bertaraf edilir. Geliştirici tarafından anında verilen nesneler elden çıkarılması gerekiyorsa, bunları `IClassFixture` uygulamada atın. Daha fazla bilgi için [bkz.](/dotnet/standard/garbage-collection/implementing-dispose)

## <a name="integration-tests-sample"></a>Entegrasyon testleri örneği

[Örnek uygulama](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) iki uygulamadan oluşur:

| Uygulama | Proje dizini | Açıklama |
| --- | ----------------- | ----------- |
| İleti uygulaması (SUT) | *src/RazorPagesProject* | Kullanıcının ileti eklemesine, silmesine, tümünü silmesine ve çözümlemesine olanak tanır. |
| Test uygulaması | *testler/RazorPagesProject.Tests* | SUT'yi tümleştirme testinde kullanılır. |

Testler Visual [Studio](https://visualstudio.microsoft.com)gibi bir IDE'nin yerleşik test özellikleri kullanılarak çalıştırılabilir. Visual [Studio Code](https://code.visualstudio.com/) veya komut satırı kullanıyorsanız, *testler/RazorPagesProject.Tests* dizininde aşağıdaki komut istemini uygulayın:

```dotnetcli
dotnet test
```

### <a name="message-app-sut-organization"></a>İleti uygulaması (SUT) organizasyonu

SUT, aşağıdaki özelliklere sahip bir Razor Pages mesaj sistemidir:

* Uygulamanın Dizin sayfası *(Pages/Index.cshtml* ve *Pages/Index.cshtml.cs)* iletilerin eklenmesini, silinmesini ve çözümlemesini (ileti başına ortalama sözcük) denetlemek için bir kullanıcı aracı ve sayfa modeli yöntemleri sağlar.
* `Message` Bir ileti sınıfı *(Data/Message.cs)* tarafından iki `Id` özelliği `Text` olan açıklanır: (anahtar) ve (ileti). Özellik `Text` gereklidir ve 200 karakterle sınırlıdır.
* İletiler [Entity Framework'ün bellek veritabanı](/ef/core/providers/in-memory/)&#8224; kullanılarak depolanır.
* Uygulama, veritabanı bağlam sınıfında bir veri erişim katmanı `AppDbContext` (DAL) içerir(*Data/AppDbContext.cs*).
* Uygulama nın başlatılmasında veritabanı boşsa, ileti deposu üç iletiyle açılır.
* Uygulama, yalnızca `/SecurePage` kimliği doğrulanmış bir kullanıcı tarafından erişilebilen bir uygulama içerir.

&#8224;EF konusu, [InMemory ile Test](/ef/core/miscellaneous/testing/in-memory), MSTest ile testler için bellek veritabanı nasıl kullanılacağını açıklar. Bu konu [xUnit](https://xunit.github.io/) test çerçevesini kullanır. Farklı test çerçeveleri arasındaki test kavramları ve test uygulamaları benzerdir, ancak aynı değildir.

Uygulama depo deseni kullanmasa ve [Çalışma Birimi (UoW) deseninin](https://martinfowler.com/eaaCatalog/unitOfWork.html)etkili bir örneği olmasa da, Razor Pages bu geliştirme modellerini destekler. Daha fazla bilgi için bkz: [Altyapı kalıcılığı katmanını](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) ve [Test denetleyicisi mantığını](/aspnet/core/mvc/controllers/testing) tasarlama (örnek depo deseni uygular).

### <a name="test-app-organization"></a>Test uygulaması organizasyonu

Test *uygulaması, testler/RazorPagesProject.Tests* dizininin içindeki bir konsol uygulamasıdır.

| Test uygulaması dizini | Açıklama |
| ------------------ | ----------- |
| *AuthTests* | Aşağıdakiler için test yöntemleri içerir:<ul><li>Kimliği doğrulanmamış bir kullanıcı tarafından güvenli bir sayfaya erişme.</li><li>Sahte kimlik doğrulaması yapılan bir kullanıcı tarafından <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>güvenli bir sayfaya erişme.</li><li>GitHub kullanıcı profili edinme ve profilin kullanıcı oturum açma denetimini.</li></ul> |
| *Temel Testler* | Yönlendirme ve içerik türü için bir test yöntemi içerir. |
| *Entegrasyon Testleri* | Özel `WebApplicationFactory` sınıf kullanarak Dizin sayfası için tümleştirme testlerini içerir. |
| *Yardımcılar/Yardımcı Programlar* | <ul><li>*Utilities.cs,* `InitializeDbForTests` veritabanını test verileriyle tohumlamak için kullanılan yöntemi içerir.</li><li>*HtmlHelpers.cs* test yöntemleri tarafından kullanılmak `IHtmlDocument` üzere bir AngleSharp döndürmek için bir yöntem sağlar.</li><li>*HttpClientExtensions.cs* SUT'ye istek göndermek için `SendAsync` aşırı yükleme sağlar.</li></ul> |

Test çerçevesi [xUnit'tir.](https://xunit.github.io/) Tümleştirme testleri [Microsoft.AspNetCore.TestHost](/dotnet/api/microsoft.aspnetcore.testhost)kullanılarak yapılır , [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)içerir. [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) paketi test ana bilgisayarını ve test sunucusunu `TestHost` `TestServer` yapılandırmak için kullanıldığından, ve paketler test uygulamasının proje dosyasında veya test uygulamasındaki geliştirici yapılandırmasında doğrudan paket başvuruları gerektirmez.

**Test için veritabanıtohumlama**

Tümleştirme testleri genellikle test yürütmeönce veritabanında küçük bir veri kümesi gerektirir. Örneğin, bir silme testi veritabanı kaydısilme yitirmesini gerektirdiğinden, veritabanının silme isteğinin başarılı olması için en az bir kaydı olmalıdır.

Örnek uygulama *veritabanını,* testlerin yürütüldüğünde kullanabileceği Utilities.cs üç iletiyle tohumlar:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/Helpers/Utilities.cs?name=snippet1)]

::: moniker-end

## <a name="additional-resources"></a>Ek kaynaklar

* [Birim testleri](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* <xref:test/razor-pages-tests>
* <xref:fundamentals/middleware/index>
* <xref:mvc/controllers/testing>
