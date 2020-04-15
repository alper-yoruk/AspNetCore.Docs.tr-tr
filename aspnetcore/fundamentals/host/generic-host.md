---
title: .NET Genel Ana Bilgisayar
author: rick-anderson
description: Uygulama başlatma ve yaşam boyu yönetimden sorumlu .NET Core Generic Host hakkında bilgi edinin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 4/17/2020
uid: fundamentals/host/generic-host
ms.openlocfilehash: b528a33fa52bfe56faaf9f3ff8c7e43db0d4e184
ms.sourcegitcommit: f29a12486313e38e0163a643d8a97c8cecc7e871
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81384031"
---
# <a name="net-generic-host"></a>.NET Genel Ana Bilgisayar

::: moniker range=">= aspnetcore-3.0 <= aspnetcore-3.1"

Core şablonlarının ASP.NET .NET Core Genel<xref:Microsoft.Extensions.Hosting.HostBuilder>Ana Bilgisayar ( ) oluşturur.

## <a name="host-definition"></a>Ana bilgisayar tanımı

*Ana bilgisayar,* bir uygulamanın kaynaklarını kapsülleyen bir nesnedir:

* Bağımlılık enjeksiyonu (DI)
* Günlüğe kaydetme
* Yapılandırma
* `IHostedService`Uygulama

Bir ana bilgisayar başlatıldığında, DI <xref:Microsoft.Extensions.Hosting.IHostedService> kapsayıcısında bulduğu her uygulamayı çağırır. `IHostedService.StartAsync` Bir web uygulamasında, `IHostedService` uygulamalardan biri http sunucu [uygulaması](xref:fundamentals/index#servers)başlatan bir web hizmetidir.

Uygulamanın tüm birbirine bağlı kaynaklarının tek bir nesneye dahil olmasının temel nedeni ömür boyu yönetimdir: uygulama başlatma üzerinde denetim ve zarif kapatma.

## <a name="set-up-a-host"></a>Ana bilgisayar ayarlama

Ana bilgisayar genellikle `Program` sınıfta kodtarafından yapılandırılır, oluşturulur ve çalıştırılır. Yöntem: `Main`

* Oluşturucu `CreateHostBuilder` nesne oluşturmak ve yapılandırmak için bir yöntem çağırır.
* Oluşturucu nesneüzerinde aramalar `Build` ve `Run` yöntemler.

ASP.NET Core web şablonları bir ana bilgisayar oluşturmak için aşağıdaki kodu oluşturur:

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStartup<Startup>();
            });
}
```

Aşağıdaki kod, DI kapsayıcısına eklenen `IHostedService` bir uygulamayla HTTP dışı bir iş yükü oluşturur.

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureServices((hostContext, services) =>
            {
               services.AddHostedService<Worker>();
            });
}
```

Bir HTTP iş `Main` yükü için yöntem `CreateHostBuilder` `ConfigureWebHostDefaults`aynıdır, ancak çağırır:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

Uygulama Entity Framework Core kullanıyorsa, yöntemin adını `CreateHostBuilder` veya imzasını değiştirmeyin. [Entity Framework Core araçları,](/ef/core/miscellaneous/cli/) `CreateHostBuilder` uygulamayı çalıştırmadan ana bilgisayarı yapılandıran bir yöntem bulmayı bekler. Daha fazla bilgi için tasarım [zamanı DbContext Oluşturma bölümüne](/ef/core/miscellaneous/cli/dbcontext-creation)bakın.

## <a name="default-builder-settings"></a>Varsayılan oluşturucu ayarları

Yöntem: <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>

