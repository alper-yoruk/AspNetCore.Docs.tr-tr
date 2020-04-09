---
title: ASP.NET Core'da birden çok ortam kullanma
author: rick-anderson
description: ASP.NET Core uygulamalarında birden fazla ortamda uygulama davranışını nasıl denetleriz öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/17/2019
uid: fundamentals/environments
ms.openlocfilehash: b0218b2c77c283c0849dca9491046534b88c5a77
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78656221"
---
# <a name="use-multiple-environments-in-aspnet-core"></a>ASP.NET Core'da birden çok ortam kullanma

::: moniker range=">= aspnetcore-3.0"

Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core, bir ortam değişkenini kullanarak uygulama davranışını çalışma zamanı ortamına göre yapılandırır.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample) ( nasıl[indirilir](xref:index#how-to-download-a-sample))

## <a name="environments"></a>Ortamlar

ASP.NET Core uygulama `ASPNETCORE_ENVIRONMENT` başlangıç çevre değişkeni okur ve [IWebHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName)değeri depolar. `ASPNETCORE_ENVIRONMENT`herhangi bir değere ayarlanabilir, ancak üç değer çerçeve tarafından sağlanır:

* <xref:Microsoft.Extensions.Hosting.Environments.Development>
* <xref:Microsoft.Extensions.Hosting.Environments.Staging>
* <xref:Microsoft.Extensions.Hosting.Environments.Production>(varsayılan)

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet)]

Yukarıdaki kod:

