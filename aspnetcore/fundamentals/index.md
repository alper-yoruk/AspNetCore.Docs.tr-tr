---
title: ASP.NET Core temelleri
author: rick-anderson
description: ASP.NET Core uygulamaları oluşturmak için temel kavramları öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/30/2020
uid: fundamentals/index
ms.openlocfilehash: da2b42a7cf5d116a36d1dd9fa586d40ab31fc52d
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80417649"
---
# <a name="aspnet-core-fundamentals"></a>ASP.NET Core temelleri

::: moniker range=">= aspnetcore-3.0"

Bu makalede, ASP.NET Core uygulamalarının nasıl geliştirileni anlamak için önemli konulara genel bir bakış sunulmaktadır.

## <a name="the-startup-class"></a>Başlangıç sınıfı

Sınıf `Startup` nerede:

* Uygulamanın gerektirdiği hizmetler yapılandırılır.
* Uygulamanın istek işleme ardışık hattı, bir dizi ara yazılım bileşeni olarak tanımlanır.

İşte örnek bir `Startup` sınıf:

[!code-csharp[](index/samples_snapshot/3.x/Startup.cs?highlight=3,12)]

Daha fazla bilgi için bkz. <xref:fundamentals/startup>.

## <a name="dependency-injection-services"></a>Bağımlılık ekleme (hizmetler)

ASP.NET Core, yapılandırılmış hizmetleri bir uygulama boyunca kullanılabilir kılan yerleşik bir bağımlılık enjeksiyonu (DI) çerçevesi içerir. Örneğin, günlük bileşeni bir hizmettir.

Yapılandırma (veya *kayıt)* hizmetlerini yapılandırma kodu `Startup.ConfigureServices` yönteme eklenir. Örneğin:

[!code-csharp[](index/samples_snapshot/3.x/ConfigureServices.cs)]

Hizmetler genellikle constructor enjeksiyonu kullanılarak DI'den çözülür. Oluşturucu enjeksiyonile, bir sınıf gerekli tür veya arabirim bir yapıcı parametre bildirir. DI çerçevesi çalışma zamanında bu hizmetin bir örneğini sağlar.

Aşağıdaki örnek, DI'den bir `RazorPagesMovieContext` çözüm için yapıcı enjeksiyon kullanır:

[!code-csharp[](index/samples_snapshot/3.x/Index.cshtml.cs?highlight=5)]

Yerleşik Denetim Ters Çevirme (IoC) kapsayıcısı bir uygulamanın tüm gereksinimlerini karşılamazsa, bunun yerine bir üçüncü taraf IoC kapsayıcısı kullanılabilir.

Daha fazla bilgi için bkz. <xref:fundamentals/dependency-injection>.

## <a name="middleware"></a>Ara yazılım

İstek işleme ardışık bir dizi ara yazılım bileşeni olarak oluşturulur. Her bileşen bir `HttpContext` işlem gerçekleştirir ve ya ardışık bir sonraki ara çağırır veya isteği sona erdirir.

Kural kuralıyla, `Use...` `Startup.Configure` yöntemde bir uzantı yöntemi çağırılarak ardışık bir bileşen ardışık bileşen eklenir. Örneğin, statik dosyaların işlenmesini etkinleştirmek `UseStaticFiles`için .

Aşağıdaki örnek, bir istek işleme ardışık yapılandırır:

[!code-csharp[](index/samples_snapshot/3.x/Configure.cs)]

ASP.NET Core dahili ara yazılım zengin bir dizi içerir. Özel ara yazılım bileşenleri de yazılabilir.

Daha fazla bilgi için bkz. <xref:fundamentals/middleware/index>.

## <a name="host"></a>Host

Başlangıç tarihinde, ASP.NET Core uygulaması bir *ana bilgisayar*oluşturur. Ev sahibi, uygulamanın tüm kaynaklarını kapsüller:

* Bir HTTP sunucu uygulaması
* Ara yazılım bileşenleri
* Günlüğe kaydetme
* Bağımlılık enjeksiyonu (DI) hizmetleri
* Yapılandırma

İki farklı ana bilgisayar vardır: 

* .NET Genel Ana Bilgisayar
* ASP.NET Çekirdek Web Host

.NET Genel Ana Bilgisayar önerilir. ASP.NET Core Web Host yalnızca geriye dönük uyumluluk için kullanılabilir.