* İçerik [kökünü](xref:fundamentals/index#content-root) döndürülen <xref:System.IO.Directory.GetCurrentDirectory*>yola ayarlar.
* Yükler ana bilgisayar yapılandırması:
  * Çevre değişkenleri . `DOTNET_`
  * Komut satırı bağımsız değişkenleri.
* Uygulama yapılandırması:
  * *appsettings.json*.
  * *ayarları. {Çevre}.json*.
  * [Uygulama](xref:security/app-secrets) `Development` ortamda çalıştığında Gizli Yönetici.
  * Ortam değişkenleri.
  * Komut satırı bağımsız değişkenleri.
* Aşağıdaki [günlük](xref:fundamentals/logging/index) sağlayıcılarını ekler:
  * Konsol
  * Hata ayıklama
  * EventSource
  * EventLog (yalnızca Windows'da çalışırken)
* Ortam Geliştirme olduğunda [kapsam doğrulama](xref:fundamentals/dependency-injection#scope-validation) ve [bağımlılık doğrulamasağlar.](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild)

Yöntem: `ConfigureWebHostDefaults`

* `ASPNETCORE_`'ile önceden belirlenmiş ortam değişkenlerinden ana bilgisayar yapılandırması yükler.
* [Kestrel](xref:fundamentals/servers/kestrel) sunucusunu web sunucusu olarak ayarlar ve uygulamanın barındırma yapılandırma sağlayıcılarını kullanarak yapılandırır. Kestrel sunucusunun varsayılan seçenekleri için <xref:fundamentals/servers/kestrel#kestrel-options>bkz.
* [Ana Bilgisayar Filtreleme ara yazılım](xref:fundamentals/servers/kestrel#host-filtering)ekler.
* `ASPNETCORE_FORWARDEDHEADERS_ENABLED` `true` [Eşitse Iletili Üstbilgi ara ware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) ekler.
* IIS tümleştirmesini sağlar. IIS varsayılan seçenekleri için <xref:host-and-deploy/iis/index#iis-options>bkz.

Bu makalenin ilerleyen bölümlerinde [tüm uygulama türleri için Ayarlar](#settings-for-all-app-types) ve web uygulamaları bölümleri için [Ayarlar,](#settings-for-web-apps) varsayılan oluşturucu ayarlarını nasıl geçersiz kılınan durumu gösterir.

## <a name="framework-provided-services"></a>Çerçeve tarafından sağlanan hizmetler

Aşağıdaki hizmetler otomatik olarak kaydedilir:

* [IHostApplicationÖmür Boyu](#ihostapplicationlifetime)
* [IHostLifetime](#ihostlifetime)
* [IHostEnvironment / IWebHostEnvironment](#ihostenvironment)

Çerçeve tarafından sağlanan hizmetler hakkında <xref:fundamentals/dependency-injection#framework-provided-services>daha fazla bilgi için bkz.

## <a name="ihostapplicationlifetime"></a>IHostApplicationÖmür Boyu

Başlatma <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> sonrası ve `IApplicationLifetime`zarif kapatma görevlerini işlemek için (eski) hizmeti herhangi bir sınıfa enjekte edin. Arabirimdeki üç özellik, uygulama başlatma ve uygulama durdurma olay işleyicisi yöntemlerini kaydetmek için kullanılan iptal belirteçleridir. Arabirim de `StopApplication` bir yöntem içerir.

Aşağıdaki örnek, `IHostedService` `IHostApplicationLifetime` olayları kaydeden bir uygulamadır:

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a>IHostLifetime

Uygulama, <xref:Microsoft.Extensions.Hosting.IHostLifetime> ana bilgisayar ne zaman başlar ve ne zaman dursın denetler. Son kayıtlı uygulama kullanılır.

`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`varsayılan `IHostLifetime` uygulamadır. `ConsoleLifetime`:

* <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT veya SIGTERM <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> için dinler ve kapatma işlemini başlatmak için çağırır.
* [RunAsync](#runasync) ve [WaitForShutdownAsync](#waitforshutdownasync)gibi uzantıları unblocks .

## <a name="ihostenvironment"></a>IHostEnvironment

Aşağıdaki <xref:Microsoft.Extensions.Hosting.IHostEnvironment> ayarlar hakkında bilgi almak için hizmeti bir sınıfa enjekte edin:

* [ApplicationName](#applicationname)
* [Çevre Adı](#environmentname)
* [İçerikRootPath](#contentroot)

Web uygulamaları, `IWebHostEnvironment` `IHostEnvironment` [WebRootPath'i](#webroot)devralan ve ekleyen arabirimi uygular.

## <a name="host-configuration"></a>Ana bilgisayar yapılandırması

Ana bilgisayar yapılandırması <xref:Microsoft.Extensions.Hosting.IHostEnvironment> uygulamanın özellikleri için kullanılır.

Ana bilgisayar yapılandırması [HostBuilderBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) içinde <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>kullanılabilir. Sonra `ConfigureAppConfiguration` `HostBuilderContext.Configuration` , uygulama config ile değiştirilir.

Ana bilgisayar yapılandırması <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> `IHostBuilder`eklemek için, ''yi arayın. `ConfigureHostConfiguration`katkı sonuçları ile birden çok kez çağrılabilir. Ana bilgisayar, belirli bir anahtarda son bir değer belirleyen seçeneği kullanır.

Önek `DOTNET_` ve komut satırı bağımsız değişkenleri olan `CreateDefaultBuilder`ortam değişken sağlayıcısı. Web uygulamaları için önek `ASPNETCORE_` li ortam değişken sağlayıcısı eklenir. Çevre değişkenleri okunduğunda önek kaldırılır. Örneğin, ortam `ASPNETCORE_ENVIRONMENT` değişken değeri `environment` anahtarın ana bilgisayar yapılandırma değeri olur.

Aşağıdaki örnek ana bilgisayar yapılandırmasını oluşturur:

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a>Uygulama yapılandırması

Uygulama yapılandırması <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> çağırarak `IHostBuilder`oluşturulur. `ConfigureAppConfiguration`katkı sonuçları ile birden çok kez çağrılabilir. Uygulama, belirli bir anahtarda son bir değer belirleyen seçeneği kullanır. 

Tarafından `ConfigureAppConfiguration` oluşturulan yapılandırma sonraki işlemler için [HostBuilderContext.Configuration'da](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) ve DI'den bir hizmet olarak kullanılabilir. Ana bilgisayar yapılandırması da uygulama yapılandırmasına eklenir.

Daha fazla bilgi için [ASP.NET Core'daki Yapılandırma'ya](xref:fundamentals/configuration/index#configureappconfiguration)bakın.

## <a name="settings-for-all-app-types"></a>Tüm uygulama türleri için ayarlar

Bu bölümde hem HTTP hem de HTTP dışı iş yükleri için geçerli olan ana bilgisayar ayarları listelenir. Varsayılan olarak, bu ayarları yapılandırmak için kullanılan `DOTNET_` `ASPNETCORE_` ortam değişkenleri bir veya önek olabilir.

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a>ApplicationName

[IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) özelliği, ana bilgisayar yapısı sırasında ana bilgisayar yapılandırmasından ayarlanır.

**Anahtar**:`applicationName`  
**Türü**:`string`  
**Varsayılan**: Uygulamanın giriş noktasını içeren derlemenin adı.  
**Çevre değişkeni**:`<PREFIX_>APPLICATIONNAME`

Bu değeri ayarlamak için ortam değişkenini kullanın. 

### <a name="contentroot"></a>İçerikKök

[IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) özelliği, ana bilgisayarın içerik dosyalarını aramaya başladığı yeri belirler. Yol yoksa, ana bilgisayar başlatılamıyor.

**Anahtar**:`contentRoot`  
**Türü**:`string`  
**Varsayılan**: Uygulama derlemesinin bulunduğu klasör.  
**Çevre değişkeni**:`<PREFIX_>CONTENTROOT`

Bu değeri ayarlamak için, ortam `UseContentRoot` değişkenini kullanın veya çağırın: `IHostBuilder`

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

Daha fazla bilgi için bkz.

* [Temel: İçerik kökü](xref:fundamentals/index#content-root)
* [Webroot](#webroot)

### <a name="environmentname"></a>Çevre Adı

[IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) özelliği herhangi bir değere ayarlanabilir. Çerçeve tanımlı `Development`değerler `Staging`, `Production`ve . Değerler büyük/küçük harf duyarlı değildir.

**Anahtar**:`environment`  
**Türü**:`string`  
**Varsayılan**:`Production`  
**Çevre değişkeni**:`<PREFIX_>ENVIRONMENT`

Bu değeri ayarlamak için, ortam `UseEnvironment` değişkenini kullanın veya çağırın: `IHostBuilder`

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a>KapatmaTimeout

[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) için <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>zaman ası ayarlar. Varsayılan değer beş saniyedir.  Zaman açılığı dönüşü nde, ana bilgisayar

* Tetikler [IHostApplicationLifetime.ApplicationStop](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).
* Barındırılan hizmetleri durdurmaya çalışır, duramayan hizmetler için günlüğe kaydetme hataları.

Zaman aşımı süresi, barındırılan hizmetlerin tümü sona ermeden önce sona ererse, uygulama kapandığında kalan tüm etkin hizmetler durdurulur. Hizmetler işleme meden ilerlememiş olsalar bile durur. Hizmetlerin durması için ek süre gerekiyorsa, zaman arasını artırın.

**Anahtar**:`shutdownTimeoutSeconds`  
**Türü**:`int`  
**Varsayılan**: 5 saniye  
**Çevre değişkeni**:`<PREFIX_>SHUTDOWNTIMEOUTSECONDS`

Bu değeri ayarlamak için ortam değişkenini kullanın veya yapılandırın. `HostOptions` Aşağıdaki örnek, zaman anına göre 20 saniyeye ayarlar:

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

## <a name="settings-for-web-apps"></a>Web uygulamaları için ayarlar

Bazı ana bilgisayar ayarları yalnızca HTTP iş yükleri için geçerlidir. Varsayılan olarak, bu ayarları yapılandırmak için kullanılan `DOTNET_` `ASPNETCORE_` ortam değişkenleri bir veya önek olabilir.

Bu ayarlar `IWebHostBuilder` için uzantı yöntemleri mevcuttur. Uzantı yöntemlerinin nasıl çağrıldığını `webBuilder` gösteren kod `IWebHostBuilder`örnekleri aşağıdaki örnekte olduğu gibi, aşağıdaki gibi bir örnek olduğunu varsayar:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a>YakalamaBaşlangıç Hataları

Ne `false`zaman , başlatma sırasında hatalar ana bilgisayar çıkışı sonucu. Ana `true`bilgisayar başlangıç sırasında özel durumları yakaladığında ve sunucuyu başlatmaya çalıştığında.

**Anahtar**:`captureStartupErrors`  
**Türü** `bool` :`true` `1`( veya )  
**Varsayılan**: Uygulama, varsayılan ın olduğu IIS'nin arkasında Kestrel ile birlikte çalıştığı `false` sürece varsayılan `true`dır.  
**Çevre değişkeni**:`<PREFIX_>CAPTURESTARTUPERRORS`

Bu değeri ayarlamak için yapılandırmayı veya aramayı `CaptureStartupErrors`kullanın:

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a>Ayrıntılı Hatalar

Etkinleştirildiğinde veya ortam `Development`etkinleştirildiğinde, uygulama ayrıntılı hataları yakalar.

**Anahtar**:`detailedErrors`  
**Türü** `bool` :`true` `1`( veya )  
**Varsayılan**:`false`  
**Çevre değişkeni**:`<PREFIX_>_DETAILEDERRORS`

Bu değeri ayarlamak için yapılandırmayı veya aramayı `UseSetting`kullanın:

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a>HostingStartupAssemblies

Başlangıç yüklemeiçin barındırma başlangıç derlemeleri yarı kolon-sınırlı dize. Yapılandırma değeri boş bir dize için varsayılan olsa da, barındırma başlangıç derlemeleri her zaman uygulamanın derlemesini içerir. Başlangıç derlemelerine barındırma sağlandığında, uygulama başlangıç sırasında ortak hizmetlerini oluşturduğunda yükleme için uygulamanın derlemesine eklenirler.

**Anahtar**:`hostingStartupAssemblies`  
**Türü**:`string`  
**Varsayılan**: Boş dize  
**Çevre değişkeni**:`<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`

Bu değeri ayarlamak için yapılandırmayı veya aramayı `UseSetting`kullanın:

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a>HostingStartupExcludeMontajies

Başlangıç tarihinde hariç tutmak için barındırma başlangıç derlemeleri yarı sütunlu sınırlı dize.

**Anahtar**:`hostingStartupExcludeAssemblies`  
**Türü**:`string`  
**Varsayılan**: Boş dize  
**Çevre değişkeni**:`<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`

Bu değeri ayarlamak için yapılandırmayı veya aramayı `UseSetting`kullanın:

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a>HTTPS_Port

HTTPS yönlendirme bağlantı noktası. [HTTPS'nin uygulanmasında](xref:security/enforcing-ssl)kullanılır.

**Anahtar**:`https_port`  
**Türü**:`string`  
**Varsayılan**: Varsayılan değer ayarlı değil.  
**Çevre değişkeni**:`<PREFIX_>HTTPS_PORT`

Bu değeri ayarlamak için yapılandırmayı veya aramayı `UseSetting`kullanın:

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a>TercihHostingUrls

Ana bilgisayar, `IWebHostBuilder` `IServer` uygulamayla yapılandırılan URL'ler yerine yapılandırılan URL'leri dinleyip dinlememesi gerektiğini gösterir.

**Anahtar**:`preferHostingUrls`  
**Türü** `bool` :`true` `1`( veya )  
**Varsayılan**:`true`  
**Çevre değişkeni**:`<PREFIX_>_PREFERHOSTINGURLS`

Bu değeri ayarlamak için ortam değişkenini kullanın veya çağırın: `PreferHostingUrls`

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a>HostingBaşlangıç önleme

Uygulamanın montajı tarafından yapılandırılan başlangıç derlemelerini barındırma da dahil olmak üzere barındırma başlangıç derlemelerinin otomatik olarak yüklenmesiengellenir. Daha fazla bilgi için bkz. <xref:fundamentals/configuration/platform-specific-configuration>.

**Anahtar**:`preventHostingStartup`  
**Türü** `bool` :`true` `1`( veya )  
**Varsayılan**:`false`  
**Çevre değişkeni**:`<PREFIX_>_PREVENTHOSTINGSTARTUP`

Bu değeri ayarlamak için ortam değişkenini kullanın veya çağırın: `UseSetting`

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a>Başlangıç Montaj

`Startup` Sınıfı aramak için derleme.

**Anahtar**:`startupAssembly`  
**Türü**:`string`  
**Varsayılan**: Uygulamanın montajı  
**Çevre değişkeni**:`<PREFIX_>STARTUPASSEMBLY`

Bu değeri ayarlamak için ortam değişkenini kullanın veya çağırın. `UseStartup` `UseStartup`bir derleme adı`string`( ) veya`TStartup`bir tür ( alabilir). Birden `UseStartup` çok yöntem çağrılırsa, sonuncusu önceliklidir.

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a>URL’ler

Sunucunun istekler için dinlemesi gereken bağlantı noktaları ve protokolleri olan IP adreslerinin veya ana bilgisayar adreslerinin yarı sütunlu sınırlı listesi. Örneğin, `http://localhost:123`. Sunucunun\*belirtilen bağlantı noktası ve protokolü kullanarak herhangi bir IP adresi veya ana bilgisayar `http://*:5000`adı üzerindeki istekleri dinlemesi gerektiğini belirtmek için " " kullanın. Protokol (`http://` `https://`veya ) her URL'ye eklenmelidir. Desteklenen biçimler sunucular arasında farklılık gösterir.

**Anahtar**:`urls`  
**Türü**:`string`  
**Varsayılan** `http://localhost:5000` : ve`https://localhost:5001`  
**Çevre değişkeni**:`<PREFIX_>URLS`

Bu değeri ayarlamak için ortam değişkenini kullanın veya çağırın: `UseUrls`

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

Kerkenez kendi bitiş noktası yapılandırma API vardır. Daha fazla bilgi için bkz. <xref:fundamentals/servers/kestrel#endpoint-configuration>.

### <a name="webroot"></a>Webroot

[IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) özelliği uygulamanın statik varlıklarıiçin göreli yolu belirler. Yol yoksa, bir no-op dosya sağlayıcısı kullanılır.  

**Anahtar**:`webroot`  
**Türü**:`string`  
**Varsayılan**: Varsayılan `wwwroot`. *{içerik kökü}/wwwroot* yolu olmalıdır.  
**Çevre değişkeni**:`<PREFIX_>WEBROOT`

Bu değeri ayarlamak için, ortam `UseWebRoot` değişkenini kullanın veya çağırın: `IWebHostBuilder`

```csharp
webBuilder.UseWebRoot("public");
```

Daha fazla bilgi için bkz.

* [Temel: Web kökü](xref:fundamentals/index#web-root)
* [İçerikKök](#contentroot)

## <a name="manage-the-host-lifetime"></a>Ev sahibinin ömrünü yönetin

Uygulamayı başlatmak ve <xref:Microsoft.Extensions.Hosting.IHost> durdurmak için yerleşik uygulamadaki yöntemleri arayın. Bu yöntemler, <xref:Microsoft.Extensions.Hosting.IHostedService> hizmet kapsayıcısında kayıtlı tüm uygulamaları etkiler.

### <a name="run"></a>Çalıştırın

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*>uygulamayı çalıştırArak, ana bilgisayar kapatılana kadar arama iş parçacığının engellenmesini engeller.

### <a name="runasync"></a>RunAsync

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*>uygulamayı çalıştırAn ve <xref:System.Threading.Tasks.Task> iptal jetonu veya kapatma tetiklendiğinde tamamlanan bir uygulama döndürür.

### <a name="runconsoleasync"></a>RunConsoleAsync

<xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>konsol desteğini sağlar, ana bilgisayarı oluşturur ve başlatır ve <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT veya SIGTERM'in kapanmasını bekler.

### <a name="start"></a>Başlat

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*>ana bilgisayarı eşzamanlı olarak başlatır.

### <a name="startasync"></a>StartAsync

<xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>ana bilgisayarı başlatır <xref:System.Threading.Tasks.Task> ve iptal belirteci veya kapatma tetiklendiğinde bunu tamamlayan bir ev verir. 

<xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*>başlamadan önce tamamlanana `StartAsync`kadar bekleyen başında denir. Bu, harici bir olay tarafından sinyal verilene kadar başlatmayı geciktirmek için kullanılabilir.

### <a name="stopasync"></a>StopAsync

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*>sağlanan zaman ası içinde ana bilgisayarı durdurmaya çalışır.

### <a name="waitforshutdown"></a>Bekleme Kapatma

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*><kbd>ctrl</kbd>+<kbd>C</kbd>/SIGINT veya SIGTERM gibi IHostLifetime tarafından başlatılana kadar arama iş parçacığı engeller.

### <a name="waitforshutdownasync"></a>WaitForShutdownAsync

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*>verilen <xref:System.Threading.Tasks.Task> belirteç ve aramalar <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>aracılığıyla kapatma tetiklendiğinde tamamlayan bir döndürür.

### <a name="external-control"></a>Dış kontrol

Ana bilgisayar ömrünün doğrudan kontrolü, dışarıdan çağrılabilen yöntemler kullanılarak elde edilebilir:

```csharp
public class Program
{
    private IHost _host;

    public Program()
    {
        _host = new HostBuilder()
            .Build();
    }

    public async Task StartAsync()
    {
        _host.StartAsync();
    }

    public async Task StopAsync()
    {
        using (_host)
        {
            await _host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core uygulamaları bir ana bilgisayar yapılandırır ve başlatın. Ev sahibi uygulama başlatma ve yaşam boyu yönetiminden sorumludur.

Bu makale, HTTP isteklerini<xref:Microsoft.Extensions.Hosting.HostBuilder>işlemeyan uygulamalar için kullanılan ASP.NET Core Genel Ana Bilgisayar (),

Genel Ana Bilgisayar'ın amacı, daha geniş bir ana bilgisayar senaryosu dizisi sağlamak için HTTP ardışık hattını Web Ana Bilgisayar API'sinden ayırmaktır. Genel Ana Bilgisayar'a dayalı mesajlaşma, arka plan görevleri ve diğer HTTP dışındaki iş yükleri yapılandırma, bağımlılık enjeksiyonu (DI) ve günlüğe kaydetme gibi çapraz kesme özelliklerinden yararlanır.

Genel Ana Bilgisayar, ASP.NET Core 2.1'de yenidir ve web barındırma senaryoları için uygun değildir. Web barındırma senaryoları için [Web Barındıran Alanı'nı](xref:fundamentals/host/web-host)kullanın. Genel Ana Bilgisayar, gelecekteki bir sürümde Web Host'un yerini alır ve hem HTTP hem de HTTP olmayan senaryolarda birincil ana bilgisayar API'si olarak hareket eder.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ( nasıl[indirilir](xref:index#how-to-download-a-sample))

Örnek uygulamayı Visual [Studio Code'da](https://code.visualstudio.com/)çalıştırırken *harici veya entegre*bir terminal kullanın. Örneği bir `internalConsole`.

Konsolu Visual Studio Code'da ayarlamak için:

1. *.vscode/launch.json* dosyasını açın.
1. **.NET Core Launch (konsol)** yapılandırmasında, **konsol** girişini bulun. Değeri ya da `externalTerminal` `integratedTerminal`.

## <a name="introduction"></a>Giriş

Genel Ana Bilgisayar kitaplığı <xref:Microsoft.Extensions.Hosting> ad alanında kullanılabilir ve [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) paketi tarafından sağlanır. Paket `Microsoft.Extensions.Hosting` [Microsoft.AspNetCore.App metapaketinde](xref:fundamentals/metapackage-app) (core 2.1 veya daha sonra ASP.NET) dahildir.

<xref:Microsoft.Extensions.Hosting.IHostedService>kod yürütmeiçin giriş noktasıdır. Her <xref:Microsoft.Extensions.Hosting.IHostedService> uygulama [ConfigureServices hizmet kaydı](#configureservices)sırasına göre yürütülür. <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*>ana bilgisayar <xref:Microsoft.Extensions.Hosting.IHostedService> başladığında her birine <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> çağrılır ve ana bilgisayar zarif bir şekilde kapandığında ters kayıt sırasına çağrılır.

## <a name="set-up-a-host"></a>Ana bilgisayar ayarlama

<xref:Microsoft.Extensions.Hosting.IHostBuilder>kitaplıkların ve uygulamaların ana bilgisayarı başlatma, oluşturmak ve çalıştırmak için kullandığı ana bileşendir:

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_HostBuilder)]

## <a name="options"></a>Seçenekler

<xref:Microsoft.Extensions.Hosting.HostOptions>için seçenekleri yapılandırmak <xref:Microsoft.Extensions.Hosting.IHost>.

### <a name="shutdown-timeout"></a>Kapatma zaman

<xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*>için <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>zaman ası ayarlar. Varsayılan değer beş saniyedir.

Aşağıdaki seçenek yapılandırmasında `Program.Main` varsayılan beş saniyelik kapatma zaman dışarısını 20 saniyeye çıkarır:

```csharp
var host = new HostBuilder()
    .ConfigureServices((hostContext, services) =>
    {
        services.Configure<HostOptions>(option =>
        {
            option.ShutdownTimeout = System.TimeSpan.FromSeconds(20);
        });
    })
    .Build();
```

## <a name="default-services"></a>Varsayılan hizmetler

Aşağıdaki hizmetler ana bilgisayar başlatma sırasında kaydedilir:

* [Çevre](xref:fundamentals/environments) <xref:Microsoft.Extensions.Hosting.IHostingEnvironment>( )
* <xref:Microsoft.Extensions.Hosting.HostBuilderContext>
* [Yapılandırma](xref:fundamentals/configuration/index) <xref:Microsoft.Extensions.Configuration.IConfiguration>( )
* <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (`Microsoft.Extensions.Hosting.Internal.ApplicationLifetime`)
* <xref:Microsoft.Extensions.Hosting.IHostLifetime> (`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`)
* <xref:Microsoft.Extensions.Hosting.IHost>
* [Seçenekler](xref:fundamentals/configuration/options) <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>( )
* [Günlük](xref:fundamentals/logging/index) <xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>( )

## <a name="host-configuration"></a>Ana bilgisayar yapılandırması

Ana bilgisayar yapılandırması:

* İçerik <xref:Microsoft.Extensions.Hosting.IHostBuilder> [kökünü](#content-root) ve [ortamını](#environment)ayarlamak için uzantı yöntemlerini çağırma.
* Yapılandırma sağlayıcılarından yapılandırmayı <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>okuma.

### <a name="extension-methods"></a>Genişletme yöntemleri

### <a name="application-key-name"></a>Uygulama anahtarı (ad)

[IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) özelliği, ana bilgisayar inşaatı sırasında ana bilgisayar yapılandırmasından ayarlanır. Değeri açıkça ayarlamak için [HostDefaults.ApplicationKey:'i](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey)kullanın:

**Anahtar**:`applicationName`  
**Türü**:`string`  
**Varsayılan**: Uygulamanın giriş noktasını içeren derlemenin adı.  
**Set kullanarak:**`HostBuilderContext.HostingEnvironment.ApplicationName`  
**Çevre değişkeni**: `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` [isteğe bağlı ve kullanıcı tanımlıdır](#configurehostconfiguration))

### <a name="content-root"></a>İçerik kökü

Bu ayar, ana bilgisayarın içerik dosyalarını aramaya nerede başlayacağını belirler.

**Anahtar**:`contentRoot`  
**Türü**:`string`  
**Varsayılan**: Varsayılan olarak uygulama derlemesinin bulunduğu klasöre iner.  
**Set kullanarak:**`UseContentRoot`  
**Çevre değişkeni**: `<PREFIX_>CONTENTROOT` (`<PREFIX_>` [isteğe bağlı ve kullanıcı tanımlıdır](#configurehostconfiguration))

Yol yoksa, ana bilgisayar başlatılamıyor.

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseContentRoot)]

Daha fazla bilgi için [temel bilgiler: İçerik kökü.](xref:fundamentals/index#content-root)

### <a name="environment"></a>Ortam

Uygulamanın [ortamını](xref:fundamentals/environments)ayarlar.

**Anahtar**:`environment`  
**Türü**:`string`  
**Varsayılan**:`Production`  
**Set kullanarak:**`UseEnvironment`  
**Çevre değişkeni**: `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` [isteğe bağlı ve kullanıcı tanımlıdır](#configurehostconfiguration))

Ortam herhangi bir değere ayarlanabilir. Çerçeve tanımlı `Development`değerler `Staging`, `Production`ve . Değerler büyük/küçük harf duyarlı değildir.

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseEnvironment)]

### <a name="configurehostconfiguration"></a>YapılandırmaHostConfiguration

<xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>ana <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> bilgisayar için <xref:Microsoft.Extensions.Configuration.IConfiguration> bir oluşturmak için kullanır. Ana bilgisayar yapılandırması, uygulamanın <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> yapı işleminde kullanım için başlatma için kullanılır.

<xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>katkı sonuçları ile birden çok kez çağrılabilir. Ana bilgisayar, belirli bir anahtarda son bir değer belirleyen seçeneği kullanır.

Hiçbir sağlayıcı varsayılan olarak dahil değildir. Aşağıdakiler de dahil olmak üzere, <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>uygulamanın gerektirdiği yapılandırma sağlayıcılarını açıkça belirtmeniz gerekir:

* Dosya yapılandırması (örneğin, bir *hostsettings.json* dosyasından).
* Çevre değişken yapılandırması.
* Komut satırı bağımsız değişken yapılandırması.
* Diğer gerekli yapılandırma sağlayıcıları.

Ana bilgisayar dosya yapılandırması, uygulamanın temel yolunu `SetBasePath` belirterek ve ardından [dosya yapılandırma sağlayıcılarından](xref:fundamentals/configuration/index#file-configuration-provider)birine yapılan bir çağrıyla etkinleştirilir. Örnek uygulama bir JSON dosyası, *hostsettings.json*ve dosyanın ana bilgisayar yapılandırma ayarlarını tüketmek için aramalar <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> kullanır.

Ana bilgisayara [ortam değişkenyapılandırması](xref:fundamentals/configuration/index#environment-variables-configuration-provider) eklemek için ana bilgisayar oluşturucuyu arayın. <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> `AddEnvironmentVariables`isteğe bağlı kullanıcı tanımlı öneki kabul eder. Örnek uygulama bir önek `PREFIX_`kullanır. Çevre değişkenleri okunduğunda önek kaldırılır. Örnek uygulamanın ana bilgisayarı yapılandırıldığında, ortam `PREFIX_ENVIRONMENT` değişken değeri `environment` anahtarın ana bilgisayar yapılandırma değeri olur.

[Visual Studio](https://visualstudio.microsoft.com) kullanırken veya bir `dotnet run`uygulamayı çalıştırırken geliştirme *sırasında, özellikler/launchSettings.json* dosyasında ortam değişkenleri ayarlanabilir. [Visual Studio Code'da](https://code.visualstudio.com/)ortam değişkenleri geliştirme sırasında *.vscode/launch.json* dosyasında ayarlanabilir. Daha fazla bilgi için bkz. <xref:fundamentals/environments>.

[Komut satırı yapılandırması](xref:fundamentals/configuration/index#command-line-configuration-provider) çağrılar <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>eklenmez. Komut satırı yapılandırması, komut satırı bağımsız değişkenlerini önceki yapılandırma sağlayıcıları tarafından sağlanan yapılandırmayı geçersiz kılmak için en son eklenir.

*hostsettings.json*:

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/hostsettings.json)]

Ek yapılandırma [applicationName](#application-key-name) ve [contentRoot](#content-root) tuşları ile sağlanabilir.

Örnek `HostBuilder` yapılandırma <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>kullanarak:

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureHostConfiguration)]

## <a name="configureappconfiguration"></a>YapılandırmaAppConfiguration

Uygulama yapılandırması, <xref:Microsoft.Extensions.Hosting.IHostBuilder> <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> uygulama çağırılarak oluşturulur. <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>uygulama <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> için bir <xref:Microsoft.Extensions.Configuration.IConfiguration> oluşturmak için kullanır. <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>katkı sonuçları ile birden çok kez çağrılabilir. Uygulama, belirli bir anahtarda son bir değer belirleyen seçeneği kullanır. Tarafından <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> oluşturulan yapılandırma sonraki işlemler için [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) kullanılabilir ve <xref:Microsoft.Extensions.Hosting.IHost.Services*>.

Uygulama [yapılandırması, ConfigureHostConfiguration](#configurehostconfiguration)tarafından sağlanan ana bilgisayar yapılandırması otomatik olarak alır.

Örnek uygulama <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>yapılandırması kullanarak:

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureAppConfiguration)]

*appsettings.json*:

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.json)]

*ayarları. Development.json*:

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Development.json)]

*ayarları. Üretim.json*:

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Production.json)]

Ayarlar dosyalarını çıktı dizinine taşımak için, proje dosyasındaki ayarlar dosyalarını [MSBuild proje öğeleri](/visualstudio/msbuild/common-msbuild-project-items) olarak belirtin. Örnek uygulama JSON uygulama ayarları dosyalarını ve *hostsettings.json'u* aşağıdaki `<Content>` öğeyle hareket ettirir:

```xml
<ItemGroup>
  <Content Include="**\*.json" Exclude="bin\**\*;obj\**\*" 
      CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

> [!NOTE]
> <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) ve <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> [Microsoft.Extensions.Configuration.EnvironmentVariables](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.EnvironmentVariables)gibi ek NuGet paketleri gibi yapılandırma uzantısı yöntemleri. Uygulama [Microsoft.AspNetCore.App metapaketini](xref:fundamentals/metapackage-app)kullanmadığı sürece, bu paketler in core [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) paketine ek olarak projeye eklenmelidir. Daha fazla bilgi için bkz. <xref:fundamentals/configuration/index>.

## <a name="configureservices"></a>YapılandırmaHizmetleri

<xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*>uygulamanın [bağımlılık enjeksiyon](xref:fundamentals/dependency-injection) konteynerine hizmetler ekler. <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*>katkı sonuçları ile birden çok kez çağrılabilir.

Barındırılan hizmet, arabirimi uygulayan arka <xref:Microsoft.Extensions.Hosting.IHostedService> plan görev mantığına sahip bir sınıftır. Daha fazla bilgi için bkz. <xref:fundamentals/host/hosted-services>.

[Örnek uygulama,](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) `AddHostedService` `LifetimeEventsHostedService`yaşam boyu olaylar için bir hizmet ve zamanlanmış arka `TimedHostedService`plan görevi, uygulamaya eklemek için uzantı yöntemini kullanır:

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureServices)]

## <a name="configurelogging"></a>YapılandırmaLogging

<xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*>sağlanan <xref:Microsoft.Extensions.Logging.ILoggingBuilder>yapılandırma için bir temsilci ekler. <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*>katkı sonuçları ile birden çok kez çağrılabilir.

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureLogging)]

### <a name="useconsolelifetime"></a>UseConsoleÖmür Boyu

<xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*><kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT veya SIGTERM'i dinler ve kapatma işlemini başlatmak için çağrıda bulunur. <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*>[RunAsync](#runasync) ve [WaitForShutdownAsync](#waitforshutdownasync)gibi uzantıları unblocks . `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`varsayılan ömür boyu uygulama olarak önceden kaydedilmiştir. Son ömür boyu kayıtlı kullanılır.

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseConsoleLifetime)]

## <a name="container-configuration"></a>Konteyner yapılandırması

Diğer kapsayıcılara takMayı desteklemek için, <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>ana bilgisayar bir . Bir fabrika sağlamak DI konteyner kaydının bir parçası değildir, ancak bunun yerine beton DI konteyner oluşturmak için kullanılan bir ana bilgisayardır. [UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder),&gt;](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) uygulamanın servis sağlayıcısını oluşturmak için kullanılan varsayılan fabrikayı geçersiz kılar.

Özel kapsayıcı yapılandırmayöntemi <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> tarafından yönetilir. <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*>kapsayıcıyı temel ana bilgisayar API'sinin üzerine yapılandırmak için güçlü bir şekilde yazılmamış bir deneyim sağlar. <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*>katkı sonuçları ile birden çok kez çağrılabilir.

Uygulama için bir hizmet kapsayıcısı oluşturun:

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainer.cs)]

Bir servis konteyner fabrikası sağlayın:

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainerFactory.cs)]

Fabrikayı kullanın ve uygulama için özel servis konteynerini yapılandırın:

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ContainerConfiguration)]

## <a name="extensibility"></a>Genişletilebilirlik

Ana bilgisayar genişletilebilirliği uzantısı yöntemleri <xref:Microsoft.Extensions.Hosting.IHostBuilder>ile gerçekleştirilir. Aşağıdaki örnek, uzantı yönteminin <xref:Microsoft.Extensions.Hosting.IHostBuilder> [timedHostedservice](xref:fundamentals/host/hosted-services#timed-background-tasks) örneğinde gösterildiği bir <xref:fundamentals/host/hosted-services>uygulamayı nasıl genişletir gösteriş olduğunu gösterir.

```csharp
var host = new HostBuilder()
    .UseHostedService<TimedHostedService>()
    .Build();

await host.StartAsync();
```

Bir uygulama, `UseHostedService` barındırılan hizmeti kaydetmek için `T`uzantı yöntemini belirler:

```csharp
using System;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;

public static class Extensions
{
    public static IHostBuilder UseHostedService<T>(this IHostBuilder hostBuilder)
        where T : class, IHostedService, IDisposable
    {
        return hostBuilder.ConfigureServices(services =>
            services.AddHostedService<T>());
    }
}
```

## <a name="manage-the-host"></a>Ev sahibini yönetme

Uygulama, <xref:Microsoft.Extensions.Hosting.IHost> hizmet konteynerine <xref:Microsoft.Extensions.Hosting.IHostedService> kayıtlı uygulamaların başlatılmasından ve durdurulmasından sorumludur.

### <a name="run"></a>Çalıştırın

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*>uygulamayı çalıştırAn ve ana bilgisayar kapatılana kadar arama iş parçacığı engeller:

```csharp
public class Program
{
    public void Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        host.Run();
    }
}
```

### <a name="runasync"></a>RunAsync

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*>uygulamayı çalıştırAn ve <xref:System.Threading.Tasks.Task> iptal jetonu veya kapatma tetiklendiğinde tamamlanan bir uygulama döndürür:

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        await host.RunAsync();
    }
}
```

### <a name="runconsoleasync"></a>RunConsoleAsync

<xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>konsol desteğini sağlar, ana bilgisayarı oluşturur ve başlatır ve <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT veya SIGTERM'in kapanmasını bekler.

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var hostBuilder = new HostBuilder();

        await hostBuilder.RunConsoleAsync();
    }
}
```

### <a name="start-and-stopasync"></a>Başlat ve DurdurAsync

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*>ana bilgisayarı eşzamanlı olarak başlatır.

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*>sağlanan zaman ası içinde ana bilgisayarı durdurmaya çalışır.

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            host.Start();

            await host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

### <a name="startasync-and-stopasync"></a>StartAsync ve StopAsync

<xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>uygulamayı başlatır.

<xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>uygulamayı durdurur.

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            await host.StartAsync();

            await host.StopAsync();
        }
    }
}
```

### <a name="waitforshutdown"></a>Bekleme Kapatma

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*><xref:Microsoft.Extensions.Hosting.IHostLifetime>(Ctrl <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT veya SIGTERM için dinler) gibi) `Microsoft.Extensions.Hosting.Internal.ConsoleLifetime` aracılığıyla tetiklenir. <xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*>çağırır. <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>

```csharp
public class Program
{
    public void Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            host.Start();

            host.WaitForShutdown();
        }
    }
}
```

### <a name="waitforshutdownasync"></a>WaitForShutdownAsync

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*>verilen <xref:System.Threading.Tasks.Task> belirteç ve aramalar <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>aracılığıyla kapatma tetiklendiğinde tamamlayan bir döndürür.

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            await host.StartAsync();

            await host.WaitForShutdownAsync();
        }

    }
}
```

