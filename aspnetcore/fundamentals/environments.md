---
title: ASP.NET Core'da birden çok ortam kullanma
author: rick-anderson
description: ASP.NET Core uygulamalarında birden çok ortamda uygulama davranışını denetlemeyi öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 7/1/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/environments
ms.openlocfilehash: 977d222ed61fa914bd4ffb70e192ef19d4da5c33
ms.sourcegitcommit: 6fb27ea41a92f6d0e91dfd0eba905d2ac1a707f7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/15/2020
ms.locfileid: "87330666"
---
# <a name="use-multiple-environments-in-aspnet-core"></a>ASP.NET Core'da birden çok ortam kullanma

::: moniker range=">= aspnetcore-3.0"

Tarafından [Rick Anderson](https://twitter.com/RickAndMSFT) ve [Kirk larkabağı](https://twitter.com/serpent5)

ASP.NET Core, bir ortam değişkeni kullanarak çalışma zamanı ortamı temelinde uygulama davranışını yapılandırır.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="environments"></a>Ortamlar

Çalışma zamanı ortamını öğrenmek için ASP.NET Core aşağıdaki ortam değişkenlerinden okur:

1. [DOTNET_ENVIRONMENT](xref:fundamentals/configuration/index#default-host-configuration)
1. `ASPNETCORE_ENVIRONMENT`ne zaman <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> çağrılır. Varsayılan ASP.NET Core Web uygulaması şablonları çağrısı `ConfigureWebHostDefaults` . `ASPNETCORE_ENVIRONMENT`Değer geçersiz kılınır `DOTNET_ENVIRONMENT` .

`IHostEnvironment.EnvironmentName`herhangi bir değere ayarlanabilir, ancak aşağıdaki değerler Framework tarafından sağlanır:

* <xref:Microsoft.Extensions.Hosting.Environments.Development>: Dosya [üzerindelaunchSettings.js](#lsj) `ASPNETCORE_ENVIRONMENT` `Development` Yerel makinede olarak ayarlanır.
* <xref:Microsoft.Extensions.Hosting.Environments.Staging>
* <xref:Microsoft.Extensions.Hosting.Environments.Production>: Varsayılan if `DOTNET_ENVIRONMENT` ve `ASPNETCORE_ENVIRONMENT` ayarlanmamışsa.

Aşağıdaki kod:

* Olarak ayarlandığında [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) çağırır `ASPNETCORE_ENVIRONMENT` `Development` .
* Değeri, veya olarak ayarlandığında [Useexceptionhandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) `ASPNETCORE_ENVIRONMENT` ' i çağırır `Staging` `Production` `Staging_2` .
* İçine çıkarır <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> `Startup.Configure` . Bu yaklaşım, uygulama yalnızca `Startup.Configure` , ortam başına en az kod farklılığı olan birkaç ortam için ayarlama gerektirdiğinde yararlıdır.
* , ASP.NET Core şablonları tarafından oluşturulan koda benzerdir.

[!code-csharp[](environments/3.1sample/EnvironmentsSample/Startup.cs?name=snippet)]

[Ortam etiketi Yardımcısı](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) , öğesinde biçimlendirme eklemek veya dışlamak Için [ıhostenvironment. environmentname](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName) değerini kullanır:

[!code-cshtml[](environments/sample-snapshot/EnvironmentsSample/Pages/About.cshtml)]

[Örnek koddan](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample) [hakkında sayfası](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample/EnvironmentsSample/Pages/About.cshtml) , önceki biçimlendirmeyi içerir ve değerini görüntüler `IWebHostEnvironment.EnvironmentName` .

Windows ve macOS 'ta, ortam değişkenleri ve değerleri büyük/küçük harfe duyarlı değildir. Linux ortam değişkenleri ve değerleri varsayılan olarak **büyük/küçük harfe duyarlıdır** .

### <a name="create-environmentssample"></a>EnvironmentsSample oluşturma

Bu belgede kullanılan [örnek kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample) , Razor *environmentssample*adlı bir sayfalar projesini temel alır.

Aşağıdaki kod *Environmentssample*adlı bir Web uygulaması oluşturur ve çalıştırır:

```bash
dotnet new webapp -o EnvironmentsSample
cd EnvironmentsSample
dotnet run --verbosity normal
```

Uygulama çalıştığında, aşağıdaki çıkışın bazılarını görüntüler:

```bash
Using launch settings from c:\tmp\EnvironmentsSample\Properties\launchSettings.json
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
      Content root path: c:\tmp\EnvironmentsSample
```

<a name="lsj"></a>

### <a name="development-and-launchsettingsjson"></a>Geliştirme ve launchSettings.js

Geliştirme ortamı, üretimde gösterilmemelidir özellikleri etkinleştirebilir. Örneğin, ASP.NET Core Şablonlar geliştirme ortamında [Geliştirici özel durum sayfasını](xref:fundamentals/error-handling#developer-exception-page) etkinleştirir.

Yerel makine geliştirme ortamı, projenin dosyasında *Properties\launchSettings.js* ayarlanabilir. Sistem ortamında ayarlanan geçersiz kılma değerlerinde *launchSettings.js* ayarlanan ortam değerleri.

Dosyadaki *launchSettings.js* :

* Yalnızca yerel geliştirme makinesinde kullanılır.
* Dağıtılmadı.
* profil ayarlarını içerir.

Aşağıdaki JSON, Visual Studio ile oluşturulan ASP.NET Core bir Web tahmini adlı *Environmentssample* adlı dosyada *launchSettings.js* göstermektedir `dotnet new` :

[!code-json[](environments/3.1sample/EnvironmentsSample/Properties/launchSettingsCopy.json)]

Yukarıdaki biçimlendirme iki profil içerir:

* `IIS Express`: Uygulama Visual Studio 'dan başlatılırken kullanılan varsayılan profil. `"commandName"`Anahtarın değeri bulunur `"IISExpress"` , bu nedenle [iisexpress](/iis/extensions/introduction-to-iis-express/iis-express-overview) Web sunucusudur. Başlatma profilini projeye veya dahil edilen başka bir profile ayarlayabilirsiniz. Örneğin, aşağıdaki görüntüde, proje adının seçilmesi [Kestrel Web sunucusunu](xref:fundamentals/servers/kestrel)başlatır.

  ![Menüdeki IIS Express Başlat](environments/_static/iisx2.png)
* `EnvironmentsSample`: Profil adı proje adıdır. Bu profil, uygulamasının ile başlatılması sırasında varsayılan olarak kullanılır `dotnet run` .  `"commandName"`Anahtarın değeri bulunur `"Project"` , bu nedenle [Kestrel Web sunucusu](xref:fundamentals/servers/kestrel) başlatılır.

Değeri, `commandName` başlatılacak Web sunucusunu belirtebilir. `commandName`aşağıdakilerden biri olabilir:

* `IISExpress`: IIS Express başlatır.
* `IIS`: Web sunucusu başlatılmadı. IIS 'nin kullanılabilir olması bekleniyor.
* `Project`: Kestrel başlatır.

Visual Studio proje özellikleri **hata ayıklama** sekmesi, dosyadaki *launchSettings.js* düzenlemek için bir GUI sağlar. Proje profillerinde yapılan değişiklikler, Web sunucusu yeniden başlatılana kadar etkili olmayabilir. Kestrel, ortamında yapılan değişiklikleri algılayabilmesi için yeniden başlatılmalıdır.

![Proje özellikleri ayar ortamı değişkenleri](environments/_static/project-properties-debug.png)

Dosyadaki şu *launchSettings.js* birden çok profil var:

[!code-json[](environments/3.1sample/EnvironmentsSample/Properties/launchSettings.json)]

Profiller seçilebilir:

* Visual Studio kullanıcı arabiriminden.
* Komutu [`dotnet run`](/dotnet/core/tools/dotnet-run) bir komut kabuğu 'nda `--launch-profile` profil adına ayarlanmış seçeneğiyle kullanma. *Bu yaklaşım yalnızca Kestrel profillerini destekler.*

  ```dotnetcli
  dotnet run --launch-profile "SampleApp"
  ```

> [!WARNING]
> *launchSettings.js* , gizli dizileri depolamamamalıdır. Gizli anahtar geliştirme için gizli dizileri depolamak için [gizli dizi Yöneticisi aracı](xref:security/app-secrets) kullanılabilir.

[Visual Studio Code](https://code.visualstudio.com/)kullanırken, ortam değişkenleri dosyadaki *. vscode/launch.js* ayarlanabilir. Aşağıdaki örnek, çeşitli [konak yapılandırma değerleri ortam değişkenlerini](xref:fundamentals/host/web-host#host-configuration-values)ayarlar:

[!code-json[](environments/3.1sample/EnvironmentsSample/.vscode/launch.json?range=4-10,32-38)]

Dosyadaki *. vscode/launch.js* yalnızca Visual Studio Code tarafından kullanılır.

### <a name="production"></a>Üretim

Üretim ortamının güvenliği, [performansı](xref:performance/performance-best-practices)ve uygulama sağlamlık düzeyini en üst düzeye çıkarmak için yapılandırılması gerekir. Geliştirmeden farklı bazı yaygın ayarlar şunlardır:

* [Önbelleğe alma](xref:performance/caching/memory).
* İstemci tarafı kaynaklar paketlenmiş, küçültülmüş ve potansiyel olarak bir CDN 'den sunulan.
* Tanılama hata sayfaları devre dışı.
* Kolay hata sayfaları etkin.
* Üretim [günlüğü](xref:fundamentals/logging/index) ve izleme etkin. Örneğin, [Application Insights](/azure/application-insights/app-insights-asp-net-core)kullanma.

## <a name="set-the-environment"></a>Ortamı ayarlama

Bir ortam değişkeni veya platform ayarıyla test için belirli bir ortam ayarlamak genellikle yararlıdır. Ortam ayarlanmamışsa, `Production` çoğu hata ayıklama özelliğini devre dışı bırakan varsayılan olarak olur. Ortamı ayarlama yöntemi işletim sistemine bağlıdır.

Konak yapılandırıldığında, uygulama tarafından okunan son ortam ayarı, uygulamanın ortamını belirler. Uygulama çalışırken uygulamanın ortamı değiştirilemez.

[Örnek koddan](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample) [hakkında sayfasında](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample/EnvironmentsSample/Pages/About.cshtml) değerini gösterir `IWebHostEnvironment.EnvironmentName` .

### <a name="azure-app-service"></a>Azure App Service

[Azure App Service](https://azure.microsoft.com/services/app-service/)ortamında ortamı ayarlamak için aşağıdaki adımları gerçekleştirin:

1. Uygulama **Hizmetleri** dikey penceresinden uygulamayı seçin.
1. **Ayarlar** grubunda **yapılandırma** dikey penceresini seçin.
1. **Uygulama ayarları** sekmesinde **Yeni uygulama ayarı**' nı seçin.
1. **Uygulama ayarı Ekle/Düzenle** penceresinde ad ' ı belirtin `ASPNETCORE_ENVIRONMENT` . **Name** **Değer**için ortamı sağlayın (örneğin, `Staging` ).
1. Dağıtım yuvaları takas edildiğinde ortam ayarının geçerli yuvada kalmasını istiyorsanız **dağıtım yuvası ayarını** onay kutusunu işaretleyin. Daha fazla bilgi için bkz. Azure belgelerindeki [Azure App Service hazırlama ortamlarını ayarlama](/azure/app-service/web-sites-staged-publishing) .
1. **Tamam ' ı** seçerek **uygulama ayarı Ekle/Düzenle** penceresini kapatın.
1. **Yapılandırma** dikey penceresinin en üstünde **Kaydet** ' i seçin.

Azure App Service, Azure portal bir uygulama ayarı eklendikten, değiştirildikten veya silindikten sonra uygulamayı otomatik olarak yeniden başlatır.

### <a name="windows"></a>Windows

Sistem ortamında ayarlanan geçersiz kılma değerleri *üzerindelaunchSettings.js* ortam değerleri.

`ASPNETCORE_ENVIRONMENT`Uygulama [DotNet çalıştırması](/dotnet/core/tools/dotnet-run)kullanılarak başlatıldığında geçerli oturum için öğesini ayarlamak için aşağıdaki komutlar kullanılır:

**Komut istemi**

```console
set ASPNETCORE_ENVIRONMENT=Staging
dotnet run --no-launch-profile
```

**PowerShell**

```powershell
$Env:ASPNETCORE_ENVIRONMENT = "Staging"
dotnet run --no-launch-profile
```

Önceki komut `ASPNETCORE_ENVIRONMENT` yalnızca bu komut penceresinden başlatılan süreçler için ayarlar.

Windows 'da genel değeri ayarlamak için aşağıdaki yaklaşımlardan birini kullanın:

* **Denetim Masası** > **sistem** > **Gelişmiş sistem ayarları** ' nı açın ve değeri ekleyin veya düzenleyin `ASPNETCORE_ENVIRONMENT` :

  ![Sistem Gelişmiş Özellikler](environments/_static/systemsetting_environment.png)

  ![ASPNET Core ortam değişkeni](environments/_static/windows_aspnetcore_environment.png)

* Bir yönetim komut istemi açın ve komutunu kullanın `setx` veya bir yönetim PowerShell komut istemi açın ve şunu kullanın `[Environment]::SetEnvironmentVariable` :

  **Komut istemi**

  ```console
  setx ASPNETCORE_ENVIRONMENT Staging /M
  ```

  `/M`Anahtar, ortam değişkenini sistem düzeyinde ayarlamaya yönelik olduğunu gösterir. `/M`Anahtar kullanılmazsa, ortam değişkeni Kullanıcı hesabı için ayarlanır.

  **PowerShell**

  ```powershell
  [Environment]::SetEnvironmentVariable("ASPNETCORE_ENVIRONMENT", "Staging", "Machine")
  ```

  `Machine`Seçenek değeri, ortam değişkeninin sistem düzeyinde ayarlandığını gösterir. Seçenek değeri olarak değiştirilirse `User` , ortam değişkeni Kullanıcı hesabı için ayarlanır.

`ASPNETCORE_ENVIRONMENT`Ortam değişkeni genel olarak ayarlandığında, `dotnet run` değer ayarlandıktan sonra açılan herhangi bir komut penceresinde için geçerli olur. Sistem ortamında ayarlanan geçersiz kılma değerleri *üzerindelaunchSettings.js* ortam değerleri.

**web.config**

`ASPNETCORE_ENVIRONMENT`Ortam değişkenini *web.config*olarak ayarlamak için, *ortam değişkenlerini ayarlama* bölümüne bakın <xref:host-and-deploy/aspnet-core-module#setting-environment-variables> .

**Proje dosyası veya yayımlama profili**

**WINDOWS IIS dağıtımları için:** `<EnvironmentName>`Özelliği [Publish profile (. pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) veya proje dosyasına ekleyin. Bu yaklaşım, proje yayımlandığında *web.config* ortamı ayarlar:

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

**IIS uygulama havuzu başına**

`ASPNETCORE_ENVIRONMENT`Yalıtılmış bir uygulama havuzunda çalışan bir uygulamanın ortam değişkenini ayarlamak için (ııs 10,0 veya üzeri sürümlerde desteklenir), [ortam değişkenleri &lt; &gt; environmentvariables](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) konusunun *AppCmd.exe komut* bölümüne bakın. `ASPNETCORE_ENVIRONMENT`Ortam değişkeni bir uygulama havuzu için ayarlandığında, değeri sistem düzeyindeki bir ayarı geçersiz kılar.

IIS 'de bir uygulama barındırırken ve ortam değişkenini eklerken ya da değiştirirken `ASPNETCORE_ENVIRONMENT` , yeni değerin uygulamalar tarafından çekilmek için aşağıdaki yaklaşımlardan birini kullanın:

* ' İ `net stop was /y` `net start w3svc` komut isteminden sonra yürütün.
* Sunucuyu yeniden başlatın.

#### <a name="macos"></a>macOS

MacOS için geçerli ortamın ayarlanması, uygulamayı çalıştırırken satır içinde gerçekleştirilebilir:

```bash
ASPNETCORE_ENVIRONMENT=Staging dotnet run
```

Alternatif olarak, `export` uygulamayı çalıştırmadan önce ortamı olarak ayarlayın:

```bash
export ASPNETCORE_ENVIRONMENT=Staging
```

Makine düzeyinde ortam değişkenleri *. bashrc* veya *. bash_profile* dosyasında ayarlanır. Herhangi bir metin düzenleyicisini kullanarak dosyayı düzenleyin. Aşağıdaki ifadeyi ekleyin:

```bash
export ASPNETCORE_ENVIRONMENT=Staging
```

#### <a name="linux"></a>Linux

Linux dağıtımları için, `export` oturum tabanlı değişken ayarları ve makine düzeyi ortam ayarları için *bash_profile* dosyası için komut isteminde komutunu kullanın.

### <a name="set-the-environment-in-code"></a>Kodda ortam ayarlama

<xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseEnvironment*>Ana bilgisayarı derlerken çağırın. Bkz. <xref:fundamentals/host/generic-host#environmentname>.

### <a name="configuration-by-environment"></a>Ortama göre yapılandırma

Yapılandırmayı ortama göre yüklemek için bkz <xref:fundamentals/configuration/index#json-configuration-provider> ..

## <a name="environment-based-startup-class-and-methods"></a>Ortam tabanlı başlangıç sınıfı ve yöntemleri

### <a name="inject-iwebhostenvironment-into-the-startup-class"></a>Başlangıç sınıfına ıwebhostenvironment ekleme

<xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> `Startup` Oluşturucuya ekleme. Bu yaklaşım, uygulama `Startup` her ortam için en az kod farklılığı olan birkaç ortam için yapılandırma gerektirdiğinde yararlıdır.

Aşağıdaki örnekte:

* Ortam `_env` alanında tutulur.
* `_env`, ve ' de, `ConfigureServices` `Configure` uygulamanın ortamına göre başlangıç yapılandırması uygulamak için kullanılır.

[!code-csharp[](environments/3.1sample/EnvironmentsSample/StartupInject.cs?name=snippet&highlight=3-11)]

### <a name="startup-class-conventions"></a>Başlangıç sınıfı kuralları

ASP.NET Core bir uygulama başlatıldığında, [Başlangıç sınıfı](xref:fundamentals/startup) uygulamayı önyükleme. Uygulama, `Startup` farklı ortamlar için birden çok sınıf tanımlayabilir. Çalışma zamanında uygun `Startup` sınıf seçildi. Geçerli ortamla eşleşen ad sonekine sahip olan sınıf önceliklendirilir. Eşleşen bir `Startup{EnvironmentName}` sınıf bulunamazsa `Startup` sınıf kullanılır. Bu yaklaşım, uygulama başına çok sayıda kod farklılığı olan birkaç ortam için başlangıç yapılandırması gerektirdiğinde yararlıdır. Tipik uygulamalar bu yaklaşıma ihtiyaç duymayacak.

Ortam tabanlı sınıfları uygulamak için `Startup` bir `Startup{EnvironmentName}` sınıflar ve geri dönüş `Startup` sınıfı oluşturun:

[!code-csharp[](environments/3.1sample/EnvironmentsSample/StartupClassConventions.cs?name=snippet)]

Derleme adını kabul eden [Usestartup (ıwebhostbuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) aşırı yüklemesini kullanın:

[!code-csharp[](environments/3.1sample/EnvironmentsSample/Program.cs?name=snippet)]

### <a name="startup-method-conventions"></a>Başlangıç yöntemi kuralları

[Yapılandırma](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) ve [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) , formun ve ortama özgü sürümlerini destekler `Configure<EnvironmentName>` `Configure<EnvironmentName>Services` . Bu yaklaşım, uygulama başına çok sayıda kod farklılığı olan birkaç ortam için başlangıç yapılandırması gerektirdiğinde yararlı olur:

[!code-csharp[](environments/3.1sample/EnvironmentsSample/StartupMethodConventions.cs?name=snippet)]

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:fundamentals/startup>
* <xref:fundamentals/configuration/index>
* <xref:blazor/fundamentals/environments>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core, bir ortam değişkeni kullanarak çalışma zamanı ortamı temelinde uygulama davranışını yapılandırır.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="environments"></a>Ortamlar

ASP.NET Core, uygulama başlangıcında ortam değişkenini okur `ASPNETCORE_ENVIRONMENT` ve değeri [ıhostingenvironment. EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName)içinde depolar. `ASPNETCORE_ENVIRONMENT`herhangi bir değere ayarlanabilir, ancak Framework tarafından üç değer sağlanır:

* <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>
* <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Staging>
* <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Production>varsayılanını

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet)]

Yukarıdaki kod:

* Olarak ayarlandığında [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) çağırır `ASPNETCORE_ENVIRONMENT` `Development` .
* Değeri aşağıdakilerden birini ayarlandığında [Useexceptionhandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) ' i çağırır `ASPNETCORE_ENVIRONMENT` :

  * `Staging`
  * `Production`
  * `Staging_2`

[Ortam etiketi Yardımcısı](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) , `IHostingEnvironment.EnvironmentName` öğesinde biçimlendirme eklemek veya dışlamak için değerini kullanır:

[!code-cshtml[](environments/sample-snapshot/EnvironmentsSample/Pages/About.cshtml)]

Windows ve macOS 'ta, ortam değişkenleri ve değerleri büyük/küçük harfe duyarlı değildir. Linux ortam değişkenleri ve değerleri varsayılan olarak büyük/küçük harfe duyarlıdır.

### <a name="development"></a>Geliştirme

Geliştirme ortamı, üretimde gösterilmemelidir özellikleri etkinleştirebilir. Örneğin, ASP.NET Core Şablonlar geliştirme ortamında [Geliştirici özel durum sayfasını](xref:fundamentals/error-handling#developer-exception-page) etkinleştirir.

Yerel makine geliştirme ortamı, projenin dosyasında *Properties\launchSettings.js* ayarlanabilir. Sistem ortamında ayarlanan geçersiz kılma değerlerinde *launchSettings.js* ayarlanan ortam değerleri.

Aşağıdaki JSON dosyadaki bir *launchSettings.js* üç profil göstermektedir:

```json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iisExpress": {
      "applicationUrl": "http://localhost:54339/",
      "sslPort": 0
    }
  },
  "profiles": {
    "IIS Express": {
      "commandName": "IISExpress",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_My_Environment": "1",
        "ASPNETCORE_DETAILEDERRORS": "1",
        "ASPNETCORE_ENVIRONMENT": "Staging"
      }
    },
    "EnvironmentsSample": {
      "commandName": "Project",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Staging"
      },
      "applicationUrl": "http://localhost:54340/"
    },
    "Kestrel Staging": {
      "commandName": "Project",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_My_Environment": "1",
        "ASPNETCORE_DETAILEDERRORS": "1",
        "ASPNETCORE_ENVIRONMENT": "Staging"
      },
      "applicationUrl": "http://localhost:51997/"
    }
  }
}
```

> [!NOTE]
> `applicationUrl` *launchSettings.jsüzerindeki* özelliği sunucu URL 'lerinin bir listesini belirtebilir. Listedeki URL 'Ler arasında noktalı virgül kullanın:
>
> ```json
> "EnvironmentsSample": {
>    "commandName": "Project",
>    "launchBrowser": true,
>    "applicationUrl": "https://localhost:5001;http://localhost:5000",
>    "environmentVariables": {
>      "ASPNETCORE_ENVIRONMENT": "Development"
>    }
> }
> ```

Uygulama [DotNet çalıştırması](/dotnet/core/tools/dotnet-run)ile başlatıldığında, ile ilk profil `"commandName": "Project"` kullanılır. Değeri, `commandName` başlatılacak Web sunucusunu belirtir. `commandName`aşağıdakilerden biri olabilir:

* `IISExpress`
* `IIS`
* `Project`(Kestrel Başlatan)

Bir uygulama [DotNet çalıştırması](/dotnet/core/tools/dotnet-run)ile başlatıldığında:

* *launchSettings.json* , varsa okundu. `environmentVariables`geçersiz kılma ortamı değişkenlerinde *launchSettings.js* ayarları.
* Barındırma ortamı görüntülenir.

Aşağıdaki çıktıda, [DotNet çalıştırması](/dotnet/core/tools/dotnet-run)ile başlatılan bir uygulama gösterilmektedir:

```bash
PS C:\Websites\EnvironmentsSample> dotnet run
Using launch settings from C:\Websites\EnvironmentsSample\Properties\launchSettings.json...
Hosting environment: Staging
Content root path: C:\Websites\EnvironmentsSample
Now listening on: http://localhost:54340
Application started. Press Ctrl+C to shut down.
```

Visual Studio proje özellikleri **hata ayıklama** sekmesi, dosyadaki *launchSettings.js* düzenlemek için bir GUI sağlar:

![Proje özellikleri ayar ortamı değişkenleri](environments/_static/project-properties-debug.png)

Proje profillerinde yapılan değişiklikler, Web sunucusu yeniden başlatılana kadar etkili olmayabilir. Kestrel, ortamında yapılan değişiklikleri algılayabilmesi için yeniden başlatılmalıdır.

> [!WARNING]
> *launchSettings.js* , gizli dizileri depolamamamalıdır. Gizli anahtar geliştirme için gizli dizileri depolamak için [gizli dizi Yöneticisi aracı](xref:security/app-secrets) kullanılabilir.

[Visual Studio Code](https://code.visualstudio.com/)kullanırken, ortam değişkenleri dosyadaki *. vscode/launch.js* ayarlanabilir. Aşağıdaki örnek, ortamı şu şekilde ayarlar `Development` :

```json
{
   "version": "0.2.0",
   "configurations": [
        {
            "name": ".NET Core Launch (web)",

            ... additional VS Code configuration settings ...

            "env": {
                "ASPNETCORE_ENVIRONMENT": "Development"
            }
        }
    ]
}
```

Projedeki bir *. vscode/launch.js* , uygulama, `dotnet run` *Özellikler/launchSettings.js*ile aynı şekilde başlatılırken okunamaz. Geliştirmede *launchSettings.js* olmayan bir uygulama başlatırken, ortama bir ortam değişkeni veya bir komut satırı bağımsız değişkeni ile ayarlayın `dotnet run` .

### <a name="production"></a>Üretim

Üretim ortamının güvenliği, performansı ve uygulama sağlamlık düzeyini en üst düzeye çıkarmak için yapılandırılması gerekir. Geliştirmeden farklı bazı yaygın ayarlar şunlardır:

* Önbelleği.
* İstemci tarafı kaynaklar paketlenmiş, küçültülmüş ve potansiyel olarak bir CDN 'den sunulan.
* Tanılama hata sayfaları devre dışı.
* Kolay hata sayfaları etkin.
* Üretim günlüğü ve izleme etkin. Örneğin, [Application Insights](/azure/application-insights/app-insights-asp-net-core).

## <a name="set-the-environment"></a>Ortamı ayarlama

Bir ortam değişkeni veya platform ayarıyla test için belirli bir ortam ayarlamak genellikle yararlıdır. Ortam ayarlanmamışsa, `Production` çoğu hata ayıklama özelliğini devre dışı bırakan varsayılan olarak olur. Ortamı ayarlama yöntemi işletim sistemine bağlıdır.

Konak yapılandırıldığında, uygulama tarafından okunan son ortam ayarı, uygulamanın ortamını belirler. Uygulama çalışırken uygulamanın ortamı değiştirilemez.

### <a name="environment-variable-or-platform-setting"></a>Ortam değişkeni veya platform ayarı

#### <a name="azure-app-service"></a>Azure App Service

[Azure App Service](https://azure.microsoft.com/services/app-service/)ortamında ortamı ayarlamak için aşağıdaki adımları gerçekleştirin:

1. Uygulama **Hizmetleri** dikey penceresinden uygulamayı seçin.
1. **Ayarlar** grubunda **yapılandırma** dikey penceresini seçin.
1. **Uygulama ayarları** sekmesinde **Yeni uygulama ayarı**' nı seçin.
1. **Uygulama ayarı Ekle/Düzenle** penceresinde ad ' ı belirtin `ASPNETCORE_ENVIRONMENT` . **Name** **Değer**için ortamı sağlayın (örneğin, `Staging` ).
1. Dağıtım yuvaları takas edildiğinde ortam ayarının geçerli yuvada kalmasını istiyorsanız **dağıtım yuvası ayarını** onay kutusunu işaretleyin. Daha fazla bilgi için bkz. Azure belgelerindeki [Azure App Service hazırlama ortamlarını ayarlama](/azure/app-service/web-sites-staged-publishing) .
1. **Tamam ' ı** seçerek **uygulama ayarı Ekle/Düzenle** penceresini kapatın.
1. **Yapılandırma** dikey penceresinin en üstünde **Kaydet** ' i seçin.

Azure App Service, Azure portal bir uygulama ayarı (ortam değişkeni) eklendikten, değiştirildikten veya silindikten sonra uygulamayı otomatik olarak yeniden başlatır.

#### <a name="windows"></a>Windows

`ASPNETCORE_ENVIRONMENT`Uygulama [DotNet çalıştırması](/dotnet/core/tools/dotnet-run)kullanılarak başlatıldığında geçerli oturum için öğesini ayarlamak için aşağıdaki komutlar kullanılır:

**Komut istemi**

```console
set ASPNETCORE_ENVIRONMENT=Development
```

**PowerShell**

```powershell
$Env:ASPNETCORE_ENVIRONMENT = "Development"
```

Bu komutlar yalnızca geçerli pencere için etkili olur. Pencere kapatıldığında, `ASPNETCORE_ENVIRONMENT` ayar varsayılan ayar veya makine değerine döner.

Windows 'da genel değeri ayarlamak için aşağıdaki yaklaşımlardan birini kullanın:

* **Denetim Masası** > **sistem** > **Gelişmiş sistem ayarları** ' nı açın ve değeri ekleyin veya düzenleyin `ASPNETCORE_ENVIRONMENT` :

  ![Sistem Gelişmiş Özellikler](environments/_static/systemsetting_environment.png)

  ![ASPNET Core ortam değişkeni](environments/_static/windows_aspnetcore_environment.png)

* Bir yönetim komut istemi açın ve komutunu kullanın `setx` veya bir yönetim PowerShell komut istemi açın ve şunu kullanın `[Environment]::SetEnvironmentVariable` :

  **Komut istemi**

  ```console
  setx ASPNETCORE_ENVIRONMENT Development /M
  ```

  `/M`Anahtar, ortam değişkenini sistem düzeyinde ayarlamaya yönelik olduğunu gösterir. `/M`Anahtar kullanılmazsa, ortam değişkeni Kullanıcı hesabı için ayarlanır.

  **PowerShell**

  ```powershell
  [Environment]::SetEnvironmentVariable("ASPNETCORE_ENVIRONMENT", "Development", "Machine")
  ```

  `Machine`Seçenek değeri, ortam değişkeninin sistem düzeyinde ayarlandığını gösterir. Seçenek değeri olarak değiştirilirse `User` , ortam değişkeni Kullanıcı hesabı için ayarlanır.

`ASPNETCORE_ENVIRONMENT`Ortam değişkeni genel olarak ayarlandığında, `dotnet run` değer ayarlandıktan sonra açılan herhangi bir komut penceresinde için geçerli olur.

**web.config**

`ASPNETCORE_ENVIRONMENT`Ortam değişkenini *web.config*olarak ayarlamak için, *ortam değişkenlerini ayarlama* bölümüne bakın <xref:host-and-deploy/aspnet-core-module#setting-environment-variables> .

**Proje dosyası veya yayımlama profili**

**WINDOWS IIS dağıtımları için:** `<EnvironmentName>`Özelliği [Publish profile (. pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) veya proje dosyasına ekleyin. Bu yaklaşım, proje yayımlandığında *web.config* ortamı ayarlar:

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

**IIS uygulama havuzu başına**

`ASPNETCORE_ENVIRONMENT`Yalıtılmış bir uygulama havuzunda çalışan bir uygulamanın ortam değişkenini ayarlamak için (ııs 10,0 veya üzeri sürümlerde desteklenir), [ortam değişkenleri &lt; &gt; environmentvariables](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) konusunun *AppCmd.exe komut* bölümüne bakın. `ASPNETCORE_ENVIRONMENT`Ortam değişkeni bir uygulama havuzu için ayarlandığında, değeri sistem düzeyindeki bir ayarı geçersiz kılar.

> [!IMPORTANT]
> IIS 'de bir uygulama barındırırken ve ortam değişkenini eklerken ya da değiştirirken `ASPNETCORE_ENVIRONMENT` , yeni değerin uygulamalar tarafından çekilmek için aşağıdaki yaklaşımlardan birini kullanın:
>
> * ' İ `net stop was /y` `net start w3svc` komut isteminden sonra yürütün.
> * Sunucuyu yeniden başlatın.

#### <a name="macos"></a>macOS

MacOS için geçerli ortamın ayarlanması, uygulamayı çalıştırırken satır içinde gerçekleştirilebilir:

```bash
ASPNETCORE_ENVIRONMENT=Development dotnet run
```

Alternatif olarak, `export` uygulamayı çalıştırmadan önce ortamı olarak ayarlayın:

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

Makine düzeyinde ortam değişkenleri *. bashrc* veya *. bash_profile* dosyasında ayarlanır. Herhangi bir metin düzenleyicisini kullanarak dosyayı düzenleyin. Aşağıdaki ifadeyi ekleyin:

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

#### <a name="linux"></a>Linux

Linux dağıtımları için, `export` oturum tabanlı değişken ayarları ve makine düzeyi ortam ayarları için *bash_profile* dosyası için komut isteminde komutunu kullanın.

### <a name="set-the-environment-in-code"></a>Kodda ortam ayarlama

<xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseEnvironment*>Ana bilgisayarı derlerken çağırın. Bkz. <xref:fundamentals/host/web-host#environment>.

### <a name="configuration-by-environment"></a>Ortama göre yapılandırma

Yapılandırmayı ortama göre yüklemek için şunları yapmanızı öneririz:

* *appSettings* dosyaları (*appSettings. { Environment}. JSON*). Bkz. <xref:fundamentals/configuration/index#json-configuration-provider>.
* Ortam değişkenleri (uygulamanın barındırıldığı her bir sistemde ayarlanır). Bkz <xref:fundamentals/host/web-host#environment> <xref:security/app-secrets#environment-variables> . ve.
* Gizli dizi Yöneticisi (yalnızca geliştirme ortamında). Bkz. <xref:security/app-secrets>.

## <a name="environment-based-startup-class-and-methods"></a>Ortam tabanlı başlangıç sınıfı ve yöntemleri

### <a name="inject-ihostingenvironment-into-startupconfigure"></a>Startup.Configure 'a ıhostingenvironment ekleme

<xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment>İçine Ekle `Startup.Configure` . Bu yaklaşım, uygulama yalnızca `Startup.Configure` , ortam başına en az kod farklılığı olan birkaç ortam için yapılandırma gerektirdiğinde yararlıdır.

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        // Development environment code
    }
    else
    {
        // Code for all other environments
    }
}
```

### <a name="inject-ihostingenvironment-into-the-startup-class"></a>Başlangıç sınıfına ıhostingenvironment ekleme

<xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> `Startup` Oluşturucuya ekleyin ve sınıfın tamamında kullanmak üzere bir alana hizmeti atayın `Startup` . Bu yaklaşım, uygulama her ortam için en az kod farklılığı olan birkaç ortam için başlatma yapılandırması gerektirdiğinde faydalıdır.

Aşağıdaki örnekte:

* Ortam `_env` alanında tutulur.
* `_env`, ve ' de, `ConfigureServices` `Configure` uygulamanın ortamına göre başlangıç yapılandırması uygulamak için kullanılır.

```csharp
public class Startup
{
    private readonly IHostingEnvironment _env;

    public Startup(IHostingEnvironment env)
    {
        _env = env;
    }

    public void ConfigureServices(IServiceCollection services)
    {
        if (_env.IsDevelopment())
        {
            // Development environment code
        }
        else if (_env.IsStaging())
        {
            // Staging environment code
        }
        else
        {
            // Code for all other environments
        }
    }

    public void Configure(IApplicationBuilder app)
    {
        if (_env.IsDevelopment())
        {
            // Development environment code
        }
        else
        {
            // Code for all other environments
        }
    }
}
```

### <a name="startup-class-conventions"></a>Başlangıç sınıfı kuralları

ASP.NET Core bir uygulama başlatıldığında, [Başlangıç sınıfı](xref:fundamentals/startup) uygulamayı önyükleme. Uygulama `Startup` farklı ortamlar için ayrı sınıflar tanımlayabilir (örneğin, `StartupDevelopment` ). Çalışma zamanında uygun `Startup` sınıf seçildi. Geçerli ortamla eşleşen ad sonekine sahip olan sınıf önceliklendirilir. Eşleşen bir `Startup{EnvironmentName}` sınıf bulunamazsa `Startup` sınıf kullanılır. Bu yaklaşım, uygulama başına çok sayıda kod farklılığı olan birkaç ortam için başlangıç yapılandırması gerektirdiğinde yararlıdır.

Ortam tabanlı sınıfları uygulamak için `Startup` , `Startup{EnvironmentName}` Kullanımdaki her ortam için bir sınıf ve bir geri dönüş sınıfı oluşturun `Startup` :

```csharp
// Startup class to use in the Development environment
public class StartupDevelopment
{
    public void ConfigureServices(IServiceCollection services)
    {
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
    }
}

// Startup class to use in the Production environment
public class StartupProduction
{
    public void ConfigureServices(IServiceCollection services)
    {
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
    }
}

// Fallback Startup class
// Selected if the environment doesn't match a Startup{EnvironmentName} class
public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
    }
}
```

Derleme adını kabul eden [Usestartup (ıwebhostbuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) aşırı yüklemesini kullanın:

```csharp
public static void Main(string[] args)
{
    CreateWebHostBuilder(args).Build().Run();
}

public static IWebHostBuilder CreateWebHostBuilder(string[] args)
{
    var assemblyName = typeof(Startup).GetTypeInfo().Assembly.FullName;

    return WebHost.CreateDefaultBuilder(args)
        .UseStartup(assemblyName);
}
```

### <a name="startup-method-conventions"></a>Başlangıç yöntemi kuralları

[Yapılandırma](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) ve [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) , formun ve ortama özgü sürümlerini destekler `Configure<EnvironmentName>` `Configure<EnvironmentName>Services` . Bu yaklaşım, uygulama başına çok sayıda kod farklılığı olan birkaç ortam için başlangıç yapılandırması gerektirdiğinde yararlıdır.

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet_all&highlight=15,42)]

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:fundamentals/startup>
* <xref:fundamentals/configuration/index>

::: moniker-end
