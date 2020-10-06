---
title: web.config dosyası
author: rick-anderson
description: web.config dosyasının içinde nelerin olduğunu ve farklı ASP.NET Core modül seçeneklerinin nasıl yapılandırılacağını öğrenin.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/iis/web-config
ms.openlocfilehash: 4d7305f7184745b66c5de6c86b907d419183cb3f
ms.sourcegitcommit: d60bfd52bfb559e805abd654b87a2a0c7eb69cf8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/06/2020
ms.locfileid: "91755308"
---
# <a name="webconfig-file"></a>`web.config` dosyasýný

, IIS `web.config` tarafından okunan bir dosyadır ve IIS ile barındırılan bir uygulamayı yapılandırmak için [ASP.NET Core modülüdür](xref:host-and-deploy/aspnet-core-module) .

## <a name="webconfig-file-location"></a>`web.config` dosya konumu

[ASP.NET Core modülünü](xref:host-and-deploy/aspnet-core-module) doğru bir şekilde ayarlamak için, `web.config` dağıtılan uygulamanın [içerik kök](xref:fundamentals/index#content-root) yolunda (genellikle uygulama temel yolu), dosyanın mevcut olması gerekir. Bu, IIS 'ye sunulan Web sitesi fiziksel yoluyla aynı konumdadır. `web.config`Web dağıtımı kullanarak birden çok uygulama yayımlamayı etkinleştirmek için uygulamanın kökünde dosya gereklidir.

Gizli dosyalar, uygulamanın fiziksel yolunda `{ASSEMBLY}.runtimeconfig.json` `{ASSEMBLY}.xml` (XML belge açıklamaları) bulunur ve `{ASSEMBLY}.deps.json` burada yer tutucunun `{ASSEMBLY}` derleme adıdır. `web.config`Dosya mevcut olduğunda ve site normal olarak başlatıldığında, ISTENIRSE IIS bu hassas dosyaları sunmaz. `web.config`Dosya eksikse, yanlış şekilde adlandırılmış veya site normal başlatma için YAPıLANDıRıMıŞSA IIS hassas dosyalara genel olarak sunabilir.

**`web.config`Dosyanın her zaman dağıtımda mevcut olması, doğru şekilde adlandırılması ve siteyi normal başlangıç için yapılandırabiliyor olması gerekir. `web.config`Dosyayı bir üretim dağıtımından hiçbir şekilde kaldırmayın.**

Projede bir `web.config` Dosya yoksa, dosya doğru şekilde oluşturulur `processPath` ve `arguments` ASP.NET Core modülünü yapılandırıp [yayımlanan çıktıya](xref:host-and-deploy/directory-structure)taşınır.

Projede bir `web.config` dosya varsa, dosya doğru şekilde dönüştürülür `processPath` ve `arguments` ASP.NET Core modülünü yapılandırıp yayımlanan çıktıya taşınır. Dönüştürme, dosyadaki IIS yapılandırma ayarlarını değiştirmez.

`web.config`Dosya, ETKIN IIS modüllerini denetleyen ek IIS yapılandırma ayarları sağlayabilir. ASP.NET Core uygulamalarla istekleri işleyebilen IIS modülleri hakkında daha fazla bilgi için bkz. [IIS modules](xref:host-and-deploy/iis/modules) konusu.

Proje yayımlandığında dosyanın oluşturulması, dönüştürülmesi ve yayımlanması `web.config` bir MSBuild hedefi () tarafından işlenir `_TransformWebConfig` . Bu hedef, Web SDK hedeflerinde () bulunur `Microsoft.NET.Sdk.Web` . SDK proje dosyasının en üstünde ayarlanır:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

Web SDK 'sının dosyayı dönüştürülmesini engellemek için `web.config` , `<IsTransformWebConfigDisabled>` Proje dosyasındaki özelliğini kullanın:

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

Web SDK 'sının dosyayı dönüştürmesiyle devre dışı bırakılırken, `processPath` ve `arguments` geliştiricisi tarafından el ile ayarlanması gerekir. Daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module>.

## <a name="configuration-of-aspnet-core-module-with-webconfig"></a>ASP.NET Core modülün yapılandırması `web.config`

ASP.NET Core modülü, `aspNetCore` `system.webServer` sitenin dosyasındaki düğümünün bölümüyle yapılandırılır `web.config` .

Aşağıdaki `web.config` Dosya, [çerçeveye bağlı bir dağıtım](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) için yayımlanır ve ASP.NET Core modülünü site isteklerini işleyecek şekilde yapılandırır:

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

Aşağıdakiler, `web.config` [kendinden bağımsız bir dağıtım](/dotnet/articles/core/deploying/#self-contained-deployments-scd)için yayımlanır:

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

<xref:System.Configuration.SectionInformation.InheritInChildApplications%2A>Özelliği, `false` öğesi içinde belirtilen ayarların [`<location>`](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) uygulamanın bir alt dizininde bulunan uygulamalar tarafından devralınmadığını belirtmek için olarak ayarlanır.

Bir uygulama [Azure App Service](https://azure.microsoft.com/services/app-service/)dağıtıldığında, `stdoutLogFile` yol olarak ayarlanır `\\?\%home%\LogFiles\stdout` . Yol, stdout günlüklerini `LogFiles` , hizmet tarafından otomatik olarak oluşturulan bir konum olan klasöre kaydeder.

IIS alt uygulama yapılandırması hakkında bilgi için bkz <xref:host-and-deploy/iis/index#sub-applications> ..

### <a name="attributes-of-the-aspnetcore-element"></a>`aspNetCore`Öğenin öznitelikleri

| Öznitelik | Açıklama | Varsayılan |
| --------- | ----------- | :-----: |
| `arguments` | <p>İsteğe bağlı dize özniteliği.</p><p>İçinde belirtilen yürütülebilirin bağımsız değişkenleri `processPath` .</p> | |
| `disableStartUpErrorPage` | <p>İsteğe bağlı Boolean özniteliği.</p><p>Doğru ise, **502,5-Işlem hatası** sayfası bastırılır ve içinde yapılandırılan 502 durum kodu sayfası `web.config` önceliklidir.</p> | `false` |
| `forwardWindowsAuthToken` | <p>İsteğe bağlı Boolean özniteliği.</p><p>True ise, belirteç `%ASPNETCORE_PORT%` istek başına ' MS-ASPNETCORE-WıNAUTHTOKEN ' üst bilgisi olarak dinleyen alt işleme iletilir. Bu, istek başına bu belirteçte CloseHandle çağırma işleminin sorumluluğundadır.</p> | `true` |
| `hostingModel` | <p>İsteğe bağlı dize özniteliği.</p><p>Barındırma modelini işlem içi ( `InProcess` / `inprocess` ) veya işlem dışı () olarak belirtir `OutOfProcess` / `outofprocess` .</p> | `InProcess`<br>`inprocess` |
| `processesPerApplication` | <p>İsteğe bağlı tamsayı özniteliği.</p><p>Ayarda belirtilen işlemin örnek sayısını belirtir `processPath` . Bu ayar, uygulama başına bir işlem yapılabilir.</p><p>&dagger;İşlem içi barındırma için, değer ile sınırlıdır `1` .</p><p>Ayar `processesPerApplication` önerilmez. Bu öznitelik gelecek bir sürümde kaldırılacak.</p> | Varsayılanını `1`<br>Min `1`<br>Biçimlendir `100`&dagger; |
| `processPath` | <p>Gerekli dize özniteliği.</p><p>HTTP isteklerini dinleyen bir işlemi başlatan yürütülebilir dosyanın yolu. Göreli yollar desteklenir. Yol ile başlıyorsa `.` , yol site köküne göreli olarak kabul edilir.</p> | |
| `rapidFailsPerMinute` | <p>İsteğe bağlı tamsayı özniteliği.</p><p>İçinde belirtilen işlemin `processPath` dakika başına kilitlenme için izin verileceğini belirtir. Bu sınır aşılırsa modül, dakika geri kalanı için işlemi başlatmayı durduruyor.</p><p>İşlem içi barındırma ile desteklenmez.</p> | Varsayılanını `10`<br>Min `0`<br>Biçimlendir `100` |
| `requestTimeout` | <p>İsteğe bağlı TimeSpan özniteliği.</p><p>ASP.NET Core modülünün% ASPNETCORE_PORT% üzerinde dinleme işleminden yanıt beklediği süreyi belirtir.</p><p>ASP.NET Core 2,1 veya üzeri sürümü ile birlikte gelen ASP.NET Core modülünün sürümlerinde, `requestTimeout` saat, dakika ve saniye cinsinden belirtilir.</p><p>İşlem içi barındırma için uygulanmaz. İşlem içi barındırma için modül, uygulamanın isteği işlemesini bekler.</p><p>Dizenin dakika ve saniye kesimleri için geçerli değerler 0-59 aralığındadır. `60`Dakika veya saniye değerinde öğesinin kullanımı, *500 Iç sunucu hatasına*neden olur.</p> | Varsayılanını `00:02:00`<br>Min `00:00:00`<br>Biçimlendir `360:00:00` |
| `shutdownTimeLimit` | <p>İsteğe bağlı tamsayı özniteliği.</p><p>Dosya algılandığında, modülün yürütülebilir dosyanın düzgün şekilde kapatılmasını beklediği saniye cinsinden süre `app_offline.htm` .</p> | Varsayılanını `10`<br>Min `0`<br>Biçimlendir `600` |
| `startupTimeLimit` | <p>İsteğe bağlı tamsayı özniteliği.</p><p>Modülün, bağlantı noktasında dinleme yapan bir işlemin başlamasını bekleyeceği saniye cinsinden süre. Bu süre sınırı aşılırsa, modül işlemi bu işlemden sonra da bir kez gider.</p><p>*İşlem içi*barındırma sırasında: **işlem yeniden başlatılmaz** ve ayarı kullanılmaz **not** `rapidFailsPerMinute` .</p><p>*İşlem dışı*barındırma sırasında, modül yeni bir istek aldığında işlemi yeniden başlatmayı dener ve uygulamanın `rapidFailsPerMinute` son geçen dakikada bir kez başlamamaya devam etmediği sürece sonraki gelen isteklerde işlemi yeniden başlatmaya çalışır.</p><p>0 (sıfır) değeri sonsuz bir zaman aşımı olarak kabul **edilmez** .</p> | Varsayılanını `120`<br>Min `0`<br>Biçimlendir `3600` |
| `stdoutLogEnabled` | <p>İsteğe bağlı Boolean özniteliği.</p><p>True ise `stdout` ve `stderr` içinde belirtilen işlem için `processPath` belirtilen dosyaya yeniden yönlendirilir `stdoutLogFile` .</p> | `false` |
| `stdoutLogFile` | <p>İsteğe bağlı dize özniteliği.</p><p>`stdout` `stderr` İçinde belirtilen işlemden ve bu işlemin günlüğe kaydedildiği göreli veya mutlak dosya yolunu belirtir `processPath` . Göreli yollar, sitenin köküne göredir. İle başlayan tüm `.` yollar, site köküne göredir ve diğer tüm yollar mutlak yollar olarak değerlendirilir. Yolda sunulan klasörler, günlük dosyası oluşturulduğunda modül tarafından oluşturulur. Alt çizgi sınırlayıcılarını kullanma, zaman damgası, işlem KIMLIĞI ve dosya uzantısı ( `.log` ) yolun son kesimine eklenir `stdoutLogFile` . `.\logs\stdout`Değer olarak sağlandıysa, `stdout_20180205194132_1934.log` `logs` 2/5/2018 işlem 1934 kimliği ile 19:41:32 ' de kaydedildiğinde, bir örnek stdout günlüğü klasöre kaydedilir.</p> | `aspnetcore-stdout` |

### <a name="set-environment-variables"></a>Ortam değişkenlerini belirleme

Özniteliği içindeki işlem için ortam değişkenleri belirtilebilir `processPath` . `<environmentVariable>`Bir koleksiyon öğesinin alt öğesi ile bir ortam değişkeni belirtin `<environmentVariables>` . Bu bölümde ayarlanan ortam değişkenleri, sistem ortamı değişkenlerine göre önceliklidir.

Aşağıdaki örnek ' de iki ortam değişkenini ayarlar `web.config` . `ASPNETCORE_ENVIRONMENT` uygulamanın ortamını olarak yapılandırır `Development` . Bir geliştirici `web.config` , uygulama özel durumunda hata ayıklarken [Geliştirici özel durum sayfasını](xref:fundamentals/error-handling) yüklemeye zorlamak için dosyada bu değeri geçici olarak ayarlayabilir. `CONFIG_DIR` , geliştiricinin, uygulamanın yapılandırma dosyasını yüklemek için bir yol oluşturmak üzere başlangıçta değeri okuyan kodu yazdığı, Kullanıcı tanımlı ortam değişkenine bir örnektir.

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
> Ortamın doğrudan ' de ayarlanmasına alternatif olarak `web.config` , `<EnvironmentName>` [Yayımlama profili ( `.pubxml` )](xref:host-and-deploy/visual-studio-publish-profiles) veya proje dosyasına özelliği dahil edilir. Bu yaklaşım, proje yayımlandığında içindeki ortamı ayarlar `web.config` :
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> `ASPNETCORE_ENVIRONMENT`Ortam değişkenini yalnızca, `Development` Internet gibi güvenilmeyen ağlarla erişilebilen hazırlama ve test etme sunucularında olarak ayarlayın.

## <a name="configuration-of-iis-with-webconfig"></a>İle IIS yapılandırması `web.config`

IIS yapılandırması, `<system.webServer>` `web.config` ASP.NET Core modülüyle ASP.NET Core uygulamalar için IŞLEVSEL olan IIS senaryoları için bölümünden etkilenir. Örneğin, IIS yapılandırması dinamik sıkıştırma için işlevseldir. IIS sunucu düzeyinde dinamik sıkıştırma kullanmak üzere yapılandırıldıysa, `<urlCompression>` uygulama `web.config` dosyasındaki öğesi ASP.NET Core bir uygulama için devre dışı bırakabilir.

Daha fazla bilgi edinmek için aşağıdaki kaynaklara bakın:

* [İçin yapılandırma başvurusu `<system.webServer>`](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

Yalıtılmış uygulama havuzlarında çalışan ayrı uygulamalara yönelik ortam değişkenlerini ayarlamak için (IIS 10,0 veya üzeri için desteklenir), IIS başvuru belgelerindeki [ortam değişkenleri `<environmentVariables>` ](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) konusunun * `AppCmd.exe` komut* bölümüne bakın.

## <a name="configuration-sections-of-webconfig"></a>Yapılandırma bölümleri `web.config`

İçindeki ASP.NET 4. x uygulamalarının yapılandırma bölümleri `web.config` yapılandırma için ASP.NET Core uygulamalar tarafından kullanılmıyor:

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

ASP.NET Core uygulamalar diğer yapılandırma sağlayıcıları kullanılarak yapılandırılır. Daha fazla bilgi için bkz. [yapılandırma](xref:fundamentals/configuration/index).

## <a name="transform-webconfig"></a>Web.config’i dönüştürme

`web.config`Yayımlama sırasında dönüştürmeniz gerekiyorsa bkz <xref:host-and-deploy/iis/transform-webconfig> .. `web.config`Yapılandırma, profil veya ortama göre ortam değişkenlerini ayarlamak için Yayımla ' ya dönüştürmeniz gerekebilir.

## <a name="additional-resources"></a>Ek kaynaklar

* [ISS \<system.webServer>](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/iis/modules>
* <xref:host-and-deploy/iis/transform-webconfig>