### <a name="external-control"></a>Dış kontrol

Ana bilgisayar dış kontrolü, dışarıdan çağrılabilen yöntemler kullanılarak elde edilebilir:

```csharp
public class Program
{
    private IHost _host;

    public Program()
    {
        _host = new HostBuilder()
            .Build();
    }

    public async Task StartAsync()
    {
        _host.StartAsync();
    }

    public async Task StopAsync()
    {
        using (_host)
        {
            await _host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

<xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*>başlamadan önce tamamlanana <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>kadar bekleyen başında denir. Bu, harici bir olay tarafından sinyal verilene kadar başlatmayı geciktirmek için kullanılabilir.

## <a name="ihostingenvironment-interface"></a>IHostingEnvironment arayüzü

<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>uygulamanın barındırma ortamı hakkında bilgi sağlar. Özelliklerini ve uzatma yöntemlerini kullanmak için [konstrüktör enjeksiyonunu](xref:fundamentals/dependency-injection) <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> kullanın:

```csharp
public class MyClass
{
    private readonly IHostingEnvironment _env;

    public MyClass(IHostingEnvironment env)
    {
        _env = env;
    }

    public void DoSomething()
    {
        var environmentName = _env.EnvironmentName;
    }
}
```

Daha fazla bilgi için bkz. <xref:fundamentals/environments>.

## <a name="iapplicationlifetime-interface"></a>IApplicationLifetime arayüzü

<xref:Microsoft.Extensions.Hosting.IApplicationLifetime>zarif kapatma istekleri de dahil olmak üzere, başlatma ve kapatma etkinliklerine izin verir. Arabirimdeki üç özellik, başlatma ve <xref:System.Action> kapatma olaylarını tanımlayan yöntemleri kaydetmek için kullanılan iptal belirteçleridir.

| İptal Jetonu | &#8230; tetiklenir |
| ------------------ | --------------------- |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStarted*> | Ev sahibi tamamen başladı. |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopped*> | Ev sahibi zarif bir kapatma tamamlıyor. Tüm istekler işlenmelidir. Bu olay tamamlanana kadar kapatma blokları. |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopping*> | Ev sahibi zarif bir kapatma gerçekleştiriyor. İstekler hala işliyor olabilir. Bu olay tamamlanana kadar kapatma blokları. |

Yapıcı herhangi bir <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> sınıfa hizmet enjekte. [Örnek uygulama](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) olayları kaydetmek için `LifetimeEventsHostedService` bir <xref:Microsoft.Extensions.Hosting.IHostedService> sınıf (bir uygulama) içine yapıcı enjeksiyon kullanır.

*LifetimeEventsHostedService.cs*:

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/LifetimeEventsHostedService.cs?name=snippet1)]

<xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*>uygulamanın sonlandırılmasını talep ediyor. Aşağıdaki sınıf, <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> sınıfın `Shutdown` yöntemi çağrıldığında bir uygulamayı zarif bir şekilde kapatmak için kullanır:

```csharp
public class MyClass
{
    private readonly IApplicationLifetime _appLifetime;

