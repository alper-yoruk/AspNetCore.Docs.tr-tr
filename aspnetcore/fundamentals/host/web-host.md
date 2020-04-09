---
title: ASP.NET Çekirdek Web Host
author: rick-anderson
description: Uygulama başlatma ve yaşam boyu yönetiminden sorumlu olan ASP.NET Core'daki Web Host hakkında bilgi edinin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/07/2019
uid: fundamentals/host/web-host
ms.openlocfilehash: e02d6efcb3aec1329469b8654e66ba845870421a
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78666714"
---
# <a name="aspnet-core-web-host"></a>ASP.NET Çekirdek Web Host

ASP.NET Core uygulamaları bir *ana bilgisayar*yapılandırır ve başlatın. Ev sahibi uygulama başlatma ve yaşam boyu yönetiminden sorumludur. En azından, ana bilgisayar bir sunucu ve istek işleme ardışık yapılandırır. Ana bilgisayar, günlüğe kaydetme, bağımlılık enjeksiyonu ve yapılandırma da ayarlayabilir.

::: moniker range=">= aspnetcore-3.0"

Bu makale, yalnızca geriye dönük uyumluluk için kullanılabilir durumda olan Web Barındıran'ı kapsar. [Genel Ana Bilgisayar](xref:fundamentals/host/generic-host) tüm uygulama türleri için önerilir.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Bu makale, web uygulamalarını barındırmak için web barındırma için web barındırma alanı kapsar. Diğer uygulama türleri için [Genel Ana Bilgisayar'ı](xref:fundamentals/host/generic-host)kullanın.

::: moniker-end

## <a name="set-up-a-host"></a>Ana bilgisayar ayarlama

[IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder)bir örnek kullanarak bir ana bilgisayar oluşturun. Bu genellikle uygulamanın giriş noktasında, yöntemde `Main` gerçekleştirilir.

Proje şablonlarında, `Main` *Program.cs*bulunur. Tipik bir uygulama, bir ana bilgisayar ayarlamaya başlamak için [CreateDefaultBuilder'ı](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) arar:

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .UseStartup<Startup>();
}
```

Çağıran `CreateDefaultBuilder` kod, oluşturucu nesneyi çağıran `CreateWebHostBuilder` `Main` `Run` koddan ayıran ,adlı bir yöntemdedir. [Varlık Framework Core araçlarını](/ef/core/miscellaneous/cli/)kullanıyorsanız bu ayırma gereklidir. Araçlar, uygulamayı çalıştırmadan ana bilgisayarı yapılandırmak için tasarım zamanında çağırabilecekleri bir `CreateWebHostBuilder` yöntem bulmayı bekler. Bir alternatif uygulamaktır. `IDesignTimeDbContextFactory` Daha fazla bilgi için tasarım [zamanı DbContext Oluşturma bölümüne](/ef/core/miscellaneous/cli/dbcontext-creation)bakın.

`CreateDefaultBuilder` aşağıdaki görevleri gerçekleştirir:

* Uygulamanın barındırma yapılandırma sağlayıcılarını kullanarak [Kestrel](xref:fundamentals/servers/kestrel) sunucusunu web sunucusu olarak yapılandırır. Kestrel sunucusunun varsayılan seçenekleri için <xref:fundamentals/servers/kestrel#kestrel-options>bkz.
* İçerik [kökünü](xref:fundamentals/index#content-root) [Directory.GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory)tarafından döndürülen yola ayarlar.
* Yükler [ana bilgisayar yapılandırması:](#host-configuration-values)
  * Ortam değişkenleri önceden `ASPNETCORE_` belirlenmiş (örneğin, `ASPNETCORE_ENVIRONMENT`).
  * Komut satırı bağımsız değişkenleri.
* Uygulama yapılandırmasını aşağıdaki sırayla yükler:
  * *appsettings.json*.
  * *ayarları. {Çevre}.json*.
  * [Uygulama](xref:security/app-secrets) giriş montajını `Development` kullanarak ortamda çalıştığında Gizli Yönetici.
  * Ortam değişkenleri.
  * Komut satırı bağımsız değişkenleri.
* Konsol ve hata ayıklama çıkışı için [günlüğe kaydetmeyi](xref:fundamentals/logging/index) yapılandırır. Günlüğe kaydetme, bir *appsettings.json* veya appsettings'in Günlük yapılandırma bölümünde belirtilen [günlük filtreleme](xref:fundamentals/logging/index#log-filtering) kurallarını *içerir.{ Çevre}.json* dosyası.
* [ASP.NET Çekirdek Modülü](xref:host-and-deploy/aspnet-core-module)ile IIS'nin arkasında koşarken, `CreateDefaultBuilder` uygulamanın temel adresini ve bağlantı noktasını yapılandıran [IIS Tümleştirmesi'ni](xref:host-and-deploy/iis/index)sağlar. IIS Tümleştirme de [başlangıç hatalarını yakalamak](#capture-startup-errors)için uygulamayı yapılandırır. IIS varsayılan seçenekleri için <xref:host-and-deploy/iis/index#iis-options>bkz.
* Uygulamanın ortamı Geliştirme `true` ise [ServiceProviderOptions.ValidateScopes'u](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) ayarlar. Daha fazla bilgi için [Kapsam doğrulama'ya](#scope-validation)bakın.

Tarafından `CreateDefaultBuilder` tanımlanan yapılandırma geçersiz kılınabilir ve [ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration)tarafından artırılabilir , [YapılandırmaLogging](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configurelogging), ve diğer yöntemler ve [iWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder)uzantısı yöntemleri . Birkaç örnek:

* [ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration) uygulama için `IConfiguration` ek belirtmek için kullanılır. Aşağıdaki `ConfigureAppConfiguration` *arama, appsettings.xml* dosyasına uygulama yapılandırmasını eklemek için bir temsilci ekler. `ConfigureAppConfiguration`birden çok kez çağrılabilir. Bu yapılandırmanın ana bilgisayar için (örneğin, sunucu URL'leri veya ortam) geçerli olmadığını unutmayın. Ana [Bilgisayar yapılandırma değerleri](#host-configuration-values) bölümüne bakın.

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureAppConfiguration((hostingContext, config) =>
        {
            config.AddXmlFile("appsettings.xml", optional: true, reloadOnChange: true);
        })
        ...
    ```

