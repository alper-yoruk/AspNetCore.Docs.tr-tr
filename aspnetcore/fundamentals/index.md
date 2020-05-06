---
title: ASP.NET Core temelleri
author: rick-anderson
description: ASP.NET Core uygulamalar oluşturmaya yönelik temel kavramları öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/30/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/index
ms.openlocfilehash: 0f0e97246b6e1381b85866bd831ee9b4b150650d
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774333"
---
# <a name="aspnet-core-fundamentals"></a>ASP.NET Core temelleri

::: moniker range=">= aspnetcore-3.0"

Bu makalede, ASP.NET Core uygulamaları geliştirmeyi anlamak için temel konulara genel bakış sunulmaktadır.

## <a name="the-startup-class"></a>Başlangıç sınıfı

`Startup` Sınıfı şu konumda:

* Uygulamanın gerektirdiği hizmetler yapılandırıldı.
* Uygulamanın istek işleme işlem hattı, bir dizi ara yazılım bileşeni olarak tanımlanmıştır.

Örnek `Startup` bir sınıf aşağıda verilmiştir:

[!code-csharp[](index/samples_snapshot/3.x/Startup.cs?highlight=3,12)]

Daha fazla bilgi için bkz. <xref:fundamentals/startup>.

## <a name="dependency-injection-services"></a>Bağımlılık ekleme (hizmetler)

ASP.NET Core, yapılandırılmış Hizmetleri bir uygulama genelinde kullanılabilir hale getiren yerleşik bağımlılık ekleme (dı) çerçevesini içerir. Örneğin, bir günlük bileşeni bir hizmettir.

Hizmetleri yapılandırmak (veya *kaydettirmek*) için kod `Startup.ConfigureServices` yöntemine eklenmiştir. Örneğin:

[!code-csharp[](index/samples_snapshot/3.x/ConfigureServices.cs)]

Hizmetler genellikle Oluşturucu Ekleme kullanılarak dı 'den çözümlenir. Oluşturucu ekleme ile bir sınıf, gerekli türde veya bir arabirimin Oluşturucu parametresini bildirir. Dı çerçevesi, çalışma zamanında bu hizmetin bir örneğini sağlar.

Aşağıdaki örnek, `RazorPagesMovieContext` from kaynağından çözümlemek için Oluşturucu Ekleme kullanır:

[!code-csharp[](index/samples_snapshot/3.x/Index.cshtml.cs?highlight=5)]

Yerleşik bir denetim (IOC) kapsayıcısının bir uygulamanın ihtiyaçlarını karşılamıyorsa, bunun yerine üçüncü taraf bir IOC kapsayıcısı kullanılabilir.

Daha fazla bilgi için bkz. <xref:fundamentals/dependency-injection>.

## <a name="middleware"></a>Ara yazılım

İstek işleme işlem hattı, bir dizi ara yazılım bileşeni olarak oluşur. Her bileşen bir üzerinde işlemler gerçekleştirir `HttpContext` ve sıradaki bir sonraki ara yazılımı çağırır ya da isteği sonlandırır.

Kurala göre, `Use...` `Startup.Configure` yöntemde bir genişletme yöntemi çağırarak işlem hattına bir ara yazılım bileşeni eklenir. Örneğin, statik dosyaları işlemeyi etkinleştirmek için çağırın `UseStaticFiles`.

Aşağıdaki örnekte bir istek işleme işlem hattı yapılandırılır:

[!code-csharp[](index/samples_snapshot/3.x/Configure.cs)]

ASP.NET Core zengin bir yerleşik ara yazılım kümesi içerir. Özel ara yazılım bileşenleri de yazılabilir.

Daha fazla bilgi için bkz. <xref:fundamentals/middleware/index>.

## <a name="host"></a>Ana bilgisayar

Başlangıçta ASP.NET Core bir uygulama bir *konak*oluşturur. Konak, uygulamanın tüm kaynaklarını kapsüller, örneğin:

* Bir HTTP sunucusu uygulama
* Ara yazılım bileşenleri
* Günlüğe Kaydetme
* Bağımlılık ekleme (dı) Hizmetleri
* Yapılandırma

İki farklı ana bilgisayar vardır: 

* .NET genel ana bilgisayar
* ASP.NET Core Web ana bilgisayarı

.NET genel ana bilgisayarı önerilir. ASP.NET Core Web konağı yalnızca geriye dönük uyumluluk için kullanılabilir.

Aşağıdaki örnek bir .NET genel ana bilgisayarı oluşturur:

[!code-csharp[](index/samples_snapshot/3.x/Program.cs)]

`CreateDefaultBuilder` Ve `ConfigureWebHostDefaults` yöntemleri, bir ana bilgisayarı varsayılan bir seçenek kümesiyle yapılandırır, örneğin:

* Web sunucusu olarak [Kestrel](#servers) kullanın ve IIS tümleştirmesini etkinleştirin.
* *AppSettings. JSON*, appSettings 'ten yapılandırma yükleyin *. { Ortam adı}. JSON*, ortam değişkenleri, komut satırı bağımsız değişkenleri ve diğer yapılandırma kaynakları.
* Günlüğe kaydetme çıkışını konsola ve hata ayıklama sağlayıcılarına gönderin.

Daha fazla bilgi için bkz. <xref:fundamentals/host/generic-host>.

### <a name="non-web-scenarios"></a>Web dışı senaryolar

Genel ana bilgisayar, diğer uygulama türlerinin günlüğe kaydetme, bağımlılık ekleme (dı), yapılandırma ve uygulama ömür yönetimi gibi çapraz kesme çerçevesi uzantıları kullanmasına izin verir. Daha fazla bilgi için <xref:fundamentals/host/generic-host> ve <xref:fundamentals/host/hosted-services> bölümlerine bakın.

## <a name="servers"></a>Sunucular

Bir ASP.NET Core uygulaması HTTP isteklerini dinlemek için HTTP sunucu uygulamasını kullanır. Sunucu, bir `HttpContext`öğesine oluşturulan [istek özellikleri](xref:fundamentals/request-features) kümesi olarak uygulamaya istekleri ister.

# <a name="windows"></a>[Windows](#tab/windows)

ASP.NET Core aşağıdaki sunucu uygulamalarını sağlar:

* *Kestrel* , platformlar arası bir Web sunucusudur. Kestrel, genellikle [IIS](https://www.iis.net/)kullanılarak ters bir ara sunucu yapılandırmasında çalıştırılır. ASP.NET Core 2,0 veya üzeri sürümlerde, Kestrel doğrudan Internet 'e açık olan bir genel kullanıma yönelik uç sunucu olarak çalıştırılabilir.
* *IIS HTTP sunucusu* , IIS kullanan bir Windows sunucusudur. Bu sunucu ile, ASP.NET Core uygulaması ve IIS aynı işlemde çalışır.
* *Http. sys* , IIS ile kullanılmayan bir Windows sunucusudur.

# <a name="macos"></a>[Mac OS](#tab/macos)

ASP.NET Core, *Kestrel* platformlar arası sunucu uygulamasını sağlar. ASP.NET Core 2,0 veya üzeri sürümlerde, Kestrel doğrudan Internet 'Te kullanıma sunulan bir genel kullanıma yönelik uç sunucu olarak çalışabilir. Kestrel, genellikle [NGINX](https://nginx.org) veya [Apache](https://httpd.apache.org/)ile ters proxy yapılandırmasında çalıştırılır.

# <a name="linux"></a>[Linux](#tab/linux)

ASP.NET Core, *Kestrel* platformlar arası sunucu uygulamasını sağlar. ASP.NET Core 2,0 veya üzeri sürümlerde, Kestrel doğrudan Internet 'Te kullanıma sunulan bir genel kullanıma yönelik uç sunucu olarak çalışabilir. Kestrel, genellikle [NGINX](https://nginx.org) veya [Apache](https://httpd.apache.org/)ile ters proxy yapılandırmasında çalıştırılır.

---

Daha fazla bilgi için bkz. <xref:fundamentals/servers/index>.

## <a name="configuration"></a>Yapılandırma

ASP.NET Core, ayarları sıralı bir yapılandırma sağlayıcıları kümesinden ad-değer çiftleri olarak alan bir yapılandırma çerçevesi sağlar. Yerleşik yapılandırma sağlayıcıları *. JSON* dosyaları, *. xml* dosyaları, ortam değişkenleri ve komut satırı bağımsız değişkenleri gibi çeşitli kaynaklar için kullanılabilir. Diğer kaynakları desteklemek için özel yapılandırma sağlayıcıları yazın.

[Varsayılan](xref:fundamentals/configuration/index#default)olarak, ASP.NET Core uygulamalar *appSettings. JSON*, ortam değişkenleri, komut satırı ve daha fazlasını okumak üzere yapılandırılır. Uygulamanın yapılandırması yüklendiğinde, ortam değişkenlerinin değerleri *appSettings. JSON*' dan değerleri geçersiz kılar.

İlgili yapılandırma değerlerini okumak için tercih edilen yol, [Seçenekler modelini](xref:fundamentals/configuration/options)kullanmaktır. Daha fazla bilgi için bkz. [Seçenekler modelini kullanarak hiyerarşik yapılandırma verilerini bağlama](xref:fundamentals/configuration/index#optpat).

Parolalar gibi gizli yapılandırma verilerini yönetmek için, ASP.NET Core [gizli dizi Yöneticisi](xref:security/app-secrets#secret-manager)sağlar. Üretim gizli dizileri için [Azure Key Vault](xref:security/key-vault-configuration)önerilir.

Daha fazla bilgi için bkz. <xref:fundamentals/configuration/index>.

## <a name="environments"></a>Ortamlar

, Ve `Production`gibi yürütme ortamları `Development` `Staging`ASP.NET Core ilk sınıf kavramlardır. `ASPNETCORE_ENVIRONMENT` Ortam değişkenini ayarlayarak bir uygulamanın çalıştığı ortamı belirtin. ASP.NET Core, uygulamanın başlangıcında bu ortam değişkenini okur ve değeri bir `IWebHostEnvironment` uygulamada depolar. Bu uygulama, bağımlılık ekleme (dı) yoluyla bir uygulamada herhangi bir yerde kullanılabilir.

Aşağıdaki örnek, uygulamayı `Development` ortamda çalışırken ayrıntılı hata bilgileri sunacak şekilde yapılandırır:

[!code-csharp[](index/samples_snapshot/3.x/StartupConfigure.cs?highlight=3-6)]

Daha fazla bilgi için bkz. <xref:fundamentals/environments>.

## <a name="logging"></a>Günlüğe Kaydetme

ASP.NET Core, çeşitli yerleşik ve üçüncü taraf günlük sağlayıcılarıyla birlikte çalışarak bir günlüğe kaydetme API 'sini destekler. Kullanılabilir sağlayıcılar şunları içerir:

* Konsol
* Hata ayıklama
* Windows üzerinde olay Izleme
* Windows olay günlüğü
* TraceSource
* Azure App Service
* Azure Application Insights

Günlükler oluşturmak için, bir hizmeti <xref:Microsoft.Extensions.Logging.ILogger%601> bağımlılık ekleme (dı) ve çağrı günlüğü yöntemlerinden bir hizmete çözümleyin <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>. Örneğin:

[!code-csharp[](index/samples_snapshot/3.x/TodoController.cs?highlight=5,13,19)]

Gibi günlüğe kaydetme yöntemleri `LogInformation` , herhangi bir sayıda alanı destekler. Bu alanlar yaygın olarak bir ileti `string`oluşturmak için kullanılır, ancak bazı günlük sağlayıcıları bunları ayrı alanlar olarak bir veri deposuna gönderir. Bu özellik, günlük sağlayıcılarının [yapılandırılmış günlük olarak da bilinen anlam günlüğü](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)uygulamasına olanak tanır.

Daha fazla bilgi için bkz. <xref:fundamentals/logging/index>.

## <a name="routing"></a>Yönlendirme

*Yol* , bir işleyiciye EŞLENMIŞ bir URL örüncidir. İşleyici genellikle bir Razor sayfası, MVC denetleyicisindeki bir eylem yöntemi veya bir ara yazılım olur. ASP.NET Core yönlendirme, uygulamanız tarafından kullanılan URL 'Ler üzerinde denetim sağlar.

Daha fazla bilgi için bkz. <xref:fundamentals/routing>.

## <a name="error-handling"></a>Hata işleme

ASP.NET Core, hataları işlemeye yönelik yerleşik özellikler içerir, örneğin:

* Geliştirici özel durum sayfası
* Özel hata sayfaları
* Statik durum kodu sayfaları
* Başlatma özel durum işleme

Daha fazla bilgi için bkz. <xref:fundamentals/error-handling>.

## <a name="make-http-requests"></a>HTTP isteğinde bulunma

Uygulamasının bir uygulamasına `IHttpClientFactory` örnek oluşturmak `HttpClient` için kullanılabilir. Fabrika:

* , Mantıksal `HttpClient` örnekleri adlandırmak ve yapılandırmak için merkezi bir konum sağlar. Örneğin, GitHub 'a erişmek için bir *GitHub* istemcisini kaydedin ve yapılandırın. Varsayılan bir istemciyi başka amaçlar için kaydedin ve yapılandırın.
* , Bir giden istek ara yazılım işlem hattı oluşturmak için birden çok temsilci seçme işleyicisinin kaydını ve zincirlemeyi destekler. Bu düzen ASP.NET Core gelen ara yazılım ardışık düzenine benzer. Bu model, önbelleğe alma, hata işleme, serileştirme ve günlüğe kaydetme dahil olmak üzere HTTP istekleri için çapraz kesme sorunlarını yönetmek için bir mekanizma sağlar.
* Geçici hata işleme için popüler bir üçüncü taraf kitaplığı olan *Polly*ile tümleşir.
* Yaşam sürelerini el ile yönetirken `HttpClientHandler` `HttpClient` oluşan yaygın DNS sorunlarından kaçınmak için temeldeki örneklerin biriktirmesini ve ömrünü yönetir.
* Fabrika tarafından oluşturulan istemciler aracılığıyla gönderilen <xref:Microsoft.Extensions.Logging.ILogger> tüm istekler için aracılığıyla yapılandırılabilir bir günlük deneyimi ekler.

Daha fazla bilgi için bkz. <xref:fundamentals/http-requests>.

## <a name="content-root"></a>İçerik kökü

İçerik kökü, için temel yoldur:

* Uygulamayı barındıran yürütülebilir dosya (*. exe*).
* Uygulamayı oluşturan derlenmiş derlemeler (*. dll*).
* Uygulama tarafından kullanılan içerik dosyaları, örneğin:
  * Razor dosyaları (*. cshtml*, *. Razor*)
  * Yapılandırma dosyaları (*. JSON*, *. xml*)
  * Veri dosyaları (*. db*)
* [Web kökü](#web-root), genellikle *Wwwroot* klasörü.

Geliştirme sırasında, içerik kökü, projenin kök dizinini varsayılan olarak belirler. Bu dizin Ayrıca uygulamanın içerik dosyaları ve [Web kökünün](#web-root)temel yoludur. [Konağı oluştururken](#host)yolunu ayarlayarak farklı bir içerik kökü belirtin. Daha fazla bilgi için bkz. [içerik kökü](xref:fundamentals/host/generic-host#contentroot).

## <a name="web-root"></a>Web kökü

Web kökü, genel, statik kaynak dosyaları için temel yoldur, örneğin:

* Stil sayfaları (*. css*)
* JavaScript (*. js*)
* Görüntüler (*. png*, *. jpg*)

Varsayılan olarak, statik dosyalar yalnızca Web kök dizininden ve alt dizinlerinden sunulur. Web kök yolu, varsayılan olarak *{Content root}/Wwwroot*olarak belirlenmiştir. [Konağı oluştururken](#host)yolunu ayarlayarak farklı bir Web kökü belirtin. Daha fazla bilgi için bkz. [Web root](xref:fundamentals/host/generic-host#webroot).

Proje dosyasındaki [ \<içerik> proje öğesi](/visualstudio/msbuild/common-msbuild-project-items#content) ile *Wwwroot* 'da dosya yayımlamayı önleyin. Aşağıdaki örnek, *Wwwroot/yerel* ve alt dizinlerinde içerik yayımlamayı engeller:

```xml
<ItemGroup>
  <Content Update="wwwroot\local\**\*.*" CopyToPublishDirectory="Never" />
</ItemGroup>
```

Razor *. cshtml* dosyalarında, tilde-eğik çizgi (`~/`) Web köküne işaret eder. İle `~/` başlayan bir yol, *sanal yol*olarak adlandırılır.

Daha fazla bilgi için bkz. <xref:fundamentals/static-files>.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Bu makale, ASP.NET Core uygulamaları geliştirmeyi anlamak için önemli konulara genel bakış sağlar.

## <a name="the-startup-class"></a>Başlangıç sınıfı

`Startup` Sınıfı şu konumda:

* Uygulamanın gerektirdiği hizmetler yapılandırıldı.
* İstek işleme işlem hattı tanımlandı.

*Hizmetler* , uygulama tarafından kullanılan bileşenlerdir. Örneğin, bir günlük bileşeni bir hizmettir. Hizmetleri yapılandırmak (veya *kaydettirmek*) için kod `Startup.ConfigureServices` yöntemine eklenmiştir.

İstek işleme işlem hattı, bir dizi *Ara yazılım* bileşeni olarak oluşur. Örneğin, bir ara yazılım statik dosyalar için istekleri işleyebilir veya HTTP isteklerini HTTPS 'ye yeniden yönlendirebilir. Her bir ara yazılım bir `HttpContext` üzerinde zaman uyumsuz işlemler gerçekleştirir ve ardından işlem hattında sonraki ara yazılımı çağırır veya isteği sonlandırır. İstek işleme işlem hattını yapılandırma kodu `Startup.Configure` yöntemine eklenir.

Örnek `Startup` bir sınıf aşağıda verilmiştir:

[!code-csharp[](index/samples_snapshot/2.x/Startup.cs?highlight=3,12)]

Daha fazla bilgi için bkz. <xref:fundamentals/startup>.

## <a name="dependency-injection-services"></a>Bağımlılık ekleme (hizmetler)

ASP.NET Core, yapılandırılmış hizmetlerin bir uygulamanın sınıfları tarafından kullanılabilmesini sağlayan yerleşik bir bağımlılık ekleme (dı) çerçevesine sahiptir. Bir sınıftaki hizmetin bir örneğini almanın bir yolu, gerekli türde bir parametreye sahip bir Oluşturucu oluşturmaktır. Parametresi hizmet türü veya arabirim olabilir. Dı sistemi, çalışma zamanında hizmeti sağlar.

İşte bir Entity Framework Core bağlam nesnesi almak için DI kullanan bir sınıf. Vurgulanan çizgi bir Oluşturucu Ekleme örneğidir:

[!code-csharp[](index/samples_snapshot/2.x/Index.cshtml.cs?highlight=5)]

Dı yerleşik olarak kullanılıyorsa, isterseniz Control (IOC) kapsayıcısının bir üçüncü taraf Inversion öğesini eklemenize olanak sağlamak üzere tasarlanmıştır.

Daha fazla bilgi için bkz. <xref:fundamentals/dependency-injection>.

## <a name="middleware"></a>Ara yazılım

İstek işleme işlem hattı, bir dizi ara yazılım bileşeni olarak oluşur. Her bileşen bir `HttpContext` üzerinde zaman uyumsuz işlemler gerçekleştirir ve ardından işlem hattında sonraki ara yazılımı çağırır veya isteği sonlandırır.

Kurala göre, `Use...` `Startup.Configure` yöntemi içindeki genişletme yöntemi çağrılarak işlem hattına bir ara yazılım bileşeni eklenir. Örneğin, statik dosyaları işlemeyi etkinleştirmek için çağırın `UseStaticFiles`.

Aşağıdaki örnekteki vurgulanan kod, istek işleme işlem hattını yapılandırır:

[!code-csharp[](index/samples_snapshot/2.x/Startup.cs?highlight=14-16)]

ASP.NET Core, zengin bir yerleşik ara yazılım kümesi içerir ve özel ara yazılım yazabilirsiniz.

Daha fazla bilgi için bkz. <xref:fundamentals/middleware/index>.

## <a name="host"></a>Ana bilgisayar

ASP.NET Core bir uygulama, başlangıçta bir *konak* oluşturur. Ana bilgisayar, uygulamanın tüm kaynaklarını kapsülleyen bir nesnedir, örneğin:

* Bir HTTP sunucusu uygulama
* Ara yazılım bileşenleri
* Günlüğe Kaydetme
* IÇERIK
* Yapılandırma

Uygulamanın tüm birbirine bağlı kaynaklarını tek bir nesnede dahil etmek için başlıca neden, yaşam süresi yönetimi: uygulama başlatma ve düzgün kapanma üzerinde denetim.

İki konak mevcuttur: Web Konağı ve genel ana bilgisayar. ASP.NET Core 2. x içinde, genel konak yalnızca Web dışı senaryolar içindir.

Bir konak oluşturma kodu `Program.Main`:

[!code-csharp[](index/samples_snapshot/2.x/Program.cs)]

`CreateDefaultBuilder` Yöntemi, aşağıdaki gibi yaygın olarak kullanılan seçeneklere sahip bir ana bilgisayar yapılandırır:

* Web sunucusu olarak [Kestrel](#servers) kullanın ve IIS tümleştirmesini etkinleştirin.
* *AppSettings. JSON*, appSettings 'ten yapılandırma yükleyin *. { Ortam adı}. JSON*, ortam değişkenleri, komut satırı bağımsız değişkenleri ve diğer yapılandırma kaynakları.
* Günlüğe kaydetme çıkışını konsola ve hata ayıklama sağlayıcılarına gönderin.

Daha fazla bilgi için bkz. <xref:fundamentals/host/web-host>.

### <a name="non-web-scenarios"></a>Web dışı senaryolar

Genel ana bilgisayar, diğer uygulama türlerinin günlüğe kaydetme, bağımlılık ekleme (dı), yapılandırma ve uygulama ömür yönetimi gibi çapraz kesme çerçevesi uzantıları kullanmasına izin verir. Daha fazla bilgi için <xref:fundamentals/host/generic-host> ve <xref:fundamentals/host/hosted-services> bölümlerine bakın.

## <a name="servers"></a>Sunucular

Bir ASP.NET Core uygulaması HTTP isteklerini dinlemek için HTTP sunucu uygulamasını kullanır. Sunucu, bir `HttpContext`öğesine oluşturulan [istek özellikleri](xref:fundamentals/request-features) kümesi olarak uygulamaya istekleri ister.

::: moniker-end

::: moniker range="= aspnetcore-2.2"

# <a name="windows"></a>[Windows](#tab/windows)

ASP.NET Core aşağıdaki sunucu uygulamalarını sağlar:

* *Kestrel* , platformlar arası bir Web sunucusudur. Kestrel, genellikle [IIS](https://www.iis.net/)kullanılarak ters bir ara sunucu yapılandırmasında çalıştırılır. Kestrel, doğrudan Internet 'e açık olan bir genel kullanıma yönelik uç sunucu olarak çalıştırılabilir.
* *IIS HTTP sunucusu* , IIS kullanan bir Windows sunucusudur. Bu sunucu ile, ASP.NET Core uygulaması ve IIS aynı işlemde çalışır.
* *Http. sys* , IIS ile kullanılmayan bir Windows sunucusudur.

# <a name="macos"></a>[Mac OS](#tab/macos)

ASP.NET Core, *Kestrel* platformlar arası sunucu uygulamasını sağlar. Kestrel, doğrudan Internet 'e açık olan bir genel kullanıma yönelik uç sunucu olarak çalıştırılabilir. Kestrel, genellikle [NGINX](https://nginx.org) veya [Apache](https://httpd.apache.org/)ile ters proxy yapılandırmasında çalıştırılır.

# <a name="linux"></a>[Linux](#tab/linux)

ASP.NET Core, *Kestrel* platformlar arası sunucu uygulamasını sağlar. Kestrel, doğrudan Internet 'e açık olan bir genel kullanıma yönelik uç sunucu olarak çalıştırılabilir. Kestrel, genellikle [NGINX](https://nginx.org) veya [Apache](https://httpd.apache.org/)ile ters proxy yapılandırmasında çalıştırılır.

---

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="windows"></a>[Windows](#tab/windows)

ASP.NET Core aşağıdaki sunucu uygulamalarını sağlar:

* *Kestrel* , platformlar arası bir Web sunucusudur. Kestrel, genellikle [IIS](https://www.iis.net/)kullanılarak ters bir ara sunucu yapılandırmasında çalıştırılır. Kestrel, doğrudan Internet 'e açık olan bir genel kullanıma yönelik uç sunucu olarak çalıştırılabilir.
* *Http. sys* , IIS ile kullanılmayan bir Windows sunucusudur.

# <a name="macos"></a>[Mac OS](#tab/macos)

ASP.NET Core, *Kestrel* platformlar arası sunucu uygulamasını sağlar. Kestrel, doğrudan Internet 'e açık olan bir genel kullanıma yönelik uç sunucu olarak çalıştırılabilir. Kestrel, genellikle [NGINX](https://nginx.org) veya [Apache](https://httpd.apache.org/)ile ters proxy yapılandırmasında çalıştırılır.

# <a name="linux"></a>[Linux](#tab/linux)

ASP.NET Core, *Kestrel* platformlar arası sunucu uygulamasını sağlar. Kestrel, doğrudan Internet 'e açık olan bir genel kullanıma yönelik uç sunucu olarak çalıştırılabilir. Kestrel, genellikle [NGINX](https://nginx.org) veya [Apache](https://httpd.apache.org/)ile ters proxy yapılandırmasında çalıştırılır.

---

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Daha fazla bilgi için bkz. <xref:fundamentals/servers/index>.

## <a name="configuration"></a>Yapılandırma

ASP.NET Core, ayarları sıralı bir yapılandırma sağlayıcıları kümesinden ad-değer çiftleri olarak alan bir yapılandırma çerçevesi sağlar. *. JSON* dosyaları, *. xml* dosyaları, ortam değişkenleri ve komut satırı bağımsız değişkenleri gibi çeşitli kaynaklar için yerleşik yapılandırma sağlayıcıları vardır. Ayrıca, özel yapılandırma sağlayıcıları da yazabilirsiniz.

Örneğin, yapılandırmanın *appSettings. JSON* ve ortam değişkenlerinden geldiğini belirtebilirsiniz. Ardından, *ConnectionString* değeri istendiğinde, Framework ilk olarak *appSettings. JSON* dosyasına bakar. Değer aynı zamanda bir ortam değişkeninde bulunursa, ortam değişkeninin değeri öncelikli olur.

Parolalar gibi gizli yapılandırma verilerini yönetmek için ASP.NET Core bir [gizli dizi Yöneticisi aracı](xref:security/app-secrets)sağlar. Üretim gizli dizileri için [Azure Key Vault](xref:security/key-vault-configuration)önerilir.

Daha fazla bilgi için bkz. <xref:fundamentals/configuration/index>.

## <a name="options"></a>Seçenekler

Mümkün olduğunda yapılandırma değerlerini depolamak ve almak için *Seçenekler deseninin* ASP.NET Core. Seçenekler stili, ilişkili ayarların gruplarını temsil etmek için sınıfları kullanır.

Örneğin, aşağıdaki kod WebSockets seçeneklerini ayarlar:

[!code-csharp[](index/samples_snapshot/2.x/UseWebSockets.cs)]

Daha fazla bilgi için bkz. <xref:fundamentals/configuration/options>.

## <a name="environments"></a>Ortamlar

*Geliştirme*, *hazırlık*ve *üretim*gibi yürütme ortamları, ASP.NET Core birinci sınıf kavramlardır. `ASPNETCORE_ENVIRONMENT` Ortam değişkenini ayarlayarak bir uygulamanın çalıştığı ortamı belirtebilirsiniz. ASP.NET Core, uygulamanın başlangıcında bu ortam değişkenini okur ve değeri bir `IHostingEnvironment` uygulamada depolar. Ortam nesnesi, uygulama tarafından her yerde DI aracılığıyla kullanılabilir.

`Startup` Sınıfından aşağıdaki örnek kod, uygulamayı yalnızca geliştirmede çalıştırıldığında ayrıntılı hata bilgilerini sunacak şekilde yapılandırır:

[!code-csharp[](index/samples_snapshot/2.x/StartupConfigure.cs?highlight=3-6)]

Daha fazla bilgi için bkz. <xref:fundamentals/environments>.

## <a name="logging"></a>Günlüğe Kaydetme

ASP.NET Core, çeşitli yerleşik ve üçüncü taraf günlük sağlayıcılarıyla birlikte çalışarak bir günlüğe kaydetme API 'sini destekler. Kullanılabilir sağlayıcılar şunları içerir:

* Konsol
* Hata ayıklama
* Windows üzerinde olay Izleme
* Windows olay günlüğü
* TraceSource
* Azure App Service
* Azure Application Insights

Dı ve çağrı günlüğü yöntemlerinden bir `ILogger` nesne alarak uygulamanın kodundaki her yerden günlükleri yazın.

Oluşturucu Ekleme ve günlük yöntemi çağrılarını vurgulanmış `ILogger` bir nesne kullanan örnek kod aşağıda verilmiştir.

[!code-csharp[](index/samples_snapshot/2.x/TodoController.cs?highlight=5,13,17)]

`ILogger` Arabirim, günlük sağlayıcısına istediğiniz sayıda alanı geçirmenize olanak sağlar. Alanlar genellikle bir ileti dizesi oluşturmak için kullanılır, ancak sağlayıcı bunları bir veri deposuna ayrı alanlar olarak da gönderebilir. Bu özellik, günlük sağlayıcılarının [yapılandırılmış günlük olarak da bilinen anlam günlüğü](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)uygulamasına olanak tanır.

Daha fazla bilgi için bkz. <xref:fundamentals/logging/index>.

## <a name="routing"></a>Yönlendirme

*Yol* , bir işleyiciye EŞLENMIŞ bir URL örüncidir. İşleyici genellikle bir Razor sayfa, MVC denetleyicisindeki bir eylem yöntemi veya bir ara yazılım olur. ASP.NET Core yönlendirme, uygulamanız tarafından kullanılan URL 'Ler üzerinde denetim sağlar.

Daha fazla bilgi için bkz. <xref:fundamentals/routing>.

## <a name="error-handling"></a>Hata işleme

ASP.NET Core, hataları işlemeye yönelik yerleşik özellikler içerir, örneğin:

* Geliştirici özel durum sayfası
* Özel hata sayfaları
* Statik durum kodu sayfaları
* Başlatma özel durum işleme

Daha fazla bilgi için bkz. <xref:fundamentals/error-handling>.

## <a name="make-http-requests"></a>HTTP isteğinde bulunma

Uygulamasının bir uygulamasına `IHttpClientFactory` örnek oluşturmak `HttpClient` için kullanılabilir. Fabrika:

* , Mantıksal `HttpClient` örnekleri adlandırmak ve yapılandırmak için merkezi bir konum sağlar. Örneğin, *GitHub istemcisi kayıtlı ve GitHub 'a* erişebilecek şekilde yapılandırılabilir. Varsayılan istemci, diğer amaçlar için kaydedilebilir.
* , Bir giden istek ara yazılım işlem hattı oluşturmak için birden çok temsilci seçme işleyicisinin kaydını ve zincirlemeyi destekler. Bu düzen, ASP.NET Core gelen ara yazılım ardışık düzenine benzer. Bu model, önbelleğe alma, hata işleme, serileştirme ve günlüğe kaydetme dahil olmak üzere HTTP istekleri etrafında çapraz kesme sorunlarını yönetmek için bir mekanizma sağlar.
* Geçici hata işleme için popüler bir üçüncü taraf kitaplığı olan *Polly*ile tümleşir.
* Yaşam sürelerini el ile yönetirken `HttpClientHandler` `HttpClient` gerçekleşen yaygın DNS sorunlarından kaçınmak için temeldeki örneklerin biriktirmesini ve ömrünü yönetir.
* Fabrika tarafından oluşturulan istemciler aracılığıyla gönderilen tüm `ILogger`istekler için yapılandırılabilir bir günlüğe kaydetme deneyimi ekler (aracılığıyla).

Daha fazla bilgi için bkz. <xref:fundamentals/http-requests>.

## <a name="content-root"></a>İçerik kökü

İçerik kökü, için temel yoldur:

* Uygulamayı barındıran yürütülebilir dosya (*. exe*).
* Uygulamayı oluşturan derlenmiş derlemeler (*. dll*).
* Uygulama tarafından kullanılan kod olmayan içerik dosyaları, örneğin:
  * Razordosyalar (*. cshtml*, *. Razor*)
  * Yapılandırma dosyaları (*. JSON*, *. xml*)
  * Veri dosyaları (*. db*)
* [Web kökü](#web-root), genellikle yayınlanan *Wwwroot* klasörü.

Geliştirme sırasında:

* İçerik kökü, projenin kök dizinini varsayılan olarak belirler.
* Projenin kök dizini şunu oluşturmak için kullanılır:
  * Uygulamanın, projenin kök dizinindeki kod olmayan içerik dosyalarının yolu.
  * [Web kökü](#web-root), genellikle projenin kök dizinindeki *Wwwroot* klasörü.

[Konak oluşturulurken](#host)alternatif bir içerik kök yolu belirtilebilir. Daha fazla bilgi için bkz. <xref:fundamentals/host/web-host#content-root>.

## <a name="web-root"></a>Web kökü

Web kökü, genel, kod olmayan statik kaynak dosyalarının temel yoludur, örneğin:

* Stil sayfaları (*. css*)
* JavaScript (*. js*)
* Görüntüler (*. png*, *. jpg*)

Statik dosyalar yalnızca Web kök dizininden (ve alt dizinlerde) varsayılan olarak sunulur.

Web kök yolu varsayılan olarak *{Content root}/Wwwroot*olarak belirlenir, ancak [konak oluşturulurken](#host)farklı bir Web kökü belirtilebilir. Daha fazla bilgi için bkz. [Web root](xref:fundamentals/host/web-host#web-root).

Proje dosyasındaki [ \<içerik> proje öğesi](/visualstudio/msbuild/common-msbuild-project-items#content) ile *Wwwroot* 'da dosya yayımlamayı önleyin. Aşağıdaki örnek, *Wwwroot/yerel* dizin ve alt dizinlerde içerik yayımlamayı engeller:

```xml
<ItemGroup>
  <Content Update="wwwroot\local\**\*.*" CopyToPublishDirectory="Never" />
</ItemGroup>
```

Razor (*. Cshtml*) dosyalarında, tilde eğik çizgi (`~/`) Web köküne işaret eder. İle `~/` başlayan bir yol, *sanal yol*olarak adlandırılır.

Daha fazla bilgi için bkz. <xref:fundamentals/static-files>.

::: moniker-end