    public MyClass(IApplicationLifetime appLifetime)
    {
        _appLifetime = appLifetime;
    }

    public void Shutdown()
    {
        _appLifetime.StopApplication();
    }
}
```

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

Core şablonlarının ASP.NET .NET Core Genel<xref:Microsoft.Extensions.Hosting.HostBuilder>Ana Bilgisayar ( ) oluşturur.

## <a name="host-definition"></a>Ana bilgisayar tanımı

*Ana bilgisayar,* bir uygulamanın kaynaklarını kapsülleyen bir nesnedir:

* Bağımlılık enjeksiyonu (DI)
* Günlüğe kaydetme
* Yapılandırma
* `IHostedService`Uygulama

Bir ana bilgisayar başlatıldığında, DI <xref:Microsoft.Extensions.Hosting.IHostedService> kapsayıcısında bulduğu her uygulamayı çağırır. `IHostedService.StartAsync` Bir web uygulamasında, `IHostedService` uygulamalardan biri http sunucu [uygulaması](xref:fundamentals/index#servers)başlatan bir web hizmetidir.

Uygulamanın tüm birbirine bağlı kaynaklarının tek bir nesneye dahil olmasının temel nedeni ömür boyu yönetimdir: uygulama başlatma üzerinde denetim ve zarif kapatma.

## <a name="set-up-a-host"></a>Ana bilgisayar ayarlama

Ana bilgisayar genellikle `Program` sınıfta kodtarafından yapılandırılır, oluşturulur ve çalıştırılır. Yöntem: `Main`

* Oluşturucu `CreateHostBuilder` nesne oluşturmak ve yapılandırmak için bir yöntem çağırır.
* Oluşturucu nesneüzerinde aramalar `Build` ve `Run` yöntemler.

ASP.NET Core web şablonları bir ana bilgisayar oluşturmak için aşağıdaki kodu oluşturur:

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStartup<Startup>();
            });
}
```