Aşağıdaki örnekte bir .NET Genel Ana Bilgisayar oluşturur:

[!code-csharp[](index/samples_snapshot/3.x/Program.cs)]

Ve `CreateDefaultBuilder` `ConfigureWebHostDefaults` yöntemler gibi varsayılan seçenekler kümesi ile bir ana bilgisayar yapılandırmak:

* [Kerkenez'i](#servers) web sunucusu olarak kullanın ve IIS tümleştirmesini etkinleştirin.
* *appsettings.json*, appsettings.{ adresinden yapılandırmayı *yükleyin. Çevre Adı}.json*, ortam değişkenleri, komut satırı bağımsız değişkenleri ve diğer yapılandırma kaynakları.
* Günlüğe kaydetme çıktısını konsola gönderin ve hata ayıklama sağlayıcılarına gönderin.

Daha fazla bilgi için bkz. <xref:fundamentals/host/generic-host>.

### <a name="non-web-scenarios"></a>Web dışı senaryolar

Genel Ana Bilgisayar, günlük, bağımlılık enjeksiyonu (DI), yapılandırma ve uygulama ömrü yönetimi gibi çapraz kesme çerçeve uzantılarını diğer uygulama türlerinin kullanmasına izin verir. Daha fazla bilgi için <xref:fundamentals/host/generic-host> ve <xref:fundamentals/host/hosted-services> bölümlerine bakın.

## <a name="servers"></a>Sunucular

Bir ASP.NET Core uygulaması, HTTP isteklerini dinlemek için bir HTTP sunucusu uygulaması kullanır. Sunucu, uygulamadaki [istekleri](xref:fundamentals/request-features) `HttpContext`bir .

# <a name="windows"></a>[Windows](#tab/windows)

ASP.NET Core aşağıdaki sunucu uygulamalarını sağlar:

* *Kerkenez* bir çapraz platform web sunucusudur. Kerkenez genellikle [IIS](https://www.iis.net/)kullanılarak ters proxy yapılandırmasında çalıştırılır. Core 2.0 veya daha sonra ASP.NET,Kestrel doğrudan Internet'e açık halka açık bir kenar sunucusu olarak çalıştırılabilir.
* *IIS HTTP Server,* Windows için IIS kullanan bir sunucudur. Bu sunucu ile ASP.NET Core uygulaması ve IIS aynı işlemde çalışır.
* *HTTP.sys,* Windows için IIS ile kullanılmayan bir sunucudur.

# <a name="macos"></a>[macOS](#tab/macos)

ASP.NET Core *Kestrel* çapraz platform sunucusu uygulaması sağlar. Core 2.0 veya daha sonra ASP.NET, Kestrel doğrudan Internet'e açık halka açık bir kenar sunucusu olarak çalıştırılabilir. Kerkenez genellikle [Nginx](https://nginx.org) veya [Apache](https://httpd.apache.org/)ile ters proxy yapılandırmaçalıştırılır.

# <a name="linux"></a>[Linux](#tab/linux)

ASP.NET Core *Kestrel* çapraz platform sunucusu uygulaması sağlar. Core 2.0 veya daha sonra ASP.NET, Kestrel doğrudan Internet'e açık halka açık bir kenar sunucusu olarak çalıştırılabilir. Kerkenez genellikle [Nginx](https://nginx.org) veya [Apache](https://httpd.apache.org/)ile ters proxy yapılandırmaçalıştırılır.

---

Daha fazla bilgi için bkz. <xref:fundamentals/servers/index>.

## <a name="configuration"></a>Yapılandırma

ASP.NET Core, sıralı bir yapılandırma sağlayıcısı kümesinden adi değer çiftleri olarak ayarları alan bir yapılandırma çerçevesi sağlar. Yerleşik yapılandırma sağlayıcıları *.json* dosyaları, *.xml* dosyaları, ortam değişkenleri ve komut satırı bağımsız değişkenleri gibi çeşitli kaynaklar için kullanılabilir. Diğer kaynakları desteklemek için özel yapılandırma sağlayıcıları yazın.

Varsayılan [olarak,](xref:fundamentals/configuration/index#default)ASP.NET Core uygulamaları *appsettings.json,* ortam değişkenleri, komut satırı ve daha fazla sını okuyacak şekilde yapılandırılır. Uygulamanın yapılandırması yüklendiğinde, ortam değişkenlerinden gelen değerler *appsettings.json'daki*değerleri geçersiz kılar.

İlgili yapılandırma değerlerini okumanın tercih edilen yolu [seçenekler deseni](xref:fundamentals/configuration/options)kullanmaktır. Daha fazla bilgi için seçenekler [deseni kullanarak Bind hiyerarşik yapılandırma verilerine](xref:fundamentals/configuration/index#optpat)bakın.

Parolalar gibi gizli yapılandırma verilerini yönetmek için ASP.NET [Core, Gizli Yönetici'yi](xref:security/app-secrets#secret-manager)sağlar. Üretim sırları için [Azure Key Vault'u](xref:security/key-vault-configuration)öneriyoruz.

Daha fazla bilgi için bkz. <xref:fundamentals/configuration/index>.

## <a name="environments"></a>Ortamlar

Yürütme ortamları, `Development` `Staging` `Production`ASP.NET Core'da birinci sınıf bir kavramdır. Ortam değişkenini ayarlayarak uygulamanın `ASPNETCORE_ENVIRONMENT` içinde koştuğu ortamı belirtin. ASP.NET Core, uygulama başlatmada ortam değişkenini `IWebHostEnvironment` okur ve değeri bir uygulamada depolar. Bu uygulama bağımlılık enjeksiyonu (DI) yoluyla bir uygulamanın her yerinde kullanılabilir.

Aşağıdaki örnek, `Development` uygulamada çalışırken ayrıntılı hata bilgileri sağlayacak şekilde uygulamayı yapılandırır:

[!code-csharp[](index/samples_snapshot/3.x/StartupConfigure.cs?highlight=3-6)]

Daha fazla bilgi için bkz. <xref:fundamentals/environments>.

## <a name="logging"></a>Günlüğe kaydetme

ASP.NET Core, çeşitli yerleşik ve üçüncü taraf günlük sağlayıcılarıyla çalışan bir günlük API'sini destekler. Kullanılabilir sağlayıcılar şunlardır:

* Konsol
* Hata ayıklama
* Windows'da Olay İzleme
* Windows Etkinlik Günlüğü
* TraceSource
* Azure App Service
* Azure Application Insights

Günlükleri oluşturmak için, <xref:Microsoft.Extensions.Logging.ILogger%601> bağımlılık enjeksiyon (DI) bir hizmet çözmek <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>ve gibi günlük yöntemleri çağırın. Örneğin:

[!code-csharp[](index/samples_snapshot/3.x/TodoController.cs?highlight=5,13,19)]

Herhangi bir `LogInformation` sayıda alanı destekleme gibi günlüğe kaydetme yöntemleri. Bu alanlar genellikle bir ileti `string`oluşturmak için kullanılır, ancak bazı günlük sağlayıcıları bunları ayrı alanlar olarak veri deposuna gönderir. Bu özellik, günlük sağlayıcılarının [yapılandırılmış günlük olarak da bilinen anlamsal günlük özelliğini](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)uygulamalarını mümkün kılar.

Daha fazla bilgi için bkz. <xref:fundamentals/logging/index>.

## <a name="routing"></a>Yönlendirme

*Rota,* işleyiciye eşlenen bir URL desenidir. Işleyici genellikle bir Razor sayfası, Bir MVC denetleyicisinde bir eylem yöntemi veya bir ara yazılımdır. ASP.NET Core yönlendirme, uygulamanız tarafından kullanılan URL'ler üzerinde kontrol sağlar.

Daha fazla bilgi için bkz. <xref:fundamentals/routing>.

## <a name="error-handling"></a>Hata işleme

ASP.NET Core, işleme hataları için yerleşik özelliklere sahiptir:

* Geliştirici özel durum sayfası
* Özel hata sayfaları
* Statik durum kodu sayfaları
* Başlangıç özel durum işleme

Daha fazla bilgi için bkz. <xref:fundamentals/error-handling>.

## <a name="make-http-requests"></a>HTTP isteğinde bulunma

Örnekler oluşturmak `IHttpClientFactory` `HttpClient` için bir uygulama kullanılabilir. Fabrika:

* Mantıksal `HttpClient` örnekleri adlandırmak ve yapılandırmak için merkezi bir konum sağlar. Örneğin, *GitHub'a* erişmek için bir github istemcisi kaydedin ve yapılandırın. Varsayılan istemciyi başka amaçlariçin kaydedin ve yapılandırın.
* Giden bir istek ara yazılım boru hattı oluşturmak için birden çok devretme işleyicilerinin kaydedilmesi ve zincirlemesi destekler. Bu desen, ASP.NET Core'un gelen ara yazılım boru hattına benzer. Desen, önbelleğe alma, hata işleme, serileştirme ve günlüğe kaydetme gibi HTTP istekleri için çapraz kesme endişelerini yönetmek için bir mekanizma sağlar.
* Geçici hata işleme için popüler bir üçüncü taraf kitaplığı olan *Polly*ile tümleşir.
* Yaşam ömürlerini el ile `HttpClientHandler` yönetirken `HttpClient` ortaya çıkan yaygın DNS sorunlarını önlemek için temel örneklerin biraraya getiriyi ve kullanım ömrünü yönetir.
* Fabrika tarafından oluşturulan istemciler aracılığıyla gönderilen tüm istekler <xref:Microsoft.Extensions.Logging.ILogger> için yapılandırılabilir bir günlük deneyimi ekler.

Daha fazla bilgi için bkz. <xref:fundamentals/http-requests>.

## <a name="content-root"></a>İçerik kökü

İçerik kökü aşağıdakiler için temel yoldur:

* Uygulamanın yürütülebilir barındırma (*.exe*).
* Uygulamayı oluşturan derleme derlemeler (*.dll*).
* Uygulama tarafından kullanılan içerik dosyaları, örneğin:
  * Jilet dosyaları (*.cshtml*, *.razor*)
  * Yapılandırma dosyaları (*.json*, *.xml*)
  * Veri dosyaları (*.db*)
* [Web kökü](#web-root), genellikle *wwwroot* klasörü.

Geliştirme sırasında, içerik kökü varsayılan olarak projenin kök dizinine iner. Bu dizin aynı zamanda hem uygulamanın içerik dosyaları hem de [Web kökü](#web-root)için temel yoldur. Ana bilgisayarı kurarken yolunu ayarlayarak farklı bir içerik kökü [belirtin.](#host) Daha fazla bilgi için [İçerik köküne](xref:fundamentals/host/generic-host#contentrootpath-1)bakın.

## <a name="web-root"></a>Web kökü

Web kökü, genel, statik kaynak dosyalarının temel yoludur:

* Stylesheets (*.css*)
* JavaScript (*.js*)
* Görüntüler (*.png*, *.jpg*)

Varsayılan olarak, statik dosyalar yalnızca web kök dizini ve alt dizinlerinden sunulur. Web kök yolu *varsayılan olarak {içerik kökü}/wwwroot.* Ana bilgisayarı kurarken yolunu ayarlayarak farklı bir web kökü [belirtin.](#host) Daha fazla bilgi için [Web köküne](xref:fundamentals/host/generic-host#webroot-1)bakın.

Proje dosyasındaki [ \<İçerik> proje öğesi](/visualstudio/msbuild/common-msbuild-project-items#content) ile *wwwroot'da* dosya yayımlamayı engelleyin. Aşağıdaki örnek, *wwwroot/local* ve alt dizinlerinde içerik yayımlamayı engeller:

```xml
<ItemGroup>
  <Content Update="wwwroot\local\**\*.*" CopyToPublishDirectory="Never" />
</ItemGroup>
```

Razor *.cshtml* dosyalarında, tilde-slash (`~/`) web kökünü işaret eder. Ile `~/` başlayan bir *yol sanal yol*olarak adlandırılır.

Daha fazla bilgi için bkz. <xref:fundamentals/static-files>.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Bu makale, ASP.NET Core uygulamalarının nasıl geliştirilebildiğini anlamak için önemli konuların genel bir özetidir.

## <a name="the-startup-class"></a>Başlangıç sınıfı

Sınıf `Startup` nerede:

* Uygulamanın gerektirdiği hizmetler yapılandırılır.
* İstek işleme ardışık alanı tanımlanır.

*Hizmetler,* uygulama tarafından kullanılan bileşenlerdir. Örneğin, günlük bileşeni bir hizmettir. Yapılandırma (veya *kayıt)* hizmetlerini yapılandırma kodu `Startup.ConfigureServices` yönteme eklenir.

İstek işleme ardışık bir dizi *ara yazılım* bileşeni olarak oluşturulur. Örneğin, bir ara yazılım statik dosyalar için isteklerini işleyebilir veya HTTP isteklerini HTTPS'ye yönlendirebilir. Her ara yazılım bir `HttpContext` üzerinde eşzamanlı işlemler gerçekleştirir ve sonra bir sonraki ara yazılımı çağırır veya isteği sonlandırır. İstek işleme ardışık hattını yapılandırmak `Startup.Configure` için kod yönteme eklenir.

İşte örnek bir `Startup` sınıf:

[!code-csharp[](index/samples_snapshot/2.x/Startup.cs?highlight=3,12)]

Daha fazla bilgi için bkz. <xref:fundamentals/startup>.

## <a name="dependency-injection-services"></a>Bağımlılık ekleme (hizmetler)

ASP.NET Core, yapılandırılmış hizmetleri uygulamasınıfları için kullanılabilir kılan yerleşik bir bağımlılık enjeksiyonu (DI) çerçevesine sahiptir. Bir sınıfta bir hizmet örneği almak için bir yolu gerekli türde bir parametre ile bir oluşturucu oluşturmaktır. Parametre hizmet türü veya arabirim olabilir. DI sistemi hizmeti çalışma zamanında sağlar.

Burada, Varlık Çerçevesi Temel bağlam nesnesi almak için DI kullanan bir sınıf ver. Vurgulanan satır yapıcı enjeksiyon bir örnektir:

[!code-csharp[](index/samples_snapshot/2.x/Index.cshtml.cs?highlight=5)]

DI yerleşik olsa da, isterseniz bir üçüncü taraf Inversion of Control (IoC) kapsayıcısı takmanıza izin vermek üzere tasarlanmıştır.

Daha fazla bilgi için bkz. <xref:fundamentals/dependency-injection>.

## <a name="middleware"></a>Ara yazılım

İstek işleme ardışık bir dizi ara yazılım bileşeni olarak oluşturulur. Her bileşen bir `HttpContext` üzerinde eşzamanlı işlemler gerçekleştirir ve ardışık ardışık ardışık bir sonraki ara yazılımı çağırır veya isteği sonlandırır.

Kural kuralıyla, `Use...` `Startup.Configure` yöntemdeki uzantı yöntemini çağırarak ardışık bir bileşen ardışık bileşen eklenir. Örneğin, statik dosyaların işlenmesini etkinleştirmek `UseStaticFiles`için .

Aşağıdaki örnekte vurgulanan kod, istek işleme ardışık hattını yapılandırır:

[!code-csharp[](index/samples_snapshot/2.x/Startup.cs?highlight=14-16)]

ASP.NET Core dahili ara yazılım zengin bir dizi içerir ve özel ara yazılım yazabilirsiniz.

Daha fazla bilgi için bkz. <xref:fundamentals/middleware/index>.

## <a name="host"></a>Host

Bir ASP.NET Core uygulaması başlangıç üzerine bir *ana bilgisayar* oluşturur. Ana bilgisayar, uygulamanın tüm kaynaklarını kapsayan bir nesnedir( örneğin:

* Bir HTTP sunucu uygulaması
* Ara yazılım bileşenleri
* Günlüğe kaydetme
* Di
* Yapılandırma

Uygulamanın tüm birbirine bağlı kaynaklarının tek bir nesneye dahil olmasının temel nedeni ömür boyu yönetimdir: uygulama başlatma üzerinde denetim ve zarif kapatma.

İki ana bilgisayar kullanılabilir: Web Ana Bilgisayar ve Genel Ana Bilgisayar. Core 2.xASP.NET, Genel Ana Bilgisayar yalnızca web olmayan senaryolar içindir.

Ana bilgisayar oluşturmak için `Program.Main`kod:

[!code-csharp[](index/samples_snapshot/2.x/Program.cs)]

Yöntem, `CreateDefaultBuilder` aşağıdakigibi sık kullanılan seçenekleri olan bir ana bilgisayarı yapılandırır:

* [Kerkenez'i](#servers) web sunucusu olarak kullanın ve IIS tümleştirmesini etkinleştirin.
* *appsettings.json*, appsettings.{ adresinden yapılandırmayı *yükleyin. Çevre Adı}.json*, ortam değişkenleri, komut satırı bağımsız değişkenleri ve diğer yapılandırma kaynakları.
* Günlüğe kaydetme çıktısını konsola gönderin ve hata ayıklama sağlayıcılarına gönderin.

Daha fazla bilgi için bkz. <xref:fundamentals/host/web-host>.

### <a name="non-web-scenarios"></a>Web dışı senaryolar

Genel Ana Bilgisayar, günlük, bağımlılık enjeksiyonu (DI), yapılandırma ve uygulama ömrü yönetimi gibi çapraz kesme çerçeve uzantılarını diğer uygulama türlerinin kullanmasına izin verir. Daha fazla bilgi için <xref:fundamentals/host/generic-host> ve <xref:fundamentals/host/hosted-services> bölümlerine bakın.

## <a name="servers"></a>Sunucular

Bir ASP.NET Core uygulaması, HTTP isteklerini dinlemek için bir HTTP sunucusu uygulaması kullanır. Sunucu, uygulamadaki [istekleri](xref:fundamentals/request-features) `HttpContext`bir .

::: moniker-end

::: moniker range="= aspnetcore-2.2"

# <a name="windows"></a>[Windows](#tab/windows)

ASP.NET Core aşağıdaki sunucu uygulamalarını sağlar:

* *Kerkenez* bir çapraz platform web sunucusudur. Kerkenez genellikle [IIS](https://www.iis.net/)kullanılarak ters proxy yapılandırmasında çalıştırılır. Kerkenez, doğrudan Internet'e açık halka açık bir kenar sunucusu olarak çalıştırılabilir.
* *IIS HTTP Server, IIS* kullanan bir windows sunucusudur. Bu sunucu ile ASP.NET Core uygulaması ve IIS aynı işlemde çalışır.
* *HTTP.sys,* Windows için IIS ile kullanılmayan bir sunucudur.

# <a name="macos"></a>[macOS](#tab/macos)

ASP.NET Core *Kestrel* çapraz platform sunucusu uygulaması sağlar. Kerkenez, doğrudan Internet'e açık halka açık bir kenar sunucusu olarak çalıştırılabilir. Kerkenez genellikle [Nginx](https://nginx.org) veya [Apache](https://httpd.apache.org/)ile ters proxy yapılandırmaçalıştırılır.

# <a name="linux"></a>[Linux](#tab/linux)

ASP.NET Core *Kestrel* çapraz platform sunucusu uygulaması sağlar. Kerkenez, doğrudan Internet'e açık halka açık bir kenar sunucusu olarak çalıştırılabilir. Kerkenez genellikle [Nginx](https://nginx.org) veya [Apache](https://httpd.apache.org/)ile ters proxy yapılandırmaçalıştırılır.

---

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="windows"></a>[Windows](#tab/windows)

ASP.NET Core aşağıdaki sunucu uygulamalarını sağlar:

* *Kerkenez* bir çapraz platform web sunucusudur. Kerkenez genellikle [IIS](https://www.iis.net/)kullanılarak ters proxy yapılandırmasında çalıştırılır. Kerkenez, doğrudan Internet'e açık halka açık bir kenar sunucusu olarak çalıştırılabilir.
* *HTTP.sys,* Windows için IIS ile kullanılmayan bir sunucudur.

# <a name="macos"></a>[macOS](#tab/macos)

ASP.NET Core *Kestrel* çapraz platform sunucusu uygulaması sağlar. Kerkenez, doğrudan Internet'e açık halka açık bir kenar sunucusu olarak çalıştırılabilir. Kerkenez genellikle [Nginx](https://nginx.org) veya [Apache](https://httpd.apache.org/)ile ters proxy yapılandırmaçalıştırılır.

# <a name="linux"></a>[Linux](#tab/linux)

ASP.NET Core *Kestrel* çapraz platform sunucusu uygulaması sağlar. Kerkenez, doğrudan Internet'e açık halka açık bir kenar sunucusu olarak çalıştırılabilir. Kerkenez genellikle [Nginx](https://nginx.org) veya [Apache](https://httpd.apache.org/)ile ters proxy yapılandırmaçalıştırılır.

---

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Daha fazla bilgi için bkz. <xref:fundamentals/servers/index>.

## <a name="configuration"></a>Yapılandırma

ASP.NET Core, sıralı bir yapılandırma sağlayıcısı kümesinden adi değer çiftleri olarak ayarları alan bir yapılandırma çerçevesi sağlar. *.json* dosyaları, *.xml* dosyaları, ortam değişkenleri ve komut satırı bağımsız değişkenleri gibi çeşitli kaynaklar için yerleşik yapılandırma sağlayıcıları vardır. Özel yapılandırma sağlayıcıları da yazabilirsiniz.

Örneğin, yapılandırmanın *appsettings.json* ve ortam değişkenlerinden geldiğini belirtebilirsiniz. Daha sonra *ConnectionString* değeri istendiğinde, çerçeve *appsettings.json* dosyasında ilk olarak görünür. Değer orada değil, aynı zamanda bir ortam değişkeninde bulunursa, ortam değişkeninden gelen değer önceliklidir.

Parolalar gibi gizli yapılandırma verilerini yönetmek için ASP.NET Core bir [Gizli Yönetici aracı](xref:security/app-secrets)sağlar. Üretim sırları için [Azure Key Vault'u](xref:security/key-vault-configuration)öneriyoruz.

Daha fazla bilgi için bkz. <xref:fundamentals/configuration/index>.

## <a name="options"></a>Seçenekler

Mümkün olduğunda, ASP.NET Core yapılandırma değerlerini depolamak ve almak için *seçenekler modelini* izler. Seçenekler deseni, ilgili ayar gruplarını temsil etmek için sınıfları kullanır.

Örneğin, aşağıdaki kod WebSockets seçeneklerini ayarlar:

[!code-csharp[](index/samples_snapshot/2.x/UseWebSockets.cs)]

Daha fazla bilgi için bkz. <xref:fundamentals/configuration/options>.

## <a name="environments"></a>Ortamlar

*Geliştirme,* *Evreleme*ve *Üretim*gibi yürütme ortamları, ASP.NET Core'da birinci sınıf bir kavramdır. `ASPNETCORE_ENVIRONMENT` Ortam değişkenini ayarlayarak bir uygulamanın içinde koştuğu ortamı belirtebilirsiniz. ASP.NET Core, uygulama başlatmada ortam değişkenini `IHostingEnvironment` okur ve değeri bir uygulamada depolar. Ortam nesnesi DI üzerinden uygulamanın her yerinde kullanılabilir.

`Startup` Sınıftan gelen aşağıdaki örnek kod, uygulamayı yalnızca geliştirme aşamasında çalıştığında ayrıntılı hata bilgileri sağlayacak şekilde yapılandırır:

[!code-csharp[](index/samples_snapshot/2.x/StartupConfigure.cs?highlight=3-6)]

Daha fazla bilgi için bkz. <xref:fundamentals/environments>.

## <a name="logging"></a>Günlüğe kaydetme

ASP.NET Core, çeşitli yerleşik ve üçüncü taraf günlük sağlayıcılarıyla çalışan bir günlük API'sini destekler. Kullanılabilir sağlayıcılar aşağıdakileri içerir:

* Konsol
* Hata ayıklama
* Windows'da Olay İzleme
* Windows Etkinlik Günlüğü
* TraceSource
* Azure App Service
* Azure Application Insights

DI'den bir `ILogger` nesne alarak ve günlük yöntemlerini çağırarak bir uygulamanın kodundaki herhangi bir yerden günlükler yazın.

Burada, oluşturucu enjeksiyonve `ILogger` günlüğe kaydetme yöntemi çağrıları vurgulanan bir nesne kullanan örnek kod.

[!code-csharp[](index/samples_snapshot/2.x/TodoController.cs?highlight=5,13,17)]

Arabirim, `ILogger` istediğiniz sayıda alanı günlük sağlayıcısına geçirmenizi sağlar. Alanlar genellikle bir ileti dizesi oluşturmak için kullanılır, ancak sağlayıcı da bir veri deposuna ayrı alanlar olarak gönderebilirsiniz. Bu özellik, günlük sağlayıcılarının [yapılandırılmış günlük olarak da bilinen anlamsal günlük özelliğini](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)uygulamalarını mümkün kılar.

Daha fazla bilgi için bkz. <xref:fundamentals/logging/index>.

## <a name="routing"></a>Yönlendirme

*Rota,* işleyiciye eşlenen bir URL desenidir. Işleyici genellikle bir Razor sayfası, Bir MVC denetleyicisinde bir eylem yöntemi veya bir ara yazılımdır. ASP.NET Core yönlendirme, uygulamanız tarafından kullanılan URL'ler üzerinde kontrol sağlar.

Daha fazla bilgi için bkz. <xref:fundamentals/routing>.

## <a name="error-handling"></a>Hata işleme

ASP.NET Core, işleme hataları için yerleşik özelliklere sahiptir:

* Geliştirici özel durum sayfası
* Özel hata sayfaları
* Statik durum kodu sayfaları
* Başlangıç özel durum işleme

Daha fazla bilgi için bkz. <xref:fundamentals/error-handling>.

## <a name="make-http-requests"></a>HTTP isteğinde bulunma

Örnekler oluşturmak `IHttpClientFactory` `HttpClient` için bir uygulama kullanılabilir. Fabrika:

* Mantıksal `HttpClient` örnekleri adlandırmak ve yapılandırmak için merkezi bir konum sağlar. Örneğin, *github* istemcisi kaydedilebilir ve GitHub'a erişmek için yapılandırılabilir. Varsayılan istemci başka amaçlarla kaydedilebilir.
* Giden bir istek ara yazılım boru hattı oluşturmak için birden çok devretme işleyicilerinin kaydedilmesi ve zincirlemesi destekler. Bu desen, ASP.NET Core'daki gelen ara yazılım düzenine benzer. Desen, önbelleğe alma, hata işleme, serileştirme ve günlüğe kaydetme gibi HTTP istekleri etrafında çapraz kesme yle ilgili endişeleri yönetmek için bir mekanizma sağlar.
* Geçici hata işleme için popüler bir üçüncü taraf kitaplığı olan *Polly*ile tümleşir.
* Yaşam ömürlerini el ile `HttpClientHandler` yönetirken `HttpClient` ortaya çıkan yaygın DNS sorunlarını önlemek için temel örneklerin biraraya getiriyi ve kullanım ömrünü yönetir.
* Fabrika tarafından oluşturulan istemciler `ILogger`aracılığıyla gönderilen tüm istekler için yapılandırılabilir bir günlük deneyimi (üzerinden) ekler.

Daha fazla bilgi için bkz. <xref:fundamentals/http-requests>.

## <a name="content-root"></a>İçerik kökü

İçerik kökü, aşağıdakilerin temel yoludur:

* Uygulama (*.exe*) barındırma yürütülebilir .
* Uygulamayı oluşturan derleme derlemeler (*.dll*).
* Uygulama tarafından kullanılan kod dışı içerik dosyaları, örneğin:
  * Jilet dosyaları (*.cshtml*, *.razor*)
  * Yapılandırma dosyaları (*.json*, *.xml*)
  * Veri dosyaları (*.db*)
* [Web kökü](#web-root), genellikle yayınlanan *wwwroot* klasörü.

Geliştirme sırasında:

* İçerik kökü varsayılan olarak projenin kök dizinine iner.
* Projenin kök dizini oluşturmak için kullanılır:
  * Projenin kök dizininde uygulamanın kod olmayan içerik dosyalarına giden yol.
  * [Web kökü](#web-root), genellikle projenin kök dizinindeki *wwwroot* klasörü.

[Ana bilgisayar inşa](#host)ederken alternatif bir içerik kök yolu belirtilebilir. Daha fazla bilgi için bkz. <xref:fundamentals/host/web-host#content-root>.

## <a name="web-root"></a>Web kökü

Web kökü, genel, kod suz, statik kaynak dosyalarına temel yoldur:

* Stylesheets (*.css*)
* JavaScript (*.js*)
* Görüntüler (*.png*, *.jpg*)

Statik dosyalar yalnızca varsayılan olarak web kök dizini (ve alt dizinler) tarafından sunulur.

Web kök yolu *varsayılan olarak {içerik kökü}/wwwroot*olarak belirlenir, ancak [ana bilgisayar ını kurarken](#host)farklı bir web kökü belirtilebilir. Daha fazla bilgi için [Web köküne](xref:fundamentals/host/web-host#web-root)bakın.

Proje dosyasındaki [ \<İçerik> proje öğesi](/visualstudio/msbuild/common-msbuild-project-items#content) ile *wwwroot'da* dosya yayımlamayı engelleyin. Aşağıdaki örnek, *wwwroot/yerel* dizinde ve alt dizinlerde içerik yayımlanmasını engeller:

```xml
<ItemGroup>
  <Content Update="wwwroot\local\**\*.*" CopyToPublishDirectory="Never" />
</ItemGroup>
```

Razor (*.cshtml*) dosyalarında, tilde-slash (`~/`) web kökünü işaret eder. Ile `~/` başlayan bir *yol sanal yol*olarak adlandırılır.

Daha fazla bilgi için bkz. <xref:fundamentals/static-files>.

::: moniker-end
