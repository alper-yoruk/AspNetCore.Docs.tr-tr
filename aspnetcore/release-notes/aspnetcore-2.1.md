---
title: ASP.NET Core 2.1'deki yenilikler
author: isaac2004
description: ASP.NET Core 2.1'deki yeni özellikler hakkında bilgi edinin.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- SignalR
uid: aspnetcore-2.1
ms.openlocfilehash: af5807b782d4acec8c7d40111dc508dfa6127057
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667547"
---
# <a name="whats-new-in-aspnet-core-21"></a>ASP.NET Core 2.1'deki yenilikler

Bu makalede, ilgili belgelere bağlantılar ile ASP.NET Core 2.1 en önemli değişiklikleri vurgulamaktadır.

## SignalR

SignalRCore 2.1 ASP.NET için yeniden yazılmıştır. ASP.NET SignalR Core bir dizi iyileştirme içerir:

* Basitleştirilmiş bir ölçeklendirme modeli.
* JQuery bağımlılığı olmayan yeni bir JavaScript istemcisi.
* MessagePack tabanlı yeni bir kompakt ikili protokol.
* Özel protokoller için destek.
* Yeni bir akış yanıt modeli.
* Çıplak WebSockets dayalı istemciler için destek.

Daha fazla bilgi için [core SignalRASP.NET ](xref:signalr/introduction)bakın.

## <a name="razor-class-libraries"></a>Razor sınıf kitaplıkları

ASP.NET Core 2.1, bir kitaplıkta Jilet tabanlı uI oluşturmayı ve eklemeyi ve birden çok proje arasında paylaşmayı kolaylaştırır. Yeni Razor SDK, NuGet paketine paketlenebilen bir sınıf kitaplığı projesine Razor dosyaları oluşturmayı sağlar. Kitaplıktaki görünümler ve sayfalar otomatik olarak keşfedilir ve uygulama tarafından geçersiz kılınabilir. Razor derlemesini yapıya entegre ederek:

* Uygulama başlatma süresi önemli ölçüde daha hızlıdır.
* Yinelemeli geliştirme iş akışının bir parçası olarak, çalışma zamanında Kizizatgörünümleri ve sayfalarına yönelik hızlı güncelleştirmeler hala kullanılabilir.