Aşağıdaki kod, DI kapsayıcısına eklenen `IHostedService` bir uygulamayla HTTP dışı bir iş yükü oluşturur.

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureServices((hostContext, services) =>
            {
               services.AddHostedService<Worker>();
            });
}
```

Bir HTTP iş `Main` yükü için yöntem `CreateHostBuilder` `ConfigureWebHostDefaults`aynıdır, ancak çağırır:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

Uygulama Entity Framework Core kullanıyorsa, yöntemin adını `CreateHostBuilder` veya imzasını değiştirmeyin. [Entity Framework Core araçları,](/ef/core/miscellaneous/cli/) `CreateHostBuilder` uygulamayı çalıştırmadan ana bilgisayarı yapılandıran bir yöntem bulmayı bekler. Daha fazla bilgi için tasarım [zamanı DbContext Oluşturma bölümüne](/ef/core/miscellaneous/cli/dbcontext-creation)bakın.

## <a name="default-builder-settings"></a>Varsayılan oluşturucu ayarları

Yöntem: <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>

* İçerik [kökünü](xref:fundamentals/index#content-root) döndürülen <xref:System.IO.Directory.GetCurrentDirectory*>yola ayarlar.
* Yükler ana bilgisayar yapılandırması:
  * Çevre değişkenleri . `DOTNET_`
  * Komut satırı bağımsız değişkenleri.
* Uygulama yapılandırması:
  * *appsettings.json*.
  * *ayarları. {Çevre}.json*.
  * [Uygulama](xref:security/app-secrets) `Development` ortamda çalıştığında Gizli Yönetici.
  * Ortam değişkenleri.
  * Komut satırı bağımsız değişkenleri.
* Aşağıdaki [günlük](xref:fundamentals/logging/index) sağlayıcılarını ekler:
  * Konsol
  * Hata ayıklama
  * EventSource
  * EventLog (yalnızca Windows'da çalışırken)
* Ortam Geliştirme olduğunda [kapsam doğrulama](xref:fundamentals/dependency-injection#scope-validation) ve [bağımlılık doğrulamasağlar.](xref:Microsoft.Extensions.DependencyInjection.ServiceProviderOptions.ValidateOnBuild)

Yöntem: `ConfigureWebHostDefaults`

* `ASPNETCORE_`'ile önceden belirlenmiş ortam değişkenlerinden ana bilgisayar yapılandırması yükler.
* [Kestrel](xref:fundamentals/servers/kestrel) sunucusunu web sunucusu olarak ayarlar ve uygulamanın barındırma yapılandırma sağlayıcılarını kullanarak yapılandırır. Kestrel sunucusunun varsayılan seçenekleri için <xref:fundamentals/servers/kestrel#kestrel-options>bkz.
* [Ana Bilgisayar Filtreleme ara yazılım](xref:fundamentals/servers/kestrel#host-filtering)ekler.
* `ASPNETCORE_FORWARDEDHEADERS_ENABLED` `true` [Eşitse Iletili Üstbilgi ara ware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers) ekler.
* IIS tümleştirmesini sağlar. IIS varsayılan seçenekleri için <xref:host-and-deploy/iis/index#iis-options>bkz.

Bu makalenin ilerleyen bölümlerinde [tüm uygulama türleri için Ayarlar](#settings-for-all-app-types) ve web uygulamaları bölümleri için [Ayarlar,](#settings-for-web-apps) varsayılan oluşturucu ayarlarını nasıl geçersiz kılınan durumu gösterir.

## <a name="framework-provided-services"></a>Çerçeve tarafından sağlanan hizmetler

Aşağıdaki hizmetler otomatik olarak kaydedilir:

* [IHostApplicationÖmür Boyu](#ihostapplicationlifetime)
* [IHostLifetime](#ihostlifetime)
* [IHostEnvironment / IWebHostEnvironment](#ihostenvironment)

Çerçeve tarafından sağlanan hizmetler hakkında <xref:fundamentals/dependency-injection#framework-provided-services>daha fazla bilgi için bkz.

## <a name="ihostapplicationlifetime"></a>IHostApplicationÖmür Boyu

Başlatma <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> sonrası ve `IApplicationLifetime`zarif kapatma görevlerini işlemek için (eski) hizmeti herhangi bir sınıfa enjekte edin. Arabirimdeki üç özellik, uygulama başlatma ve uygulama durdurma olay işleyicisi yöntemlerini kaydetmek için kullanılan iptal belirteçleridir. Arabirim de `StopApplication` bir yöntem içerir.

Aşağıdaki örnek, `IHostedService` `IHostApplicationLifetime` olayları kaydeden bir uygulamadır:

[!code-csharp[](generic-host/samples-snapshot/3.x/LifetimeEventsHostedService.cs?name=snippet_LifetimeEvents)]

## <a name="ihostlifetime"></a>IHostLifetime

Uygulama, <xref:Microsoft.Extensions.Hosting.IHostLifetime> ana bilgisayar ne zaman başlar ve ne zaman dursın denetler. Son kayıtlı uygulama kullanılır.

`Microsoft.Extensions.Hosting.Internal.ConsoleLifetime`varsayılan `IHostLifetime` uygulamadır. `ConsoleLifetime`:

* <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT veya SIGTERM <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime.StopApplication*> için dinler ve kapatma işlemini başlatmak için çağırır.
* [RunAsync](#runasync) ve [WaitForShutdownAsync](#waitforshutdownasync)gibi uzantıları unblocks .

## <a name="ihostenvironment"></a>IHostEnvironment

Aşağıdaki <xref:Microsoft.Extensions.Hosting.IHostEnvironment> ayarlar hakkında bilgi almak için hizmeti bir sınıfa enjekte edin:

* [ApplicationName](#applicationname)
* [Çevre Adı](#environmentname)
* [İçerikRootPath](#contentroot)

Web uygulamaları, `IWebHostEnvironment` `IHostEnvironment` [WebRootPath'i](#webroot)devralan ve ekleyen arabirimi uygular.

## <a name="host-configuration"></a>Ana bilgisayar yapılandırması

Ana bilgisayar yapılandırması <xref:Microsoft.Extensions.Hosting.IHostEnvironment> uygulamanın özellikleri için kullanılır.

Ana bilgisayar yapılandırması [HostBuilderBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration) içinde <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>kullanılabilir. Sonra `ConfigureAppConfiguration` `HostBuilderContext.Configuration` , uygulama config ile değiştirilir.

Ana bilgisayar yapılandırması <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> `IHostBuilder`eklemek için, ''yi arayın. `ConfigureHostConfiguration`katkı sonuçları ile birden çok kez çağrılabilir. Ana bilgisayar, belirli bir anahtarda son bir değer belirleyen seçeneği kullanır.

Önek `DOTNET_` ve komut satırı bağımsız değişkenleri olan `CreateDefaultBuilder`ortam değişken sağlayıcısı. Web uygulamaları için önek `ASPNETCORE_` li ortam değişken sağlayıcısı eklenir. Çevre değişkenleri okunduğunda önek kaldırılır. Örneğin, ortam `ASPNETCORE_ENVIRONMENT` değişken değeri `environment` anahtarın ana bilgisayar yapılandırma değeri olur.

Aşağıdaki örnek ana bilgisayar yapılandırmasını oluşturur:

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostConfig)]

## <a name="app-configuration"></a>Uygulama yapılandırması

Uygulama yapılandırması <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> çağırarak `IHostBuilder`oluşturulur. `ConfigureAppConfiguration`katkı sonuçları ile birden çok kez çağrılabilir. Uygulama, belirli bir anahtarda son bir değer belirleyen seçeneği kullanır. 

Tarafından `ConfigureAppConfiguration` oluşturulan yapılandırma sonraki işlemler için [HostBuilderContext.Configuration'da](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) ve DI'den bir hizmet olarak kullanılabilir. Ana bilgisayar yapılandırması da uygulama yapılandırmasına eklenir.

Daha fazla bilgi için [ASP.NET Core'daki Yapılandırma'ya](xref:fundamentals/configuration/index#configureappconfiguration)bakın.

## <a name="settings-for-all-app-types"></a>Tüm uygulama türleri için ayarlar

Bu bölümde hem HTTP hem de HTTP dışı iş yükleri için geçerli olan ana bilgisayar ayarları listelenir. Varsayılan olarak, bu ayarları yapılandırmak için kullanılan `DOTNET_` `ASPNETCORE_` ortam değişkenleri bir veya önek olabilir.

<!-- In the following sections, two spaces at end of line are used to force line breaks in the rendered page. -->

### <a name="applicationname"></a>ApplicationName

[IHostEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ApplicationName*) özelliği, ana bilgisayar yapısı sırasında ana bilgisayar yapılandırmasından ayarlanır.

**Anahtar**:`applicationName`  
**Türü**:`string`  
**Varsayılan**: Uygulamanın giriş noktasını içeren derlemenin adı.  
**Çevre değişkeni**:`<PREFIX_>APPLICATIONNAME`

Bu değeri ayarlamak için ortam değişkenini kullanın. 

### <a name="contentroot"></a>İçerikKök

[IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath*) özelliği, ana bilgisayarın içerik dosyalarını aramaya başladığı yeri belirler. Yol yoksa, ana bilgisayar başlatılamıyor.

**Anahtar**:`contentRoot`  
**Türü**:`string`  
**Varsayılan**: Uygulama derlemesinin bulunduğu klasör.  
**Çevre değişkeni**:`<PREFIX_>CONTENTROOT`

Bu değeri ayarlamak için, ortam `UseContentRoot` değişkenini kullanın veya çağırın: `IHostBuilder`

```csharp
Host.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\content-root")
    //...
