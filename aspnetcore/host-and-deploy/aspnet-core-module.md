---
title: ASP.NET Core Modülü
author: rick-anderson
description: ASP.NET Core uygulamalarını barındırmak için ASP.NET Core modülünü yapılandırmayı öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/aspnet-core-module
ms.openlocfilehash: e16735ec06e6d84118e5949fe56c514015105242
ms.sourcegitcommit: 6eacadf1be61679ab8e6f781ece76b7395512879
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/04/2020
ms.locfileid: "87758586"
---
# <a name="aspnet-core-module"></a>ASP.NET Core Modülü

, [Tom Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Chris](https://github.com/Tratcher)No, [Rick Anderson](https://twitter.com/RickAndMSFT), [sourabh Shirhatti](https://twitter.com/sshirhatti)ve ka [kotalık](https://github.com/jkotalik)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core modülü, IIS ardışık düzenine şu şekilde takılan yerel bir IIS modülüdür:

* `w3wp.exe` [İşlem içi barındırma modeli](#in-process-hosting-model)olarak adlandırılan IIS çalışan işleminin () içinde bir ASP.NET Core uygulaması barındırın.
* Web isteklerini, [işlem dışı barındırma modeli](#out-of-process-hosting-model)olarak adlandırılan [Kestrel sunucusunu](xref:fundamentals/servers/kestrel)çalıştıran bir arka uç ASP.NET Core uygulamasına iletin.

Desteklenen Windows sürümleri:

* Windows 7 veya üzeri
* Windows Server 2012 R2 veya üzeri

İşlem içinde barındırırken, modül IIS HTTP sunucusu () olarak adlandırılan IIS için işlem içi sunucu uygulamasını kullanır `IISHttpServer` .

İşlem dışı barındırma sırasında modül yalnızca Kestrel ile birlikte kullanılabilir. Modül [HTTP.sys](xref:fundamentals/servers/httpsys)ile çalışmıyor.

## <a name="hosting-models"></a>Barındırma modelleri

### <a name="in-process-hosting-model"></a>İşlem içi barındırma modeli

Uygulamalar, işlem içi barındırma modelinde varsayılan olarak ASP.NET Core.

İşlem içi barındırma sırasında aşağıdaki özellikler geçerlidir:

* Kestrel Server yerine IIS HTTP sunucusu ( `IISHttpServer` ) kullanılır [Kestrel](xref:fundamentals/servers/kestrel) . İşlem içi için [Createdefaultbuilder](xref:fundamentals/host/generic-host#default-builder-settings) aşağıdakileri öğesine çağırır <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> :

  * Kaydolun `IISHttpServer` .
  * ASP.NET Core modülünün arkasında çalışırken sunucunun dinlemesi gereken bağlantı noktasını ve temel yolu yapılandırın.
  * Konağı, başlatma hatalarını yakalamak üzere yapılandırın.

* [RequestTimeout özniteliği](#attributes-of-the-aspnetcore-element) işlem içi barındırma için uygulanmaz.

* Uygulama havuzunu uygulamalar arasında paylaşma desteklenmez. Uygulama başına bir uygulama havuzu kullanın.

* [Web dağıtımı](/iis/publish/using-web-deploy/introduction-to-web-deploy) kullanırken veya dağıtıma el ile bir [app_offline.htm dosyası](xref:host-and-deploy/iis/index#locked-deployment-files)yerleştirilirken, açık bir bağlantı varsa uygulama hemen kapanmayabilir. Örneğin, bir WebSocket bağlantısı, uygulamanın kapatılmasını erteleyebilir.

* Uygulamanın mimarisi (bit genişliği) ve yüklü çalışma zamanının (x64 veya x86) uygulama havuzunun mimarisiyle eşleşmesi gerekir.

* İstemci bağlantısı kesiliyor algılandı. İstemci bağlantısı kesildiğinde [HttpContext. RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) iptal belirteci iptal edilir.

* ASP.NET Core 2.2.1 veya önceki sürümlerde, <xref:System.IO.Directory.GetCurrentDirectory*> uygulamanın dizini yerıne IIS tarafından başlatılan işlemin çalışan dizinini döndürür (örneğin, *w3wp.exe*için *c:\Windows\System32\inetsrv* ).

  Uygulamanın geçerli dizinini ayarlayan örnek kod için bkz. [Currentdirectoryyardımcıları sınıfı](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/3.x/CurrentDirectoryHelpers.cs). Yöntemini çağırın `SetCurrentDirectory` . <xref:System.IO.Directory.GetCurrentDirectory*>Uygulamanın dizinini sağlamak için sonraki çağrılar.

* İşlem içi barındırma sırasında, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> bir kullanıcıyı başlatmak için dahili olarak çağrılmaz. Bu nedenle, <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> her kimlik doğrulaması sonrasında talepleri dönüştürmek için kullanılan bir uygulama varsayılan olarak etkinleştirilmez. Talepleri bir <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> uygulamayla dönüştürürken, <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> kimlik doğrulama hizmetleri Ekle ' yi çağırın:

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
  
  * [Web paketi (tek dosya) dağıtımları](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages) desteklenmez.

### <a name="out-of-process-hosting-model"></a>İşlem dışı barındırma modeli

Bir uygulamayı işlem dışı barındırmak üzere yapılandırmak için, `<AspNetCoreHostingModel>` özelliğinin değerini `OutOfProcess` Proje dosyasında (*. csproj*) olarak ayarlayın:

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

İşlem içi barındırma `InProcess` , varsayılan değer olan ile ayarlanır.

Değeri `<AspNetCoreHostingModel>` büyük/küçük harfe duyarlıdır `inprocess` ve `outofprocess` geçerli değerlerdir.

IIS HTTP Server () yerine [Kestrel](xref:fundamentals/servers/kestrel) Server kullanılır `IISHttpServer` .

İşlem dışı için [Createdefaultbuilder](xref:fundamentals/host/generic-host#default-builder-settings) aşağıdakileri <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> öğesine çağırır:

* ASP.NET Core modülünün arkasında çalışırken sunucunun dinlemesi gereken bağlantı noktasını ve temel yolu yapılandırın.
* Konağı, başlatma hatalarını yakalamak üzere yapılandırın.

### <a name="hosting-model-changes"></a>Barındırma modeli değişiklikleri

Ayar, `hostingModel` *web.config* dosyasında değiştirilirse ( [web.configile yapılandırma](#configuration-with-webconfig) bölümünde açıklanan), modül IIS için çalışan işlemini geri dönüştürür.

IIS Express için modül çalışan işlemini geri dönüştürmez, bunun yerine geçerli IIS Express işleminin düzgün bir şekilde kapatılmasını tetikler. Uygulamaya yönelik bir sonraki istek, yeni bir IIS Express işlem olarak çoğaltılır.

### <a name="process-name"></a>İşlem adı

`Process.GetCurrentProcess().ProcessName`raporlar `w3wp` / `iisexpress` (işlem içi) veya `dotnet` (işlem dışı).

Windows kimlik doğrulaması gibi birçok yerel modül etkin kalır. ASP.NET Core modülüyle etkin IIS modülleri hakkında daha fazla bilgi edinmek için bkz <xref:host-and-deploy/iis/modules> ..

ASP.NET Core modülü de şunları yapabilir:

* Çalışan işlem için ortam değişkenlerini ayarlayın.
* Başlatma sorunlarını gidermek için stdout çıkışını dosya depolama alanına kaydedin.
* Windows kimlik doğrulama belirteçlerini ilet.

## <a name="how-to-install-and-use-the-aspnet-core-module"></a>ASP.NET Core modülünü yüklemek ve kullanmak

ASP.NET Core modülünün nasıl yükleneceğine ilişkin yönergeler için bkz. [.NET Core barındırma paketi 'Ni yüklemek](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).

## <a name="configuration-with-webconfig"></a>web.config ile yapılandırma

ASP.NET Core modülü, `aspNetCore` `system.webServer` sitenin *web.config* dosyasındaki düğümünün bölümüyle yapılandırılır.

Aşağıdaki *web.config* dosyası [çerçeveye bağlı bir dağıtım](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) Için yayımlanır ve ASP.NET Core modülünü site isteklerini işleyecek şekilde yapılandırır:

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

Aşağıdaki *web.config* , [kendinden bağımsız bir dağıtım](/dotnet/articles/core/deploying/#self-contained-deployments-scd)için yayımlanır:

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

<xref:System.Configuration.SectionInformation.InheritInChildApplications*>Özelliği, `false` öğesi içinde belirtilen ayarların [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) uygulamanın bir alt dizininde bulunan uygulamalar tarafından devralınmadığını belirtmek için olarak ayarlanır.

Bir uygulama [Azure App Service](https://azure.microsoft.com/services/app-service/)dağıtıldığında, `stdoutLogFile` yol olarak ayarlanır `\\?\%home%\LogFiles\stdout` . Yol, stdout günlüklerini hizmet tarafından otomatik olarak oluşturulan bir konum olan *LogFiles* klasörüne kaydeder.

IIS alt uygulama yapılandırması hakkında bilgi için bkz <xref:host-and-deploy/iis/index#sub-applications> ..

### <a name="attributes-of-the-aspnetcore-element"></a>AspNetCore öğesinin öznitelikleri

| Öznitelik | Açıklama | Varsayılan |
| --------- | ----------- | :-----: |
| `arguments` | <p>İsteğe bağlı dize özniteliği.</p><p>**ProcessPath**içinde belirtilen yürütülebilir dosya için bağımsız değişkenler.</p> | |
| `disableStartUpErrorPage` | <p>İsteğe bağlı Boolean özniteliği.</p><p>Doğru ise, **502,5-Işlem hatası** sayfası bastırılır ve *web.config* yapılandırılan 502 durum kodu sayfası önceliklidir.</p> | `false` |
| `forwardWindowsAuthToken` | <p>İsteğe bağlı Boolean özniteliği.</p><p>True ise belirteç, istek başına ' MS-ASPNETCORE-WıNAUTHTOKEN ' üst bilgisi olarak% ASPNETCORE_PORT% üzerinde dinleme yapan alt işleme iletilir. Bu, istek başına bu belirteçte CloseHandle çağırma işleminin sorumluluğundadır.</p> | `true` |
| `hostingModel` | <p>İsteğe bağlı dize özniteliği.</p><p>Barındırma modelini işlem içi ( `InProcess` / `inprocess` ) veya işlem dışı () olarak belirtir `OutOfProcess` / `outofprocess` .</p> | `InProcess`<br>`inprocess` |
| `processesPerApplication` | <p>İsteğe bağlı tamsayı özniteliği.</p><p>**ProcessPath** ayarında belirtilen işlemin örnek sayısını, uygulama başına bir şekilde işleyecek şekilde belirtir.</p><p>&dagger;İşlem içi barındırma için, değer ile sınırlıdır `1` .</p><p>Ayar `processesPerApplication` önerilmez. Bu öznitelik gelecek bir sürümde kaldırılacak.</p> | Varsayılanını`1`<br>Min`1`<br>Biçimlendir`100`&dagger; |
| `processPath` | <p>Gerekli dize özniteliği.</p><p>HTTP isteklerini dinleyen bir işlemi başlatan yürütülebilir dosyanın yolu. Göreli yollar desteklenir. Yol ile başlıyorsa `.` , yol site köküne göreli olarak kabul edilir.</p> | |
| `rapidFailsPerMinute` | <p>İsteğe bağlı tamsayı özniteliği.</p><p>**ProcessPath** içinde belirtilen işleme dakika başına kilitlenme için izin verilen sayıyı belirtir. Bu sınır aşılırsa modül, dakika geri kalanı için işlemi başlatmayı durduruyor.</p><p>İşlem içi barındırma ile desteklenmez.</p> | Varsayılanını`10`<br>Min`0`<br>Biçimlendir`100` |
| `requestTimeout` | <p>İsteğe bağlı TimeSpan özniteliği.</p><p>ASP.NET Core modülünün% ASPNETCORE_PORT% üzerinde dinleme işleminden yanıt beklediği süreyi belirtir.</p><p>ASP.NET Core 2,1 veya üzeri sürümü ile birlikte gelen ASP.NET Core modülünün sürümlerinde, `requestTimeout` saat, dakika ve saniye cinsinden belirtilir.</p><p>İşlem içi barındırma için uygulanmaz. İşlem içi barındırma için modül, uygulamanın isteği işlemesini bekler.</p><p>Dizenin dakika ve saniye kesimleri için geçerli değerler 0-59 aralığındadır. Dakika veya saniye değerindeki **60** kullanımı, *500-iç sunucu hatasına*neden olur.</p> | Varsayılanını`00:02:00`<br>Min`00:00:00`<br>Biçimlendir`360:00:00` |
| `shutdownTimeLimit` | <p>İsteğe bağlı tamsayı özniteliği.</p><p>*app_offline.htm* dosyası algılandığında, modülün yürütülebilir dosyanın düzgün şekilde kapatılmasını beklediği saniye cinsinden süre.</p> | Varsayılanını`10`<br>Min`0`<br>Biçimlendir`600` |
| `startupTimeLimit` | <p>İsteğe bağlı tamsayı özniteliği.</p><p>Modülün, bağlantı noktasında dinleme yapan bir işlemin başlamasını bekleyeceği saniye cinsinden süre. Bu süre sınırı aşılırsa, modül işlemi bu işlemden sonra da bir kez gider.</p><p>*İşlem içi*barındırma sırasında: **işlem yeniden başlatılmaz** ve **rapidFailsPerMinute** ayarını **kullanmaz.**</p><p>*İşlem dışı*barındırma sırasında, modül yeni bir istek aldığında işlemi yeniden başlatmayı dener ve uygulamanın son geçen dakikada **rapidFailsPerMinute** sayısını başlatamadıkça sonraki gelen isteklerde işlemi yeniden başlatmayı dener.</p><p>0 (sıfır) değeri sonsuz bir zaman aşımı olarak kabul **edilmez** .</p> | Varsayılanını`120`<br>Min`0`<br>Biçimlendir`3600` |
| `stdoutLogEnabled` | <p>İsteğe bağlı Boolean özniteliği.</p><p>True ise, **processPath** içinde belirtilen işlem için **stdout** ve **stderr** , **stdoutLogFile**içinde belirtilen dosyaya yeniden yönlendirilir.</p> | `false` |
| `stdoutLogFile` | <p>İsteğe bağlı dize özniteliği.</p><p>**ProcessPath** içinde belirtilen işlemden **stdout** ve **stderr** 'in günlüğe kaydedildiği göreli veya mutlak dosya yolunu belirtir. Göreli yollar, sitenin köküne göredir. İle başlayan tüm `.` yollar, site köküne göredir ve diğer tüm yollar mutlak yollar olarak değerlendirilir. Yolda sunulan klasörler, günlük dosyası oluşturulduğunda modül tarafından oluşturulur. Alt çizgi sınırlayıcılarını kullanma, bir zaman damgası, işlem KIMLIĞI ve dosya uzantısı (*. log*) **stdoutLogFile** yolunun son kesimine eklenir. `.\logs\stdout`Değer olarak sağlandıysa, bir 2/5/2018 işlem 1934 kimliği ile 19:41:32 ' de ' de kaydedildiğinde günlük *logs* dosyasında bir örnek stdout günlüğü *stdout_20180205194132_1934* kaydedilir.</p> | `aspnetcore-stdout` |

### <a name="set-environment-variables"></a>Ortam değişkenlerini belirleme

Özniteliği içindeki işlem için ortam değişkenleri belirtilebilir `processPath` . `<environmentVariable>`Bir koleksiyon öğesinin alt öğesi ile bir ortam değişkeni belirtin `<environmentVariables>` . Bu bölümde ayarlanan ortam değişkenleri, sistem ortamı değişkenlerine göre önceliklidir.

Aşağıdaki örnek *web.config*iki ortam değişkenini ayarlar. `ASPNETCORE_ENVIRONMENT`uygulamanın ortamını olarak yapılandırır `Development` . Bir geliştirici, uygulama özel durumunda hata ayıklarken [Geliştirici özel durum sayfasını](xref:fundamentals/error-handling) yüklemeye zorlamak için bu değeri geçici olarak *web.config* dosyasında ayarlayabilir. `CONFIG_DIR`, geliştiricinin, uygulamanın yapılandırma dosyasını yüklemek için bir yol oluşturmak üzere başlangıçta değeri okuyan kodu yazdığı, Kullanıcı tanımlı ortam değişkenine bir örnektir.

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
> Ortamı doğrudan *web.config* olarak ayarlamanın alternatifi, `<EnvironmentName>` özelliği [Publish profile (. pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) veya proje dosyasına dahil kullanmaktır. Bu yaklaşım, proje yayımlandığında *web.config* ortamı ayarlar:
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> `ASPNETCORE_ENVIRONMENT`Ortam değişkenini yalnızca, `Development` Internet gibi güvenilmeyen ağlarla erişilebilen hazırlama ve test etme sunucularında olarak ayarlayın.

## <a name="app_offlinehtm"></a>app_offline.htm

Bir uygulamanın kök dizininde *app_offline.htm* adında bir dosya algılanırsa, ASP.NET Core modülü uygulamayı düzgün bir şekilde kapatmaya ve gelen istekleri işlemeyi durdurmaya çalışır. Uygulama, içinde tanımlanan saniye sayısından sonra hala çalışıyorsa `shutdownTimeLimit` , ASP.NET Core modülü çalışan işlemi de yok eder.

*app_offline.htm* dosyası mevcut olsa da, ASP.NET Core modülü *app_offline.htm* dosyanın içeriğini geri göndererek isteklere yanıt verir. *app_offline.htm* dosyası kaldırıldığında, sonraki istek uygulamayı başlatır.

İşlem dışı barındırma modeli kullanılırken, açık bir bağlantı varsa uygulama hemen kapanmayabilir. Örneğin, bir WebSocket bağlantısı, uygulamanın kapatılmasını erteleyebilir.

## <a name="start-up-error-page"></a>Başlatma hatası sayfası

Hem işlem içi hem de işlem dışı barındırma, uygulamayı başlatamadıklarında özel hata sayfaları üretir.

ASP.NET Core modülü işlem içi veya işlem dışı istek işleyicisini bulamazsa, *500,0-işlem içi/işlem dışı Işleyici yükleme hatası* durum kodu sayfası görüntülenir.

ASP.NET Core modülü uygulamayı başlatamadığında işlem içi barındırma için, *500,30-başlatma hatası* durum kodu sayfası görüntülenir.

ASP.NET Core modülü arka uç işlemini başlatamadığında veya arka uç işlemi başlatılırsa ancak yapılandırılmış bağlantı noktasında dinleyemediğinde, işlem dışı barındırma için *502,5-Işlem hatası* durum kodu sayfası görüntülenir.

Bu sayfayı bastırın ve varsayılan IIS 5xx durum kodu sayfasına dönmek için `disableStartUpErrorPage` özniteliğini kullanın. Özel hata iletilerini yapılandırma hakkında daha fazla bilgi için bkz. [http \<httpErrors> hataları ](/iis/configuration/system.webServer/httpErrors/).

## <a name="log-creation-and-redirection"></a>Günlük oluşturma ve yeniden yönlendirme

ASP.NET Core modülü, `stdoutLogEnabled` `stdoutLogFile` öğesinin ve öznitelikleri ayarlandıysa stdout ve stderr konsol çıkışını diske yeniden yönlendirir `aspNetCore` . Yoldaki tüm klasörler, `stdoutLogFile` günlük dosyası oluşturulduğunda modül tarafından oluşturulur. Uygulama havuzunun, günlüklerin yazıldığı konuma yazma erişimi olması gerekir ( `IIS AppPool\<app_pool_name>` yazma izni sağlamak için kullanın).

İşlem geri dönüştürme/yeniden başlatma gerçekleşmediği sürece Günlükler döndürülemez. Bu, günlüklerin tükettiği disk alanını sınırlamak için barındırıcının sorumluluğundadır.

Stdout günlüğünün kullanılması yalnızca IIS 'de barındırırken veya [Visual Studio Ile IIS için geliştirme zamanı desteği](xref:host-and-deploy/iis/development-time-iis-support)kullanılırken değil, yerel olarak hata ayıklarken ve uygulamayı IIS Express ile çalıştırırken yalnızca uygulama başlatma sorunlarını gidermek için önerilir.

Genel uygulama günlüğü amaçları için stdout günlüğünü kullanmayın. ASP.NET Core uygulamasında rutin günlük kaydı için, günlük dosyası boyutunu sınırlayan ve günlükleri döndüren bir günlüğe kaydetme kitaplığı kullanın. Daha fazla bilgi için bkz. [üçüncü taraf günlüğü sağlayıcıları](xref:fundamentals/logging/index#third-party-logging-providers).

Günlük dosyası oluşturulduğunda zaman damgası ve dosya uzantısı otomatik olarak eklenir. Günlük dosyası adı, alt *.log* `stdoutLogFile` çizgi ile ayrılmış yolun (genellikle *stdout*) son KESIMINE zaman damgası, işlem kimliği ve dosya uzantısı (. log) eklenerek oluşur. `stdoutLogFile`Yol *stdout*ile sonlanıyorsa, 1934 ' de 19:42:32 2/5/2018 ' de oluşturulan PID 'sine sahip bir uygulama için günlük *stdout_20180205194132_1934*dosya adı vardır.

`stdoutLogEnabled`Yanlış ise, uygulama başlangıcında oluşan hatalar yakalanır ve 30 KB 'a kadar olay günlüğüne yayınlanır. Başlangıçtan sonra tüm ek Günlükler atılır.

Aşağıdaki örnek `aspNetCore` öğesi, göreli yoldaki stdout günlüğünü yapılandırır `.\log\` . AppPool Kullanıcı kimliğinin, belirtilen yola yazma izni olduğunu doğrulayın.

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

Azure App Service dağıtım için bir uygulama yayımlarken, Web SDK `stdoutLogFile` değeri olarak ayarlanır `\\?\%home%\LogFiles\stdout` . `%home`Ortam değişkeni, Azure App Service tarafından barındırılan uygulamalar için önceden tanımlanmıştır.

Günlüğe kaydetme filtresi kuralları oluşturmak için ASP.NET Core günlük belgelerinin [yapılandırma](xref:fundamentals/logging/index#log-filtering) ve [günlük filtreleme](xref:fundamentals/logging/index#log-filtering) bölümlerine bakın.

Yol biçimleri hakkında daha fazla bilgi için bkz. [Windows sistemlerinde dosya yolu biçimleri](/dotnet/standard/io/file-path-formats).

## <a name="enhanced-diagnostic-logs"></a>Gelişmiş tanılama günlükleri

ASP.NET Core modülü, gelişmiş tanılama günlükleri sağlamak için yapılandırılabilir. `<handlerSettings>`Öğesini `<aspNetCore>` *web.config*öğesine ekleyin. İçin ayarı `debugLevel` , `TRACE` Tanılama bilgilerini daha yüksek bir şekilde kullanıma sunar:

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

Yoldaki tüm klasörler (önceki örnekteki*Günlükler* ), günlük dosyası oluşturulduğunda modül tarafından oluşturulur. Uygulama havuzunun, günlüklerin yazıldığı konuma yazma erişimi olması gerekir ( `IIS AppPool\<app_pool_name>` yazma izni sağlamak için kullanın).

Hata ayıklama düzeyi ( `debugLevel` ) değerleri hem düzeyi hem de konumu içerebilir.

Düzeyler (en az ayrıntıdan en fazla ayrıntı sırasına göre):

* HATA
* UYARI
* BILGISINE
* TRACE

Konumlar (birden çok konuma izin verilir):

* KONSOLA
* EVENTLOG
* DOSYA

İşleyici ayarları, ortam değişkenleri aracılığıyla da kullanılabilir:

* `ASPNETCORE_MODULE_DEBUG_FILE`: Hata ayıklama günlük dosyasının yolu. (Varsayılan: *aspnetcore-Debug. log*)
* `ASPNETCORE_MODULE_DEBUG`: Hata ayıklama düzeyi ayarı.

> [!WARNING]
> Bir sorunu gidermek için dağıtımda hata ayıklama günlüğü 'nün gerekenden uzun süre **etkin bırakmayın.** Günlüğün boyutu sınırlı değil. Hata ayıklama günlüğünün etkin bırakılması, kullanılabilir disk alanını tüketebilir ve sunucu veya App Service 'i kilitlemez.

web.configdosyasındaki öğenin bir örneği için [web.configyapılandırma](#configuration-with-webconfig) konusuna bakın `aspNetCore` . *web.config*

## <a name="modify-the-stack-size"></a>Yığın boyutunu değiştirme

*Yalnızca işlem içi barındırma modeli kullanılırken geçerlidir.*

Yönetilen yığın boyutunu `stackSize` *web.config*bayt cinsinden ayarla ayarını kullanarak yapılandırın. Varsayılan boyut bayt 'tır `1048576` (1 MB).

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

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a>Proxy yapılandırması HTTP protokolünü ve eşleştirme belirtecini kullanır

*Yalnızca işlem dışı barındırma için geçerlidir.*

ASP.NET Core modülü ve Kestrel arasında oluşturulan ara sunucu HTTP protokolünü kullanır. Modül ve Kestrel arasındaki trafiği sunucu dışı bir konumdan bırakırken gizlice dinleme riski yoktur.

Eşleştirme belirteci, Kestrel tarafından alınan isteklerin IIS tarafından proxy aldığından ve başka bir kaynaktan gelmediğinden emin olmak için kullanılır. Eşleştirme belirteci oluşturulur ve modül tarafından bir ortam değişkenine () ayarlanır `ASPNETCORE_TOKEN` . Eşleştirme belirteci, her proxy istek için de bir üst bilgi ( `MS-ASPNETCORE-TOKEN` ) olarak ayarlanır. IIS ara yazılımı, eşleştirme belirteci üstbilgi değerinin ortam değişkeni değeriyle eşleşip eşleşmediğini doğrulamak için aldığı her isteği denetler. Belirteç değerleri uyuşmadıysa, istek günlüğe kaydedilir ve reddedilir. Eşleştirme belirteci ortam değişkeni ve modülle Kestrel arasındaki trafik, sunucu dışında bir konumdan erişilebilir değildir. Eşleştirme belirteç değerini bilmeden, bir saldırgan IIS ara yazılımı 'ndaki denetimi atlayan istekleri gönderemez.

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a>IIS paylaşılan yapılandırmasıyla ASP.NET Core modülü

ASP.NET Core modülü yükleyicisi, **TrustedInstaller** hesabının ayrıcalıklarıyla çalışır. Yerel sistem hesabı, IIS paylaşılan Yapılandırması tarafından kullanılan paylaşım yolu için değiştirme iznine sahip olmadığından, yükleyici paylaşımdaki *applicationHost.config* dosyasında modül ayarlarını yapılandırmaya çalışırken bir erişim reddedildi hatası atar.

IIS yüklemesiyle aynı makinede bir IIS paylaşılan yapılandırması kullanırken, şu şekilde ayarlanan parametre ile birlikte ASP.NET Core barındırma paketi yükleyicisini çalıştırın `OPT_NO_SHARED_CONFIG_CHECK` `1` :

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

Paylaşılan yapılandırmanın yolu IIS yüklemesiyle aynı makinede olmadığında, şu adımları izleyin:

1. IIS paylaşılan yapılandırmasını devre dışı bırakın.
1. Yükleyiciyi çalıştırın.
1. Güncelleştirilmiş *applicationHost.config* dosyasını paylaşıma dışarı aktarın.
1. IIS paylaşılan yapılandırmasını yeniden etkinleştirin.

## <a name="module-version-and-hosting-bundle-installer-logs"></a>Modül sürümü ve barındırma paketi yükleyici günlükleri

Yüklü ASP.NET Core modülünün sürümünü öğrenmek için:

1. Barındırma sisteminde *%windir%\system32\inetsrv dizinine*gidin.
1. *aspnetcore.dll* dosyasını bulun.
1. Dosyaya sağ tıklayın ve bağlam menüsünden **Özellikler** ' i seçin.
1. **Ayrıntılar** sekmesini seçin. **Dosya sürümü** ve **ürün sürümü** , modülün yüklü sürümünü temsil eder.

Modülün barındırma paketi yükleyici günlükleri *C: \\ Users \\ % UserName% \\ AppData \\ Local \\ Temp*konumunda bulunur. Dosya *dd_DotNetCoreWinSvrHosting__ \<timestamp> _000_AspNetCoreModule_x64. log*olarak adlandırılmıştır.

## <a name="module-schema-and-configuration-file-locations"></a>Modül, şema ve yapılandırma dosyası konumları

### <a name="module"></a>Modül

**IIS (X86/AMD64):**

* % windir% \System32\inetsrv\aspnetcore.dll

* % windir% \SysWOW64\inetsrv\aspnetcore.dll

* %ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll

* % ProgramFiles (x86)% \ ııs\ ASP.NET Core Module\V2\aspnetcorev2.dll

**IIS Express (X86/AMD64):**

* %ProgramFiles%\IIS Express\aspnetcore.dll

* % ProgramFiles (x86)% \ IIS Express\aspnetcore.dll

* %ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll

* % ProgramFiles (x86)% \ IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll

### <a name="schema"></a>Şema

**IIS**

* % windir% \System32\inetsrv\config\schema\aspnetcore_schema.xml

* % windir% \System32\inetsrv\config\schema\aspnetcore_schema_v2.xml

**IIS Express**

* %ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml

* %ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml

### <a name="configuration"></a>Yapılandırma

**IIS**

* % windir% \System32\inetsrv\config\applicationHost.config

**IIS Express**

* Visual Studio: {APPLICATION ROOT} \\.vs\config\applicationHost.config

* *iisexpress.exe* CLı:% USERPROFILE% \Documents\IISExpress\config\applicationhost.config

Dosyalar, *applicationHost.config* dosyasında *aspnetcore* ' u arayarak bulunabilir.

::: moniker-end

::: moniker range="= aspnetcore-2.2"

ASP.NET Core modülü, IIS ardışık düzenine şu şekilde takılan yerel bir IIS modülüdür:

* `w3wp.exe` [İşlem içi barındırma modeli](#in-process-hosting-model)olarak adlandırılan IIS çalışan işleminin () içinde bir ASP.NET Core uygulaması barındırın.
* Web isteklerini, [işlem dışı barındırma modeli](#out-of-process-hosting-model)olarak adlandırılan [Kestrel sunucusunu](xref:fundamentals/servers/kestrel)çalıştıran bir arka uç ASP.NET Core uygulamasına iletin.

Desteklenen Windows sürümleri:

* Windows 7 veya üzeri
* Windows Server 2008 R2 veya sonraki sürümü

İşlem içinde barındırırken, modül IIS HTTP sunucusu () olarak adlandırılan IIS için işlem içi sunucu uygulamasını kullanır `IISHttpServer` .

İşlem dışı barındırma sırasında modül yalnızca Kestrel ile birlikte kullanılabilir. Modül [HTTP.sys](xref:fundamentals/servers/httpsys)ile çalışmıyor.

## <a name="hosting-models"></a>Barındırma modelleri

### <a name="in-process-hosting-model"></a>İşlem içi barındırma modeli

Uygulamayı işlem içi barındırma için yapılandırmak için, `<AspNetCoreHostingModel>` özelliği uygulamanın proje dosyasına `InProcess` (işlem dışı barındırma ile ayarlanır `OutOfProcess` ) ekleyin:

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>InProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

İşlem içi barındırma modeli, .NET Framework hedef ASP.NET Core uygulamalar için desteklenmez.

Değeri `<AspNetCoreHostingModel>` büyük/küçük harfe duyarlıdır `inprocess` ve `outofprocess` geçerli değerlerdir.

`<AspNetCoreHostingModel>`Özellik dosyada yoksa, varsayılan değer olur `OutOfProcess` .

İşlem içi barındırma sırasında aşağıdaki özellikler geçerlidir:

* Kestrel Server yerine IIS HTTP sunucusu ( `IISHttpServer` ) kullanılır [Kestrel](xref:fundamentals/servers/kestrel) . İşlem içi için [Createdefaultbuilder](xref:fundamentals/host/web-host#set-up-a-host) aşağıdakileri öğesine çağırır <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> :

  * Kaydolun `IISHttpServer` .
  * ASP.NET Core modülünün arkasında çalışırken sunucunun dinlemesi gereken bağlantı noktasını ve temel yolu yapılandırın.
  * Konağı, başlatma hatalarını yakalamak üzere yapılandırın.

* [RequestTimeout özniteliği](#attributes-of-the-aspnetcore-element) işlem içi barındırma için uygulanmaz.

* Uygulama havuzunu uygulamalar arasında paylaşma desteklenmez. Uygulama başına bir uygulama havuzu kullanın.

* [Web dağıtımı](/iis/publish/using-web-deploy/introduction-to-web-deploy) kullanırken veya dağıtıma el ile bir [app_offline.htm dosyası](xref:host-and-deploy/iis/index#locked-deployment-files)yerleştirilirken, açık bir bağlantı varsa uygulama hemen kapanmayabilir. Örneğin, bir WebSocket bağlantısı, uygulamanın kapatılmasını erteleyebilir.

* Uygulamanın mimarisi (bit genişliği) ve yüklü çalışma zamanının (x64 veya x86) uygulama havuzunun mimarisiyle eşleşmesi gerekir.

* İstemci bağlantısı kesiliyor algılandı. İstemci bağlantısı kesildiğinde [HttpContext. RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) iptal belirteci iptal edilir.

* ASP.NET Core 2.2.1 veya önceki sürümlerde, <xref:System.IO.Directory.GetCurrentDirectory*> uygulamanın dizini yerıne IIS tarafından başlatılan işlemin çalışan dizinini döndürür (örneğin, *w3wp.exe*için *c:\Windows\System32\inetsrv* ).

  Uygulamanın geçerli dizinini ayarlayan örnek kod için bkz. [Currentdirectoryyardımcıları sınıfı](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs). Yöntemini çağırın `SetCurrentDirectory` . <xref:System.IO.Directory.GetCurrentDirectory*>Uygulamanın dizinini sağlamak için sonraki çağrılar.

* İşlem içi barındırma sırasında, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> bir kullanıcıyı başlatmak için dahili olarak çağrılmaz. Bu nedenle, <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> her kimlik doğrulaması sonrasında talepleri dönüştürmek için kullanılan bir uygulama varsayılan olarak etkinleştirilmez. Talepleri bir <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> uygulamayla dönüştürürken, <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> kimlik doğrulama hizmetleri Ekle ' yi çağırın:

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

Bir uygulamayı işlem dışı barındırmak üzere yapılandırmak için, proje dosyasında aşağıdaki yaklaşımlardan birini kullanın:

* `<AspNetCoreHostingModel>`Özelliği belirtmeyin. `<AspNetCoreHostingModel>`Özellik dosyada yoksa, varsayılan değer olur `OutOfProcess` .
* `<AspNetCoreHostingModel>`Özelliğin değerini olarak ayarlayın `OutOfProcess` (işlem içi barındırma ile ayarlanır `InProcess` ):

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

Değer büyük/küçük harfe duyarlıdır `inprocess` ve `outofprocess` geçerli değerlerdir.

IIS HTTP Server () yerine [Kestrel](xref:fundamentals/servers/kestrel) Server kullanılır `IISHttpServer` .

İşlem dışı için [Createdefaultbuilder](xref:fundamentals/host/web-host#set-up-a-host) aşağıdakileri <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> öğesine çağırır:

* ASP.NET Core modülünün arkasında çalışırken sunucunun dinlemesi gereken bağlantı noktasını ve temel yolu yapılandırın.
* Konağı, başlatma hatalarını yakalamak üzere yapılandırın.

### <a name="hosting-model-changes"></a>Barındırma modeli değişiklikleri

Ayar, `hostingModel` *web.config* dosyasında değiştirilirse ( [web.configile yapılandırma](#configuration-with-webconfig) bölümünde açıklanan), modül IIS için çalışan işlemini geri dönüştürür.

IIS Express için modül çalışan işlemini geri dönüştürmez, bunun yerine geçerli IIS Express işleminin düzgün bir şekilde kapatılmasını tetikler. Uygulamaya yönelik bir sonraki istek, yeni bir IIS Express işlem olarak çoğaltılır.

### <a name="process-name"></a>İşlem adı

`Process.GetCurrentProcess().ProcessName`raporlar `w3wp` / `iisexpress` (işlem içi) veya `dotnet` (işlem dışı).

Windows kimlik doğrulaması gibi birçok yerel modül etkin kalır. ASP.NET Core modülüyle etkin IIS modülleri hakkında daha fazla bilgi edinmek için bkz <xref:host-and-deploy/iis/modules> ..

ASP.NET Core modülü de şunları yapabilir:

* Çalışan işlem için ortam değişkenlerini ayarlayın.
* Başlatma sorunlarını gidermek için stdout çıkışını dosya depolama alanına kaydedin.
* Windows kimlik doğrulama belirteçlerini ilet.

## <a name="how-to-install-and-use-the-aspnet-core-module"></a>ASP.NET Core modülünü yüklemek ve kullanmak

ASP.NET Core modülünün nasıl yükleneceğine ilişkin yönergeler için bkz. [.NET Core barındırma paketi 'Ni yüklemek](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).

## <a name="configuration-with-webconfig"></a>web.config ile yapılandırma

ASP.NET Core modülü, `aspNetCore` `system.webServer` sitenin *web.config* dosyasındaki düğümünün bölümüyle yapılandırılır.

Aşağıdaki *web.config* dosyası [çerçeveye bağlı bir dağıtım](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) Için yayımlanır ve ASP.NET Core modülünü site isteklerini işleyecek şekilde yapılandırır:

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

Aşağıdaki *web.config* , [kendinden bağımsız bir dağıtım](/dotnet/articles/core/deploying/#self-contained-deployments-scd)için yayımlanır:

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

<xref:System.Configuration.SectionInformation.InheritInChildApplications*>Özelliği, `false` öğesi içinde belirtilen ayarların [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) uygulamanın bir alt dizininde bulunan uygulamalar tarafından devralınmadığını belirtmek için olarak ayarlanır.

Bir uygulama [Azure App Service](https://azure.microsoft.com/services/app-service/)dağıtıldığında, `stdoutLogFile` yol olarak ayarlanır `\\?\%home%\LogFiles\stdout` . Yol, stdout günlüklerini hizmet tarafından otomatik olarak oluşturulan bir konum olan *LogFiles* klasörüne kaydeder.

IIS alt uygulama yapılandırması hakkında bilgi için bkz <xref:host-and-deploy/iis/index#sub-applications> ..

### <a name="attributes-of-the-aspnetcore-element"></a>AspNetCore öğesinin öznitelikleri

| Öznitelik | Açıklama | Varsayılan |
| --------- | ----------- | :-----: |
| `arguments` | <p>İsteğe bağlı dize özniteliği.</p><p>**ProcessPath**içinde belirtilen yürütülebilir dosya için bağımsız değişkenler.</p> | |
| `disableStartUpErrorPage` | <p>İsteğe bağlı Boolean özniteliği.</p><p>Doğru ise, **502,5-Işlem hatası** sayfası bastırılır ve *web.config* yapılandırılan 502 durum kodu sayfası önceliklidir.</p> | `false` |
| `forwardWindowsAuthToken` | <p>İsteğe bağlı Boolean özniteliği.</p><p>True ise belirteç, istek başına ' MS-ASPNETCORE-WıNAUTHTOKEN ' üst bilgisi olarak% ASPNETCORE_PORT% üzerinde dinleme yapan alt işleme iletilir. Bu, istek başına bu belirteçte CloseHandle çağırma işleminin sorumluluğundadır.</p> | `true` |
| `hostingModel` | <p>İsteğe bağlı dize özniteliği.</p><p>Barındırma modelini işlem içi ( `InProcess` / `inprocess` ) veya işlem dışı () olarak belirtir `OutOfProcess` / `outofprocess` .</p> | `OutOfProcess`<br>`outofprocess` |
| `processesPerApplication` | <p>İsteğe bağlı tamsayı özniteliği.</p><p>**ProcessPath** ayarında belirtilen işlemin örnek sayısını, uygulama başına bir şekilde işleyecek şekilde belirtir.</p><p>&dagger;İşlem içi barındırma için, değer ile sınırlıdır `1` .</p><p>Ayar `processesPerApplication` önerilmez. Bu öznitelik gelecek bir sürümde kaldırılacak.</p> | Varsayılanını`1`<br>Min`1`<br>Biçimlendir`100`&dagger; |
| `processPath` | <p>Gerekli dize özniteliği.</p><p>HTTP isteklerini dinleyen bir işlemi başlatan yürütülebilir dosyanın yolu. Göreli yollar desteklenir. Yol ile başlıyorsa `.` , yol site köküne göreli olarak kabul edilir.</p> | |
| `rapidFailsPerMinute` | <p>İsteğe bağlı tamsayı özniteliği.</p><p>**ProcessPath** içinde belirtilen işleme dakika başına kilitlenme için izin verilen sayıyı belirtir. Bu sınır aşılırsa modül, dakika geri kalanı için işlemi başlatmayı durduruyor.</p><p>İşlem içi barındırma ile desteklenmez.</p> | Varsayılanını`10`<br>Min`0`<br>Biçimlendir`100` |
| `requestTimeout` | <p>İsteğe bağlı TimeSpan özniteliği.</p><p>ASP.NET Core modülünün% ASPNETCORE_PORT% üzerinde dinleme işleminden yanıt beklediği süreyi belirtir.</p><p>ASP.NET Core 2,1 veya üzeri sürümü ile birlikte gelen ASP.NET Core modülünün sürümlerinde, `requestTimeout` saat, dakika ve saniye cinsinden belirtilir.</p><p>İşlem içi barındırma için uygulanmaz. İşlem içi barındırma için modül, uygulamanın isteği işlemesini bekler.</p><p>Dizenin dakika ve saniye kesimleri için geçerli değerler 0-59 aralığındadır. Dakika veya saniye değerindeki **60** kullanımı, *500-iç sunucu hatasına*neden olur.</p> | Varsayılanını`00:02:00`<br>Min`00:00:00`<br>Biçimlendir`360:00:00` |
| `shutdownTimeLimit` | <p>İsteğe bağlı tamsayı özniteliği.</p><p>*app_offline.htm* dosyası algılandığında, modülün yürütülebilir dosyanın düzgün şekilde kapatılmasını beklediği saniye cinsinden süre.</p> | Varsayılanını`10`<br>Min`0`<br>Biçimlendir`600` |
| `startupTimeLimit` | <p>İsteğe bağlı tamsayı özniteliği.</p><p>Modülün, bağlantı noktasında dinleme yapan bir işlemin başlamasını bekleyeceği saniye cinsinden süre. Bu süre sınırı aşılırsa, modül işlemi bu işlemden sonra da bir kez gider.</p><p>*İşlem içi*barındırma sırasında: **işlem yeniden başlatılmaz** ve **rapidFailsPerMinute** ayarını **kullanmaz.**</p><p>*İşlem dışı*barındırma sırasında, modül yeni bir istek aldığında işlemi yeniden başlatmayı dener ve uygulamanın son geçen dakikada **rapidFailsPerMinute** sayısını başlatamadıkça sonraki gelen isteklerde işlemi yeniden başlatmayı dener.</p><p>0 (sıfır) değeri sonsuz bir zaman aşımı olarak kabul **edilmez** .</p> | Varsayılanını`120`<br>Min`0`<br>Biçimlendir`3600` |
| `stdoutLogEnabled` | <p>İsteğe bağlı Boolean özniteliği.</p><p>True ise, **processPath** içinde belirtilen işlem için **stdout** ve **stderr** , **stdoutLogFile**içinde belirtilen dosyaya yeniden yönlendirilir.</p> | `false` |
| `stdoutLogFile` | <p>İsteğe bağlı dize özniteliği.</p><p>**ProcessPath** içinde belirtilen işlemden **stdout** ve **stderr** 'in günlüğe kaydedildiği göreli veya mutlak dosya yolunu belirtir. Göreli yollar, sitenin köküne göredir. İle başlayan tüm `.` yollar, site köküne göredir ve diğer tüm yollar mutlak yollar olarak değerlendirilir. Yolda sunulan klasörler, günlük dosyası oluşturulduğunda modül tarafından oluşturulur. Alt çizgi sınırlayıcılarını kullanma, bir zaman damgası, işlem KIMLIĞI ve dosya uzantısı (*. log*) **stdoutLogFile** yolunun son kesimine eklenir. `.\logs\stdout`Değer olarak sağlandıysa, bir 2/5/2018 işlem 1934 kimliği ile 19:41:32 ' de ' de kaydedildiğinde günlük *logs* dosyasında bir örnek stdout günlüğü *stdout_20180205194132_1934* kaydedilir.</p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a>Ortam değişkenlerini ayarlama

Özniteliği içindeki işlem için ortam değişkenleri belirtilebilir `processPath` . `<environmentVariable>`Bir koleksiyon öğesinin alt öğesi ile bir ortam değişkeni belirtin `<environmentVariables>` . Bu bölümde ayarlanan ortam değişkenleri, sistem ortamı değişkenlerine göre önceliklidir.

Aşağıdaki örnek iki ortam değişkenini ayarlar. `ASPNETCORE_ENVIRONMENT`uygulamanın ortamını olarak yapılandırır `Development` . Bir geliştirici, uygulama özel durumunda hata ayıklarken [Geliştirici özel durum sayfasını](xref:fundamentals/error-handling) yüklemeye zorlamak için bu değeri geçici olarak *web.config* dosyasında ayarlayabilir. `CONFIG_DIR`, geliştiricinin, uygulamanın yapılandırma dosyasını yüklemek için bir yol oluşturmak üzere başlangıçta değeri okuyan kodu yazdığı, Kullanıcı tanımlı ortam değişkenine bir örnektir.

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
> Ortamı doğrudan *web.config* olarak ayarlamanın alternatifi, `<EnvironmentName>` özelliği [Publish profile (. pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) veya proje dosyasına dahil kullanmaktır. Bu yaklaşım, proje yayımlandığında *web.config* ortamı ayarlar:
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> `ASPNETCORE_ENVIRONMENT`Ortam değişkenini yalnızca, `Development` Internet gibi güvenilmeyen ağlarla erişilebilen hazırlama ve test etme sunucularında olarak ayarlayın.

## <a name="app_offlinehtm"></a>app_offline.htm

Bir uygulamanın kök dizininde *app_offline.htm* adında bir dosya algılanırsa, ASP.NET Core modülü uygulamayı düzgün bir şekilde kapatmaya ve gelen istekleri işlemeyi durdurmaya çalışır. Uygulama, içinde tanımlanan saniye sayısından sonra hala çalışıyorsa `shutdownTimeLimit` , ASP.NET Core modülü çalışan işlemi de yok eder.

*app_offline.htm* dosyası mevcut olsa da, ASP.NET Core modülü *app_offline.htm* dosyanın içeriğini geri göndererek isteklere yanıt verir. *app_offline.htm* dosyası kaldırıldığında, sonraki istek uygulamayı başlatır.

İşlem dışı barındırma modeli kullanılırken, açık bir bağlantı varsa uygulama hemen kapanmayabilir. Örneğin, bir WebSocket bağlantısı, uygulamanın kapatılmasını erteleyebilir.

## <a name="start-up-error-page"></a>Başlatma hatası sayfası

Hem işlem içi hem de işlem dışı barındırma, uygulamayı başlatamadıklarında özel hata sayfaları üretir.

ASP.NET Core modülü işlem içi veya işlem dışı istek işleyicisini bulamazsa, *500,0-işlem içi/işlem dışı Işleyici yükleme hatası* durum kodu sayfası görüntülenir.

ASP.NET Core modülü uygulamayı başlatamadığında işlem içi barındırma için, *500,30-başlatma hatası* durum kodu sayfası görüntülenir.

ASP.NET Core modülü arka uç işlemini başlatamadığında veya arka uç işlemi başlatılırsa ancak yapılandırılmış bağlantı noktasında dinleyemediğinde, işlem dışı barındırma için *502,5-Işlem hatası* durum kodu sayfası görüntülenir.

Bu sayfayı bastırın ve varsayılan IIS 5xx durum kodu sayfasına dönmek için `disableStartUpErrorPage` özniteliğini kullanın. Özel hata iletilerini yapılandırma hakkında daha fazla bilgi için bkz. [http \<httpErrors> hataları ](/iis/configuration/system.webServer/httpErrors/).

## <a name="log-creation-and-redirection"></a>Günlük oluşturma ve yeniden yönlendirme

ASP.NET Core modülü, `stdoutLogEnabled` `stdoutLogFile` öğesinin ve öznitelikleri ayarlandıysa stdout ve stderr konsol çıkışını diske yeniden yönlendirir `aspNetCore` . Yoldaki tüm klasörler, `stdoutLogFile` günlük dosyası oluşturulduğunda modül tarafından oluşturulur. Uygulama havuzunun, günlüklerin yazıldığı konuma yazma erişimi olması gerekir ( `IIS AppPool\<app_pool_name>` yazma izni sağlamak için kullanın).

İşlem geri dönüştürme/yeniden başlatma gerçekleşmediği sürece Günlükler döndürülemez. Bu, günlüklerin tükettiği disk alanını sınırlamak için barındırıcının sorumluluğundadır.

Stdout günlüğünün kullanılması yalnızca IIS 'de barındırırken veya [Visual Studio Ile IIS için geliştirme zamanı desteği](xref:host-and-deploy/iis/development-time-iis-support)kullanılırken değil, yerel olarak hata ayıklarken ve uygulamayı IIS Express ile çalıştırırken yalnızca uygulama başlatma sorunlarını gidermek için önerilir.

Genel uygulama günlüğü amaçları için stdout günlüğünü kullanmayın. ASP.NET Core uygulamasında rutin günlük kaydı için, günlük dosyası boyutunu sınırlayan ve günlükleri döndüren bir günlüğe kaydetme kitaplığı kullanın. Daha fazla bilgi için bkz. [üçüncü taraf günlüğü sağlayıcıları](xref:fundamentals/logging/index#third-party-logging-providers).

Günlük dosyası oluşturulduğunda zaman damgası ve dosya uzantısı otomatik olarak eklenir. Günlük dosyası adı, alt *.log* `stdoutLogFile` çizgi ile ayrılmış yolun (genellikle *stdout*) son KESIMINE zaman damgası, işlem kimliği ve dosya uzantısı (. log) eklenerek oluşur. `stdoutLogFile`Yol *stdout*ile sonlanıyorsa, 1934 ' de 19:42:32 2/5/2018 ' de oluşturulan PID 'sine sahip bir uygulama için günlük *stdout_20180205194132_1934*dosya adı vardır.

`stdoutLogEnabled`Yanlış ise, uygulama başlangıcında oluşan hatalar yakalanır ve 30 KB 'a kadar olay günlüğüne yayınlanır. Başlangıçtan sonra tüm ek Günlükler atılır.

Aşağıdaki örnek `aspNetCore` öğesi, göreli yoldaki stdout günlüğünü yapılandırır `.\log\` . AppPool Kullanıcı kimliğinin, belirtilen yola yazma izni olduğunu doğrulayın.

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

Azure App Service dağıtım için bir uygulama yayımlarken, Web SDK `stdoutLogFile` değeri olarak ayarlanır `\\?\%home%\LogFiles\stdout` . `%home`Ortam değişkeni, Azure App Service tarafından barındırılan uygulamalar için önceden tanımlanmıştır.

Yol biçimleri hakkında daha fazla bilgi için bkz. [Windows sistemlerinde dosya yolu biçimleri](/dotnet/standard/io/file-path-formats).

## <a name="enhanced-diagnostic-logs"></a>Gelişmiş tanılama günlükleri

ASP.NET Core modülü, gelişmiş tanılama günlükleri sağlamak için yapılandırılabilir. `<handlerSettings>`Öğesini `<aspNetCore>` *web.config*öğesine ekleyin. İçin ayarı `debugLevel` , `TRACE` Tanılama bilgilerini daha yüksek bir şekilde kullanıma sunar:

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

Değer için belirtilen yoldaki klasörler `<handlerSetting>` (önceki örnekteki*Günlükler* ) modül tarafından otomatik olarak oluşturulmaz ve dağıtımda önceden var olmalıdır. Uygulama havuzunun, günlüklerin yazıldığı konuma yazma erişimi olması gerekir ( `IIS AppPool\<app_pool_name>` yazma izni sağlamak için kullanın).

Hata ayıklama düzeyi ( `debugLevel` ) değerleri hem düzeyi hem de konumu içerebilir.

Düzeyler (en az ayrıntıdan en fazla ayrıntı sırasına göre):

* HATA
* UYARI
* BILGISINE
* TRACE

Konumlar (birden çok konuma izin verilir):

* KONSOLA
* EVENTLOG
* DOSYA

İşleyici ayarları, ortam değişkenleri aracılığıyla da kullanılabilir:

* `ASPNETCORE_MODULE_DEBUG_FILE`: Hata ayıklama günlük dosyasının yolu. (Varsayılan: *aspnetcore-Debug. log*)
* `ASPNETCORE_MODULE_DEBUG`: Hata ayıklama düzeyi ayarı.

> [!WARNING]
> Bir sorunu gidermek için dağıtımda hata ayıklama günlüğü 'nün gerekenden uzun süre **etkin bırakmayın.** Günlüğün boyutu sınırlı değil. Hata ayıklama günlüğünün etkin bırakılması, kullanılabilir disk alanını tüketebilir ve sunucu veya App Service 'i kilitlemez.

web.configdosyasındaki öğenin bir örneği için [web.configyapılandırma](#configuration-with-webconfig) konusuna bakın `aspNetCore` . *web.config*

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a>Proxy yapılandırması HTTP protokolünü ve eşleştirme belirtecini kullanır

*Yalnızca işlem dışı barındırma için geçerlidir.*

ASP.NET Core modülü ve Kestrel arasında oluşturulan ara sunucu HTTP protokolünü kullanır. Modül ve Kestrel arasındaki trafiği sunucu dışı bir konumdan bırakırken gizlice dinleme riski yoktur.

Eşleştirme belirteci, Kestrel tarafından alınan isteklerin IIS tarafından proxy aldığından ve başka bir kaynaktan gelmediğinden emin olmak için kullanılır. Eşleştirme belirteci oluşturulur ve modül tarafından bir ortam değişkenine () ayarlanır `ASPNETCORE_TOKEN` . Eşleştirme belirteci, her proxy istek için de bir üst bilgi ( `MS-ASPNETCORE-TOKEN` ) olarak ayarlanır. IIS ara yazılımı, eşleştirme belirteci üstbilgi değerinin ortam değişkeni değeriyle eşleşip eşleşmediğini doğrulamak için aldığı her isteği denetler. Belirteç değerleri uyuşmadıysa, istek günlüğe kaydedilir ve reddedilir. Eşleştirme belirteci ortam değişkeni ve modülle Kestrel arasındaki trafik, sunucu dışında bir konumdan erişilebilir değildir. Eşleştirme belirteç değerini bilmeden, bir saldırgan IIS ara yazılımı 'ndaki denetimi atlayan istekleri gönderemez.

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a>IIS paylaşılan yapılandırmasıyla ASP.NET Core modülü

ASP.NET Core modülü yükleyicisi, **TrustedInstaller** hesabının ayrıcalıklarıyla çalışır. Yerel sistem hesabı, IIS paylaşılan Yapılandırması tarafından kullanılan paylaşım yolu için değiştirme iznine sahip olmadığından, yükleyici paylaşımdaki *applicationHost.config* dosyasında modül ayarlarını yapılandırmaya çalışırken bir erişim reddedildi hatası atar.

IIS yüklemesiyle aynı makinede bir IIS paylaşılan yapılandırması kullanırken, şu şekilde ayarlanan parametre ile birlikte ASP.NET Core barındırma paketi yükleyicisini çalıştırın `OPT_NO_SHARED_CONFIG_CHECK` `1` :

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

Paylaşılan yapılandırmanın yolu IIS yüklemesiyle aynı makinede olmadığında, şu adımları izleyin:

1. IIS paylaşılan yapılandırmasını devre dışı bırakın.
1. Yükleyiciyi çalıştırın.
1. Güncelleştirilmiş *applicationHost.config* dosyasını paylaşıma dışarı aktarın.
1. IIS paylaşılan yapılandırmasını yeniden etkinleştirin.

## <a name="module-version-and-hosting-bundle-installer-logs"></a>Modül sürümü ve barındırma paketi yükleyici günlükleri

Yüklü ASP.NET Core modülünün sürümünü öğrenmek için:

1. Barındırma sisteminde *%windir%\system32\inetsrv dizinine*gidin.
1. *aspnetcore.dll* dosyasını bulun.
1. Dosyaya sağ tıklayın ve bağlam menüsünden **Özellikler** ' i seçin.
1. **Ayrıntılar** sekmesini seçin. **Dosya sürümü** ve **ürün sürümü** , modülün yüklü sürümünü temsil eder.

Modülün barındırma paketi yükleyici günlükleri *C: \\ Users \\ % UserName% \\ AppData \\ Local \\ Temp*konumunda bulunur. Dosya *dd_DotNetCoreWinSvrHosting__ \<timestamp> _000_AspNetCoreModule_x64. log*olarak adlandırılmıştır.

## <a name="module-schema-and-configuration-file-locations"></a>Modül, şema ve yapılandırma dosyası konumları

### <a name="module"></a>Modül

**IIS (X86/AMD64):**

* % windir% \System32\inetsrv\aspnetcore.dll

* % windir% \SysWOW64\inetsrv\aspnetcore.dll

* %ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll

* % ProgramFiles (x86)% \ ııs\ ASP.NET Core Module\V2\aspnetcorev2.dll

**IIS Express (X86/AMD64):**

* %ProgramFiles%\IIS Express\aspnetcore.dll

* % ProgramFiles (x86)% \ IIS Express\aspnetcore.dll

* %ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll

* % ProgramFiles (x86)% \ IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll

### <a name="schema"></a>Şema

**IIS**

* % windir% \System32\inetsrv\config\schema\aspnetcore_schema.xml

* % windir% \System32\inetsrv\config\schema\aspnetcore_schema_v2.xml

**IIS Express**

* %ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml

* %ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml

### <a name="configuration"></a>Yapılandırma

**IIS**

* % windir% \System32\inetsrv\config\applicationHost.config

**IIS Express**

* Visual Studio: {APPLICATION ROOT} \\.vs\config\applicationHost.config

* *iisexpress.exe* CLı:% USERPROFILE% \Documents\IISExpress\config\applicationhost.config

Dosyalar, *applicationHost.config* dosyasında *aspnetcore* ' u arayarak bulunabilir.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

ASP.NET Core modülü, Web isteklerini arka uca ASP.NET Core uygulamalarına iletmek için IIS ardışık düzenine takılan yerel bir IIS modülüdür.

Desteklenen Windows sürümleri:

* Windows 7 veya üzeri
* Windows Server 2008 R2 veya sonraki sürümü

Modül yalnızca Kestrel ile birlikte kullanılabilir. Modül [HTTP.sys](xref:fundamentals/servers/httpsys)uyumsuz.

ASP.NET Core uygulamalar IIS çalışan işleminden ayrı bir işlemde çalıştığından, modül işlem yönetimini de işler. Modül, ilk istek ulaştığında ASP.NET Core App işlemini başlatır ve kilitlenirse uygulamayı yeniden başlatır. Bu aslında, [Windows Işlem etkinleştirme hizmeti (was)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was)tarafından yönetilen IIS 'de işlem içinde çalışan ASP.NET 4. x uygulamaları ile görüldüğü aynı davranıştır.

Aşağıdaki diyagramda IIS, ASP.NET Core modülü ve bir uygulama arasındaki ilişki gösterilmektedir:

![ASP.NET Core Modülü](aspnet-core-module/_static/ancm-outofprocess.png)

İstekler Web 'den çekirdek modu HTTP.sys sürücüsüne ulaşır. Sürücü, istekleri Web sitesinin yapılandırılmış bağlantı noktasında IIS 'ye yönlendirir, genellikle 80 (HTTP) veya 443 (HTTPS). Modül, 80 veya 443 numaralı bağlantı noktası olmayan uygulama için rastgele bir bağlantı noktasında istekleri Kestrel 'e iletir.

Modül, başlangıç sırasında bir ortam değişkeni aracılığıyla bağlantı noktasını belirtir ve [IIS tümleştirme ara yazılımı](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) sunucuyu dinleyecek şekilde yapılandırır `http://localhost:{port}` . Ek denetimler gerçekleştirilir ve modülünden kaynaklanmayan istekler reddedilir. Modül HTTPS iletmeyi desteklemez, bu nedenle istekler HTTPS üzerinden IIS tarafından alınsa bile HTTP üzerinden iletilir.

Kestrel, isteği modülden başlattıktan sonra, istek ASP.NET Core ara yazılım ardışık düzenine gönderilir. Ara yazılım ardışık düzeni isteği işler ve `HttpContext` uygulamanın mantığına bir örnek olarak geçirir. IIS tümleştirmesi tarafından eklenen ara yazılım, isteği Kestrel iletmek için düzen, uzak IP ve pathbase 'i hesaba göre güncelleştirir. Uygulamanın yanıtı IIS 'e geri geçirilir ve bu, isteği başlatan HTTP istemcisine geri gönderilir.

Windows kimlik doğrulaması gibi birçok yerel modül etkin kalır. ASP.NET Core modülüyle etkin IIS modülleri hakkında daha fazla bilgi edinmek için bkz <xref:host-and-deploy/iis/modules> ..

ASP.NET Core modülü de şunları yapabilir:

* Çalışan işlem için ortam değişkenlerini ayarlayın.
* Başlatma sorunlarını gidermek için stdout çıkışını dosya depolama alanına kaydedin.
* Windows kimlik doğrulama belirteçlerini ilet.

## <a name="how-to-install-and-use-the-aspnet-core-module"></a>ASP.NET Core modülünü yüklemek ve kullanmak

ASP.NET Core modülünün nasıl yükleneceğine ilişkin yönergeler için bkz. [.NET Core barındırma paketi 'Ni yüklemek](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).

## <a name="configuration-with-webconfig"></a>web.config ile yapılandırma

ASP.NET Core modülü, `aspNetCore` `system.webServer` sitenin *web.config* dosyasındaki düğümünün bölümüyle yapılandırılır.

Aşağıdaki *web.config* dosyası [çerçeveye bağlı bir dağıtım](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) Için yayımlanır ve ASP.NET Core modülünü site isteklerini işleyecek şekilde yapılandırır:

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

Aşağıdaki *web.config* , [kendinden bağımsız bir dağıtım](/dotnet/articles/core/deploying/#self-contained-deployments-scd)için yayımlanır:

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

Bir uygulama [Azure App Service](https://azure.microsoft.com/services/app-service/)dağıtıldığında, `stdoutLogFile` yol olarak ayarlanır `\\?\%home%\LogFiles\stdout` . Yol, stdout günlüklerini hizmet tarafından otomatik olarak oluşturulan bir konum olan *LogFiles* klasörüne kaydeder.

IIS alt uygulama yapılandırması hakkında bilgi için bkz <xref:host-and-deploy/iis/index#sub-applications> ..

### <a name="attributes-of-the-aspnetcore-element"></a>AspNetCore öğesinin öznitelikleri

| Öznitelik | Açıklama | Varsayılan |
| --------- | ----------- | :-----: |
| `arguments` | <p>İsteğe bağlı dize özniteliği.</p><p>**ProcessPath**içinde belirtilen yürütülebilir dosya için bağımsız değişkenler.</p>| |
| `disableStartUpErrorPage` | <p>İsteğe bağlı Boolean özniteliği.</p><p>Doğru ise, **502,5-Işlem hatası** sayfası bastırılır ve *web.config* yapılandırılan 502 durum kodu sayfası önceliklidir.</p> | `false` |
| `forwardWindowsAuthToken` | <p>İsteğe bağlı Boolean özniteliği.</p><p>True ise belirteç, istek başına ' MS-ASPNETCORE-WıNAUTHTOKEN ' üst bilgisi olarak% ASPNETCORE_PORT% üzerinde dinleme yapan alt işleme iletilir. Bu, istek başına bu belirteçte CloseHandle çağırma işleminin sorumluluğundadır.</p> | `true` |
| `processesPerApplication` | <p>İsteğe bağlı tamsayı özniteliği.</p><p>**ProcessPath** ayarında belirtilen işlemin örnek sayısını, uygulama başına bir şekilde işleyecek şekilde belirtir.</p><p>Ayar `processesPerApplication` önerilmez. Bu öznitelik gelecek bir sürümde kaldırılacak.</p> | Varsayılanını`1`<br>Min`1`<br>Biçimlendir`100` |
| `processPath` | <p>Gerekli dize özniteliği.</p><p>HTTP isteklerini dinleyen bir işlemi başlatan yürütülebilir dosyanın yolu. Göreli yollar desteklenir. Yol ile başlıyorsa `.` , yol site köküne göreli olarak kabul edilir.</p> | |
| `rapidFailsPerMinute` | <p>İsteğe bağlı tamsayı özniteliği.</p><p>**ProcessPath** içinde belirtilen işleme dakika başına kilitlenme için izin verilen sayıyı belirtir. Bu sınır aşılırsa modül, dakika geri kalanı için işlemi başlatmayı durduruyor.</p> | Varsayılanını`10`<br>Min`0`<br>Biçimlendir`100` |
| `requestTimeout` | <p>İsteğe bağlı TimeSpan özniteliği.</p><p>ASP.NET Core modülünün% ASPNETCORE_PORT% üzerinde dinleme işleminden yanıt beklediği süreyi belirtir.</p><p>ASP.NET Core 2,1 veya üzeri sürümü ile birlikte gelen ASP.NET Core modülünün sürümlerinde, `requestTimeout` saat, dakika ve saniye cinsinden belirtilir.</p> | Varsayılanını`00:02:00`<br>Min`00:00:00`<br>Biçimlendir`360:00:00` |
| `shutdownTimeLimit` | <p>İsteğe bağlı tamsayı özniteliği.</p><p>*app_offline.htm* dosyası algılandığında, modülün yürütülebilir dosyanın düzgün şekilde kapatılmasını beklediği saniye cinsinden süre.</p> | Varsayılanını`10`<br>Min`0`<br>Biçimlendir`600` |
| `startupTimeLimit` | <p>İsteğe bağlı tamsayı özniteliği.</p><p>Modülün, bağlantı noktasında dinleme yapan bir işlemin başlamasını bekleyeceği saniye cinsinden süre. Bu süre sınırı aşılırsa, modül işlemi bu işlemden sonra da bir kez gider. Modül, yeni bir istek aldığında işlemi yeniden başlatmayı dener ve uygulamanın son geçen dakikada **rapidFailsPerMinute** kez başlayamadığı sürece sonraki gelen isteklerde işlemi yeniden başlatmayı dener.</p><p>0 (sıfır) değeri sonsuz bir zaman aşımı olarak kabul **edilmez** .</p> | Varsayılanını`120`<br>Min`0`<br>Biçimlendir`3600` |
| `stdoutLogEnabled` | <p>İsteğe bağlı Boolean özniteliği.</p><p>True ise, **processPath** içinde belirtilen işlem için **stdout** ve **stderr** , **stdoutLogFile**içinde belirtilen dosyaya yeniden yönlendirilir.</p> | `false` |
| `stdoutLogFile` | <p>İsteğe bağlı dize özniteliği.</p><p>**ProcessPath** içinde belirtilen işlemden **stdout** ve **stderr** 'in günlüğe kaydedildiği göreli veya mutlak dosya yolunu belirtir. Göreli yollar, sitenin köküne göredir. İle başlayan tüm `.` yollar, site köküne göredir ve diğer tüm yollar mutlak yollar olarak değerlendirilir. Modülün günlük dosyasını oluşturması için yolda sunulan klasörlerin bulunması gerekir. Alt çizgi sınırlayıcılarını kullanma, bir zaman damgası, işlem KIMLIĞI ve dosya uzantısı (*. log*) **stdoutLogFile** yolunun son kesimine eklenir. `.\logs\stdout`Değer olarak sağlandıysa, bir 2/5/2018 işlem 1934 kimliği ile 19:41:32 ' de ' de kaydedildiğinde günlük *logs* dosyasında bir örnek stdout günlüğü *stdout_20180205194132_1934* kaydedilir.</p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a>Ortam değişkenlerini ayarlama

Özniteliği içindeki işlem için ortam değişkenleri belirtilebilir `processPath` . `<environmentVariable>`Bir koleksiyon öğesinin alt öğesi ile bir ortam değişkeni belirtin `<environmentVariables>` .

> [!WARNING]
> Bu bölümde ayarlanan ortam değişkenleri, aynı ada sahip sistem ortam değişkenleri ile çakışıyor. Bir ortam değişkeni hem *web.config* dosyasında hem de Windows 'un sistem düzeyinde ayarlandıysa, *web.config* dosyasındaki değer, uygulamanın başlamasını önleyen sistem ortam değişkeni değerine (örneğin, `ASPNETCORE_ENVIRONMENT: Development;Development` ) eklenir.

Aşağıdaki örnek iki ortam değişkenini ayarlar. `ASPNETCORE_ENVIRONMENT`uygulamanın ortamını olarak yapılandırır `Development` . Bir geliştirici, uygulama özel durumunda hata ayıklarken [Geliştirici özel durum sayfasını](xref:fundamentals/error-handling) yüklemeye zorlamak için bu değeri geçici olarak *web.config* dosyasında ayarlayabilir. `CONFIG_DIR`, geliştiricinin, uygulamanın yapılandırma dosyasını yüklemek için bir yol oluşturmak üzere başlangıçta değeri okuyan kodu yazdığı, Kullanıcı tanımlı ortam değişkenine bir örnektir.

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
> `ASPNETCORE_ENVIRONMENT`Ortam değişkenini yalnızca, `Development` Internet gibi güvenilmeyen ağlarla erişilebilen hazırlama ve test etme sunucularında olarak ayarlayın.

## <a name="app_offlinehtm"></a>app_offline.htm

Bir uygulamanın kök dizininde *app_offline.htm* adında bir dosya algılanırsa, ASP.NET Core modülü uygulamayı düzgün bir şekilde kapatmaya ve gelen istekleri işlemeyi durdurmaya çalışır. Uygulama, içinde tanımlanan saniye sayısından sonra hala çalışıyorsa `shutdownTimeLimit` , ASP.NET Core modülü çalışan işlemi de yok eder.

*app_offline.htm* dosyası mevcut olsa da, ASP.NET Core modülü *app_offline.htm* dosyanın içeriğini geri göndererek isteklere yanıt verir. *app_offline.htm* dosyası kaldırıldığında, sonraki istek uygulamayı başlatır.

## <a name="start-up-error-page"></a>Başlatma hatası sayfası

ASP.NET Core modülü arka uç işlemini başlatamaz veya arka uç işlemi başlatılır, ancak yapılandırılmış bağlantı noktasında dinleme başarısız olursa, *502,5-Işlem hatası* durum kodu sayfası görüntülenir. Bu sayfayı bastırın ve varsayılan IIS 502 durum kodu sayfasına dönmek için `disableStartUpErrorPage` özniteliğini kullanın. Özel hata iletilerini yapılandırma hakkında daha fazla bilgi için bkz. [http \<httpErrors> hataları ](/iis/configuration/system.webServer/httpErrors/).

![502,5 işlem hatası durum kodu sayfası](aspnet-core-module/_static/ANCM-502_5.png)

## <a name="log-creation-and-redirection"></a>Günlük oluşturma ve yeniden yönlendirme

ASP.NET Core modülü, `stdoutLogEnabled` `stdoutLogFile` öğesinin ve öznitelikleri ayarlandıysa stdout ve stderr konsol çıkışını diske yeniden yönlendirir `aspNetCore` . Yoldaki tüm klasörler, `stdoutLogFile` günlük dosyası oluşturulduğunda modül tarafından oluşturulur. Uygulama havuzunun, günlüklerin yazıldığı konuma yazma erişimi olması gerekir ( `IIS AppPool\<app_pool_name>` yazma izni sağlamak için kullanın).

İşlem geri dönüştürme/yeniden başlatma gerçekleşmediği sürece Günlükler döndürülemez. Bu, günlüklerin tükettiği disk alanını sınırlamak için barındırıcının sorumluluğundadır.

Stdout günlüğünün kullanılması yalnızca IIS 'de barındırırken veya [Visual Studio Ile IIS için geliştirme zamanı desteği](xref:host-and-deploy/iis/development-time-iis-support)kullanılırken değil, yerel olarak hata ayıklarken ve uygulamayı IIS Express ile çalıştırırken yalnızca uygulama başlatma sorunlarını gidermek için önerilir.

Genel uygulama günlüğü amaçları için stdout günlüğünü kullanmayın. ASP.NET Core uygulamasında rutin günlük kaydı için, günlük dosyası boyutunu sınırlayan ve günlükleri döndüren bir günlüğe kaydetme kitaplığı kullanın. Daha fazla bilgi için bkz. [üçüncü taraf günlüğü sağlayıcıları](xref:fundamentals/logging/index#third-party-logging-providers).

Günlük dosyası oluşturulduğunda zaman damgası ve dosya uzantısı otomatik olarak eklenir. Günlük dosyası adı, alt *.log* `stdoutLogFile` çizgi ile ayrılmış yolun (genellikle *stdout*) son KESIMINE zaman damgası, işlem kimliği ve dosya uzantısı (. log) eklenerek oluşur. `stdoutLogFile`Yol *stdout*ile sonlanıyorsa, 1934 ' de 19:42:32 2/5/2018 ' de oluşturulan PID 'sine sahip bir uygulama için günlük *stdout_20180205194132_1934*dosya adı vardır.

Aşağıdaki örnek `aspNetCore` öğesi, göreli yoldaki stdout günlüğünü yapılandırır `.\log\` . AppPool Kullanıcı kimliğinin, belirtilen yola yazma izni olduğunu doğrulayın.

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout">
</aspNetCore>
```

Azure App Service dağıtım için bir uygulama yayımlarken, Web SDK `stdoutLogFile` değeri olarak ayarlanır `\\?\%home%\LogFiles\stdout` . `%home`Ortam değişkeni, Azure App Service tarafından barındırılan uygulamalar için önceden tanımlanmıştır.

Günlüğe kaydetme filtresi kuralları oluşturmak için ASP.NET Core günlük belgelerinin [yapılandırma](xref:fundamentals/logging/index#log-filtering) ve [günlük filtreleme](xref:fundamentals/logging/index#log-filtering) bölümlerine bakın.

Yol biçimleri hakkında daha fazla bilgi için bkz. [Windows sistemlerinde dosya yolu biçimleri](/dotnet/standard/io/file-path-formats).

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a>Proxy yapılandırması HTTP protokolünü ve eşleştirme belirtecini kullanır

ASP.NET Core modülü ve Kestrel arasında oluşturulan ara sunucu HTTP protokolünü kullanır. Modül ve Kestrel arasındaki trafiği sunucu dışı bir konumdan bırakırken gizlice dinleme riski yoktur.

Eşleştirme belirteci, Kestrel tarafından alınan isteklerin IIS tarafından proxy aldığından ve başka bir kaynaktan gelmediğinden emin olmak için kullanılır. Eşleştirme belirteci oluşturulur ve modül tarafından bir ortam değişkenine () ayarlanır `ASPNETCORE_TOKEN` . Eşleştirme belirteci, her proxy istek için de bir üst bilgi ( `MS-ASPNETCORE-TOKEN` ) olarak ayarlanır. IIS ara yazılımı, eşleştirme belirteci üstbilgi değerinin ortam değişkeni değeriyle eşleşip eşleşmediğini doğrulamak için aldığı her isteği denetler. Belirteç değerleri uyuşmadıysa, istek günlüğe kaydedilir ve reddedilir. Eşleştirme belirteci ortam değişkeni ve modülle Kestrel arasındaki trafik, sunucu dışında bir konumdan erişilebilir değildir. Eşleştirme belirteç değerini bilmeden, bir saldırgan IIS ara yazılımı 'ndaki denetimi atlayan istekleri gönderemez.

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a>IIS paylaşılan yapılandırmasıyla ASP.NET Core modülü

ASP.NET Core modülü yükleyicisi, **TrustedInstaller** hesabının ayrıcalıklarıyla çalışır. Yerel sistem hesabı, IIS paylaşılan Yapılandırması tarafından kullanılan paylaşım yolu için değiştirme iznine sahip olmadığından, yükleyici paylaşımdaki *applicationHost.config* dosyasında modül ayarlarını yapılandırmaya çalışırken bir erişim reddedildi hatası atar.

IIS paylaşılan yapılandırması kullanırken, şu adımları izleyin:

1. IIS paylaşılan yapılandırmasını devre dışı bırakın.
1. Yükleyiciyi çalıştırın.
1. Güncelleştirilmiş *applicationHost.config* dosyasını paylaşıma dışarı aktarın.
1. IIS paylaşılan yapılandırmasını yeniden etkinleştirin.

## <a name="module-version-and-hosting-bundle-installer-logs"></a>Modül sürümü ve barındırma paketi yükleyici günlükleri

Yüklü ASP.NET Core modülünün sürümünü öğrenmek için:

1. Barındırma sisteminde *%windir%\system32\inetsrv dizinine*gidin.
1. *aspnetcore.dll* dosyasını bulun.
1. Dosyaya sağ tıklayın ve bağlam menüsünden **Özellikler** ' i seçin.
1. **Ayrıntılar** sekmesini seçin. **Dosya sürümü** ve **ürün sürümü** , modülün yüklü sürümünü temsil eder.

Modülün barındırma paketi yükleyici günlükleri *C: \\ Users \\ % UserName% \\ AppData \\ Local \\ Temp*konumunda bulunur. Dosya *dd_DotNetCoreWinSvrHosting__ \<timestamp> _000_AspNetCoreModule_x64. log*olarak adlandırılmıştır.

## <a name="module-schema-and-configuration-file-locations"></a>Modül, şema ve yapılandırma dosyası konumları

### <a name="module"></a>Modül

**IIS (X86/AMD64):**

* % windir% \System32\inetsrv\aspnetcore.dll

* % windir% \SysWOW64\inetsrv\aspnetcore.dll

**IIS Express (X86/AMD64):**

* %ProgramFiles%\IIS Express\aspnetcore.dll

* % ProgramFiles (x86)% \ IIS Express\aspnetcore.dll

### <a name="schema"></a>Şema

**IIS**

* % windir% \System32\inetsrv\config\schema\aspnetcore_schema.xml

**IIS Express**

* %ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml

### <a name="configuration"></a>Yapılandırma

**IIS**

* % windir% \System32\inetsrv\config\applicationHost.config

**IIS Express**

* Visual Studio: {APPLICATION ROOT} \\.vs\config\applicationHost.config

* *iisexpress.exe* CLı:% USERPROFILE% \Documents\IISExpress\config\applicationhost.config

Dosyalar, *applicationHost.config* dosyasında *aspnetcore* ' u arayarak bulunabilir.

::: moniker-end

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/azure-apps/index>
* [ASP.NET Core modülü başvuru kaynağı (ana dal)](https://github.com/dotnet/aspnetcore/tree/master/src/Servers/IIS/AspNetCoreModuleV2): belirli bir sürümü seçmek için **dal** açılan listesini kullanın (örneğin, `release/3.1` ).
* <xref:host-and-deploy/iis/modules>