Daha fazla bilgi için, [Razor Class Kitaplığı projesini kullanarak yeniden kullanılabilir kullanıcı arabirimi oluşturma'ya](xref:razor-pages/ui-class)bakın.

## <a name="identity-ui-library--scaffolding"></a>Kimlik UI kitaplığı & iskele

ASP.NET Core 2.1, Razor [Sınıf Kitaplığı](xref:razor-pages/ui-class)olarak [ASP.NET Çekirdek Kimlik](xref:security/authentication/identity) sağlar. Kimlik içeren uygulamalar, Kimlik Jilet Sınıf Kitaplığı'nda (RCL) bulunan kaynak kodunu seçarak eklemek için yeni Kimlik iskelesini uygulayabilir. Kodu değiştirebilmek ve davranışı değiştirmek için kaynak kodu oluşturmak isteyebilirsiniz. Örneğin, iskele klasörüne kayıtta kullanılan kodu oluşturması için talimat verebilirsiniz. Oluşturulan kod, Identity RCL'de aynı koddan önce gelir.

Kimlik **doğrulamaiçermeyen** uygulamalar, RCL Identity paketini eklemek için Kimlik iskelesini uygulayabilir. Oluşturulacak Kimlik kodu seçme seçeneğiniz vardır.

Daha fazla bilgi için [ASP.NET Core projelerinde İskele Kimliği'ne](xref:security/authentication/scaffold-identity)bakın.

## <a name="https"></a>HTTPS

Güvenlik ve gizlilik üzerindeki artan odak la birlikte, web uygulamaları için HTTPS'yi etkinleştirmek önemlidir. HTTPS uygulama web üzerinde giderek daha sıkı hale gelmektedir. HTTPS kullanmayan siteler güvensiz olarak kabul edilir. Tarayıcılar (Krom, Mozilla) web özelliklerinin güvenli bir bağlamda kullanılması gerektiğini zorlamaya başlıyor. [GDPR,](xref:security/gdpr) kullanıcı gizliliğini korumak için HTTPS kullanımını gerektirir. Üretimde HTTPS kullanmak kritik olmakla birlikte, https'yi geliştirme de dağıtımdaki sorunları (örneğin, güvensiz bağlantılar) önlemeye yardımcı olabilir. ASP.NET Core 2.1, HTTPS'nin geliştirme aşamasında kullanılmasını ve üretimde HTTPS'yi yapılandırmayı kolaylaştıran bir dizi iyileştirme içerir. Daha fazla bilgi için [bkz.](xref:security/enforcing-ssl)

### <a name="on-by-default"></a>Varsayılan olarak

Güvenli web sitesi geliştirmeyi kolaylaştırmak için, HTTPS artık varsayılan olarak etkinleştirilir. 2.1'den başlayarak, Kestrel yerel bir geliştirme sertifikasının `https://localhost:5001` ne zaman mevcut olduğunu dinler. Geliştirme sertifikası oluşturulur:

* .NET Core SDK ilk çalıştırma deneyiminin bir parçası olarak, SDK'yı ilk kez kullandığınızda.
* Yeni `dev-certs` aracı el ile kullanma.

Sertifikaya güvenmek için çalıştırın. `dotnet dev-certs https --trust`

### <a name="https-redirection-and-enforcement"></a>HTTPS yeniden yönlendirme ve uygulama

Web uygulamalarının genellikle hem HTTP hem de HTTPS'yi dinlemesi gerekir, ancak daha sonra tüm HTTP trafiğini HTTPS'ye yönlendirir. 2.1'de, yapılandırma veya bağlı sunucu bağlantı noktalarının varlığına göre akıllıca yeniden yönlendiren özel LEŞTIRILMIŞ HTTPS yeniden yönlendirme ara ware'i kullanıma sunulmuştur.

HTTPS kullanımı HTTP Sıkı [Taşıma Güvenlik Protokolü (HSTS)](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts)kullanılarak daha da zorlanabilir. HSTS, tarayıcılara siteye her zaman HTTPS üzerinden erişmelerini bildirir. ASP.NET Core 2.1, maksimum yaş, alt etki alanları ve HSTS ön yükleme listesi seçeneklerini destekleyen HSTS ara yazılımlarını ekler.

### <a name="configuration-for-production"></a>Üretim için yapılandırma

Üretimde, HTTPS açıkça yapılandırılmalıdır. 2.1'de, Kestrel için HTTPS yapılandırmak için varsayılan yapılandırma şeması eklenmiştir. Uygulamalar şu şekilde yapılandırılabilir:

* URL'ler de dahil olmak üzere birden çok uç nokta. Daha fazla bilgi için [Kestrel web sunucusu uygulamasına bakın: Bitiş Noktası yapılandırması.](xref:fundamentals/servers/kestrel#endpoint-configuration)
* HTTPS için kullanılacak sertifika diskteki bir dosyadan veya bir sertifika deposundan.

## <a name="gdpr"></a>GDPR

ASP.NET Core, [AB Genel Veri Koruma Yönetmeliği (GDPR)](https://www.eugdpr.org/) gerekliliklerinin bazılarını karşılamaya yardımcı olmak için API'ler ve şablonlar sağlar. Daha fazla bilgi için [ASP.NET Core'daki GDPR desteğine](xref:security/gdpr)bakın. Örnek bir [uygulama](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) nasıl kullanılacağını gösterir ve ASP.NET Core 2.1 şablonlarına eklenen GDPR uzantı noktalarının ve API'lerin çoğunu test etmenizi sağlar.

## <a name="integration-tests"></a>Tümleştirme testleri

Test oluşturma ve yürütmeyi kolaylaştıran yeni bir paket tanıtıldı. [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing/) paketi aşağıdaki görevleri işler:

* Bağımlılık dosyasını (*\*.deps)* test uygulamasından test projesinin *çöp kutusu* klasörüne kopyalar.
* Testler yürütüldüğünde statik dosyaların ve sayfaların/görünümlerin bulunması için içerik kökünü test edilen uygulamanın proje köküne ayarlar.
* [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)ile test edilen uygulamayı yeniden düzene sokmak için [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) sınıfını sağlar.

Aşağıdaki test, Dizin sayfasının başarı durum kodu ve doğru İçerik Türü üstbilgisi ile yükleyip yüklemediğini kontrol etmek için [xUnit'i](https://xunit.github.io/) kullanır:

```csharp
public class BasicTests
    : IClassFixture<WebApplicationFactory<RazorPagesProject.Startup>>
{
    private readonly HttpClient _client;

    public BasicTests(WebApplicationFactory<RazorPagesProject.Startup> factory)
    {
        _client = factory.CreateClient();
    }

    [Fact]
    public async Task GetHomePage()
    {
        // Act
        var response = await _client.GetAsync("/");

        // Assert
        response.EnsureSuccessStatusCode(); // Status Code 200-299
        Assert.Equal("text/html; charset=utf-8",
            response.Content.Headers.ContentType.ToString());
    }
}
```

Daha fazla bilgi için [Tümleştirme testleri](xref:test/integration-tests) konusuna bakın.

## <a name="apicontroller-actionresultt"></a>[ApiController], ActionResult\<T>

ASP.NET Core 2.1, temiz ve açıklayıcı web API'leri oluşturmayı kolaylaştıran yeni programlama kuralları ekler. `ActionResult<T>`bir uygulamanın yanıt türünü veya başka bir eylem sonucunu (IActionResult'a benzer) döndürmesine izin vermek için eklenen yeni bir türdür. Öznitelik, `[ApiController]` Web API'ye özgü kuralları ve davranışları seçmenin yolu olarak da eklenmiştir.

Daha fazla bilgi için ASP.NET [Core ile Web API'leri Oluştur'a](xref:web-api/index)bakın.

## <a name="ihttpclientfactory"></a>ihttpclientfactory

ASP.NET Core 2.1, `IHttpClientFactory` `HttpClient` uygulamalardaki örnekleri yapılandırmayı ve tüketmeyi kolaylaştıran yeni bir hizmet içerir. `HttpClient`zaten giden HTTP istekleri için birbirine bağlanabilir işleyicileri delegating kavramı vardır. Fabrika:

* Adlandırılmış istemci `HttpClient` başına örneklerinin kaydedilmesi daha sezgisel hale getirir.
* Polly politikalarının Retry, CircuitBreakers, vb. için kullanılmasını sağlayan bir Polly işleyicisi uygular.

Daha fazla bilgi için http [isteklerini başlat'a](xref:fundamentals/http-requests)bakın.

## <a name="kestrel-transport-configuration"></a>Kerkenez taşıma yapılandırması

Core 2.1ASP.NET piyasaya sürülmesiyle, Kestrel'in varsayılan taşıması artık Libuv'a değil, yönetilen soketlere dayanmaktadır. Daha fazla bilgi için [Kestrel web sunucusu uygulamasına bakın: Aktarım yapılandırması.](xref:fundamentals/servers/kestrel#transport-configuration)

## <a name="generic-host-builder"></a>Genel ana bilgisayar oluşturucu

Genel Ana Bilgisayar`HostBuilder`Oluşturucu ( ) tanıtıldı. Bu oluşturucu, HTTP isteklerini işlemeyan uygulamalar (Mesajlaşma, arka plan görevleri, vb.) için kullanılabilir.

Daha fazla bilgi için [bkz.](xref:fundamentals/host/generic-host)

## <a name="updated-spa-templates"></a>Güncelleştirilmiş SPA şablonları

Açısal, Tepki ve Redux ile Tepki için Tek Sayfauygulaması şablonları, her bir çerçeve için standart proje yapılarını ve yapı sistemlerini kullanmak üzere güncelleştirilir.

Açısal şablon Açısal CLI'yi, Tepki şablonları ise create-react-app'ı temel adatır.

Daha fazla bilgi için bkz.

* <xref:spa/angular>
* <xref:spa/react>
* <xref:spa/react-with-redux>

## <a name="razor-pages-search-for-razor-assets"></a>Razor Pages, Razor varlıkları arama

2.1'de, Razor Pages listelenen sırada aşağıdaki dizinlerde Razor varlıklarını (düzenler ve kısmiler gibi) arar:

1. Geçerli Sayfalar klasörü.
1. */Sayfalar/Paylaşılan/*
1. */Görünümler/Paylaşılanlar/*

## <a name="razor-pages-in-an-area"></a>Bir alandaki Jilet Sayfaları

Razor Pages artık [alanları](xref:mvc/controllers/areas)destekliyor. Alanların bir örneğini görmek için, tek tek kullanıcı hesaplarıiçeren yeni bir Razor Pages web uygulaması oluşturun. Bireysel kullanıcı hesapları ile bir Razor Pages web uygulaması */ Alanlar / Kimlik / Sayfalar*içerir.

## <a name="mvc-compatibility-version"></a>MVC uyumluluk sürümü

Yöntem, <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> bir uygulamanın Core MVC 2.1 veya sonraki ASP.NET'da tanıtılan olası ihlal davranış değişikliklerini kabul etmesine veya devre dışı bırakmasına olanak tanır.

Daha fazla bilgi için bkz. <xref:mvc/compatibility-version>.

## <a name="migrate-from-20-to-21"></a>2,0'dan 2,1'e geçiş

Bkz. [ASP.NET Çekirdek 2.0'dan 2.1'e geçir.](xref:migration/20_21)

## <a name="additional-information"></a>Ek bilgiler

Değişikliklerin tam listesi [için, ASP.NET Core 2.1 Sürüm Notları](https://github.com/dotnet/aspnetcore/releases/tag/2.1.0)bölümüne bakın.