```

Daha fazla bilgi için bkz.

* [Temel: İçerik kökü](xref:fundamentals/index#content-root)
* [Webroot](#webroot)

### <a name="environmentname"></a>Çevre Adı

[IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName*) özelliği herhangi bir değere ayarlanabilir. Çerçeve tanımlı `Development`değerler `Staging`, `Production`ve . Değerler büyük/küçük harf duyarlı değildir.

**Anahtar**:`environment`  
**Türü**:`string`  
**Varsayılan**:`Production`  
**Çevre değişkeni**:`<PREFIX_>ENVIRONMENT`

Bu değeri ayarlamak için, ortam `UseEnvironment` değişkenini kullanın veya çağırın: `IHostBuilder`

```csharp
Host.CreateDefaultBuilder(args)
    .UseEnvironment("Development")
    //...
```

### <a name="shutdowntimeout"></a>KapatmaTimeout

[HostOptions.ShutdownTimeout](xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*) için <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>zaman ası ayarlar. Varsayılan değer beş saniyedir.  Zaman açılığı dönüşü nde, ana bilgisayar

* Tetikler [IHostApplicationLifetime.ApplicationStop](/dotnet/api/microsoft.extensions.hosting.ihostapplicationlifetime.applicationstopping).
* Barındırılan hizmetleri durdurmaya çalışır, duramayan hizmetler için günlüğe kaydetme hataları.

Zaman aşımı süresi, barındırılan hizmetlerin tümü sona ermeden önce sona ererse, uygulama kapandığında kalan tüm etkin hizmetler durdurulur. Hizmetler işleme meden ilerlememiş olsalar bile durur. Hizmetlerin durması için ek süre gerekiyorsa, zaman arasını artırın.

**Anahtar**:`shutdownTimeoutSeconds`  
**Türü**:`int`  
**Varsayılan**: 5 saniye  
**Çevre değişkeni**:`<PREFIX_>SHUTDOWNTIMEOUTSECONDS`

Bu değeri ayarlamak için ortam değişkenini kullanın veya yapılandırın. `HostOptions` Aşağıdaki örnek, zaman anına göre 20 saniyeye ayarlar:

[!code-csharp[](generic-host/samples-snapshot/3.x/Program.cs?name=snippet_HostOptions)]

### <a name="disable-app-configuration-reload-on-change"></a>Değişiklikte uygulama yapılandırması yeniden yüklemeyi devre dışı kaldırma

[Varsayılan olarak,](xref:fundamentals/configuration/index#default) *appsettings.json* ve *appsettings.{ Dosya değiştiğinde Environment}.json* yeniden yüklenir. Bu yeniden yükleme davranışını ASP.NET Core 5.0 Preview 3 `hostBuilder:reloadConfigOnChange` veya `false`sonraki ASP.NET devre dışı bırakabilirsiniz, anahtarı .

**Anahtar**:`hostBuilder:reloadConfigOnChange`  
**Türü** `bool` :`true` `1`( veya )  
**Varsayılan**:`true`  
**Komut satırı bağımsız değişkeni**:`hostBuilder:reloadConfigOnChange`  
**Çevre değişkeni**:`<PREFIX_>hostBuilder:reloadConfigOnChange`

> [!WARNING]
> İki nokta`:`üst üste ( ) ayırıcı tüm platformlarda ortam değişkeni hiyerarşik anahtarlarla çalışmaz. Daha fazla bilgi için [Bkz. Çevre değişkenleri.](xref:fundamentals/configuration/index#environment-variables)

## <a name="settings-for-web-apps"></a>Web uygulamaları için ayarlar

Bazı ana bilgisayar ayarları yalnızca HTTP iş yükleri için geçerlidir. Varsayılan olarak, bu ayarları yapılandırmak için kullanılan `DOTNET_` `ASPNETCORE_` ortam değişkenleri bir veya önek olabilir.

Bu ayarlar `IWebHostBuilder` için uzantı yöntemleri mevcuttur. Uzantı yöntemlerinin nasıl çağrıldığını `webBuilder` gösteren kod `IWebHostBuilder`örnekleri aşağıdaki örnekte olduğu gibi, aşağıdaki gibi bir örnek olduğunu varsayar:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.CaptureStartupErrors(true);
            webBuilder.UseStartup<Startup>();
        });
```