* Aşağıdaki `ConfigureLogging` [çağrı, LogLevel.Warning'e](/dotnet/api/microsoft.extensions.logging.loglevel)minimum günlük düzeyini[(SetMinimumLevel)](/dotnet/api/microsoft.extensions.logging.loggingbuilderextensions.setminimumlevel)yapılandırmak için bir temsilci ekler. Bu ayar, uygulama ayarlarındaki ayarları geçersiz *kılar. Development.json* `LogLevel.Debug`( ) ve *uygulama ayarları. Production.json* `LogLevel.Error`( ) `CreateDefaultBuilder`tarafından yapılandırılır. `ConfigureLogging`birden çok kez çağrılabilir.

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureLogging(logging => 
        {
            logging.SetMinimumLevel(LogLevel.Warning);
        })
        ...
    ```

::: moniker range=">= aspnetcore-2.2"

* Kestrel `ConfigureKestrel` tarafından yapılandırıldığında kurulan 30.000.000 bayt varsayılan [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) geçersiz kılar `CreateDefaultBuilder`için aşağıdaki çağrı:

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureKestrel((context, options) =>
        {
            options.Limits.MaxRequestBodySize = 20000000;
        });
    ```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* [UseKestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel) için aşağıdaki çağrı, Kestrel tarafından yapılandırıldığında kurulan 30.000.000 baytlık varsayılan [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) geçersiz `CreateDefaultBuilder`kılar:

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .UseKestrel(options =>
        {
            options.Limits.MaxRequestBodySize = 20000000;
        });
    ```

::: moniker-end

[İçerik kökü,](xref:fundamentals/index#content-root) ana bilgisayarın MVC görünüm dosyaları gibi içerik dosyalarını nerede aramasını belirler. Uygulama projenin kök klasöründen başlatıldığında, projenin kök klasörü içerik kökü olarak kullanılır. Bu, [Visual Studio](https://visualstudio.microsoft.com) ve [dotnet yeni şablonlarında](/dotnet/core/tools/dotnet-new)kullanılan varsayılandır.

Uygulama yapılandırması hakkında daha <xref:fundamentals/configuration/index>fazla bilgi için bkz.

> [!NOTE]
> Statik `CreateDefaultBuilder` yöntemi kullanarak bir alternatif olarak, [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) bir ana bilgisayar oluşturma core 2.x ASP.NET ile desteklenen bir yaklaşımdır.

Bir ana bilgisayar ayarlarken, Hizmetleri [Yapılandır](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configure) ve [Yapılandırışla](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.configureservices) työntem sağlanabilir. Bir `Startup` sınıf belirtilirse, bir `Configure` yöntem tanımlaması gerekir. Daha fazla bilgi için bkz. <xref:fundamentals/startup>. Birbiriniz `ConfigureServices` için birden fazla çağrı. Önceki ayarları `Configure` `UseStartup` n `WebHostBuilder` için veya değiştirme degistirme degistirme degistirme degistirme.

## <a name="host-configuration-values"></a>Ana bilgisayar yapılandırma değerleri

[WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) ana bilgisayar yapılandırma değerlerini ayarlamak için aşağıdaki yaklaşımlara dayanır:

* Biçimi ile ortam değişkenleri içeren ana `ASPNETCORE_{configurationKey}`bilgisayar oluşturucu yapılandırma. Örneğin, `ASPNETCORE_ENVIRONMENT`.
* [UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot) ve [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) gibi uzantılar [(Override yapılandırma](#override-configuration) bölümüne bakın).
* [UseSetting](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.usesetting) ve ilişkili anahtar. Bir değer ayarı `UseSetting`yaparken, değer türüne bakılmaksızın bir dize olarak ayarlanır.

Ana bilgisayar, değeri son olarak ayarlayan seçeneği kullanır. Daha fazla bilgi için, sonraki bölümde [Geçersiz Kılma yapılandırması](#override-configuration) bakın.

### <a name="application-key-name"></a>Uygulama Anahtarı (Ad)

::: moniker range=">= aspnetcore-3.0"

Ev `IWebHostEnvironment.ApplicationName` sahibi inşaatı sırasında [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) veya [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) çağrıldığında özellik otomatik olarak ayarlanır. Değer, uygulamanın giriş noktasını içeren derlemenin adına ayarlanır. Değeri açıkça ayarlamak için [WebHostDefaults.ApplicationKey'i](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey)kullanın:

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[IHostingEnvironment.ApplicationName](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment.applicationname) [özelliği, Ev](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) sahibi inşaatı sırasında UseStartup veya [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) çağrıldığında otomatik olarak ayarlanır. Değer, uygulamanın giriş noktasını içeren derlemenin adına ayarlanır. Değeri açıkça ayarlamak için [WebHostDefaults.ApplicationKey'i](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey)kullanın:

::: moniker-end

**Anahtar**: applicationName  
**Türü**: *string*  
**Varsayılan**: Uygulamanın giriş noktasını içeren derlemenin adı.  
**Set kullanarak:**`UseSetting`  
**Çevre değişkeni**:`ASPNETCORE_APPLICATIONNAME`

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.ApplicationKey, "CustomApplicationName")
```

### <a name="capture-startup-errors"></a>Başlangıç Hatalarını Yakalama

Bu ayar, başlangıç hatalarının yakalanmasını denetler.

**Anahtar**: yakalamaBaşlangıçHataları  
**Türü**: *bool* (`true` veya `1`)  
**Varsayılan**: Uygulama, varsayılan ın olduğu IIS'nin arkasında Kestrel ile birlikte çalıştığı `false` sürece varsayılan `true`dır.  
**Set kullanarak:**`CaptureStartupErrors`  
**Çevre değişkeni**:`ASPNETCORE_CAPTURESTARTUPERRORS`

Ne `false`zaman , başlatma sırasında hatalar ana bilgisayar çıkışı sonucu. Ana `true`bilgisayar başlangıç sırasında özel durumları yakaladığında ve sunucuyu başlatmaya çalıştığında.

```csharp
WebHost.CreateDefaultBuilder(args)
    .CaptureStartupErrors(true)
```

### <a name="content-root"></a>İçerik kökü

Bu ayar, ASP.NET Core'un içerik dosyalarını aramaya başladığı yeri belirler.

**Anahtar**: contentRoot  
**Türü**: *string*  
**Varsayılan**: Varsayılan olarak uygulama derlemesinin bulunduğu klasöre iner.  
**Set kullanarak:**`UseContentRoot`  
**Çevre değişkeni**:`ASPNETCORE_CONTENTROOT`

İçerik kökü de [web kökü](xref:fundamentals/index#web-root)için temel yol olarak kullanılır. İçerik kökü yolu yoksa, ana bilgisayar başlatılamıyor.

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\<content-root>")
```

Daha fazla bilgi için bkz.

* [Temel: İçerik kökü](xref:fundamentals/index#content-root)
* [Web kökü](#web-root)

### <a name="detailed-errors"></a>Ayrıntılı Hatalar

Ayrıntılı hataların yakalanması gerektiğini belirler.

**Anahtar**: detailedErrors  
**Türü**: *bool* (`true` veya `1`)  
**Varsayılan**: false  
**Set kullanarak:**`UseSetting`  
**Çevre değişkeni**:`ASPNETCORE_DETAILEDERRORS`

Etkinleştirildiğinde (veya <a href="#environment">Ortam</a> `Development`ayarlandığında), uygulama ayrıntılı özel durumları yakalar.

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.DetailedErrorsKey, "true")
```

### <a name="environment"></a>Ortam

Uygulamanın ortamını ayarlar.

**Anahtar**: çevre  
**Türü**: *string*  
**Varsayılan**: Üretim  
**Set kullanarak:**`UseEnvironment`  
**Çevre değişkeni**:`ASPNETCORE_ENVIRONMENT`

Ortam herhangi bir değere ayarlanabilir. Çerçeve tanımlı `Development`değerler `Staging`, `Production`ve . Değerler büyük/küçük harf duyarlı değildir. Varsayılan olarak, *Çevre* çevre `ASPNETCORE_ENVIRONMENT` değişkeninden okunur. Visual [Studio](https://visualstudio.microsoft.com)kullanırken, ortam değişkenleri *launchSettings.json* dosyasında ayarlanabilir. Daha fazla bilgi için bkz. <xref:fundamentals/environments>.

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseEnvironment(EnvironmentName.Development)
```

### <a name="hosting-startup-assemblies"></a>Hosting Başlangıç Meclisleri

Uygulamanın başlangıç derlemelerini barındırmasını ayarlar.

**Anahtar**: hostingStartupAssemblies  
**Türü**: *string*  
**Varsayılan**: Boş dize  
**Set kullanarak:**`UseSetting`  
**Çevre değişkeni**:`ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`

Başlangıç yüklemeiçin barındırma başlangıç derlemeleri yarı kolon-sınırlı dize.

Yapılandırma değeri boş bir dize için varsayılan olsa da, barındırma başlangıç derlemeleri her zaman uygulamanın derlemesini içerir. Başlangıç derlemelerine barındırma sağlandığında, uygulama başlangıç sırasında ortak hizmetlerini oluşturduğunda yükleme için uygulamanın derlemesine eklenirler.

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2")
```

### <a name="https-port"></a>HTTPS Bağlantı Noktası

HTTPS yönlendirme bağlantı noktasını ayarlayın. [HTTPS'nin uygulanmasında](xref:security/enforcing-ssl)kullanılır.

**Anahtar**: https_port **Türü**: *string*
**Default**: Varsayılan değer ayarlı değildir.
**Set**using `UseSetting` 
: **Çevre değişkeni**:`ASPNETCORE_HTTPS_PORT`

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting("https_port", "8080")
```

### <a name="hosting-startup-exclude-assemblies"></a>Hosting Başlangıç Hariç Derlemeler

Başlangıç tarihinde hariç tutmak için barındırma başlangıç derlemeleri yarı sütunlu sınırlı dize.

**Anahtar**: hostingStartupExcludeAssemblies  
**Türü**: *string*  
**Varsayılan**: Boş dize  
**Set kullanarak:**`UseSetting`  
**Çevre değişkeni**:`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2")
```

### <a name="prefer-hosting-urls"></a>Hosting URL'lerini tercih edin

Ana bilgisayar, `WebHostBuilder` `IServer` uygulamayla yapılandırılanlar yerine yapılandırılan URL'leri dinleyip dinlememesi gerektiğini gösterir.

**Anahtar**: tercihHostingUrls  
**Türü**: *bool* (`true` veya `1`)  
**Varsayılan**: true  
**Set kullanarak:**`PreferHostingUrls`  
**Çevre değişkeni**:`ASPNETCORE_PREFERHOSTINGURLS`

```csharp
WebHost.CreateDefaultBuilder(args)
    .PreferHostingUrls(false)
```

### <a name="prevent-hosting-startup"></a>Hosting Başlangıç önleme

Uygulamanın montajı tarafından yapılandırılan başlangıç derlemelerini barındırma da dahil olmak üzere barındırma başlangıç derlemelerinin otomatik olarak yüklenmesiengellenir. Daha fazla bilgi için bkz. <xref:fundamentals/configuration/platform-specific-configuration>.

**Anahtar**: preventHostingStartup  
**Türü**: *bool* (`true` veya `1`)  
**Varsayılan**: false  
**Set kullanarak:**`UseSetting`  
**Çevre değişkeni**:`ASPNETCORE_PREVENTHOSTINGSTARTUP`

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.PreventHostingStartupKey, "true")
```

### <a name="server-urls"></a>Sunucu URL'leri

Ip adreslerini veya ana bilgisayar adreslerini, sunucunun istekler için dinlemesi gereken bağlantı noktaları ve protokollerle gösterir.

**Anahtar**: urls  
**Türü**: *string*  
**Varsayılan**:http://localhost:5000  
**Set kullanarak:**`UseUrls`  
**Çevre değişkeni**:`ASPNETCORE_URLS`

Yarı kolon ayrılmış olarak ayarlayın (;) sunucunun yanıt vermesi gereken URL önekleri listesi. Örneğin, `http://localhost:123`. Sunucunun\*belirtilen bağlantı noktası ve protokolü kullanarak herhangi bir IP adresi veya ana bilgisayar `http://*:5000`adı üzerindeki istekleri dinlemesi gerektiğini belirtmek için " " kullanın. Protokol (`http://` `https://`veya ) her URL'ye eklenmelidir. Desteklenen biçimler sunucular arasında farklılık gösterir.

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002")
```

Kerkenez kendi bitiş noktası yapılandırma API vardır. Daha fazla bilgi için bkz. <xref:fundamentals/servers/kestrel#endpoint-configuration>.

### <a name="shutdown-timeout"></a>Kapatma Zaman

Web Barındıran'ın kapanmasını beklemek için gereken süreyi belirtir.

**Anahtar**: shutdownTimeoutSeconds  
**Türü**: *int*  
**Varsayılan**: 5  
**Set kullanarak:**`UseShutdownTimeout`  
**Çevre değişkeni**:`ASPNETCORE_SHUTDOWNTIMEOUTSECONDS`

Anahtar ile bir *int* `UseSetting` kabul etse `.UseSetting(WebHostDefaults.ShutdownTimeoutKey, "10")`de (örneğin, ), [UseShutdownTimeout](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useshutdowntimeout) uzantısı yöntemi bir [TimeSpan](/dotnet/api/system.timespan)alır.

Zaman ekme döneminde, barındırma:

* Tetikleyiciler [IApplicationLifetime.ApplicationStop](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.applicationstopping).
* Barındırılan hizmetleri durdurmaya çalışır, duramayan hizmetler için hataları günlüğe kaydedin.

Zaman aşımı süresi, barındırılan hizmetlerin tümü sona ermeden önce sona ererse, uygulama kapandığında kalan tüm etkin hizmetler durdurulur. Hizmetler işleme meden ilerlememiş olsalar bile durur. Hizmetlerin durması için ek süre gerekiyorsa, zaman arasını artırın.

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseShutdownTimeout(TimeSpan.FromSeconds(10))
```

### <a name="startup-assembly"></a>Başlangıç Montajı

Sınıfı aramak için derlemeyi `Startup` belirler.

**Anahtar**: startupAssembly  
**Türü**: *string*  
**Varsayılan**: Uygulamanın montajı  
**Set kullanarak:**`UseStartup`  
**Çevre değişkeni**:`ASPNETCORE_STARTUPASSEMBLY`

Ada göre`string`derleme (`TStartup`) veya türü ( ) başvurulabilir. Birden `UseStartup` çok yöntem çağrılırsa, sonuncusu önceliklidir.

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseStartup("StartupAssemblyName")
```

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseStartup<TStartup>()
```

### <a name="web-root"></a>Web kökü

Uygulamanın statik varlıklarına göreli yolu ayarlar.

**Anahtar**: webroot  
**Türü**: *string*  
**Varsayılan**: Varsayılan `wwwroot`. *{içerik kökü}/wwwroot* yolu olmalıdır. Yol yoksa, bir no-op dosya sağlayıcısı kullanılır.  
**Set kullanarak:**`UseWebRoot`  
**Çevre değişkeni**:`ASPNETCORE_WEBROOT`

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseWebRoot("public")
```

Daha fazla bilgi için bkz.

* [Temel: Web kökü](xref:fundamentals/index#web-root)
* [İçerik kökü](#content-root)

## <a name="override-configuration"></a>Yapılandırmayı geçersiz kılma

Web Barındıran'ı yapılandırmak için [Yapılandırma'yı](xref:fundamentals/configuration/index) kullanın. Aşağıdaki örnekte, ana bilgisayar yapılandırması isteğe bağlı olarak bir *hostsettings.json* dosyasında belirtilir. *hostsettings.json* dosyasından yüklenen yapılandırma komut satırı bağımsız değişkenleri tarafından geçersiz kılınabilir. Yapılandırılan yapılandırma `config`(in) barındıran bilgisayarı [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration)ile yapılandırmak için kullanılır. `IWebHostBuilder`yapılandırma uygulamanın yapılandırmasına eklenir, ancak tersi doğru&mdash; `ConfigureAppConfiguration` değildir `IWebHostBuilder` yapılandırmayı etkilemez.

*Hostsettings.json* `UseUrls` config ilk, komut satırı config ikinci ile sağlanan yapılandırma geçersiz kılma:

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args)
    {
        var config = new ConfigurationBuilder()
            .SetBasePath(Directory.GetCurrentDirectory())
            .AddJsonFile("hostsettings.json", optional: true)
            .AddCommandLine(args)
            .Build();

        return WebHost.CreateDefaultBuilder(args)
            .UseUrls("http://*:5000")
            .UseConfiguration(config)
            .Configure(app =>
            {
                app.Run(context => 
                    context.Response.WriteAsync("Hello, World!"));
            });
    }
}
```

*hostsettings.json*:

```json
{
    urls: "http://*:5005"
}
```

> [!NOTE]
> [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) yalnızca sağlanan `IConfiguration` ana bilgisayar oluşturucu yapılandırmasından anahtarları kopyalar. Bu nedenle, JSON, INI ve XML ayarları dosyaları için ayar `reloadOnChange: true` hiçbir etkisi yoktur.

Belirli bir URL'de ana bilgisayar çalışmasını belirtmek için, [dotnet çalıştırırken](/dotnet/core/tools/dotnet-run)istenen değer komut isteminden geçirilebilir. Komut satırı bağımsız değişkeni `urls` *hostsettings.json* dosyasındaki değeri geçersiz kılar ve sunucu 8080 bağlantı noktasında dinler:

```dotnetcli
dotnet run --urls "http://*:8080"
```

## <a name="manage-the-host"></a>Ev sahibini yönetme

**Çalıştırmak**

Yöntem `Run` web uygulamasını başlatır ve ana bilgisayar kapatAna kadar arama iş parçacığı engeller:

```csharp
host.Run();
```

**Başlangıç**

Ana bilgisayarı, yöntemini çağırarak engelleyici `Start` olmayan bir şekilde çalıştırın:

```csharp
using (host)
{
    host.Start();
    Console.ReadLine();
}
```

URL'lerin bir listesi `Start` yönteme aktarılırsa, belirtilen URL'leri dinler:

```csharp
var urls = new List<string>()
{
    "http://*:5000",
    "http://localhost:5001"
};

var host = new WebHostBuilder()
    .UseKestrel()
    .UseStartup<Startup>()
    .Start(urls.ToArray());

using (host)
{
    Console.ReadLine();
}
```

Uygulama, statik bir kolaylık yöntemi `CreateDefaultBuilder` ni kullanarak önceden yapılandırılmış varsayılanları kullanarak yeni bir ana bilgisayar başlatabilir ve başlatabilir. Bu yöntemler konsol çıkışı olmadan sunucu başlatmak ve [WaitForShutdown](/dotnet/api/microsoft.aspnetcore.hosting.webhostextensions.waitforshutdown) bir mola (Ctrl-C/SIGINT veya SIGTERM) bekleyin:

**Başlat(RequestDelegate uygulaması)**

A `RequestDelegate`ile başlayın:

```csharp
using (var host = WebHost.Start(app => app.Response.WriteAsync("Hello, World!")))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

"Merhaba Dünya!" yanıtını `http://localhost:5000` almak için tarayıcıda bir istekte bulunun. `WaitForShutdown`bir ara verilene kadar (Ctrl-C/SIGINT veya SIGTERM) bloke edilir. Uygulama `Console.WriteLine` iletiyi görüntüler ve bir tuş tuşuna basılmasını bekler.

**Başlat(string url, RequestDelegate uygulaması)**

URL ile başlayın `RequestDelegate`ve:

```csharp
using (var host = WebHost.Start("http://localhost:8080", app => app.Response.WriteAsync("Hello, World!")))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

**Başlat(RequestDelegate uygulaması)** ile aynı sonucu üretir , `http://localhost:8080`ancak uygulama yanıt verir.

**Başlat (Eylem\<IRouteBuilder> routeBuilder)**

Yönlendirme aracını `IRouteBuilder` kullanmak için[(Microsoft.AspNetCore.Routing)](https://www.nuget.org/packages/Microsoft.AspNetCore.Routing/)örneğini kullanın:

```csharp
using (var host = WebHost.Start(router => router
    .MapGet("hello/{name}", (req, res, data) => 
        res.WriteAsync($"Hello, {data.Values["name"]}!"))
    .MapGet("buenosdias/{name}", (req, res, data) => 
        res.WriteAsync($"Buenos dias, {data.Values["name"]}!"))
    .MapGet("throw/{message?}", (req, res, data) => 
        throw new Exception((string)data.Values["message"] ?? "Uh oh!"))
    .MapGet("{greeting}/{name}", (req, res, data) => 
        res.WriteAsync($"{data.Values["greeting"]}, {data.Values["name"]}!"))
    .MapGet("", (req, res, data) => res.WriteAsync("Hello, World!"))))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

Aşağıdaki tarayıcı isteklerini örnekle kullanın:

| İstek                                    | Yanıt                                 |
| ------------------------------------------ | ---------------------------------------- |
| `http://localhost:5000/hello/Martin`       | Merhaba Martin!                           |
| `http://localhost:5000/buenosdias/Catrina` | Buenos dias, Catrina!                    |
| `http://localhost:5000/throw/ooops!`       | Dize "ooops" ile bir istisna atar! |
| `http://localhost:5000/throw`              | Dize "Uh oh!" ile bir istisna atar |
| `http://localhost:5000/Sante/Kevin`        | Sante, Kevin!                            |
| `http://localhost:5000`                    | Merhaba Dünya!                             |

`WaitForShutdown`bir ara verilene kadar (Ctrl-C/SIGINT veya SIGTERM) bloke edilir. Uygulama `Console.WriteLine` iletiyi görüntüler ve bir tuş tuşuna basılmasını bekler.

**Başlat(string url,\<Action IRouteBuilder> routeBuilder)**

Bir URL ve bir `IRouteBuilder`örnek kullanın:

```csharp
using (var host = WebHost.Start("http://localhost:8080", router => router
    .MapGet("hello/{name}", (req, res, data) => 
        res.WriteAsync($"Hello, {data.Values["name"]}!"))
    .MapGet("buenosdias/{name}", (req, res, data) => 
        res.WriteAsync($"Buenos dias, {data.Values["name"]}!"))
    .MapGet("throw/{message?}", (req, res, data) => 
        throw new Exception((string)data.Values["message"] ?? "Uh oh!"))
    .MapGet("{greeting}/{name}", (req, res, data) => 
        res.WriteAsync($"{data.Values["greeting"]}, {data.Values["name"]}!"))
    .MapGet("", (req, res, data) => res.WriteAsync("Hello, World!"))))
{
    Console.WriteLine("Use Ctrl-C to shut down the host...");
    host.WaitForShutdown();
}
```

**Start (Action\<IRouteBuilder> routeBuilder)** ile aynı sonucu üretir, uygulama dışında `http://localhost:8080`yanıt verir .

**StartWith(Action\<IApplicationBuilder> uygulaması)**

Bir yapılandırmak için `IApplicationBuilder`bir temsilci sağlayın:

```csharp
using (var host = WebHost.StartWith(app => 
    app.Use(next => 
    {
        return async context => 
        {
            await context.Response.WriteAsync("Hello World!");
        };
    })))
{
    Console.WriteLine("Use Ctrl-C to shut down the host...");
    host.WaitForShutdown();
}
```

"Merhaba Dünya!" yanıtını `http://localhost:5000` almak için tarayıcıda bir istekte bulunun. `WaitForShutdown`bir ara verilene kadar (Ctrl-C/SIGINT veya SIGTERM) bloke edilir. Uygulama `Console.WriteLine` iletiyi görüntüler ve bir tuş tuşuna basılmasını bekler.

**StartWith(string url,\<Action IApplicationBuilder> uygulaması)**

Bir url ve bir yapılandırılan `IApplicationBuilder`bir temsilci sağlayın:

```csharp
using (var host = WebHost.StartWith("http://localhost:8080", app => 
    app.Use(next => 
    {
        return async context => 
        {
            await context.Response.WriteAsync("Hello World!");
        };
    })))
{
    Console.WriteLine("Use Ctrl-C to shut down the host...");
    host.WaitForShutdown();
}
```

**StartWith (Action\<IApplicationBuilder> uygulaması)** ile aynı sonucu üretir `http://localhost:8080`, uygulama nın yanıt vermesi dışında.

::: moniker range=">= aspnetcore-3.0"

## <a name="iwebhostenvironment-interface"></a>IWebHostEnvironment arayüzü

Arayüz, `IWebHostEnvironment` uygulamanın web barındırma ortamı hakkında bilgi sağlar. Özelliklerini ve uzatma yöntemlerini kullanmak için [konstrüktör enjeksiyonunu](xref:fundamentals/dependency-injection) `IWebHostEnvironment` kullanın:

```csharp
public class CustomFileReader
{
    private readonly IWebHostEnvironment _env;

    public CustomFileReader(IWebHostEnvironment env)
    {
        _env = env;
    }

    public string ReadFile(string filePath)
    {
        var fileProvider = _env.WebRootFileProvider;
        // Process the file here
    }
}
```

Uygulamayı başlangıçta ortama göre yapılandırmak için [kural tabanlı](xref:fundamentals/environments#environment-based-startup-class-and-methods) bir yaklaşım kullanılabilir. Alternatif olarak, `IWebHostEnvironment` kullanmak `Startup` için yapıcı içine `ConfigureServices`enjekte:

```csharp
public class Startup
{
    public Startup(IWebHostEnvironment env)
    {
        HostingEnvironment = env;
    }

    public IWebHostEnvironment HostingEnvironment { get; }

    public void ConfigureServices(IServiceCollection services)
    {
        if (HostingEnvironment.IsDevelopment())
        {
            // Development configuration
        }
        else
        {
            // Staging/Production configuration
        }

        var contentRootPath = HostingEnvironment.ContentRootPath;
    }
}
```

> [!NOTE]
> Uzatma yöntemine `IsDevelopment` ek `IWebHostEnvironment` olarak, `IsProduction`teklifler `IsEnvironment(string environmentName)` `IsStaging`, , ve yöntemleri. Daha fazla bilgi için bkz. <xref:fundamentals/environments>.

Hizmet, `IWebHostEnvironment` işleme ardışık `Configure` hattını ayarlama yöntemine doğrudan enjekte edilebilir:

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    if (env.IsDevelopment())
    {
        // In Development, use the Developer Exception Page
        app.UseDeveloperExceptionPage();
    }
    else
    {
        // In Staging/Production, route exceptions to /error
        app.UseExceptionHandler("/error");
    }

    var contentRootPath = env.ContentRootPath;
}
```

`IWebHostEnvironment`özel [ara yazılım](xref:fundamentals/middleware/write) `Invoke` oluştururken yöntemiçine enjekte edilebilir:

```csharp
public async Task Invoke(HttpContext context, IWebHostEnvironment env)
{
    if (env.IsDevelopment())
    {
        // Configure middleware for Development
    }
    else
    {
        // Configure middleware for Staging/Production
    }

    var contentRootPath = env.ContentRootPath;
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="ihostingenvironment-interface"></a>IHostingEnvironment arayüzü

[IHostingEnvironment arayüzü](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment) uygulamanın web barındırma ortamı hakkında bilgi sağlar. Özelliklerini ve uzatma yöntemlerini kullanmak için [konstrüktör enjeksiyonunu](xref:fundamentals/dependency-injection) `IHostingEnvironment` kullanın:

```csharp
public class CustomFileReader
{
    private readonly IHostingEnvironment _env;

    public CustomFileReader(IHostingEnvironment env)
    {
        _env = env;
    }

    public string ReadFile(string filePath)
    {
        var fileProvider = _env.WebRootFileProvider;
        // Process the file here
    }
}
```

Uygulamayı başlangıçta ortama göre yapılandırmak için [kural tabanlı](xref:fundamentals/environments#environment-based-startup-class-and-methods) bir yaklaşım kullanılabilir. Alternatif olarak, `IHostingEnvironment` kullanmak `Startup` için yapıcı içine `ConfigureServices`enjekte:

```csharp
public class Startup
{
    public Startup(IHostingEnvironment env)
    {
        HostingEnvironment = env;
    }

    public IHostingEnvironment HostingEnvironment { get; }

    public void ConfigureServices(IServiceCollection services)
    {
        if (HostingEnvironment.IsDevelopment())
        {
            // Development configuration
        }
        else
        {
            // Staging/Production configuration
        }

        var contentRootPath = HostingEnvironment.ContentRootPath;
    }
}
```

> [!NOTE]
> Uzatma yöntemine `IsDevelopment` ek `IHostingEnvironment` olarak, `IsProduction`teklifler `IsEnvironment(string environmentName)` `IsStaging`, , ve yöntemleri. Daha fazla bilgi için bkz. <xref:fundamentals/environments>.

Hizmet, `IHostingEnvironment` işleme ardışık `Configure` hattını ayarlama yöntemine doğrudan enjekte edilebilir:

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        // In Development, use the Developer Exception Page
        app.UseDeveloperExceptionPage();
    }
    else
    {
        // In Staging/Production, route exceptions to /error
        app.UseExceptionHandler("/error");
    }

    var contentRootPath = env.ContentRootPath;
}
```

`IHostingEnvironment`özel [ara yazılım](xref:fundamentals/middleware/write) `Invoke` oluştururken yöntemiçine enjekte edilebilir:

```csharp
public async Task Invoke(HttpContext context, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        // Configure middleware for Development
    }
    else
    {
        // Configure middleware for Staging/Production
    }

    var contentRootPath = env.ContentRootPath;
}
```

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="ihostapplicationlifetime-interface"></a>IHostApplicationÖmür arayüzü

`IHostApplicationLifetime`başlatma ve kapatma işlemlerine izin verir. Arabirimdeki üç özellik, başlatma ve `Action` kapatma olaylarını tanımlayan yöntemleri kaydetmek için kullanılan iptal belirteçleridir.

| İptal Jetonu    | &#8230; tetiklenir |
| --------------------- | --------------------- |
| `ApplicationStarted`  | Ev sahibi tamamen başladı. |
| `ApplicationStopped`  | Ev sahibi zarif bir kapatma tamamlıyor. Tüm istekler işlenmelidir. Bu olay tamamlanana kadar kapatma blokları. |
| `ApplicationStopping` | Ev sahibi zarif bir kapatma gerçekleştiriyor. İstekler hala işliyor olabilir. Bu olay tamamlanana kadar kapatma blokları. |

```csharp
public class Startup
{
    public void Configure(IApplicationBuilder app, IHostApplicationLifetime appLifetime)
    {
        appLifetime.ApplicationStarted.Register(OnStarted);
        appLifetime.ApplicationStopping.Register(OnStopping);
        appLifetime.ApplicationStopped.Register(OnStopped);

        Console.CancelKeyPress += (sender, eventArgs) =>
        {
            appLifetime.StopApplication();
            // Don't terminate the process immediately, wait for the Main thread to exit gracefully.
            eventArgs.Cancel = true;
        };
    }

    private void OnStarted()
    {
        // Perform post-startup activities here
    }

    private void OnStopping()
    {
        // Perform on-stopping activities here
    }

    private void OnStopped()
    {
        // Perform post-stopped activities here
    }
}
```

`StopApplication`uygulamanın sonlandırılmasını talep ediyor. Aşağıdaki sınıf, `StopApplication` sınıfın `Shutdown` yöntemi çağrıldığında bir uygulamayı zarif bir şekilde kapatmak için kullanır:

```csharp
public class MyClass
{
    private readonly IHostApplicationLifetime _appLifetime;

    public MyClass(IHostApplicationLifetime appLifetime)
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

::: moniker range="< aspnetcore-3.0"

## <a name="iapplicationlifetime-interface"></a>IApplicationLifetime arayüzü

[iApplicationLifetime,](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime) başlangıç ve kapatma sonrası etkinliklere izin verir. Arabirimdeki üç özellik, başlatma ve `Action` kapatma olaylarını tanımlayan yöntemleri kaydetmek için kullanılan iptal belirteçleridir.

| İptal Jetonu    | &#8230; tetiklenir |
| --------------------- | --------------------- |
| [Başvurular Başladı](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstarted) | Ev sahibi tamamen başladı. |
| [Başvurular Durduruldu](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstopped) | Ev sahibi zarif bir kapatma tamamlıyor. Tüm istekler işlenmelidir. Bu olay tamamlanana kadar kapatma blokları. |
| [Uygulama Durdurma](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstopping) | Ev sahibi zarif bir kapatma gerçekleştiriyor. İstekler hala işliyor olabilir. Bu olay tamamlanana kadar kapatma blokları. |

```csharp
public class Startup
{
    public void Configure(IApplicationBuilder app, IApplicationLifetime appLifetime)
    {
        appLifetime.ApplicationStarted.Register(OnStarted);
        appLifetime.ApplicationStopping.Register(OnStopping);
        appLifetime.ApplicationStopped.Register(OnStopped);

        Console.CancelKeyPress += (sender, eventArgs) =>
        {
            appLifetime.StopApplication();
            // Don't terminate the process immediately, wait for the Main thread to exit gracefully.
            eventArgs.Cancel = true;
        };
    }

    private void OnStarted()
    {
        // Perform post-startup activities here
    }

    private void OnStopping()
    {
        // Perform on-stopping activities here
    }

    private void OnStopped()
    {
        // Perform post-stopped activities here
    }
}
```

[StopApplication,](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.stopapplication) uygulamanın sonlandırılmasını ister. Aşağıdaki sınıf, `StopApplication` sınıfın `Shutdown` yöntemi çağrıldığında bir uygulamayı zarif bir şekilde kapatmak için kullanır:

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

## <a name="scope-validation"></a>Kapsam doğrulama

[CreateDefaultBuilder,](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) uygulamanın ortamı Geliştirme `true` ise [ServiceProviderOptions.ValidateScopes'u](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) ayarlar.

Ne `ValidateScopes` zaman `true`ayarlanır , varsayılan hizmet sağlayıcısı bunu doğrulamak için denetimler gerçekleştirir:

* Kapsamlı hizmetler, kök hizmet sağlayıcısından doğrudan veya dolaylı olarak çözülmez.
* Kapsamlı hizmetler doğrudan veya dolaylı olarak singleton içine enjekte değildir.

[BuildServiceProvider](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectioncontainerbuilderextensions.buildserviceprovider) çağrıldığında kök hizmet sağlayıcısı oluşturulur. Kök servis sağlayıcısının ömrü, sağlayıcı nın uygulamayla birlikte işe başlaması ve uygulama kapandığında imha edilmesiyle uygulamanın/sunucunun ömrüne karşılık gelir.

Kapsamlı hizmetler, bunları oluşturan kapsayıcı tarafından bertaraf edilir. Kök kapsayıcıda kapsamlı bir hizmet oluşturulursa, yalnızca uygulama/sunucu kapatıldığında kök kapsayıcı tarafından bertaraf edildiğinden, hizmetin ömrü etkin bir şekilde singleton'a yükseltilir. Hizmet kapsamlarını doğrulama çağrıldığında `BuildServiceProvider` bu durumları yakalar.

Üretim ortamında da dahil olmak üzere kapsamları her zaman doğrulamak için, [ServiceProviderOptions'ı](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions) ev sahibi oluşturucuda [UseDefaultServiceProvider](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usedefaultserviceprovider) ile yapılandırın:

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseDefaultServiceProvider((context, options) => {
        options.ValidateScopes = true;
    })
```

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/linux-nginx>
* <xref:host-and-deploy/linux-apache>
* <xref:host-and-deploy/windows-service>