* Çağırır [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) olarak ayarlandığında `ASPNETCORE_ENVIRONMENT` `Development`.
* Değeri `ASPNETCORE_ENVIRONMENT` aşağıdakilerden biri ayarlandığında [UseExceptionHandler'ı](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) çağırır:

  * `Staging`
  * `Production`
  * `Staging_2`

[Çevre Etiketi Yardımcısı,](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) `IHostingEnvironment.EnvironmentName` öğeye biçimlendirme eklemek veya hariç tutmak için aşağıdakileri kullanır:

[!code-cshtml[](environments/sample-snapshot/EnvironmentsSample/Pages/About.cshtml)]

Windows ve macOS'ta ortam değişkenleri ve değerleri büyük/küçük harf duyarlı değildir. Linux ortamı değişkenleri ve değerleri varsayılan olarak **büyük/küçük harf duyarlıdır.**

### <a name="development"></a>Geliştirme

Geliştirme ortamı, üretimde açığa çıkmaması gereken özellikleri etkinleştirebilir. Örneğin, ASP.NET Çekirdek şablonları geliştirme ortamında [Geliştirici Özel Durum Sayfasını](xref:fundamentals/error-handling#developer-exception-page) etkinleştirin.

Yerel makine geliştirme ortamı projenin *Properties\launchSettings.json* dosyasında ayarlanabilir. *launchSettings.json'da* ayarlanan ortam değerleri sistem ortamında ayarlanan değerleri geçersiz kılar.

Aşağıdaki JSON *bir launchSettings.json* dosyasından üç profilleri gösterir:

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
> `applicationUrl` *launchSettings.json'daki* özellik, sunucu URL'lerinin bir listesini belirtebilir. Listedeki URL'ler arasında bir yarı nokta kullanın:
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

[Uygulama dotnet çalıştır](/dotnet/core/tools/dotnet-run)ı ile başlatıldığında, `"commandName": "Project"` ilk profil kullanılır. Değer başlatmak `commandName` için web sunucusu belirtir. `commandName`aşağıdakilerden herhangi biri olabilir:

* `IISExpress`
* `IIS`
* `Project`(Hangi Kerkenez başlattı)

Bir uygulama [dotnet çalıştırılan](/dotnet/core/tools/dotnet-run)başlatıldığında:

* *launchSettings.json* varsa okunur. `environmentVariables`*launchSettings.json'daki* ayarlar ortam değişkenlerini geçersiz kılar.
* Barındırma ortamı görüntülenir.

Aşağıdaki [çıktı, dotnet çalıştırıile](/dotnet/core/tools/dotnet-run)başlayan bir uygulamayı gösterir:

```bash
PS C:\Websites\EnvironmentsSample> dotnet run
Using launch settings from C:\Websites\EnvironmentsSample\Properties\launchSettings.json...
Hosting environment: Staging
Content root path: C:\Websites\EnvironmentsSample
Now listening on: http://localhost:54340
Application started. Press Ctrl+C to shut down.
```

Visual Studio proje özellikleri **Hata ayıklama** sekmesi *launchSettings.json* dosyasını düzenlemek için bir GUI sağlar:

![Proje Özellikleri Ayar Ortamı değişkenleri](environments/_static/project-properties-debug.png)

Proje profillerinde yapılan değişiklikler, web sunucusu yeniden başlatılına kadar etkili olmayabilir. Kerkenez, ortamında yapılan değişiklikleri algılayabilmesi için yeniden başlatılmalıdır.

> [!WARNING]
> *launchSettings.json* sırları saklamamalıdır. [Gizli Yönetici aracı](xref:security/app-secrets) yerel gelişim için sırları depolamak için kullanılabilir.

Visual [Studio Code](https://code.visualstudio.com/)kullanırken ortam değişkenleri *.vscode/launch.json* dosyasında ayarlanabilir. Aşağıdaki örnek, ortamı `Development`şu şekilde ayarlar:

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

Projedeki *bir .vscode/launch.json* dosyası, uygulama `dotnet run` nın *özellikleri/launchSettings.json*ile aynı şekilde başlatıldığında okunmaz. *LaunchSettings.json* dosyası olmayan geliştirme aşamasında bir uygulama başlatırken, ortamı bir ortam değişkeni veya `dotnet run` komut satırı bağımsız değişkeniyle komuta ayarlayın.

### <a name="production"></a>Üretim

Üretim ortamı, güvenliği, performansı ve uygulama sağlamlığını en üst düzeye çıkaracak şekilde yapılandırılmalıdır. Geliştirmeden farklı olan bazı yaygın ayarlar şunlardır:

* Önbelleğe alma.
* İstemci tarafı kaynakları bir CDN'den paketlenir, minified edilir ve potansiyel olarak sunulur.
* Tanılama hata sayfaları devre dışı.
* Dost hata sayfaları etkin.
* Üretim günlüğü ve izleme etkin. Örneğin, [Uygulama Öngörüleri.](/azure/application-insights/app-insights-asp-net-core)

## <a name="set-the-environment"></a>Ortamı ayarlama

Bir ortam değişkeni veya platform ayarı ile sınama için belirli bir ortam ayarlamak genellikle yararlıdır. Ortam ayarlanmıyorsa, varsayılan olarak `Production`hata ayıklama özelliklerinin çoğunu devre dışı bırakmaktadır. Ortamı ayarlama yöntemi işletim sistemine bağlıdır.

Ana bilgisayar oluşturulduğunda, uygulama tarafından okunan son ortam ayarı uygulamanın ortamını belirler. Uygulama çalışırken uygulamanın ortamı değiştirilemez.

### <a name="environment-variable-or-platform-setting"></a>Ortam değişkeni veya platform ayarı

#### <a name="azure-app-service"></a>Azure App Service

[Azure Uygulama Hizmeti'nde](https://azure.microsoft.com/services/app-service/)ortamı ayarlamak için aşağıdaki adımları gerçekleştirin:

1. **Uygulamayı Uygulama Hizmetleri** bıçağından seçin.
1. **Ayarlar** grubunda **Yapılandırma** bıçağını seçin.
1. Uygulama **ayarları** sekmesinde **Yeni uygulama ayarı'nı**seçin.
1. **Ekle/Yap uygulama ayarı** penceresinde, `ASPNETCORE_ENVIRONMENT` **Adı'nı**sağlayın. **Değer**için, ortamı sağlayın (örneğin, `Staging`).
1. Dağıtım yuvaları değiş tokuş edildiğinde ortam ayarının geçerli yuvada kalmasını istiyorsanız **Dağıtım yuvası ayar** onay kutusunu seçin. Daha fazla bilgi için bkz. Azure [dokümanında Azure Uygulama Hizmeti'nde hazırlama ortamları ayarlayın.](/azure/app-service/web-sites-staged-publishing)
1. **Ekle/Yap uygulama ayar** penceresini kapatmak için **Tamam'ı** seçin.
1. **Yapılandırma** bıçağının üst kısmında **Kaydet'i** seçin.

Azure Uygulama Hizmeti, Azure portalına bir uygulama ayarı (ortam değişkeni) eklendikten, değiştirildikten veya silindikten sonra uygulamayı otomatik olarak yeniden başlatır.

#### <a name="windows"></a>Windows

Uygulama `ASPNETCORE_ENVIRONMENT` [dotnet çalıştır'ı](/dotnet/core/tools/dotnet-run)kullanmaya başladığında geçerli oturumu ayarlamak için aşağıdaki komutlar kullanılır:

**Komut**

```console
set ASPNETCORE_ENVIRONMENT=Development
```

**PowerShell**

```powershell
$Env:ASPNETCORE_ENVIRONMENT = "Development"
```

Bu komutlar yalnızca geçerli pencere için etkili olur. Pencere kapatıldığında, `ASPNETCORE_ENVIRONMENT` ayar varsayılan ayarına veya makine değerine geri döner.

Windows'da değeri genel olarak ayarlamak için aşağıdaki yaklaşımlardan birini kullanın:

* Denetim **Masası** > **Sistemi** > **Gelişmiş sistem ayarlarını** açın `ASPNETCORE_ENVIRONMENT` ve değeri ekleyin veya düzenesin:

  ![Sistem Gelişmiş Özellikleri](environments/_static/systemsetting_environment.png)

  ![ASPNET Çekirdek Çevre Değişkeni](environments/_static/windows_aspnetcore_environment.png)

* İdari komut istemini açın `setx` ve komutu kullanın veya bir `[Environment]::SetEnvironmentVariable`yönetim PowerShell komut istemi ve kullanın:

  **Komut**

  ```console
  setx ASPNETCORE_ENVIRONMENT Development /M
  ```

  Anahtar, `/M` sistem düzeyinde ortam değişkenini ayarlamak için gösterir. `/M` Anahtar kullanılmazsa, ortam değişkeni kullanıcı hesabı için ayarlanır.

  **PowerShell**

  ```powershell
  [Environment]::SetEnvironmentVariable("ASPNETCORE_ENVIRONMENT", "Development", "Machine")
  ```

  Seçenek `Machine` değeri, sistem düzeyinde ortam değişkenini ayarlamak için gösterir. Seçenek değeri `User`, ortam değişkeni kullanıcı hesabı için ayarlanır değiştirilirse.

Ortam `ASPNETCORE_ENVIRONMENT` değişkeni genel olarak ayarlandığında, `dotnet run` değer ayarlandıktan sonra açılan herhangi bir komut penceresinde etkili olur.

**Config**

`ASPNETCORE_ENVIRONMENT` *web.config*ile ortam değişkenini ayarlamak için, ortam <xref:host-and-deploy/aspnet-core-module#setting-environment-variables> *değişkenlerini ayarlama* bölümüne bakın.

**Proje dosyası veya yayımlama profili**

**Windows IIS dağıtımları için:** `<EnvironmentName>` Özelliği [yayımlama profiline (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) veya proje dosyasına ekleyin. Bu yaklaşım, proje yayımlandığında *web.config'deki* ortamı ayarlar:

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

**IIS Başına Uygulama Havuzu**

Yalıtılmış bir Uygulama Havuzunda çalışan bir uygulama için `ASPNETCORE_ENVIRONMENT` ortam değişkenini ayarlamak için (IIS 10.0 veya sonraki saatlerde desteklenen), Çevre [Değişkenleri ortamıDeğişkenler &lt;&gt; ](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) konusunun *AppCmd.exe komut* bölümüne bakın. Ortam `ASPNETCORE_ENVIRONMENT` değişkeni bir uygulama havuzu için ayarlandığında, değeri sistem düzeyindeki bir ayarı geçersiz kılar.

> [!IMPORTANT]
> IIS'de bir uygulamayı barındırırken `ASPNETCORE_ENVIRONMENT` ve ortam değişkenini eklerken veya değiştirirken, yeni değerin uygulamalar tarafından alınması için aşağıdaki yaklaşımlardan birini kullanın:
>
> * Bir `net stop was /y` komut `net start w3svc` istemi tarafından takip yürütme.
> * Sunucuyu yeniden başlatın.

#### <a name="macos"></a>macOS

macOS için geçerli ortamı ayarlama, uygulamayı çalıştırırken satır içi olarak gerçekleştirilebilir:

```bash
ASPNETCORE_ENVIRONMENT=Development dotnet run
```

Alternatif olarak, uygulamayı `export` çalıştırmadan önce ortamı ayarlayın:

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

Makine düzeyinde ortam değişkenleri *.bashrc* veya *.bash_profile* dosyasında ayarlanır. Herhangi bir metin düzenleyicisini kullanarak dosyayı edin. Aşağıdaki ifadeyi ekleyin:

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

#### <a name="linux"></a>Linux

Linux dağıtımları için, `export` oturum tabanlı değişken ayarları için komut istemikomutunu ve makine düzeyinde ortam ayarları için *bash_profile* dosyayı kullanın.

### <a name="set-the-environment-in-code"></a>Ortamı kodda ayarlama

Ev <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseEnvironment*> sahibini kurarken arayın. Bkz. <xref:fundamentals/host/generic-host#environmentname>.


### <a name="configuration-by-environment"></a>Ortama göre yapılandırma

Yapılandırmayı ortama göre yüklemek için şunları öneririz:

* *uygulama ayarları* dosyaları (*appsettings.{ Çevre}.json*). Bkz. <xref:fundamentals/configuration/index#json-configuration-provider>.
* Ortam değişkenleri (uygulamanın barındırıldığı her sistemde ayarlayın). Bakın <xref:fundamentals/host/generic-host#environmentname> <xref:security/app-secrets#environment-variables>ve .
* Gizli Yönetici (yalnızca Geliştirme ortamında). Bkz. <xref:security/app-secrets>.

## <a name="environment-based-startup-class-and-methods"></a>Çevre tabanlı Başlangıç sınıfı ve yöntemleri

### <a name="inject-iwebhostenvironment-into-startupconfigure"></a>Inject IWebHostEnvironment içine Startup.Configure

Enjekte <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> `Startup.Configure`edin. Bu yaklaşım, uygulama yalnızca ortam `Startup.Configure` başına en az kod farkı olan birkaç ortam için ayarlama gerektiriyorsa yararlıdır.

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
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

### <a name="inject-iwebhostenvironment-into-the-startup-class"></a>Başlangıç sınıfına IWebHostEnvironment enjekte edin

Yapıcıya <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> `Startup` enjekte edin. Bu yaklaşım, uygulama ortam başına `Startup` en az kod farkı olan yalnızca birkaç ortam için yapılandırma gerektiriyorsa yararlıdır.

Aşağıdaki örnekte:

* Çevre `_env` alanında tutulur.
* `_env`uygulamanın `ConfigureServices` ortamına göre başlangıç yapılandırmasında kullanılır. `Configure`

```csharp
public class Startup
{
    private readonly IWebHostEnvironment _env;

    public Startup(IWebHostEnvironment env)
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

bir ASP.NET Core uygulaması başlatıldığında, [Başlangıç sınıfı](xref:fundamentals/startup) uygulamayı önyüklemeye yakalar. Uygulama farklı ortamlar için ayrı `Startup` sınıflar `StartupDevelopment`tanımlayabilir (örneğin, ). Çalışma `Startup` zamanında uygun sınıf seçilir. Ad soneki geçerli ortamla eşleşen sınıfa öncelik verilir. Eşleşen `Startup{EnvironmentName}` bir sınıf bulunamazsa, `Startup` sınıf kullanılır. Bu yaklaşım, uygulama, ortam başına birçok kod farkı olan çeşitli ortamlar için başlangıç yapılandırması gerektiriyorsa yararlıdır.

Çevre tabanlı `Startup` sınıfları uygulamak `Startup{EnvironmentName}` için, kullanılan her ortam için `Startup` bir sınıf ve bir geri dönüş sınıfı oluşturun:

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

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

Bir derleme adını kabul eden [UseStartup(IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) aşırı yüklemesini kullanın:

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

[Yapılandırma](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) ve [YapılandırmaHizmetleri,](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) formun `Configure<EnvironmentName>` ortama özgü `Configure<EnvironmentName>Services`sürümlerini destekler ve. Bu yaklaşım, uygulama, ortam başına birçok kod farkı olan çeşitli ortamlar için başlangıç yapılandırması gerektiriyorsa yararlıdır.

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet_all&highlight=15,42)]

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:fundamentals/startup>
* <xref:fundamentals/configuration/index>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core, bir ortam değişkenini kullanarak uygulama davranışını çalışma zamanı ortamına göre yapılandırır.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample) ( nasıl[indirilir](xref:index#how-to-download-a-sample))

## <a name="environments"></a>Ortamlar

ASP.NET Core uygulama `ASPNETCORE_ENVIRONMENT` başlangıç çevre değişkenokur ve [IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName)değeri depolar. `ASPNETCORE_ENVIRONMENT`herhangi bir değere ayarlanabilir, ancak üç değer çerçeve tarafından sağlanır:

* <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>
* <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Staging>
* <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Production>(varsayılan)

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet)]

Yukarıdaki kod:

* Çağırır [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) olarak ayarlandığında `ASPNETCORE_ENVIRONMENT` `Development`.
* Değeri `ASPNETCORE_ENVIRONMENT` aşağıdakilerden biri ayarlandığında [UseExceptionHandler'ı](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) çağırır:

  * `Staging`
  * `Production`
  * `Staging_2`

[Çevre Etiketi Yardımcısı,](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) `IHostingEnvironment.EnvironmentName` öğeye biçimlendirme eklemek veya hariç tutmak için aşağıdakileri kullanır:

[!code-cshtml[](environments/sample-snapshot/EnvironmentsSample/Pages/About.cshtml)]

Windows ve macOS'ta ortam değişkenleri ve değerleri büyük/küçük harf duyarlı değildir. Linux ortamı değişkenleri ve değerleri varsayılan olarak **büyük/küçük harf duyarlıdır.**

### <a name="development"></a>Geliştirme

Geliştirme ortamı, üretimde açığa çıkmaması gereken özellikleri etkinleştirebilir. Örneğin, ASP.NET Çekirdek şablonları geliştirme ortamında [Geliştirici Özel Durum Sayfasını](xref:fundamentals/error-handling#developer-exception-page) etkinleştirin.

Yerel makine geliştirme ortamı projenin *Properties\launchSettings.json* dosyasında ayarlanabilir. *launchSettings.json'da* ayarlanan ortam değerleri sistem ortamında ayarlanan değerleri geçersiz kılar.

Aşağıdaki JSON *bir launchSettings.json* dosyasından üç profilleri gösterir:

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
> `applicationUrl` *launchSettings.json'daki* özellik, sunucu URL'lerinin bir listesini belirtebilir. Listedeki URL'ler arasında bir yarı nokta kullanın:
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

[Uygulama dotnet çalıştır](/dotnet/core/tools/dotnet-run)ı ile başlatıldığında, `"commandName": "Project"` ilk profil kullanılır. Değer başlatmak `commandName` için web sunucusu belirtir. `commandName`aşağıdakilerden herhangi biri olabilir:

* `IISExpress`
* `IIS`
* `Project`(Hangi Kerkenez başlattı)

Bir uygulama [dotnet çalıştırılan](/dotnet/core/tools/dotnet-run)başlatıldığında:

* *launchSettings.json* varsa okunur. `environmentVariables`*launchSettings.json'daki* ayarlar ortam değişkenlerini geçersiz kılar.
* Barındırma ortamı görüntülenir.

Aşağıdaki [çıktı, dotnet çalıştırıile](/dotnet/core/tools/dotnet-run)başlayan bir uygulamayı gösterir:

```bash
PS C:\Websites\EnvironmentsSample> dotnet run
Using launch settings from C:\Websites\EnvironmentsSample\Properties\launchSettings.json...
Hosting environment: Staging
Content root path: C:\Websites\EnvironmentsSample
Now listening on: http://localhost:54340
Application started. Press Ctrl+C to shut down.
```

Visual Studio proje özellikleri **Hata ayıklama** sekmesi *launchSettings.json* dosyasını düzenlemek için bir GUI sağlar:

![Proje Özellikleri Ayar Ortamı değişkenleri](environments/_static/project-properties-debug.png)

Proje profillerinde yapılan değişiklikler, web sunucusu yeniden başlatılına kadar etkili olmayabilir. Kerkenez, ortamında yapılan değişiklikleri algılayabilmesi için yeniden başlatılmalıdır.

> [!WARNING]
> *launchSettings.json* sırları saklamamalıdır. [Gizli Yönetici aracı](xref:security/app-secrets) yerel gelişim için sırları depolamak için kullanılabilir.

Visual [Studio Code](https://code.visualstudio.com/)kullanırken ortam değişkenleri *.vscode/launch.json* dosyasında ayarlanabilir. Aşağıdaki örnek, ortamı `Development`şu şekilde ayarlar:

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

Projedeki *bir .vscode/launch.json* dosyası, uygulama `dotnet run` nın *özellikleri/launchSettings.json*ile aynı şekilde başlatıldığında okunmaz. *LaunchSettings.json* dosyası olmayan geliştirme aşamasında bir uygulama başlatırken, ortamı bir ortam değişkeni veya `dotnet run` komut satırı bağımsız değişkeniyle komuta ayarlayın.

### <a name="production"></a>Üretim

Üretim ortamı, güvenliği, performansı ve uygulama sağlamlığını en üst düzeye çıkaracak şekilde yapılandırılmalıdır. Geliştirmeden farklı olan bazı yaygın ayarlar şunlardır:

* Önbelleğe alma.
* İstemci tarafı kaynakları bir CDN'den paketlenir, minified edilir ve potansiyel olarak sunulur.
* Tanılama hata sayfaları devre dışı.
* Dost hata sayfaları etkin.
* Üretim günlüğü ve izleme etkin. Örneğin, [Uygulama Öngörüleri.](/azure/application-insights/app-insights-asp-net-core)

## <a name="set-the-environment"></a>Ortamı ayarlama

Bir ortam değişkeni veya platform ayarı ile sınama için belirli bir ortam ayarlamak genellikle yararlıdır. Ortam ayarlanmıyorsa, varsayılan olarak `Production`hata ayıklama özelliklerinin çoğunu devre dışı bırakmaktadır. Ortamı ayarlama yöntemi işletim sistemine bağlıdır.

Ana bilgisayar oluşturulduğunda, uygulama tarafından okunan son ortam ayarı uygulamanın ortamını belirler. Uygulama çalışırken uygulamanın ortamı değiştirilemez.

### <a name="environment-variable-or-platform-setting"></a>Ortam değişkeni veya platform ayarı

#### <a name="azure-app-service"></a>Azure App Service

[Azure Uygulama Hizmeti'nde](https://azure.microsoft.com/services/app-service/)ortamı ayarlamak için aşağıdaki adımları gerçekleştirin:

1. **Uygulamayı Uygulama Hizmetleri** bıçağından seçin.
1. **Ayarlar** grubunda **Yapılandırma** bıçağını seçin.
1. Uygulama **ayarları** sekmesinde **Yeni uygulama ayarı'nı**seçin.
1. **Ekle/Yap uygulama ayarı** penceresinde, `ASPNETCORE_ENVIRONMENT` **Adı'nı**sağlayın. **Değer**için, ortamı sağlayın (örneğin, `Staging`).
1. Dağıtım yuvaları değiş tokuş edildiğinde ortam ayarının geçerli yuvada kalmasını istiyorsanız **Dağıtım yuvası ayar** onay kutusunu seçin. Daha fazla bilgi için bkz. Azure [dokümanında Azure Uygulama Hizmeti'nde hazırlama ortamları ayarlayın.](/azure/app-service/web-sites-staged-publishing)
1. **Ekle/Yap uygulama ayar** penceresini kapatmak için **Tamam'ı** seçin.
1. **Yapılandırma** bıçağının üst kısmında **Kaydet'i** seçin.

Azure Uygulama Hizmeti, Azure portalına bir uygulama ayarı (ortam değişkeni) eklendikten, değiştirildikten veya silindikten sonra uygulamayı otomatik olarak yeniden başlatır.

#### <a name="windows"></a>Windows

Uygulama `ASPNETCORE_ENVIRONMENT` [dotnet çalıştır'ı](/dotnet/core/tools/dotnet-run)kullanmaya başladığında geçerli oturumu ayarlamak için aşağıdaki komutlar kullanılır:

**Komut**

```console
set ASPNETCORE_ENVIRONMENT=Development
```

**PowerShell**

```powershell
$Env:ASPNETCORE_ENVIRONMENT = "Development"
```

Bu komutlar yalnızca geçerli pencere için etkili olur. Pencere kapatıldığında, `ASPNETCORE_ENVIRONMENT` ayar varsayılan ayarına veya makine değerine geri döner.

Windows'da değeri genel olarak ayarlamak için aşağıdaki yaklaşımlardan birini kullanın:

* Denetim **Masası** > **Sistemi** > **Gelişmiş sistem ayarlarını** açın `ASPNETCORE_ENVIRONMENT` ve değeri ekleyin veya düzenesin:

  ![Sistem Gelişmiş Özellikleri](environments/_static/systemsetting_environment.png)

  ![ASPNET Çekirdek Çevre Değişkeni](environments/_static/windows_aspnetcore_environment.png)

* İdari komut istemini açın `setx` ve komutu kullanın veya bir `[Environment]::SetEnvironmentVariable`yönetim PowerShell komut istemi ve kullanın:

  **Komut**

  ```console
  setx ASPNETCORE_ENVIRONMENT Development /M
  ```

  Anahtar, `/M` sistem düzeyinde ortam değişkenini ayarlamak için gösterir. `/M` Anahtar kullanılmazsa, ortam değişkeni kullanıcı hesabı için ayarlanır.

  **PowerShell**

  ```powershell
  [Environment]::SetEnvironmentVariable("ASPNETCORE_ENVIRONMENT", "Development", "Machine")
  ```

  Seçenek `Machine` değeri, sistem düzeyinde ortam değişkenini ayarlamak için gösterir. Seçenek değeri `User`, ortam değişkeni kullanıcı hesabı için ayarlanır değiştirilirse.

Ortam `ASPNETCORE_ENVIRONMENT` değişkeni genel olarak ayarlandığında, `dotnet run` değer ayarlandıktan sonra açılan herhangi bir komut penceresinde etkili olur.

**Config**

`ASPNETCORE_ENVIRONMENT` *web.config*ile ortam değişkenini ayarlamak için, ortam <xref:host-and-deploy/aspnet-core-module#setting-environment-variables> *değişkenlerini ayarlama* bölümüne bakın.

**Proje dosyası veya yayımlama profili**

**Windows IIS dağıtımları için:** `<EnvironmentName>` Özelliği [yayımlama profiline (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) veya proje dosyasına ekleyin. Bu yaklaşım, proje yayımlandığında *web.config'deki* ortamı ayarlar:

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

**IIS Başına Uygulama Havuzu**

Yalıtılmış bir Uygulama Havuzunda çalışan bir uygulama için `ASPNETCORE_ENVIRONMENT` ortam değişkenini ayarlamak için (IIS 10.0 veya sonraki saatlerde desteklenen), Çevre [Değişkenleri ortamıDeğişkenler &lt;&gt; ](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) konusunun *AppCmd.exe komut* bölümüne bakın. Ortam `ASPNETCORE_ENVIRONMENT` değişkeni bir uygulama havuzu için ayarlandığında, değeri sistem düzeyindeki bir ayarı geçersiz kılar.

> [!IMPORTANT]
> IIS'de bir uygulamayı barındırırken `ASPNETCORE_ENVIRONMENT` ve ortam değişkenini eklerken veya değiştirirken, yeni değerin uygulamalar tarafından alınması için aşağıdaki yaklaşımlardan birini kullanın:
>
> * Bir `net stop was /y` komut `net start w3svc` istemi tarafından takip yürütme.
> * Sunucuyu yeniden başlatın.

#### <a name="macos"></a>macOS

macOS için geçerli ortamı ayarlama, uygulamayı çalıştırırken satır içi olarak gerçekleştirilebilir:

```bash
ASPNETCORE_ENVIRONMENT=Development dotnet run
```

Alternatif olarak, uygulamayı `export` çalıştırmadan önce ortamı ayarlayın:

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

Makine düzeyinde ortam değişkenleri *.bashrc* veya *.bash_profile* dosyasında ayarlanır. Herhangi bir metin düzenleyicisini kullanarak dosyayı edin. Aşağıdaki ifadeyi ekleyin:

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

#### <a name="linux"></a>Linux

Linux dağıtımları için, `export` oturum tabanlı değişken ayarları için komut istemikomutunu ve makine düzeyinde ortam ayarları için *bash_profile* dosyayı kullanın.

### <a name="set-the-environment-in-code"></a>Ortamı kodda ayarlama

Ev <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseEnvironment*> sahibini kurarken arayın. Bkz. <xref:fundamentals/host/web-host#environment>.

### <a name="configuration-by-environment"></a>Ortama göre yapılandırma

Yapılandırmayı ortama göre yüklemek için şunları öneririz:

* *uygulama ayarları* dosyaları (*appsettings.{ Çevre}.json*). Bkz. <xref:fundamentals/configuration/index#json-configuration-provider>.
* Ortam değişkenleri (uygulamanın barındırıldığı her sistemde ayarlayın). Bakın <xref:fundamentals/host/web-host#environment> <xref:security/app-secrets#environment-variables>ve .
* Gizli Yönetici (yalnızca Geliştirme ortamında). Bkz. <xref:security/app-secrets>.

## <a name="environment-based-startup-class-and-methods"></a>Çevre tabanlı Başlangıç sınıfı ve yöntemleri

### <a name="inject-ihostingenvironment-into-startupconfigure"></a>Inject IHostingEnvironment içine Startup.Configure

Enjekte <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> `Startup.Configure`edin. Bu yaklaşım, uygulama yalnızca ortam `Startup.Configure` başına en az kod farkı olan yalnızca birkaç ortam için yapılandırma gerektiriyorsa yararlıdır.

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

### <a name="inject-ihostingenvironment-into-the-startup-class"></a>Başlangıç sınıfına IHostingEnvironment enjekte edin

Oluşturucuya enjekte <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> edin `Startup` ve hizmeti `Startup` sınıf boyunca kullanılmak üzere bir alana atayın. Bu yaklaşım, uygulama, ortam başına en az kod farkı olan yalnızca birkaç ortam için başlangıç yapılandırması gerektiriyorsa yararlıdır.

Aşağıdaki örnekte:

* Çevre `_env` alanında tutulur.
* `_env`uygulamanın `ConfigureServices` ortamına göre başlangıç yapılandırmasında kullanılır. `Configure`

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

bir ASP.NET Core uygulaması başlatıldığında, [Başlangıç sınıfı](xref:fundamentals/startup) uygulamayı önyüklemeye yakalar. Uygulama farklı ortamlar için ayrı `Startup` sınıflar `StartupDevelopment`tanımlayabilir (örneğin, ). Çalışma `Startup` zamanında uygun sınıf seçilir. Ad soneki geçerli ortamla eşleşen sınıfa öncelik verilir. Eşleşen `Startup{EnvironmentName}` bir sınıf bulunamazsa, `Startup` sınıf kullanılır. Bu yaklaşım, uygulama, ortam başına birçok kod farkı olan çeşitli ortamlar için başlangıç yapılandırması gerektiriyorsa yararlıdır.

Çevre tabanlı `Startup` sınıfları uygulamak `Startup{EnvironmentName}` için, kullanılan her ortam için `Startup` bir sınıf ve bir geri dönüş sınıfı oluşturun:

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

Bir derleme adını kabul eden [UseStartup(IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) aşırı yüklemesini kullanın:

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

[Yapılandırma](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) ve [YapılandırmaHizmetleri,](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) formun `Configure<EnvironmentName>` ortama özgü `Configure<EnvironmentName>Services`sürümlerini destekler ve. Bu yaklaşım, uygulama, ortam başına birçok kod farkı olan çeşitli ortamlar için başlangıç yapılandırması gerektiriyorsa yararlıdır.

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet_all&highlight=15,42)]

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:fundamentals/startup>
* <xref:fundamentals/configuration/index>

::: moniker-end