### <a name="capturestartuperrors"></a>YakalamaBaşlangıç Hataları

Ne `false`zaman , başlatma sırasında hatalar ana bilgisayar çıkışı sonucu. Ana `true`bilgisayar başlangıç sırasında özel durumları yakaladığında ve sunucuyu başlatmaya çalıştığında.

**Anahtar**:`captureStartupErrors`  
**Türü** `bool` :`true` `1`( veya )  
**Varsayılan**: Uygulama, varsayılan ın olduğu IIS'nin arkasında Kestrel ile birlikte çalıştığı `false` sürece varsayılan `true`dır.  
**Çevre değişkeni**:`<PREFIX_>CAPTURESTARTUPERRORS`

Bu değeri ayarlamak için yapılandırmayı veya aramayı `CaptureStartupErrors`kullanın:

```csharp
webBuilder.CaptureStartupErrors(true);
```

### <a name="detailederrors"></a>Ayrıntılı Hatalar

Etkinleştirildiğinde veya ortam `Development`etkinleştirildiğinde, uygulama ayrıntılı hataları yakalar.

**Anahtar**:`detailedErrors`  
**Türü** `bool` :`true` `1`( veya )  
**Varsayılan**:`false`  
**Çevre değişkeni**:`<PREFIX_>_DETAILEDERRORS`

Bu değeri ayarlamak için yapılandırmayı veya aramayı `UseSetting`kullanın:

```csharp
webBuilder.UseSetting(WebHostDefaults.DetailedErrorsKey, "true");
```

### <a name="hostingstartupassemblies"></a>HostingStartupAssemblies

