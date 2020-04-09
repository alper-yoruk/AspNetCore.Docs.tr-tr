---
title: ASP.NET Core Modülü
author: rick-anderson
description: ASP.NET Core uygulamalarını barındırmak için ASP.NET Çekirdek Modül'ASP.NET nasıl yapılandırılabildiğini öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
uid: host-and-deploy/aspnet-core-module
ms.openlocfilehash: 298d424557600735668217e1ef07ace606dac60b
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667302"
---
# <a name="aspnet-core-module"></a>ASP.NET Core Modülü

Tom [Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Chris Ross](https://github.com/Tratcher), Rick [Anderson](https://twitter.com/RickAndMSFT), [Sourabh Shirhatti](https://twitter.com/sshirhatti)ve [Justin Kotalik](https://github.com/jkotalik) tarafından

::: moniker range=">= aspnetcore-3.0"

ASP.NET Çekirdek Modülü, IIS boru hattına aşağıdakilerden herhangi birini takılan yerel bir IIS modülüdür:

* IIS işçi sürecinin içinde bir ASP.NET`w3wp.exe`Core uygulaması barındırın ( ), [süreç içi barındırma modeli](#in-process-hosting-model)olarak adlandırılır.
* [Kestrel sunucusu](xref:fundamentals/servers/kestrel)nu çalıştıran bir arka uç ASP.NET Core uygulamasına web isteklerini [iletin, işlem dışı barındırma modeli](#out-of-process-hosting-model)olarak adlandırılır.

Desteklenen Windows sürümleri:

* Windows 7 veya sonraki
* Windows Server 2008 R2 veya sonraki sürümü

Süreç içinde barındırma yaparken, modül IIS için IIS HTTP Server (`IISHttpServer`) adı verilen bir işlem içi sunucu uygulaması kullanır.

Proses dışı ev sahipliği yaparken, modül sadece Kerkenez ile çalışır. Modül [HTTP.sys](xref:fundamentals/servers/httpsys)ile çalışmıyor.

## <a name="hosting-models"></a>Barındırma modelleri

### <a name="in-process-hosting-model"></a>Süreç içi barındırma modeli

ASP.NET Core uygulamaları, işlem içi barındırma modeline varsayılan olarak.

Süreç içinde barındırma yaparken aşağıdaki özellikler geçerlidir:

* IIS HTTP`IISHttpServer`Server ( ) [Kerkenez](xref:fundamentals/servers/kestrel) sunucusu yerine kullanılır. Süreç içinde [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) şu <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> çağrıları ile çağırır:

  * Kaydedin. `IISHttpServer`
  * ASP.NET Çekirdek Modülü'nün arkasında çalışırken sunucunun dinlemesi gereken bağlantı noktası ve temel yolunu yapılandırın.
  * Başlangıç hatalarını yakalamak için ana bilgisayarı yapılandırın.

* [RequestTimeout özniteliği](#attributes-of-the-aspnetcore-element) işlem içi barındırma için geçerli değildir.

* Uygulamalar arasında uygulama havuzu paylaşmak desteklenmez. Uygulama başına bir uygulama havuzu kullanın.

* Web [Dağıtımı'nı](/iis/publish/using-web-deploy/introduction-to-web-deploy) kullanırken veya [dağıtıma bir app_offline.htm dosyasını](xref:host-and-deploy/iis/index#locked-deployment-files)el ile yerleştirirken, açık bir bağlantı varsa uygulama hemen kapanmayabilir. Örneğin, bir websocket bağlantısı uygulamanın kapatılmasını geciktirebilir.

* Uygulamanın mimarisi (bitness) ve yüklü çalışma süresi (x64 veya x86) uygulama havuzunun mimarisi yle eşleşmelidir.

* İstemci bağlantıları algılanır. [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) iptal belirteci, istemci bağlantısı kesildiğinde iptal edilir.

* Core 2.2.1 veya daha <xref:System.IO.Directory.GetCurrentDirectory*> önceki ASP.NET, uygulamanın dizini yerine IIS tarafından başlatılan işlemin alt dizini döndürür (örneğin, *w3wp.exe*için *C:\Windows\System32\inetsrv).*

  Uygulamanın geçerli dizinini ayarlayan örnek kod için [CurrentDirectoryHelpers sınıfına](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/3.x/CurrentDirectoryHelpers.cs)bakın. `SetCurrentDirectory` Yöntemi ara. Uygulamanın <xref:System.IO.Directory.GetCurrentDirectory*> dizinini sağlamak için sonraki çağrılar.

* Süreç içinde barındırma <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> yaparken, bir kullanıcıyı başlatmak için dahili olarak çağrılmaz. Bu nedenle, her kimlik doğrulaması varsayılan olarak etkinleştirilmedikten sonra talepleri dönüştürmek için kullanılan bir <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> uygulama. Bir <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> uygulamayla talepleri dönüştürürken, kimlik doğrulama hizmetleri eklemeyi çağırın: <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*>

  ```csharp
  public void ConfigureServices(IServiceCollection services)
  {
      services.AddTransient<IClaimsTransformation, ClaimsTransformer>();
      services.AddAuthentication(IISServerDefaults.AuthenticationScheme);
  }

  public void Configure(IApplicationBuilder app)
  {
      app.UseAuthentication();
  }
  ```
  
  * [Web Paketi (tek dosyalı) dağıtımlar](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages) desteklenmez.

### <a name="out-of-process-hosting-model"></a>İşlem dışı barındırma modeli

İşlem dışı barındırma için bir uygulamayı yapılandırmak için, `<AspNetCoreHostingModel>` özelliğin `OutOfProcess` değerini proje dosyasındaki *(.csproj)* ayarlayın:

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

Süreç içi `InProcess`barındırma, varsayılan değer olan ile ayarlanır.

Değeri büyük/küçük harf `<AspNetCoreHostingModel>` duyarsız, bu yüzden `inprocess` ve `outofprocess` geçerli değerlerdir.

[Kerkenez](xref:fundamentals/servers/kestrel) sunucusu Yerine IIS HTTP`IISHttpServer`Server ( ) kullanılır.

İşlem dışı için [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) şunları <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> çağırır:

* ASP.NET Çekirdek Modülü'nün arkasında çalışırken sunucunun dinlemesi gereken bağlantı noktası ve temel yolunu yapılandırın.
* Başlangıç hatalarını yakalamak için ana bilgisayarı yapılandırın.

### <a name="hosting-model-changes"></a>Barındırma modeli değişiklikleri

Ayarı `hostingModel` *web.config* dosyasında değiştirilirse [(web.config](#configuration-with-webconfig) bölümü ile Yapılandırma'da açıklanırsa), modül IIS için alt işlemi geri dönüştürür.

IIS Express için modül, alt işlemi geri dönüştürmez, bunun yerine geçerli IIS Express işleminin zarif bir şekilde kapatılmasını tetikler. Uygulamanın bir sonraki isteği yeni bir IIS Express işlemi doğurur.

### <a name="process-name"></a>İşlem adı

`Process.GetCurrentProcess().ProcessName``w3wp` / raporlar `iisexpress` (süreç içi) `dotnet` veya (işlem dışı).

Windows Kimlik Doğrulama gibi birçok yerel modül etkin kalır. ASP.NET Çekirdek Modülü ile etkin olan IIS modülleri hakkında daha fazla bilgi için bkz. <xref:host-and-deploy/iis/modules>

ASP.NET Çekirdek Modülü de şunları yapabilir:

* Alt işlem için ortam değişkenlerini ayarlayın.
* Sorun giderme başlangıç sorunları için dosya depolamasına stdout çıktısını günlüğe kaydedin.
* İleri Windows kimlik doğrulama belirteçleri.

## <a name="how-to-install-and-use-the-aspnet-core-module"></a>ASP.NET Çekirdek Modülü nasıl yüklenir ve kullanılır?

ASP.NET Çekirdek Modülü nasıl yüklenir yönergeleri [için](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)bkz.

## <a name="configuration-with-webconfig"></a>web.config ile yapılandırma

ASP.NET Çekirdek Modülü, sitenin `aspNetCore` `system.webServer` *web.config* dosyasındaki düğüm bölümüyle yapılandırılır.

Aşağıdaki *web.config* [dosyası, çerçeveye bağımlı](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) bir dağıtım için yayımlanır ve site isteklerini işlemek için ASP.NET Çekirdek Modül'u yapılandırır:

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath="dotnet"
                  arguments=".\MyApp.dll"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

Aşağıdaki *web.config* [kendi kendine yeten bir dağıtım](/dotnet/articles/core/deploying/#self-contained-deployments-scd)için yayınlanır:

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath=".\MyApp.exe"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

Özellik, <xref:System.Configuration.SectionInformation.InheritInChildApplications*> konumu `false` [ \<>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) öğesi içinde belirtilen ayarların uygulamanın bir alt dizininde bulunan uygulamalar tarafından devralınılolmadığını belirtmek üzere ayarlanmıştır.

Bir uygulama [Azure Uygulama Hizmeti'ne](https://azure.microsoft.com/services/app-service/) `stdoutLogFile` dağıtıldığında, `\\?\%home%\LogFiles\stdout`yol . Yol, hizmet tarafından otomatik olarak oluşturulan bir konum olan *LogFiles* klasörüne stdout günlüklerini kaydeder.

IIS alt uygulama yapılandırması hakkında <xref:host-and-deploy/iis/index#sub-applications>bilgi için bkz.

### <a name="attributes-of-the-aspnetcore-element"></a>aspNetCore öğesinin öznitelikleri

| Öznitelik | Açıklama | Varsayılan |
| --------- | ----------- | :-----: |
| `arguments` | <p>İsteğe bağlı dize özniteliği.</p><p>**ProcessPath'de**belirtilen yürütülebilir bağımsız değişkenler.</p> | |
| `disableStartUpErrorPage` | <p>İsteğe bağlı Boolean özniteliği.</p><p>Doğruysa, **502.5 - İşlem Hatası** sayfası bastırılır ve *web.config'de* yapılandırılan 502 durum kodu sayfası önceliklidir.</p> | `false` |
| `forwardWindowsAuthToken` | <p>İsteğe bağlı Boolean özniteliği.</p><p>Doğruysa, belirteç istek başına 'MS-ASPNETCORE-WINAUTHTOKEN' üstbilgi olarak %ASPNETCORE_PORT'da dinleyerek alt işleme iletilir. İstek başına bu belirteç için CloseHandle'ı aramak bu işlemin sorumluluğundadır.</p> | `true` |
| `hostingModel` | <p>İsteğe bağlı dize özniteliği.</p><p>Barındırma modelini süreç içi (`InProcess`/`inprocess`) veya süreç dışı`OutOfProcess`/`outofprocess`olarak belirtir ( ).</p> | `InProcess`<br>`inprocess` |
| `processesPerApplication` | <p>İsteğe bağlı birer sayışma özniteliği.</p><p>Uygulama başına döndürülebilen **processPath** ayarında belirtilen işlemin örnek sayısını belirtir.</p><p>&dagger;Süreç içi barındırma için, değer `1`.</p><p>Ayar `processesPerApplication` önerilmez. Bu öznitelik gelecekteki bir sürümde kaldırılır.</p> | Varsayılan:`1`<br>Dk:`1`<br>Max:`100`&dagger; |
| `processPath` | <p>Gerekli dize özniteliği.</p><p>HTTP isteklerini dinleyerek bir işlem başlatan yürütülebilir yol. Göreli yollar desteklenir. Yol ile `.`başlarsa, yol site köküne göre olarak kabul edilir.</p> | |
| `rapidFailsPerMinute` | <p>İsteğe bağlı birer sayışma özniteliği.</p><p>**ProcessPath'de** belirtilen işlemin dakikada çökmesine izin verilme sayısını belirtir. Bu sınır aşılırsa, modül dakikanın geri kalanı için işlemi başlatmayı durdurur.</p><p>Süreç içi barındırma ile desteklenmez.</p> | Varsayılan:`10`<br>Dk:`0`<br>Max:`100` |
| `requestTimeout` | <p>İsteğe bağlı zaman alanı özniteliği.</p><p>ASP.NET Çekirdek Modülü'nün %ASPNETCORE_PORT'de dinleme işleminden yanıt beklediği süreyi belirtir.</p><p>ASP.NET Core 2.1 veya daha sonra sürümü ile gönderilen ASP.NET `requestTimeout` Çekirdek Modülü sürümlerinde, saat, dakika ve saniye cinsinden belirtilir.</p><p>Süreç içi barındırma için geçerli değildir. Süreç içi barındırma için modül, uygulamanın isteği işlemesini bekler.</p><p>Dize dakika ve saniye segmentleri için geçerli değerler 0-59 aralığındadır. 60 **60** değerinin dakika veya saniye olarak kullanılması *500 ile*sonuçlanır - Internal Server Hatası.</p> | Varsayılan:`00:02:00`<br>Dk:`00:00:00`<br>Max:`360:00:00` |
| `shutdownTimeLimit` | <p>İsteğe bağlı birer sayışma özniteliği.</p><p>*app_offline.htm* dosyası algılandığında, modülün yürütülebilir inin düzgün bir şekilde kapanmasını beklediği saniye cinsinden süre.</p> | Varsayılan:`10`<br>Dk:`0`<br>Max:`600` |
| `startupTimeLimit` | <p>İsteğe bağlı birer sayışma özniteliği.</p><p>Modülün çalıştırılabilenin bağlantı noktasını dinleyerek bir işlem başlatmasını beklediği saniye cinsinden süre. Bu zaman sınırı aşılırsa, modül işlemi öldürür. Modül, yeni bir istek aldığında işlemi yeniden başlatmaya çalışır ve uygulama son dakika içinde hızlı bir **şekilde FailsPerMinute** sayısını başlatamazsa sonraki gelen isteklerde işlemi yeniden başlatmaya çalışır.</p><p>0 (sıfır) değeri sonsuz zaman kaybı olarak kabul **edilmez.**</p> | Varsayılan:`120`<br>Dk:`0`<br>Max:`3600` |
| `stdoutLogEnabled` | <p>İsteğe bağlı Boolean özniteliği.</p><p>Doğruysa, **processPath** belirtilen işlem için **stdout** ve **stderr** **stdoutLogFile**belirtilen dosyaya yönlendirilir.</p> | `false` |
| `stdoutLogFile` | <p>İsteğe bağlı dize özniteliği.</p><p>**ProcessPath'de** belirtilen işlemden **stdout** ve **stderr** günlüğe kaydedilmiş olan göreli veya mutlak dosya yolunu belirtir. Göreli yollar sitenin köküne göredir. Herhangi bir `.` yol ile başlayan site köküne göre ve diğer tüm yollar mutlak yollar olarak kabul edilir. Yol dosyasında sağlanan tüm klasörler, günlük dosyası oluşturulduğunda modül tarafından oluşturulur. Alt çizgi delimiters kullanarak, bir zaman damgası, işlem kimliği ve dosya uzantısı (*.log*) **stdoutLogFile** yolunun son bölümüne eklenir. Değer `.\logs\stdout` olarak veseği, 2/5/2018 tarihinde saat 19:41:32'de 1934 işlem kimliğiyle kaydedilirken, örneğin stdout günlüğü *stdout_20180205194132_1934.log* olarak kaydedilir. *logs*</p> | `aspnetcore-stdout` |

### <a name="set-environment-variables"></a>Ortam değişkenlerini belirleme

Öznitelikteki `processPath` işlem için ortam değişkenleri belirtilebilir. Bir koleksiyon öğesinin `<environmentVariable>` alt öğesi ile bir ortam değişkeni belirtin. `<environmentVariables>` Bu bölümde ayarlanan ortam değişkenleri sistem ortamı değişkenlerinden önceliklidir.

Aşağıdaki örnek, *web.config'de*iki ortam değişkeni belirler. `ASPNETCORE_ENVIRONMENT` uygulamanın ortamını `Development`. Geliştirici, geliştirici [özel durum sayfasını](xref:fundamentals/error-handling) hata ayıklarken Geliştirici Özel Durum Sayfasını yüklemeye zorlamak için bu değeri *web.config* dosyasında geçici olarak ayarlayabilir. `CONFIG_DIR`geliştirici, uygulamanın yapılandırma dosyasını yüklemek için bir yol oluşturmak için başlangıç değerini okuyan bir kod yazdığı kullanıcı tanımlı ortam değişkenine bir örnektir.

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout"
      hostingModel="inprocess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

> [!NOTE]
> Ortamı doğrudan *web.config'de* ayarlamanın alternatifi, `<EnvironmentName>` özelliği [yayımlama profiline (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) veya proje dosyasına eklemektir. Bu yaklaşım, proje yayımlandığında *web.config'deki* ortamı ayarlar:
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> Ortam değişkenini `ASPNETCORE_ENVIRONMENT` `Development` yalnızca Internet gibi güvenilmeyen ağlar tarafından erişilemeyen hazırlama ve sınama sunucularına ayarlayın.

## <a name="app_offlinehtm"></a>app_offline.htm

Bir uygulamanın kök dizininde *app_offline.htm* adında bir dosya algılanırsa, ASP.NET Çekirdek Modülü uygulamayı zarif bir şekilde kapatmaya ve gelen istekleri işlemeyi durdurmaya çalışır. Uygulama tanımlanan saniye sayısından sonra çalışmaya `shutdownTimeLimit`devam ediyorsa, ASP.NET Çekirdek Modülü çalışma işlemini öldürür.

*app_offline.htm* dosyası mevcut ken, ASP.NET Çekirdek Modülü *app_offline.htm* dosyasının içeriğini geri göndererek isteklere yanıt verir. *app_offline.htm* dosyası kaldırıldığında, bir sonraki istek uygulamayı başlatır.

İşlem dışı barındırma modelini kullanırken, açık bir bağlantı varsa uygulama hemen kapanmayabilir. Örneğin, bir websocket bağlantısı uygulamanın kapatılmasını geciktirebilir.

## <a name="start-up-error-page"></a>Başlatma hatası sayfası

Hem süreç içi hem de süreç dışı barındırma, uygulamayı başlatamadıkları zaman özel hata sayfaları üretir.

ASP.NET Çekirdek Modülü işlem içi veya işlem dışı istek işleyicisini bulamazsa, *500.0 - İşlem İçi/İşlem Dışı İşleyici Yük Hatası* kod sayfası görüntülenir.

ASP.NET Çekirdek Modülü uygulamayı başlatamazsa, işlem içi barındırma için *500.30 - Start Failure* durum kodu sayfası görüntülenir.

ASP.NET Çekirdek Modülü arka uç işlemini başlatamazsa veya arka uç işlemi başlarsa ancak yapılandırılan bağlantı noktasını dinlemezse, *502.5 - İşlem Hatası* durum kodu sayfası görüntülenir.

Bu sayfayı bastırmak ve varsayılan IIS 5xx durum kodu `disableStartUpErrorPage` sayfasına geri dönmek için özniteliği kullanın. Özel hata iletilerinin yapılandırılması hakkında daha fazla bilgi için [HTTP Hataları \<httpErrors>. ](/iis/configuration/system.webServer/httpErrors/)

## <a name="log-creation-and-redirection"></a>Günlük oluşturma ve yeniden yönlendirme

ASP.NET Çekirdek Modülü, `stdoutLogEnabled` `stdoutLogFile` `aspNetCore` öğenin öznitelikleri ve öznitelikleri ayarlanmışsa, stdout ve stderr konsol çıktısını diske yönlendirir. `stdoutLogFile` Yoldaki tüm klasörler, günlük dosyası oluşturulduğunda modül tarafından oluşturulur. Uygulama havuzu, günlüklerin yazıldığı konuma yazma erişimine sahip `IIS AppPool\<app_pool_name>` olmalıdır (yazma izni sağlamak için kullanın).

İşlem geri dönüşümü/yeniden başlatma gerçekleşmedikçe günlükler döndürülemez. Günlüklerin tükettiği disk alanını sınırlamak ana bilgisayarın sorumluluğundadır.

Stdout günlüğünün kullanılması yalnızca IIS'de barındırma yaparken veya [Visual Studio ile IIS için geliştirme zamanı desteğini](xref:host-and-deploy/iis/development-time-iis-support)kullanırken sorun giderme uygulaması başlatma sorunları için önerilir, yerel olarak hata ayıklarken ve uygulamayı IIS Express ile çalıştırırken değil.

Stdout günlüğünü genel uygulama günlüğü amacıyla kullanmayın. ASP.NET Core uygulamasında rutin günlüğe kaydetme için, günlük dosya boyutunu sınırlayan ve günlükleri döndüren bir günlük kitaplığı kullanın. Daha fazla bilgi için [üçüncü taraf günlük sağlayıcılarına](xref:fundamentals/logging/index#third-party-logging-providers)bakın.

Günlük dosyası oluşturulduğunda otomatik olarak bir zaman damgası ve dosya uzantısı eklenir. Günlük dosya adı, `stdoutLogFile` zaman damgası, işlem kimliği ve dosya uzantısı *(.log*) yolun son kesimine (genellikle *stdout)* alt çizgiyle sınırlandırılmış olarak eklenerek oluşturulur. `stdoutLogFile` Yol *stdout*ile biterse , 2/5/2018 tarihinde 19:42:32 tarihinde oluşturulan 1934 PID ile bir uygulama için bir günlük *stdout_20180205194132_1934.log*dosya adı vardır.

Yanlışsa, `stdoutLogEnabled` uygulama başlatmada oluşan hatalar yakalanır ve 30 KB'a kadar olay günlüğüne yayılır. Başlatmadan sonra, tüm ek günlükler atılır.

Aşağıdaki örnek `aspNetCore` öğe, nispi yolda `.\log\`stdout günlüğe yapılandırır. AppPool kullanıcı kimliğinin sağlanan yola yazma izni olduğunu doğrulayın.

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

Azure Uygulama Hizmeti dağıtımı için bir uygulama yayımlarken, Web SDK `stdoutLogFile` değerini `\\?\%home%\LogFiles\stdout`. Ortam `%home` değişkeni, Azure Uygulama Hizmeti tarafından barındırılan uygulamalar için önceden tanımlanmıştır.

Günlük filtresi kuralları oluşturmak için, ASP.NET Core günlük belgelerinin [Yapılandırma](xref:fundamentals/logging/index#log-filtering) ve [Günlük filtreleme](xref:fundamentals/logging/index#log-filtering) bölümlerine bakın.

Yol biçimleri hakkında daha fazla bilgi için [Windows sistemlerinde Dosya yolu biçimlerine](/dotnet/standard/io/file-path-formats)bakın.

## <a name="enhanced-diagnostic-logs"></a>Geliştirilmiş tanılama günlükleri

ASP.NET Çekirdek Modülü gelişmiş tanılama günlükleri sağlamak için yapılandırılabilir. Öğeyi `<handlerSettings>` `<aspNetCore>` *web.config'deki*öğeye ekleyin. Tanılama `debugLevel` `TRACE` bilgilerinin daha yüksek bir sadakatini ortaya çıkaracak şekilde ayarlanın:

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="debugFile" value=".\logs\aspnetcore-debug.log" />
    <handlerSetting name="debugLevel" value="FILE,TRACE" />
  </handlerSettings>
</aspNetCore>
```

Yoldaki tüm klasörler (önceki örnekte*günlükler)* günlük dosyası oluşturulduğunda modül tarafından oluşturulur. Uygulama havuzu, günlüklerin yazıldığı konuma yazma erişimine sahip `IIS AppPool\<app_pool_name>` olmalıdır (yazma izni sağlamak için kullanın).

Hata ayıklama`debugLevel`düzeyi ( ) değerleri hem düzeyi hem de konumu içerebilir.

Düzeyler (en azdan en ayrıntılısıraya kadar):

* HATA
* UYARI
* Bilgi
* TRACE

Konumlar (birden çok konuma izin verilir):

* Konsol
* Eventlog
* DOSYA

İşleyici ayarları ortam değişkenleri aracılığıyla da sağlanabilir:

* `ASPNETCORE_MODULE_DEBUG_FILE`&ndash; Hata ayıklama günlüğü dosyasına giden yol. (Varsayılan: *aspnetcore-debug.log*)
* `ASPNETCORE_MODULE_DEBUG`&ndash; Hata ayıklama düzeyi ayarı.

> [!WARNING]
> Hata **not** ayıklama günlüğekaydetmeyi dağıtımda bir sorunu gidermek için gerekenden daha uzun süre etkin bırakmayın. Günlüğün boyutu sınırlı değildir. Hata ayıklama günlüğünü etkin bırakmak, kullanılabilir disk alanını tüketebilir ve sunucuyu veya uygulama hizmetini kilitleyebilir.

*Web.config* dosyasındaki `aspNetCore` öğenin bir örneği için [web.config ile Yapılandırma'ya](#configuration-with-webconfig) bakın.

## <a name="modify-the-stack-size"></a>Yığın boyutunu değiştirme

*Yalnızca işlem içi barındırma modelini kullanırken geçerlidir.*

yönetilen yığın boyutunu `stackSize` *web.config'deki*baytayarını kullanarak yapılandırın. Varsayılan boyut `1048576` bayt (1 MB) 'dir.

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="stackSize" value="2097152" />
  </handlerSettings>
</aspNetCore>
```

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a>Proxy yapılandırması HTTP protokolü ve eşleştirme belirteci kullanır

*Yalnızca işlem dışı barındırma için geçerlidir.*

ASP.NET Çekirdek Modülü ile Kestrel arasında oluşturulan proxy HTTP protokolünü kullanır. Modül ve Kestrel arasındaki trafiği sunucunun dışında bir konumdan gizlice dinleme riski yoktur.

Eşleştirme belirteci, Kestrel tarafından alınan isteklerin IIS tarafından yerine getirilmiş olduğunu ve başka bir kaynaktan gelmediğini garanti etmek için kullanılır. Eşleştirme belirteci modül tarafından oluşturulur ve`ASPNETCORE_TOKEN`bir ortam değişkeni () olarak ayarlanır. Eşleştirme belirteci, her yakın istekte`MS-ASPNETCORE-TOKEN`bir üstbilgi () olarak da ayarlanır. IIS Middleware, eşleştirme belirteç üstbilgi değerinin ortam değişken değeriyle eşleştiğini doğrulamak için aldığı her isteği denetler. Belirteç değerleri eşleşmezse, istek günlüğe kaydedilir ve reddedilir. Eşleştirme belirteç ortamı değişkeni ve modül ile Kestrel arasındaki trafiğe sunucunun dışında bir konumdan erişilemez. Eşleştirme belirteç değerini bilmeden, saldırgan IIS Middleware'de denetimi atlayan istekler gönderemez.

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a>IIS Paylaşılan Yapılandırmalı ASP.NET Çekirdek Modülü

ASP.NET Çekirdek Modülü yükleyici, **TrustedInstaller** hesabının ayrıcalıklarıyla çalışır. Yerel sistem hesabı, IIS Paylaşılan Yapılandırması tarafından kullanılan paylaşım yolu için değişiklik izni olmadığından, yükleci, paylaşımdaki *applicationHost.config* dosyasındaki modül ayarlarını yapılandırmaya çalışırken erişim reddedilen bir hata atar.

IIS yüklemesi ile aynı makinede bir IIS Paylaşılan Yapılandırma kullanırken, `OPT_NO_SHARED_CONFIG_CHECK` parametre ayarlanmış `1`ASP.NET Core Hosting Bundle yükleyicisini çalıştırın:

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

Paylaşılan yapılandırmaya giden yol IIS yüklemeyle aynı makinede değilse aşağıdaki adımları izleyin:

1. IIS Paylaşılan Yapılandırmasını devre dışı kınla.
1. Yükleyiciyi çalıştırın.
1. Paylaşıma güncelleştirilmiş *applicationHost.config* dosyasını dışa aktarın.
1. IIS Paylaşılan Yapılandırmasını yeniden etkinleştirin.

## <a name="module-version-and-hosting-bundle-installer-logs"></a>Modül sürümü ve Hosting Paketi yükleyici günlükleri

Yüklü ASP.NET Çekirdek Modülü sürümünü belirlemek için:

1. Barındırma sisteminde *% windir%\System32\inetsrv'e*gidin.
1. *aspnetcore.dll* dosyasını bulun.
1. Dosyaya sağ tıklayın ve bağlamsal menüden **Özellikler'i** seçin.
1. **Ayrıntılar** sekmesini seçin. **Dosya sürümü** ve Ürün **sürümü** modülün yüklü sürümünü temsil eder.

Modül için Hosting Paketi yükleyici günlükleri *C\\\\bulunur: Kullanıcılar\\%UserName%\\AppData Local\\Temp*. Dosya zaman *damgası>_000_AspNetCoreModule_x64.log dd_DotNetCoreWinSvrHosting__\<* adlandırılır.

## <a name="module-schema-and-configuration-file-locations"></a>Modül, şema ve yapılandırma dosya konumları

### <a name="module"></a>Modül

**IIS (x86/amd64):**

* %windir%\System32\inetsrv\aspnetcore.dll

* %windir%\SysWOW64\inetsrv\aspnetcore.dll

* %ProgramFiles%\IIS\Asp.Net Çekirdek Modülü\V2\aspnetcorev2.dll

* %ProgramFiles(x86)%\IIS\Asp.Net Çekirdek Modülü\V2\aspnetcorev2.dll

**IIS Express (x86/amd64):**

* %ProgramFiles%\IIS Express\aspnetcore.dll

* %ProgramFiles(x86)%\IIS Express\aspnetcore.dll

* %ProgramFiles%\IIS Express\Asp.Net Çekirdek Modülü\V2\aspnetcorev2.dll

* %ProgramFiles(x86)%\IIS Express\Asp.Net Çekirdek Modülü\V2\aspnetcorev2.dll

### <a name="schema"></a>Şema

**IIS**

* %windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml

* %windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml

**IIS Ekspresi**

* %ProgramFiles%\IIS Express\config\şema\aspnetcore_schema.xml

* %ProgramFiles%\IIS Express\config\şema\aspnetcore_schema_v2.xml

### <a name="configuration"></a>Yapılandırma

**IIS**

* %windir%\System32\inetsrv\config\applicationHost.config

**IIS Ekspresi**

* Görsel Stüdyo: {APPLICATION\\ROOT} .vs\config\applicationHost.config

* *iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config

Dosyalar *applicationHost.config* dosyasında *aspnetcore* arayarak bulunabilir.

::: moniker-end

::: moniker range="= aspnetcore-2.2"

ASP.NET Çekirdek Modülü, IIS boru hattına aşağıdakilerden herhangi birini takılan yerel bir IIS modülüdür:

* IIS işçi sürecinin içinde bir ASP.NET`w3wp.exe`Core uygulaması barındırın ( ), [süreç içi barındırma modeli](#in-process-hosting-model)olarak adlandırılır.
* [Kestrel sunucusu](xref:fundamentals/servers/kestrel)nu çalıştıran bir arka uç ASP.NET Core uygulamasına web isteklerini [iletin, işlem dışı barındırma modeli](#out-of-process-hosting-model)olarak adlandırılır.

Desteklenen Windows sürümleri:

* Windows 7 veya sonraki
* Windows Server 2008 R2 veya sonraki sürümü

Süreç içinde barındırma yaparken, modül IIS için IIS HTTP Server (`IISHttpServer`) adı verilen bir işlem içi sunucu uygulaması kullanır.

Proses dışı ev sahipliği yaparken, modül sadece Kerkenez ile çalışır. Modül [HTTP.sys](xref:fundamentals/servers/httpsys)ile çalışmıyor.

## <a name="hosting-models"></a>Barındırma modelleri

### <a name="in-process-hosting-model"></a>Süreç içi barındırma modeli

İşlem içi barındırma için bir uygulamayı yapılandırmak için, özelliği uygulamanın `<AspNetCoreHostingModel>` proje `InProcess` dosyasına bir değerle ekleyin `OutOfProcess`(işlem dışı barındırma ile ayarlanır):

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>InProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

İşlem içi barındırma modeli,.NET Framework'ASP.NET hedefleyen ASP.NET Core uygulamaları için desteklenmez.

Değeri büyük/küçük harf `<AspNetCoreHostingModel>` duyarsız, bu yüzden `inprocess` ve `outofprocess` geçerli değerlerdir.

`<AspNetCoreHostingModel>` Özellik dosyada yoksa, varsayılan `OutOfProcess`değer.

Süreç içinde barındırma yaparken aşağıdaki özellikler geçerlidir:

* IIS HTTP`IISHttpServer`Server ( ) [Kerkenez](xref:fundamentals/servers/kestrel) sunucusu yerine kullanılır. Süreç içinde [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) şu <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> çağrıları ile çağırır:

  * Kaydedin. `IISHttpServer`
  * ASP.NET Çekirdek Modülü'nün arkasında çalışırken sunucunun dinlemesi gereken bağlantı noktası ve temel yolunu yapılandırın.
  * Başlangıç hatalarını yakalamak için ana bilgisayarı yapılandırın.

* [RequestTimeout özniteliği](#attributes-of-the-aspnetcore-element) işlem içi barındırma için geçerli değildir.

* Uygulamalar arasında uygulama havuzu paylaşmak desteklenmez. Uygulama başına bir uygulama havuzu kullanın.

* Web [Dağıtımı'nı](/iis/publish/using-web-deploy/introduction-to-web-deploy) kullanırken veya [dağıtıma bir app_offline.htm dosyasını](xref:host-and-deploy/iis/index#locked-deployment-files)el ile yerleştirirken, açık bir bağlantı varsa uygulama hemen kapanmayabilir. Örneğin, bir websocket bağlantısı uygulamanın kapatılmasını geciktirebilir.

* Uygulamanın mimarisi (bitness) ve yüklü çalışma süresi (x64 veya x86) uygulama havuzunun mimarisi yle eşleşmelidir.

* İstemci bağlantıları algılanır. [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) iptal belirteci, istemci bağlantısı kesildiğinde iptal edilir.

* Core 2.2.1 veya daha <xref:System.IO.Directory.GetCurrentDirectory*> önceki ASP.NET, uygulamanın dizini yerine IIS tarafından başlatılan işlemin alt dizini döndürür (örneğin, *w3wp.exe*için *C:\Windows\System32\inetsrv).*

  Uygulamanın geçerli dizinini ayarlayan örnek kod için [CurrentDirectoryHelpers sınıfına](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs)bakın. `SetCurrentDirectory` Yöntemi ara. Uygulamanın <xref:System.IO.Directory.GetCurrentDirectory*> dizinini sağlamak için sonraki çağrılar.

* Süreç içinde barındırma <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> yaparken, bir kullanıcıyı başlatmak için dahili olarak çağrılmaz. Bu nedenle, her kimlik doğrulaması varsayılan olarak etkinleştirilmedikten sonra talepleri dönüştürmek için kullanılan bir <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> uygulama. Bir <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> uygulamayla talepleri dönüştürürken, kimlik doğrulama hizmetleri eklemeyi çağırın: <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*>

  ```csharp
  public void ConfigureServices(IServiceCollection services)
  {
      services.AddTransient<IClaimsTransformation, ClaimsTransformer>();
      services.AddAuthentication(IISServerDefaults.AuthenticationScheme);
  }

  public void Configure(IApplicationBuilder app)
  {
      app.UseAuthentication();
  }
  ```

### <a name="out-of-process-hosting-model"></a>İşlem dışı barındırma modeli

İşlem dışı barındırma için bir uygulamayı yapılandırmak için proje dosyasındaki aşağıdaki yaklaşımlardan birini kullanın:

* Özelliği belirtmeyin. `<AspNetCoreHostingModel>` `<AspNetCoreHostingModel>` Özellik dosyada yoksa, varsayılan `OutOfProcess`değer.
* Özelliğin `<AspNetCoreHostingModel>` değerini `OutOfProcess` (işlem içi barındırma ile `InProcess`ayarlanır) ayarlayın:

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

Değer büyük/küçük harf `inprocess` duyarsız, bu nedenle ve `outofprocess` geçerli değerlerdir.

[Kerkenez](xref:fundamentals/servers/kestrel) sunucusu Yerine IIS HTTP`IISHttpServer`Server ( ) kullanılır.

İşlem dışı için [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) şunları <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> çağırır:

* ASP.NET Çekirdek Modülü'nün arkasında çalışırken sunucunun dinlemesi gereken bağlantı noktası ve temel yolunu yapılandırın.
* Başlangıç hatalarını yakalamak için ana bilgisayarı yapılandırın.

### <a name="hosting-model-changes"></a>Barındırma modeli değişiklikleri

Ayarı `hostingModel` *web.config* dosyasında değiştirilirse [(web.config](#configuration-with-webconfig) bölümü ile Yapılandırma'da açıklanırsa), modül IIS için alt işlemi geri dönüştürür.

IIS Express için modül, alt işlemi geri dönüştürmez, bunun yerine geçerli IIS Express işleminin zarif bir şekilde kapatılmasını tetikler. Uygulamanın bir sonraki isteği yeni bir IIS Express işlemi doğurur.

### <a name="process-name"></a>İşlem adı

`Process.GetCurrentProcess().ProcessName``w3wp` / raporlar `iisexpress` (süreç içi) `dotnet` veya (işlem dışı).

Windows Kimlik Doğrulama gibi birçok yerel modül etkin kalır. ASP.NET Çekirdek Modülü ile etkin olan IIS modülleri hakkında daha fazla bilgi için bkz. <xref:host-and-deploy/iis/modules>

ASP.NET Çekirdek Modülü de şunları yapabilir:

* Alt işlem için ortam değişkenlerini ayarlayın.
* Sorun giderme başlangıç sorunları için dosya depolamasına stdout çıktısını günlüğe kaydedin.
* İleri Windows kimlik doğrulama belirteçleri.

## <a name="how-to-install-and-use-the-aspnet-core-module"></a>ASP.NET Çekirdek Modülü nasıl yüklenir ve kullanılır?

ASP.NET Çekirdek Modülü nasıl yüklenir yönergeleri [için](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)bkz.

## <a name="configuration-with-webconfig"></a>web.config ile yapılandırma

ASP.NET Çekirdek Modülü, sitenin `aspNetCore` `system.webServer` *web.config* dosyasındaki düğüm bölümüyle yapılandırılır.

Aşağıdaki *web.config* [dosyası, çerçeveye bağımlı](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) bir dağıtım için yayımlanır ve site isteklerini işlemek için ASP.NET Çekirdek Modül'u yapılandırır:

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath="dotnet"
                  arguments=".\MyApp.dll"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

Aşağıdaki *web.config* [kendi kendine yeten bir dağıtım](/dotnet/articles/core/deploying/#self-contained-deployments-scd)için yayınlanır:

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath=".\MyApp.exe"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

Özellik, <xref:System.Configuration.SectionInformation.InheritInChildApplications*> konumu `false` [ \<>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) öğesi içinde belirtilen ayarların uygulamanın bir alt dizininde bulunan uygulamalar tarafından devralınılolmadığını belirtmek üzere ayarlanmıştır.

Bir uygulama [Azure Uygulama Hizmeti'ne](https://azure.microsoft.com/services/app-service/) `stdoutLogFile` dağıtıldığında, `\\?\%home%\LogFiles\stdout`yol . Yol, hizmet tarafından otomatik olarak oluşturulan bir konum olan *LogFiles* klasörüne stdout günlüklerini kaydeder.

IIS alt uygulama yapılandırması hakkında <xref:host-and-deploy/iis/index#sub-applications>bilgi için bkz.

### <a name="attributes-of-the-aspnetcore-element"></a>aspNetCore öğesinin öznitelikleri

| Öznitelik | Açıklama | Varsayılan |
| --------- | ----------- | :-----: |
| `arguments` | <p>İsteğe bağlı dize özniteliği.</p><p>**ProcessPath'de**belirtilen yürütülebilir bağımsız değişkenler.</p> | |
| `disableStartUpErrorPage` | <p>İsteğe bağlı Boolean özniteliği.</p><p>Doğruysa, **502.5 - İşlem Hatası** sayfası bastırılır ve *web.config'de* yapılandırılan 502 durum kodu sayfası önceliklidir.</p> | `false` |
| `forwardWindowsAuthToken` | <p>İsteğe bağlı Boolean özniteliği.</p><p>Doğruysa, belirteç istek başına 'MS-ASPNETCORE-WINAUTHTOKEN' üstbilgi olarak %ASPNETCORE_PORT'da dinleyerek alt işleme iletilir. İstek başına bu belirteç için CloseHandle'ı aramak bu işlemin sorumluluğundadır.</p> | `true` |
| `hostingModel` | <p>İsteğe bağlı dize özniteliği.</p><p>Barındırma modelini süreç içi (`InProcess`/`inprocess`) veya süreç dışı`OutOfProcess`/`outofprocess`olarak belirtir ( ).</p> | `OutOfProcess`<br>`outofprocess` |
| `processesPerApplication` | <p>İsteğe bağlı birer sayışma özniteliği.</p><p>Uygulama başına döndürülebilen **processPath** ayarında belirtilen işlemin örnek sayısını belirtir.</p><p>&dagger;Süreç içi barındırma için, değer `1`.</p><p>Ayar `processesPerApplication` önerilmez. Bu öznitelik gelecekteki bir sürümde kaldırılır.</p> | Varsayılan:`1`<br>Dk:`1`<br>Max:`100`&dagger; |
| `processPath` | <p>Gerekli dize özniteliği.</p><p>HTTP isteklerini dinleyerek bir işlem başlatan yürütülebilir yol. Göreli yollar desteklenir. Yol ile `.`başlarsa, yol site köküne göre olarak kabul edilir.</p> | |
| `rapidFailsPerMinute` | <p>İsteğe bağlı birer sayışma özniteliği.</p><p>**ProcessPath'de** belirtilen işlemin dakikada çökmesine izin verilme sayısını belirtir. Bu sınır aşılırsa, modül dakikanın geri kalanı için işlemi başlatmayı durdurur.</p><p>Süreç içi barındırma ile desteklenmez.</p> | Varsayılan:`10`<br>Dk:`0`<br>Max:`100` |
| `requestTimeout` | <p>İsteğe bağlı zaman alanı özniteliği.</p><p>ASP.NET Çekirdek Modülü'nün %ASPNETCORE_PORT'de dinleme işleminden yanıt beklediği süreyi belirtir.</p><p>ASP.NET Core 2.1 veya daha sonra sürümü ile gönderilen ASP.NET `requestTimeout` Çekirdek Modülü sürümlerinde, saat, dakika ve saniye cinsinden belirtilir.</p><p>Süreç içi barındırma için geçerli değildir. Süreç içi barındırma için modül, uygulamanın isteği işlemesini bekler.</p><p>Dize dakika ve saniye segmentleri için geçerli değerler 0-59 aralığındadır. 60 **60** değerinin dakika veya saniye olarak kullanılması *500 ile*sonuçlanır - Internal Server Hatası.</p> | Varsayılan:`00:02:00`<br>Dk:`00:00:00`<br>Max:`360:00:00` |
| `shutdownTimeLimit` | <p>İsteğe bağlı birer sayışma özniteliği.</p><p>*app_offline.htm* dosyası algılandığında, modülün yürütülebilir inin düzgün bir şekilde kapanmasını beklediği saniye cinsinden süre.</p> | Varsayılan:`10`<br>Dk:`0`<br>Max:`600` |
| `startupTimeLimit` | <p>İsteğe bağlı birer sayışma özniteliği.</p><p>Modülün çalıştırılabilenin bağlantı noktasını dinleyerek bir işlem başlatmasını beklediği saniye cinsinden süre. Bu zaman sınırı aşılırsa, modül işlemi öldürür. Modül, yeni bir istek aldığında işlemi yeniden başlatmaya çalışır ve uygulama son dakika içinde hızlı bir **şekilde FailsPerMinute** sayısını başlatamazsa sonraki gelen isteklerde işlemi yeniden başlatmaya çalışır.</p><p>0 (sıfır) değeri sonsuz zaman kaybı olarak kabul **edilmez.**</p> | Varsayılan:`120`<br>Dk:`0`<br>Max:`3600` |
| `stdoutLogEnabled` | <p>İsteğe bağlı Boolean özniteliği.</p><p>Doğruysa, **processPath** belirtilen işlem için **stdout** ve **stderr** **stdoutLogFile**belirtilen dosyaya yönlendirilir.</p> | `false` |
| `stdoutLogFile` | <p>İsteğe bağlı dize özniteliği.</p><p>**ProcessPath'de** belirtilen işlemden **stdout** ve **stderr** günlüğe kaydedilmiş olan göreli veya mutlak dosya yolunu belirtir. Göreli yollar sitenin köküne göredir. Herhangi bir `.` yol ile başlayan site köküne göre ve diğer tüm yollar mutlak yollar olarak kabul edilir. Yol dosyasında sağlanan tüm klasörler, günlük dosyası oluşturulduğunda modül tarafından oluşturulur. Alt çizgi delimiters kullanarak, bir zaman damgası, işlem kimliği ve dosya uzantısı (*.log*) **stdoutLogFile** yolunun son bölümüne eklenir. Değer `.\logs\stdout` olarak veseği, 2/5/2018 tarihinde saat 19:41:32'de 1934 işlem kimliğiyle kaydedilirken, örneğin stdout günlüğü *stdout_20180205194132_1934.log* olarak kaydedilir. *logs*</p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a>Ortam değişkenlerini ayarlama

Öznitelikteki `processPath` işlem için ortam değişkenleri belirtilebilir. Bir koleksiyon öğesinin `<environmentVariable>` alt öğesi ile bir ortam değişkeni belirtin. `<environmentVariables>` Bu bölümde ayarlanan ortam değişkenleri sistem ortamı değişkenlerinden önceliklidir.

Aşağıdaki örnekte iki ortam değişkeni kümesi yer alıyor. `ASPNETCORE_ENVIRONMENT`uygulamanın ortamını `Development`. Geliştirici, geliştirici [özel durum sayfasını](xref:fundamentals/error-handling) hata ayıklarken Geliştirici Özel Durum Sayfasını yüklemeye zorlamak için bu değeri *web.config* dosyasında geçici olarak ayarlayabilir. `CONFIG_DIR`geliştirici, uygulamanın yapılandırma dosyasını yüklemek için bir yol oluşturmak için başlangıç değerini okuyan bir kod yazdığı kullanıcı tanımlı ortam değişkenine bir örnektir.

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout"
      hostingModel="inprocess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

> [!NOTE]
> Ortamı doğrudan *web.config'de* ayarlamanın alternatifi, `<EnvironmentName>` özelliği [yayımlama profiline (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) veya proje dosyasına eklemektir. Bu yaklaşım, proje yayımlandığında *web.config'deki* ortamı ayarlar:
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> Ortam değişkenini `ASPNETCORE_ENVIRONMENT` `Development` yalnızca Internet gibi güvenilmeyen ağlar tarafından erişilemeyen hazırlama ve sınama sunucularına ayarlayın.

## <a name="app_offlinehtm"></a>app_offline.htm

Bir uygulamanın kök dizininde *app_offline.htm* adında bir dosya algılanırsa, ASP.NET Çekirdek Modülü uygulamayı zarif bir şekilde kapatmaya ve gelen istekleri işlemeyi durdurmaya çalışır. Uygulama tanımlanan saniye sayısından sonra çalışmaya `shutdownTimeLimit`devam ediyorsa, ASP.NET Çekirdek Modülü çalışma işlemini öldürür.

*app_offline.htm* dosyası mevcut ken, ASP.NET Çekirdek Modülü *app_offline.htm* dosyasının içeriğini geri göndererek isteklere yanıt verir. *app_offline.htm* dosyası kaldırıldığında, bir sonraki istek uygulamayı başlatır.

İşlem dışı barındırma modelini kullanırken, açık bir bağlantı varsa uygulama hemen kapanmayabilir. Örneğin, bir websocket bağlantısı uygulamanın kapatılmasını geciktirebilir.

## <a name="start-up-error-page"></a>Başlatma hatası sayfası

Hem süreç içi hem de süreç dışı barındırma, uygulamayı başlatamadıkları zaman özel hata sayfaları üretir.

ASP.NET Çekirdek Modülü işlem içi veya işlem dışı istek işleyicisini bulamazsa, *500.0 - İşlem İçi/İşlem Dışı İşleyici Yük Hatası* kod sayfası görüntülenir.

ASP.NET Çekirdek Modülü uygulamayı başlatamazsa, işlem içi barındırma için *500.30 - Start Failure* durum kodu sayfası görüntülenir.

ASP.NET Çekirdek Modülü arka uç işlemini başlatamazsa veya arka uç işlemi başlarsa ancak yapılandırılan bağlantı noktasını dinlemezse, *502.5 - İşlem Hatası* durum kodu sayfası görüntülenir.

Bu sayfayı bastırmak ve varsayılan IIS 5xx durum kodu `disableStartUpErrorPage` sayfasına geri dönmek için özniteliği kullanın. Özel hata iletilerinin yapılandırılması hakkında daha fazla bilgi için [HTTP Hataları \<httpErrors>. ](/iis/configuration/system.webServer/httpErrors/)

## <a name="log-creation-and-redirection"></a>Günlük oluşturma ve yeniden yönlendirme

ASP.NET Çekirdek Modülü, `stdoutLogEnabled` `stdoutLogFile` `aspNetCore` öğenin öznitelikleri ve öznitelikleri ayarlanmışsa, stdout ve stderr konsol çıktısını diske yönlendirir. `stdoutLogFile` Yoldaki tüm klasörler, günlük dosyası oluşturulduğunda modül tarafından oluşturulur. Uygulama havuzu, günlüklerin yazıldığı konuma yazma erişimine sahip `IIS AppPool\<app_pool_name>` olmalıdır (yazma izni sağlamak için kullanın).

İşlem geri dönüşümü/yeniden başlatma gerçekleşmedikçe günlükler döndürülemez. Günlüklerin tükettiği disk alanını sınırlamak ana bilgisayarın sorumluluğundadır.

Stdout günlüğünün kullanılması yalnızca IIS'de barındırma yaparken veya [Visual Studio ile IIS için geliştirme zamanı desteğini](xref:host-and-deploy/iis/development-time-iis-support)kullanırken sorun giderme uygulaması başlatma sorunları için önerilir, yerel olarak hata ayıklarken ve uygulamayı IIS Express ile çalıştırırken değil.

Stdout günlüğünü genel uygulama günlüğü amacıyla kullanmayın. ASP.NET Core uygulamasında rutin günlüğe kaydetme için, günlük dosya boyutunu sınırlayan ve günlükleri döndüren bir günlük kitaplığı kullanın. Daha fazla bilgi için [üçüncü taraf günlük sağlayıcılarına](xref:fundamentals/logging/index#third-party-logging-providers)bakın.

Günlük dosyası oluşturulduğunda otomatik olarak bir zaman damgası ve dosya uzantısı eklenir. Günlük dosya adı, `stdoutLogFile` zaman damgası, işlem kimliği ve dosya uzantısı *(.log*) yolun son kesimine (genellikle *stdout)* alt çizgiyle sınırlandırılmış olarak eklenerek oluşturulur. `stdoutLogFile` Yol *stdout*ile biterse , 2/5/2018 tarihinde 19:42:32 tarihinde oluşturulan 1934 PID ile bir uygulama için bir günlük *stdout_20180205194132_1934.log*dosya adı vardır.

Yanlışsa, `stdoutLogEnabled` uygulama başlatmada oluşan hatalar yakalanır ve 30 KB'a kadar olay günlüğüne yayılır. Başlatmadan sonra, tüm ek günlükler atılır.

Aşağıdaki örnek `aspNetCore` öğe, nispi yolda `.\log\`stdout günlüğe yapılandırır. AppPool kullanıcı kimliğinin sağlanan yola yazma izni olduğunu doğrulayın.

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

Azure Uygulama Hizmeti dağıtımı için bir uygulama yayımlarken, Web SDK `stdoutLogFile` değerini `\\?\%home%\LogFiles\stdout`. Ortam `%home` değişkeni, Azure Uygulama Hizmeti tarafından barındırılan uygulamalar için önceden tanımlanmıştır.

Yol biçimleri hakkında daha fazla bilgi için [Windows sistemlerinde Dosya yolu biçimlerine](/dotnet/standard/io/file-path-formats)bakın.

## <a name="enhanced-diagnostic-logs"></a>Geliştirilmiş tanılama günlükleri

ASP.NET Çekirdek Modülü gelişmiş tanılama günlükleri sağlamak için yapılandırılabilir. Öğeyi `<handlerSettings>` `<aspNetCore>` *web.config'deki*öğeye ekleyin. Tanılama `debugLevel` `TRACE` bilgilerinin daha yüksek bir sadakatini ortaya çıkaracak şekilde ayarlanın:

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="debugFile" value=".\logs\aspnetcore-debug.log" />
    <handlerSetting name="debugLevel" value="FILE,TRACE" />
  </handlerSettings>
</aspNetCore>
```

`<handlerSetting>` Değere sağlanan yoldaki klasörler (önceki örnekte*günlükler)* modül tarafından otomatik olarak oluşturulmaz ve dağıtımda önceden var olmalıdır. Uygulama havuzu, günlüklerin yazıldığı konuma yazma erişimine sahip `IIS AppPool\<app_pool_name>` olmalıdır (yazma izni sağlamak için kullanın).

Hata ayıklama`debugLevel`düzeyi ( ) değerleri hem düzeyi hem de konumu içerebilir.

Düzeyler (en azdan en ayrıntılısıraya kadar):

* HATA
* UYARI
* Bilgi
* TRACE

Konumlar (birden çok konuma izin verilir):

* Konsol
* Eventlog
* DOSYA

İşleyici ayarları ortam değişkenleri aracılığıyla da sağlanabilir:

* `ASPNETCORE_MODULE_DEBUG_FILE`&ndash; Hata ayıklama günlüğü dosyasına giden yol. (Varsayılan: *aspnetcore-debug.log*)
* `ASPNETCORE_MODULE_DEBUG`&ndash; Hata ayıklama düzeyi ayarı.

> [!WARNING]
> Hata **not** ayıklama günlüğekaydetmeyi dağıtımda bir sorunu gidermek için gerekenden daha uzun süre etkin bırakmayın. Günlüğün boyutu sınırlı değildir. Hata ayıklama günlüğünü etkin bırakmak, kullanılabilir disk alanını tüketebilir ve sunucuyu veya uygulama hizmetini kilitleyebilir.

*Web.config* dosyasındaki `aspNetCore` öğenin bir örneği için [web.config ile Yapılandırma'ya](#configuration-with-webconfig) bakın.

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a>Proxy yapılandırması HTTP protokolü ve eşleştirme belirteci kullanır

*Yalnızca işlem dışı barındırma için geçerlidir.*

ASP.NET Çekirdek Modülü ile Kestrel arasında oluşturulan proxy HTTP protokolünü kullanır. Modül ve Kestrel arasındaki trafiği sunucunun dışında bir konumdan gizlice dinleme riski yoktur.

Eşleştirme belirteci, Kestrel tarafından alınan isteklerin IIS tarafından yerine getirilmiş olduğunu ve başka bir kaynaktan gelmediğini garanti etmek için kullanılır. Eşleştirme belirteci modül tarafından oluşturulur ve`ASPNETCORE_TOKEN`bir ortam değişkeni () olarak ayarlanır. Eşleştirme belirteci, her yakın istekte`MS-ASPNETCORE-TOKEN`bir üstbilgi () olarak da ayarlanır. IIS Middleware, eşleştirme belirteç üstbilgi değerinin ortam değişken değeriyle eşleştiğini doğrulamak için aldığı her isteği denetler. Belirteç değerleri eşleşmezse, istek günlüğe kaydedilir ve reddedilir. Eşleştirme belirteç ortamı değişkeni ve modül ile Kestrel arasındaki trafiğe sunucunun dışında bir konumdan erişilemez. Eşleştirme belirteç değerini bilmeden, saldırgan IIS Middleware'de denetimi atlayan istekler gönderemez.

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a>IIS Paylaşılan Yapılandırmalı ASP.NET Çekirdek Modülü

ASP.NET Çekirdek Modülü yükleyici, **TrustedInstaller** hesabının ayrıcalıklarıyla çalışır. Yerel sistem hesabı, IIS Paylaşılan Yapılandırması tarafından kullanılan paylaşım yolu için değişiklik izni olmadığından, yükleci, paylaşımdaki *applicationHost.config* dosyasındaki modül ayarlarını yapılandırmaya çalışırken erişim reddedilen bir hata atar.

IIS yüklemesi ile aynı makinede bir IIS Paylaşılan Yapılandırma kullanırken, `OPT_NO_SHARED_CONFIG_CHECK` parametre ayarlanmış `1`ASP.NET Core Hosting Bundle yükleyicisini çalıştırın:

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

Paylaşılan yapılandırmaya giden yol IIS yüklemeyle aynı makinede değilse aşağıdaki adımları izleyin:

1. IIS Paylaşılan Yapılandırmasını devre dışı kınla.
1. Yükleyiciyi çalıştırın.
1. Paylaşıma güncelleştirilmiş *applicationHost.config* dosyasını dışa aktarın.
1. IIS Paylaşılan Yapılandırmasını yeniden etkinleştirin.

## <a name="module-version-and-hosting-bundle-installer-logs"></a>Modül sürümü ve Hosting Paketi yükleyici günlükleri

Yüklü ASP.NET Çekirdek Modülü sürümünü belirlemek için:

1. Barındırma sisteminde *% windir%\System32\inetsrv'e*gidin.
1. *aspnetcore.dll* dosyasını bulun.
1. Dosyaya sağ tıklayın ve bağlamsal menüden **Özellikler'i** seçin.
1. **Ayrıntılar** sekmesini seçin. **Dosya sürümü** ve Ürün **sürümü** modülün yüklü sürümünü temsil eder.

Modül için Hosting Paketi yükleyici günlükleri *C\\\\bulunur: Kullanıcılar\\%UserName%\\AppData Local\\Temp*. Dosya zaman *damgası>_000_AspNetCoreModule_x64.log dd_DotNetCoreWinSvrHosting__\<* adlandırılır.

## <a name="module-schema-and-configuration-file-locations"></a>Modül, şema ve yapılandırma dosya konumları

### <a name="module"></a>Modül

**IIS (x86/amd64):**

* %windir%\System32\inetsrv\aspnetcore.dll

* %windir%\SysWOW64\inetsrv\aspnetcore.dll

* %ProgramFiles%\IIS\Asp.Net Çekirdek Modülü\V2\aspnetcorev2.dll

* %ProgramFiles(x86)%\IIS\Asp.Net Çekirdek Modülü\V2\aspnetcorev2.dll

**IIS Express (x86/amd64):**

* %ProgramFiles%\IIS Express\aspnetcore.dll

* %ProgramFiles(x86)%\IIS Express\aspnetcore.dll

* %ProgramFiles%\IIS Express\Asp.Net Çekirdek Modülü\V2\aspnetcorev2.dll

* %ProgramFiles(x86)%\IIS Express\Asp.Net Çekirdek Modülü\V2\aspnetcorev2.dll

### <a name="schema"></a>Şema

**IIS**

* %windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml

* %windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml

**IIS Ekspresi**

* %ProgramFiles%\IIS Express\config\şema\aspnetcore_schema.xml

* %ProgramFiles%\IIS Express\config\şema\aspnetcore_schema_v2.xml

### <a name="configuration"></a>Yapılandırma

**IIS**

* %windir%\System32\inetsrv\config\applicationHost.config

**IIS Ekspresi**

* Görsel Stüdyo: {APPLICATION\\ROOT} .vs\config\applicationHost.config

* *iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config

Dosyalar *applicationHost.config* dosyasında *aspnetcore* arayarak bulunabilir.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

ASP.NET Çekirdek Modülü, Web isteklerini Core ASP.NET uygulamalarını geri almak için iletmek için IIS boru hattına takılan yerel bir IIS modülüdür.

Desteklenen Windows sürümleri:

* Windows 7 veya sonraki
* Windows Server 2008 R2 veya sonraki sürümü

Modül sadece Kerkenez ile çalışır. Modül [HTTP.sys](xref:fundamentals/servers/httpsys)ile uyumsuzdur.

Core uygulamaları ASP.NET IIS alt işleminden ayrı bir işlemde çalıştığı için, modül aynı zamanda işlem yönetimini de işler. Modül, ilk istek geldiğinde ASP.NET Core uygulaması için süreci başlatır ve çökmediğinde uygulamayı yeniden başlatır. Bu aslında [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was)tarafından yönetilen IIS'de işlem gören ASP.NET 4.x uygulamasında görülen davranışla aynıdır.

Aşağıdaki diyagram, IIS, ASP.NET Çekirdek Modülü ve bir uygulama arasındaki ilişkiyi göstermektedir:

![ASP.NET Core Modülü](aspnet-core-module/_static/ancm-outofprocess.png)

İstekler web'den çekirdek modu HTTP.sys sürücüsüne gelir. Sürücü, istekleri genellikle 80 (HTTP) veya 443 (HTTPS) olarak web sitesinin yapılandırılmış bağlantı noktasında IIS'ye yönlendirir. Modül, 80 veya 443 bağlantı noktası olmayan uygulama için rastgele bir bağlantı noktası üzerindeki istekleri Kestrel'e iletiyor.

Modül başlangıçta bir ortam değişkeni üzerinden bağlantı noktasını belirtir ve [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) sunucuyu dinleyecek şekilde `http://localhost:{port}`yapılandırır. Ek denetimler gerçekleştirilir ve modülden kaynaklanmıyor istekleri reddedilir. Modül HTTPS iletmesini desteklemez, bu nedenle iIS tarafından HTTPS üzerinden alınsa bile istekler HTTP üzerinden iletilir.

Kestrel modülden isteği aldıktan sonra, istek ASP.NET Core ara yazılım boru hattına itilir. Ara yazılım ardışık alanı isteği işler ve `HttpContext` uygulamanın mantığına örnek olarak aktarın. IIS Integration tarafından eklenen ara yazılım, isteği Kestrel'e iletmek için hesap vermek üzere şemayı, uzak IP'yi ve yol tabanını güncelleştirir. Uygulamanın yanıtı IIS'ye geri aktarılır ve bu da isteği başlatan HTTP istemcisine geri iter.

Windows Kimlik Doğrulama gibi birçok yerel modül etkin kalır. ASP.NET Çekirdek Modülü ile etkin olan IIS modülleri hakkında daha fazla bilgi için bkz. <xref:host-and-deploy/iis/modules>

ASP.NET Çekirdek Modülü de şunları yapabilir:

* Alt işlem için ortam değişkenlerini ayarlayın.
* Sorun giderme başlangıç sorunları için dosya depolamasına stdout çıktısını günlüğe kaydedin.
* İleri Windows kimlik doğrulama belirteçleri.

## <a name="how-to-install-and-use-the-aspnet-core-module"></a>ASP.NET Çekirdek Modülü nasıl yüklenir ve kullanılır?

ASP.NET Çekirdek Modülü nasıl yüklenir yönergeleri [için](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)bkz.

## <a name="configuration-with-webconfig"></a>web.config ile yapılandırma

ASP.NET Çekirdek Modülü, sitenin `aspNetCore` `system.webServer` *web.config* dosyasındaki düğüm bölümüyle yapılandırılır.

Aşağıdaki *web.config* [dosyası, çerçeveye bağımlı](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) bir dağıtım için yayımlanır ve site isteklerini işlemek için ASP.NET Çekirdek Modül'u yapılandırır:

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModule" resourceType="Unspecified" />
    </handlers>
    <aspNetCore processPath="dotnet"
                arguments=".\MyApp.dll"
                stdoutLogEnabled="false"
                stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

Aşağıdaki *web.config* [kendi kendine yeten bir dağıtım](/dotnet/articles/core/deploying/#self-contained-deployments-scd)için yayınlanır:

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModule" resourceType="Unspecified" />
    </handlers>
    <aspNetCore processPath=".\MyApp.exe"
                stdoutLogEnabled="false"
                stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

Bir uygulama [Azure Uygulama Hizmeti'ne](https://azure.microsoft.com/services/app-service/) `stdoutLogFile` dağıtıldığında, `\\?\%home%\LogFiles\stdout`yol . Yol, hizmet tarafından otomatik olarak oluşturulan bir konum olan *LogFiles* klasörüne stdout günlüklerini kaydeder.

IIS alt uygulama yapılandırması hakkında <xref:host-and-deploy/iis/index#sub-applications>bilgi için bkz.

### <a name="attributes-of-the-aspnetcore-element"></a>aspNetCore öğesinin öznitelikleri

| Öznitelik | Açıklama | Varsayılan |
| --------- | ----------- | :-----: |
| `arguments` | <p>İsteğe bağlı dize özniteliği.</p><p>**ProcessPath'de**belirtilen yürütülebilir bağımsız değişkenler.</p>| |
| `disableStartUpErrorPage` | <p>İsteğe bağlı Boolean özniteliği.</p><p>Doğruysa, **502.5 - İşlem Hatası** sayfası bastırılır ve *web.config'de* yapılandırılan 502 durum kodu sayfası önceliklidir.</p> | `false` |
| `forwardWindowsAuthToken` | <p>İsteğe bağlı Boolean özniteliği.</p><p>Doğruysa, belirteç istek başına 'MS-ASPNETCORE-WINAUTHTOKEN' üstbilgi olarak %ASPNETCORE_PORT'da dinleyerek alt işleme iletilir. İstek başına bu belirteç için CloseHandle'ı aramak bu işlemin sorumluluğundadır.</p> | `true` |
| `processesPerApplication` | <p>İsteğe bağlı birer sayışma özniteliği.</p><p>Uygulama başına döndürülebilen **processPath** ayarında belirtilen işlemin örnek sayısını belirtir.</p><p>Ayar `processesPerApplication` önerilmez. Bu öznitelik gelecekteki bir sürümde kaldırılır.</p> | Varsayılan:`1`<br>Dk:`1`<br>Max:`100` |
| `processPath` | <p>Gerekli dize özniteliği.</p><p>HTTP isteklerini dinleyerek bir işlem başlatan yürütülebilir yol. Göreli yollar desteklenir. Yol ile `.`başlarsa, yol site köküne göre olarak kabul edilir.</p> | |
| `rapidFailsPerMinute` | <p>İsteğe bağlı birer sayışma özniteliği.</p><p>**ProcessPath'de** belirtilen işlemin dakikada çökmesine izin verilme sayısını belirtir. Bu sınır aşılırsa, modül dakikanın geri kalanı için işlemi başlatmayı durdurur.</p> | Varsayılan:`10`<br>Dk:`0`<br>Max:`100` |
| `requestTimeout` | <p>İsteğe bağlı zaman alanı özniteliği.</p><p>ASP.NET Çekirdek Modülü'nün %ASPNETCORE_PORT'de dinleme işleminden yanıt beklediği süreyi belirtir.</p><p>ASP.NET Core 2.1 veya daha sonra sürümü ile gönderilen ASP.NET `requestTimeout` Çekirdek Modülü sürümlerinde, saat, dakika ve saniye cinsinden belirtilir.</p> | Varsayılan:`00:02:00`<br>Dk:`00:00:00`<br>Max:`360:00:00` |
| `shutdownTimeLimit` | <p>İsteğe bağlı birer sayışma özniteliği.</p><p>*app_offline.htm* dosyası algılandığında, modülün yürütülebilir inin düzgün bir şekilde kapanmasını beklediği saniye cinsinden süre.</p> | Varsayılan:`10`<br>Dk:`0`<br>Max:`600` |
| `startupTimeLimit` | <p>İsteğe bağlı birer sayışma özniteliği.</p><p>Modülün çalıştırılabilenin bağlantı noktasını dinleyerek bir işlem başlatmasını beklediği saniye cinsinden süre. Bu zaman sınırı aşılırsa, modül işlemi öldürür. Modül, yeni bir istek aldığında işlemi yeniden başlatmaya çalışır ve uygulama son dakika içinde hızlı bir **şekilde FailsPerMinute** sayısını başlatamazsa sonraki gelen isteklerde işlemi yeniden başlatmaya çalışır.</p><p>0 (sıfır) değeri sonsuz zaman kaybı olarak kabul **edilmez.**</p> | Varsayılan:`120`<br>Dk:`0`<br>Max:`3600` |
| `stdoutLogEnabled` | <p>İsteğe bağlı Boolean özniteliği.</p><p>Doğruysa, **processPath** belirtilen işlem için **stdout** ve **stderr** **stdoutLogFile**belirtilen dosyaya yönlendirilir.</p> | `false` |
| `stdoutLogFile` | <p>İsteğe bağlı dize özniteliği.</p><p>**ProcessPath'de** belirtilen işlemden **stdout** ve **stderr** günlüğe kaydedilmiş olan göreli veya mutlak dosya yolunu belirtir. Göreli yollar sitenin köküne göredir. Herhangi bir `.` yol ile başlayan site köküne göre ve diğer tüm yollar mutlak yollar olarak kabul edilir. Modülün günlük dosyasını oluşturabilmesi için yolda sağlanan klasörlerin bulunması gerekir. Alt çizgi delimiters kullanarak, bir zaman damgası, işlem kimliği ve dosya uzantısı (*.log*) **stdoutLogFile** yolunun son bölümüne eklenir. Değer `.\logs\stdout` olarak veseği, 2/5/2018 tarihinde saat 19:41:32'de 1934 işlem kimliğiyle kaydedilirken, örneğin stdout günlüğü *stdout_20180205194132_1934.log* olarak kaydedilir. *logs*</p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a>Ortam değişkenlerini ayarlama

Öznitelikteki `processPath` işlem için ortam değişkenleri belirtilebilir. Bir koleksiyon öğesinin `<environmentVariable>` alt öğesi ile bir ortam değişkeni belirtin. `<environmentVariables>`

> [!WARNING]
> Bu bölümde ayarlanan ortam değişkenleri, aynı ada göre ayarlanmış sistem ortamı değişkenleriyle çakışMaktadır. Hem *web.config* dosyasında hem de Windows'daki sistem düzeyinde bir ortam değişkeni ayarlanırsa, *web.config* dosyasındaki değer, `ASPNETCORE_ENVIRONMENT: Development;Development`uygulamanın başlatılmasını engelleyen sistem ortamı değişken değerine (örneğin) eklenir.

Aşağıdaki örnekte iki ortam değişkeni kümesi yer alıyor. `ASPNETCORE_ENVIRONMENT`uygulamanın ortamını `Development`. Geliştirici, geliştirici [özel durum sayfasını](xref:fundamentals/error-handling) hata ayıklarken Geliştirici Özel Durum Sayfasını yüklemeye zorlamak için bu değeri *web.config* dosyasında geçici olarak ayarlayabilir. `CONFIG_DIR`geliştirici, uygulamanın yapılandırma dosyasını yüklemek için bir yol oluşturmak için başlangıç değerini okuyan bir kod yazdığı kullanıcı tanımlı ortam değişkenine bir örnektir.

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile="\\?\%home%\LogFiles\stdout">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

> [!WARNING]
> Ortam değişkenini `ASPNETCORE_ENVIRONMENT` `Development` yalnızca Internet gibi güvenilmeyen ağlar tarafından erişilemeyen hazırlama ve sınama sunucularına ayarlayın.

## <a name="app_offlinehtm"></a>app_offline.htm

Bir uygulamanın kök dizininde *app_offline.htm* adında bir dosya algılanırsa, ASP.NET Çekirdek Modülü uygulamayı zarif bir şekilde kapatmaya ve gelen istekleri işlemeyi durdurmaya çalışır. Uygulama tanımlanan saniye sayısından sonra çalışmaya `shutdownTimeLimit`devam ediyorsa, ASP.NET Çekirdek Modülü çalışma işlemini öldürür.

*app_offline.htm* dosyası mevcut ken, ASP.NET Çekirdek Modülü *app_offline.htm* dosyasının içeriğini geri göndererek isteklere yanıt verir. *app_offline.htm* dosyası kaldırıldığında, bir sonraki istek uygulamayı başlatır.

## <a name="start-up-error-page"></a>Başlatma hatası sayfası

ASP.NET Çekirdek Modülü arka uç işlemini başlatamazsa veya arka uç işlemi başlarsa ancak yapılandırılan bağlantı noktasını dinlemezse, *502.5 - İşlem Hatası* durum kodu sayfası görüntülenir. Bu sayfayı bastırmak ve varsayılan IIS 502 durum kodu `disableStartUpErrorPage` sayfasına geri dönmek için özniteliği kullanın. Özel hata iletilerinin yapılandırılması hakkında daha fazla bilgi için [HTTP Hataları \<httpErrors>. ](/iis/configuration/system.webServer/httpErrors/)

![502.5 İşlem Hatası Durum Kodu Sayfası](aspnet-core-module/_static/ANCM-502_5.png)

## <a name="log-creation-and-redirection"></a>Günlük oluşturma ve yeniden yönlendirme

ASP.NET Çekirdek Modülü, `stdoutLogEnabled` `stdoutLogFile` `aspNetCore` öğenin öznitelikleri ve öznitelikleri ayarlanmışsa, stdout ve stderr konsol çıktısını diske yönlendirir. `stdoutLogFile` Yoldaki tüm klasörler, günlük dosyası oluşturulduğunda modül tarafından oluşturulur. Uygulama havuzu, günlüklerin yazıldığı konuma yazma erişimine sahip `IIS AppPool\<app_pool_name>` olmalıdır (yazma izni sağlamak için kullanın).

İşlem geri dönüşümü/yeniden başlatma gerçekleşmedikçe günlükler döndürülemez. Günlüklerin tükettiği disk alanını sınırlamak ana bilgisayarın sorumluluğundadır.

Stdout günlüğünün kullanılması yalnızca IIS'de barındırma yaparken veya [Visual Studio ile IIS için geliştirme zamanı desteğini](xref:host-and-deploy/iis/development-time-iis-support)kullanırken sorun giderme uygulaması başlatma sorunları için önerilir, yerel olarak hata ayıklarken ve uygulamayı IIS Express ile çalıştırırken değil.

Stdout günlüğünü genel uygulama günlüğü amacıyla kullanmayın. ASP.NET Core uygulamasında rutin günlüğe kaydetme için, günlük dosya boyutunu sınırlayan ve günlükleri döndüren bir günlük kitaplığı kullanın. Daha fazla bilgi için [üçüncü taraf günlük sağlayıcılarına](xref:fundamentals/logging/index#third-party-logging-providers)bakın.

Günlük dosyası oluşturulduğunda otomatik olarak bir zaman damgası ve dosya uzantısı eklenir. Günlük dosya adı, `stdoutLogFile` zaman damgası, işlem kimliği ve dosya uzantısı *(.log*) yolun son kesimine (genellikle *stdout)* alt çizgiyle sınırlandırılmış olarak eklenerek oluşturulur. `stdoutLogFile` Yol *stdout*ile biterse , 2/5/2018 tarihinde 19:42:32 tarihinde oluşturulan 1934 PID ile bir uygulama için bir günlük *stdout_20180205194132_1934.log*dosya adı vardır.

Aşağıdaki örnek `aspNetCore` öğe, nispi yolda `.\log\`stdout günlüğe yapılandırır. AppPool kullanıcı kimliğinin sağlanan yola yazma izni olduğunu doğrulayın.

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout">
</aspNetCore>
```

Azure Uygulama Hizmeti dağıtımı için bir uygulama yayımlarken, Web SDK `stdoutLogFile` değerini `\\?\%home%\LogFiles\stdout`. Ortam `%home` değişkeni, Azure Uygulama Hizmeti tarafından barındırılan uygulamalar için önceden tanımlanmıştır.

Günlük filtresi kuralları oluşturmak için, ASP.NET Core günlük belgelerinin [Yapılandırma](xref:fundamentals/logging/index#log-filtering) ve [Günlük filtreleme](xref:fundamentals/logging/index#log-filtering) bölümlerine bakın.

Yol biçimleri hakkında daha fazla bilgi için [Windows sistemlerinde Dosya yolu biçimlerine](/dotnet/standard/io/file-path-formats)bakın.

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a>Proxy yapılandırması HTTP protokolü ve eşleştirme belirteci kullanır

ASP.NET Çekirdek Modülü ile Kestrel arasında oluşturulan proxy HTTP protokolünü kullanır. Modül ve Kestrel arasındaki trafiği sunucunun dışında bir konumdan gizlice dinleme riski yoktur.

Eşleştirme belirteci, Kestrel tarafından alınan isteklerin IIS tarafından yerine getirilmiş olduğunu ve başka bir kaynaktan gelmediğini garanti etmek için kullanılır. Eşleştirme belirteci modül tarafından oluşturulur ve`ASPNETCORE_TOKEN`bir ortam değişkeni () olarak ayarlanır. Eşleştirme belirteci, her yakın istekte`MS-ASPNETCORE-TOKEN`bir üstbilgi () olarak da ayarlanır. IIS Middleware, eşleştirme belirteç üstbilgi değerinin ortam değişken değeriyle eşleştiğini doğrulamak için aldığı her isteği denetler. Belirteç değerleri eşleşmezse, istek günlüğe kaydedilir ve reddedilir. Eşleştirme belirteç ortamı değişkeni ve modül ile Kestrel arasındaki trafiğe sunucunun dışında bir konumdan erişilemez. Eşleştirme belirteç değerini bilmeden, saldırgan IIS Middleware'de denetimi atlayan istekler gönderemez.

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a>IIS Paylaşılan Yapılandırmalı ASP.NET Çekirdek Modülü

ASP.NET Çekirdek Modülü yükleyici, **TrustedInstaller** hesabının ayrıcalıklarıyla çalışır. Yerel sistem hesabı, IIS Paylaşılan Yapılandırması tarafından kullanılan paylaşım yolu için değişiklik izni olmadığından, yükleci, paylaşımdaki *applicationHost.config* dosyasındaki modül ayarlarını yapılandırmaya çalışırken erişim reddedilen bir hata atar.

IIS Paylaşılan Yapılandırması kullanırken aşağıdaki adımları izleyin:

1. IIS Paylaşılan Yapılandırmasını devre dışı kınla.
1. Yükleyiciyi çalıştırın.
1. Paylaşıma güncelleştirilmiş *applicationHost.config* dosyasını dışa aktarın.
1. IIS Paylaşılan Yapılandırmasını yeniden etkinleştirin.

## <a name="module-version-and-hosting-bundle-installer-logs"></a>Modül sürümü ve Hosting Paketi yükleyici günlükleri

Yüklü ASP.NET Çekirdek Modülü sürümünü belirlemek için:

1. Barındırma sisteminde *% windir%\System32\inetsrv'e*gidin.
1. *aspnetcore.dll* dosyasını bulun.
1. Dosyaya sağ tıklayın ve bağlamsal menüden **Özellikler'i** seçin.
1. **Ayrıntılar** sekmesini seçin. **Dosya sürümü** ve Ürün **sürümü** modülün yüklü sürümünü temsil eder.

Modül için Hosting Paketi yükleyici günlükleri *C\\\\bulunur: Kullanıcılar\\%UserName%\\AppData Local\\Temp*. Dosya zaman *damgası>_000_AspNetCoreModule_x64.log dd_DotNetCoreWinSvrHosting__\<* adlandırılır.

## <a name="module-schema-and-configuration-file-locations"></a>Modül, şema ve yapılandırma dosya konumları

### <a name="module"></a>Modül

**IIS (x86/amd64):**

* %windir%\System32\inetsrv\aspnetcore.dll

* %windir%\SysWOW64\inetsrv\aspnetcore.dll

**IIS Express (x86/amd64):**

* %ProgramFiles%\IIS Express\aspnetcore.dll

* %ProgramFiles(x86)%\IIS Express\aspnetcore.dll

### <a name="schema"></a>Şema

**IIS**

* %windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml

**IIS Ekspresi**

* %ProgramFiles%\IIS Express\config\şema\aspnetcore_schema.xml

### <a name="configuration"></a>Yapılandırma

**IIS**

* %windir%\System32\inetsrv\config\applicationHost.config

**IIS Ekspresi**

* Görsel Stüdyo: {APPLICATION\\ROOT} .vs\config\applicationHost.config

* *iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config

Dosyalar *applicationHost.config* dosyasında *aspnetcore* arayarak bulunabilir.

::: moniker-end

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/azure-apps/index>
* [ASP.NET Çekirdek Modül başvuru kaynağı (ana dal)](https://github.com/dotnet/aspnetcore/tree/master/src/Servers/IIS/AspNetCoreModuleV2) &ndash; Belirli bir sürümü seçmek `release/3.1`için **Şube** açılır listesini kullanın (örneğin, ).
* <xref:host-and-deploy/iis/modules>