Başlangıç yüklemeiçin barındırma başlangıç derlemeleri yarı kolon-sınırlı dize. Yapılandırma değeri boş bir dize için varsayılan olsa da, barındırma başlangıç derlemeleri her zaman uygulamanın derlemesini içerir. Başlangıç derlemelerine barındırma sağlandığında, uygulama başlangıç sırasında ortak hizmetlerini oluşturduğunda yükleme için uygulamanın derlemesine eklenirler.

**Anahtar**:`hostingStartupAssemblies`  
**Türü**:`string`  
**Varsayılan**: Boş dize  
**Çevre değişkeni**:`<PREFIX_>_HOSTINGSTARTUPASSEMBLIES`

Bu değeri ayarlamak için yapılandırmayı veya aramayı `UseSetting`kullanın:

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2");
```

### <a name="hostingstartupexcludeassemblies"></a>HostingStartupExcludeMontajies

Başlangıç tarihinde hariç tutmak için barındırma başlangıç derlemeleri yarı sütunlu sınırlı dize.

**Anahtar**:`hostingStartupExcludeAssemblies`  
**Türü**:`string`  
**Varsayılan**: Boş dize  
**Çevre değişkeni**:`<PREFIX_>_HOSTINGSTARTUPEXCLUDEASSEMBLIES`

Bu değeri ayarlamak için yapılandırmayı veya aramayı `UseSetting`kullanın:

```csharp
webBuilder.UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2");
```

### <a name="https_port"></a>HTTPS_Port

HTTPS yönlendirme bağlantı noktası. [HTTPS'nin uygulanmasında](xref:security/enforcing-ssl)kullanılır.

**Anahtar**:`https_port`  
**Türü**:`string`  
**Varsayılan**: Varsayılan değer ayarlı değil.  
**Çevre değişkeni**:`<PREFIX_>HTTPS_PORT`

Bu değeri ayarlamak için yapılandırmayı veya aramayı `UseSetting`kullanın:

```csharp
webBuilder.UseSetting("https_port", "8080");
```

### <a name="preferhostingurls"></a>TercihHostingUrls

Ana bilgisayar, `IWebHostBuilder` `IServer` uygulamayla yapılandırılan URL'ler yerine yapılandırılan URL'leri dinleyip dinlememesi gerektiğini gösterir.

**Anahtar**:`preferHostingUrls`  
**Türü** `bool` :`true` `1`( veya )  
**Varsayılan**:`true`  
**Çevre değişkeni**:`<PREFIX_>_PREFERHOSTINGURLS`

Bu değeri ayarlamak için ortam değişkenini kullanın veya çağırın: `PreferHostingUrls`

```csharp
webBuilder.PreferHostingUrls(false);
```

### <a name="preventhostingstartup"></a>HostingBaşlangıç önleme

Uygulamanın montajı tarafından yapılandırılan başlangıç derlemelerini barındırma da dahil olmak üzere barındırma başlangıç derlemelerinin otomatik olarak yüklenmesiengellenir. Daha fazla bilgi için bkz. <xref:fundamentals/configuration/platform-specific-configuration>.

**Anahtar**:`preventHostingStartup`  
**Türü** `bool` :`true` `1`( veya )  
**Varsayılan**:`false`  
**Çevre değişkeni**:`<PREFIX_>_PREVENTHOSTINGSTARTUP`

Bu değeri ayarlamak için ortam değişkenini kullanın veya çağırın: `UseSetting`

```csharp
webBuilder.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true");
```

### <a name="startupassembly"></a>Başlangıç Montaj

`Startup` Sınıfı aramak için derleme.

**Anahtar**:`startupAssembly`  
**Türü**:`string`  
**Varsayılan**: Uygulamanın montajı  
**Çevre değişkeni**:`<PREFIX_>STARTUPASSEMBLY`

Bu değeri ayarlamak için ortam değişkenini kullanın veya çağırın. `UseStartup` `UseStartup`bir derleme adı`string`( ) veya`TStartup`bir tür ( alabilir). Birden `UseStartup` çok yöntem çağrılırsa, sonuncusu önceliklidir.

```csharp
webBuilder.UseStartup("StartupAssemblyName");
```

```csharp
webBuilder.UseStartup<Startup>();
```

### <a name="urls"></a>URL’ler

Sunucunun istekler için dinlemesi gereken bağlantı noktaları ve protokolleri olan IP adreslerinin veya ana bilgisayar adreslerinin yarı sütunlu sınırlı listesi. Örneğin, `http://localhost:123`. Sunucunun\*belirtilen bağlantı noktası ve protokolü kullanarak herhangi bir IP adresi veya ana bilgisayar `http://*:5000`adı üzerindeki istekleri dinlemesi gerektiğini belirtmek için " " kullanın. Protokol (`http://` `https://`veya ) her URL'ye eklenmelidir. Desteklenen biçimler sunucular arasında farklılık gösterir.

**Anahtar**:`urls`  
**Türü**:`string`  
**Varsayılan** `http://localhost:5000` : ve`https://localhost:5001`  
**Çevre değişkeni**:`<PREFIX_>URLS`

Bu değeri ayarlamak için ortam değişkenini kullanın veya çağırın: `UseUrls`

```csharp
webBuilder.UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002");
```

Kerkenez kendi bitiş noktası yapılandırma API vardır. Daha fazla bilgi için bkz. <xref:fundamentals/servers/kestrel#endpoint-configuration>.

### <a name="webroot"></a>Webroot

[IWebHostEnvironment.WebRootPath](xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment.WebRootPath) özelliği uygulamanın statik varlıklarıiçin göreli yolu belirler. Yol yoksa, bir no-op dosya sağlayıcısı kullanılır.  

**Anahtar**:`webroot`  
**Türü**:`string`  
**Varsayılan**: Varsayılan `wwwroot`. *{içerik kökü}/wwwroot* yolu olmalıdır.  
**Çevre değişkeni**:`<PREFIX_>WEBROOT`

Bu değeri ayarlamak için, ortam `UseWebRoot` değişkenini kullanın veya çağırın: `IWebHostBuilder`

```csharp
webBuilder.UseWebRoot("public");
```

Daha fazla bilgi için bkz.

* [Temel: Web kökü](xref:fundamentals/index#web-root)
* [İçerikKök](#contentroot)

## <a name="manage-the-host-lifetime"></a>Ev sahibinin ömrünü yönetin

Uygulamayı başlatmak ve <xref:Microsoft.Extensions.Hosting.IHost> durdurmak için yerleşik uygulamadaki yöntemleri arayın. Bu yöntemler, <xref:Microsoft.Extensions.Hosting.IHostedService> hizmet kapsayıcısında kayıtlı tüm uygulamaları etkiler.

### <a name="run"></a>Çalıştırın

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*>uygulamayı çalıştırArak, ana bilgisayar kapatılana kadar arama iş parçacığının engellenmesini engeller.

### <a name="runasync"></a>RunAsync

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*>uygulamayı çalıştırAn ve <xref:System.Threading.Tasks.Task> iptal jetonu veya kapatma tetiklendiğinde tamamlanan bir uygulama döndürür.

### <a name="runconsoleasync"></a>RunConsoleAsync

<xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*>konsol desteğini sağlar, ana bilgisayarı oluşturur ve başlatır ve <kbd>Ctrl</kbd>+<kbd>C</kbd>/SIGINT veya SIGTERM'in kapanmasını bekler.

### <a name="start"></a>Başlat

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*>ana bilgisayarı eşzamanlı olarak başlatır.

### <a name="startasync"></a>StartAsync

<xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>ana bilgisayarı başlatır <xref:System.Threading.Tasks.Task> ve iptal belirteci veya kapatma tetiklendiğinde bunu tamamlayan bir ev verir. 

<xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*>başlamadan önce tamamlanana `StartAsync`kadar bekleyen başında denir. Bu, harici bir olay tarafından sinyal verilene kadar başlatmayı geciktirmek için kullanılabilir.

### <a name="stopasync"></a>StopAsync

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*>sağlanan zaman ası içinde ana bilgisayarı durdurmaya çalışır.

### <a name="waitforshutdown"></a>Bekleme Kapatma

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*><kbd>ctrl</kbd>+<kbd>C</kbd>/SIGINT veya SIGTERM gibi IHostLifetime tarafından başlatılana kadar arama iş parçacığı engeller.

### <a name="waitforshutdownasync"></a>WaitForShutdownAsync

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*>verilen <xref:System.Threading.Tasks.Task> belirteç ve aramalar <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*>aracılığıyla kapatma tetiklendiğinde tamamlayan bir döndürür.

### <a name="external-control"></a>Dış kontrol

Ana bilgisayar ömrünün doğrudan kontrolü, dışarıdan çağrılabilen yöntemler kullanılarak elde edilebilir:

```csharp
public class Program
{
    private IHost _host;

    public Program()
    {
        _host = new HostBuilder()
            .Build();
    }

    public async Task StartAsync()
    {
        _host.StartAsync();
    }

    public async Task StopAsync()
    {
        using (_host)
        {
            await _host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

::: moniker-end

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:fundamentals/host/hosted-services>
