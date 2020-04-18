---
title: IIS ile Windows'da Ana Bilgisayar ASP.NET Core
author: rick-anderson
description: Windows Server Internet Bilgi Hizmetleri'nde (IIS) ASP.NET Core uygulamalarını nasıl barındırabileceğinizi öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/17/2020
uid: host-and-deploy/iis/index
ms.openlocfilehash: 77f07ba89de4449c6d13006a5fd61499cb5cdfc0
ms.sourcegitcommit: 3d07e21868dafc503530ecae2cfa18a7490b58a6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/18/2020
ms.locfileid: "81642746"
---
# <a name="host-aspnet-core-on-windows-with-iis"></a>IIS ile Windows'da Ana Bilgisayar ASP.NET Core

<!-- 

    NOTE FOR 5.0
    
    When making the 5.0 version of this topic, remove the Hosting Bundle
    direct download section from the (new) <5.0 & >2.2 version and modify 
    the text and heading for the *Earlier versions of the installer* 
    section. See the 2.2 version for an example.
    
-->

::: moniker range=">= aspnetcore-3.0"

Bir ASP.NET Core uygulamasını bir IIS sunucusuna yayımlama konusunda öğretici bir deneyim için bkz. <xref:tutorials/publish-to-iis>

[.NET Core Hosting Paketini yükleyin](#install-the-net-core-hosting-bundle)

## <a name="supported-operating-systems"></a>Desteklenen işletim sistemleri

Aşağıdaki işletim sistemleri desteklenmektedir:

* Windows 7 veya sonraki
* Windows Server 2012 R2 veya sonrası

[HTTP.sys sunucusu](xref:fundamentals/servers/httpsys) (eski adıyla WebListener) IIS ile ters proxy yapılandırmasında çalışmaz. [Kerkenez sunucusunu](xref:fundamentals/servers/kestrel)kullanın.

Azure'da barındırma hakkında <xref:host-and-deploy/azure-apps/index>daha fazla bilgi için bkz.

Sorun giderme kılavuzu <xref:test/troubleshoot>için bkz.

## <a name="supported-platforms"></a>Desteklenen platformlar

32 bit (x86) veya 64 bit (x64) dağıtım için yayınlanan uygulamalar desteklenir. Uygulama olmadıkça 32 bit (x86) .NET Core SDK içeren bir 32 bit uygulaması dağıtın:

* 64 bit uygulama için kullanılabilen daha büyük sanal bellek adresi alanı gerektirir.
* Büyük IIS yığın boyutunu gerektirir.
* 64 bit yerel bağımlılıkları vardır.

64 bit lik bir uygulama yayınlamak için 64 bit (x64) .NET Core SDK kullanın. Ana bilgisayar sisteminde 64 bit çalışma süresi bulunmalıdır.

## <a name="hosting-models"></a>Barındırma modelleri

### <a name="in-process-hosting-model"></a>Süreç içi barındırma modeli

ASP.NET Core uygulaması, süreç içi barındırmayı kullanarak IIS alt işlemiyle aynı işlemde çalışır. İstekler giden ağ trafiğini aynı makineye geri döndüren bir ağ arabirimi olan döngü bağdaştırıcısı üzerinden yakınlamadığından, işlem dışı barındırma işlemi barındırma üzerinde gelişmiş performans sağlar. IIS, Windows Process [Etkinleştirme Hizmeti (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was)ile işlem yönetimini işler.

[ASP.NET Çekirdek Modülü](xref:host-and-deploy/aspnet-core-module):

* Uygulama başlatma gerçekleştirir.
  * [CoreCLR](/dotnet/standard/glossary#coreclr)yükler.
  * Aramalar `Program.Main`.
* IIS yerel isteğinin kullanım ömrünü yönetir.

İşlem içi barındırma modeli,.NET Framework'ASP.NET hedefleyen ASP.NET Core uygulamaları için desteklenmez.

Aşağıdaki diyagram, IIS, ASP.NET Çekirdek Modülü ve işlem içinde barındırılan bir uygulama arasındaki ilişkiyi göstermektedir:

![süreç içi barındırma senaryosunda ASP.NET Çekirdek Modülü](index/_static/ancm-inprocess.png)

Web'den çekirdek modu HTTP.sys sürücüsüne bir istek gelir. Sürücü, genellikle 80 (HTTP) veya 443 (HTTPS) olarak web sitesinin yapılandırılmış bağlantı noktasında yerel isteği IIS'ye yönlendirir. ASP.NET Çekirdek Modülü yerel isteği alır ve IIS`IISHttpServer`HTTP Server 'a iletir ( ). IIS HTTP Server, IIS için isteği yerelden yönetilene dönüştüren bir işlem sunucusu uygulamasıdır.

IIS HTTP Server isteği işledikten sonra, istek ASP.NET Core ara yazılım boru hattına itilir. Ara yazılım ardışık alanı isteği işler ve `HttpContext` uygulamanın mantığına örnek olarak aktarın. Uygulamanın yanıtı IIS HTTP Server üzerinden IIS'e geri aktarılır. IIS yanıtı isteği başlatan istemciye gönderir.

Süreç içi barındırma mevcut uygulamalar için tercih edilir, ancak [dotnet yeni](/dotnet/core/tools/dotnet-new) şablonlar tüm IIS ve IIS Express senaryoları için süreç içi barındırma modeli varsayılan.

`CreateDefaultBuilder`[CoreCLR](/dotnet/standard/glossary#coreclr) önyükleme <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> ve IIS işçi sürecinin içinde uygulama barındırmak için yöntemi arayarak bir <xref:Microsoft.AspNetCore.Hosting.Server.IServer> örnek ekler (*w3wp.exe* veya *iisexpress.exe).* Performans testleri, bir .NET Core uygulamasının iş aşamasında barındırılmaya karşı, uygulamanın işlem dışı barındırma ve [Kerkenez](xref:fundamentals/servers/kestrel) sunucusuna proxy isteklerinin proxy edilmesiyle karşılaştırıldığında önemli ölçüde daha yüksek istek verimi sağladığını gösterir.

> [!NOTE]
> Yürütülebilir tek bir dosya olarak yayınlanan uygulamalar, işlem içi barındırma modeli tarafından yüklenebilir.

### <a name="out-of-process-hosting-model"></a>İşlem dışı barındırma modeli

Core uygulamaları ASP.NET IIS alt işleminden ayrı bir işlemde çalıştığı için, ASP.NET Çekirdek Modülü işlem yönetimini işler. Modül, ilk istek geldiğinde ASP.NET Core uygulaması için süreci başlatır ve kapanDığında veya çökerse uygulamayı yeniden başlatır. Bu aslında [Windows Process Etkinleştirme Hizmeti (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was)tarafından yönetilen süreç içinde çalışan uygulamalarda görülen aynı davranıştır.

Aşağıdaki diyagram, IIS, ASP.NET Çekirdek Modülü ve işlem dışı barındırılan bir uygulama arasındaki ilişkiyi göstermektedir:

![ASP.NET Çekirdek Modülü, işlem dışı barındırma senaryosunda](index/_static/ancm-outofprocess.png)

İstekler web'den çekirdek modu HTTP.sys sürücüsüne gelir. Sürücü, istekleri genellikle 80 (HTTP) veya 443 (HTTPS) olarak web sitesinin yapılandırılmış bağlantı noktasında IIS'ye yönlendirir. Modül, 80 veya 443 bağlantı noktası olmayan uygulama için rastgele bir bağlantı noktası üzerindeki istekleri Kestrel'e iletiyor.

Modül, başlangıçta bir ortam değişkeni üzerinden bağlantı <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> noktasını belirtir ve uzantı `http://localhost:{PORT}`sunucuyu dinleyecek şekilde yapılandırır. Ek denetimler gerçekleştirilir ve modülden kaynaklanmıyor istekleri reddedilir. Modül HTTPS iletmesini desteklemez, bu nedenle iIS tarafından HTTPS üzerinden alınsa bile istekler HTTP üzerinden iletilir.

Kestrel modülden isteği aldıktan sonra, istek ASP.NET Core ara yazılım boru hattına itilir. Ara yazılım ardışık alanı isteği işler ve `HttpContext` uygulamanın mantığına örnek olarak aktarın. IIS Integration tarafından eklenen ara yazılım, isteği Kestrel'e iletmek için hesap vermek üzere şemayı, uzak IP'yi ve yol tabanını güncelleştirir. Uygulamanın yanıtı IIS'ye geri aktarılır ve bu da isteği başlatan HTTP istemcisine geri iter.

ASP.NET Çekirdek Modülü yapılandırma <xref:host-and-deploy/aspnet-core-module>kılavuzu için bkz.

Barındırma hakkında daha fazla bilgi için, [ASP.NET Core'da Host'a](xref:fundamentals/index#host)bakın.

## <a name="application-configuration"></a>Uygulama yapılandırması

### <a name="enable-the-iisintegration-components"></a>IISIntegration bileşenlerini etkinleştirme

(Program.cs) içinde `CreateHostBuilder` *Program.cs*bir ev <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> sahibi kurarken, IIS entegrasyonunu etkinleştirmek için çağrı:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        ...
```

Daha fazla `CreateDefaultBuilder`bilgi <xref:fundamentals/host/generic-host#default-builder-settings>için bkz.

### <a name="iis-options"></a>IIS seçenekleri

**Süreç içi barındırma modeli**

IIS Server seçeneklerini yapılandırmak için, <xref:Microsoft.AspNetCore.Builder.IISServerOptions> 'de için bir hizmet yapılandırması <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>ekleyin. Aşağıdaki örnek Otomatik Kimlik Doğrulamasını devre dışı bırakmaz:

```csharp
services.Configure<IISServerOptions>(options => 
{
    options.AutomaticAuthentication = false;
});
```

| Seçenek                         | Varsayılan | Ayar |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | IIS `true`Server, Windows `HttpContext.User` Kimlik Doğrulama sı tarafından kimlik doğrulaması yapılandırışlarını [ayarlarsa.](xref:security/authentication/windowsauth) Sunucu `false`yalnızca bir kimlik sağlar `HttpContext.User` ve açıkça tarafından istendiğinde zorluklara `AuthenticationScheme`yanıt verir. Windows Kimlik Doğrulama'nın çalışması `AutomaticAuthentication` için IIS'de etkinleştirilmesi gerekir. Daha fazla bilgi için [Windows Kimlik Doğrulama'ya](xref:security/authentication/windowsauth)bakın. |
| `AuthenticationDisplayName`    | `null`  | Oturum açma sayfalarında kullanıcılara gösterilen görüntü adını ayarlar. |
| `AllowSynchronousIO`           | `false` | Senkron G/Ç'ye `HttpContext.Request` izin verilip `HttpContext.Response`verilmedive . |
| `MaxRequestBodySize`           | `30000000`  | Alır veya için maksimum istek `HttpRequest`gövde boyutunu ayarlar. IIS'nin kendisi, `maxAllowedContentLength` `MaxRequestBodySize` 'deki `IISServerOptions`setten önce işlenecektir. `MaxRequestBodySize` Değiştirme, `maxAllowedContentLength`..00.000.000.000.000.000 Artırmak `maxAllowedContentLength`için, daha yüksek bir değere `maxAllowedContentLength` ayarlamak için *web.config* bir giriş ekleyin. Daha fazla bilgi için [Configuration'a](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/#configuration)bakın. |

**İşlem dışı barındırma modeli**

IIS seçeneklerini yapılandırmak için, <xref:Microsoft.AspNetCore.Builder.IISOptions> için <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>bir hizmet yapılandırması ekleyin. Aşağıdaki örnek, uygulamanın doldurmasını `HttpContext.Connection.ClientCertificate`engeller:

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| Seçenek                         | Varsayılan | Ayar |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | `true`IIS [Integration Middleware,](#enable-the-iisintegration-components) `HttpContext.User` Windows Kimlik Doğrulaması tarafından kimlik doğrulaması yapılan aracı [ayarlarsa.](xref:security/authentication/windowsauth) Eğer, `false`ara yazılım sadece bir `HttpContext.User` kimlik sağlar ve açıkça tarafından talep `AuthenticationScheme`edildiğinde zorluklara yanıt verir. Windows Kimlik Doğrulama'nın çalışması `AutomaticAuthentication` için IIS'de etkinleştirilmesi gerekir. Daha fazla bilgi için [Windows Kimlik Doğrulama](xref:security/authentication/windowsauth) konusuna bakın. |
| `AuthenticationDisplayName`    | `null`  | Oturum açma sayfalarında kullanıcılara gösterilen görüntü adını ayarlar. |
| `ForwardClientCertificate`     | `true`  | İstek `true` üstbilgisi varsa, `MS-ASPNETCORE-CLIENTCERT` `HttpContext.Connection.ClientCertificate` doldurulur. |

### <a name="proxy-server-and-load-balancer-scenarios"></a>Proxy sunucusu ve yük dengeleyici senaryoları

İleriye Dönük Üstbilgileri Ara Yazılım'ı yapılandıran [IIS Tümleştirme Aracı](#enable-the-iisintegration-components)ve ASP.NET Çekirdek Modülü, düzeni (HTTP/HTTPS) ve isteğin kaynağı olduğu uzak IP adresini iletecek şekilde yapılandırılır. Ek proxy sunucuları ve yük dengeleyicileri arkasında barındırılan uygulamalar için ek yapılandırma gerekebilir. Daha fazla bilgi için proxy [sunucuları ve yük dengeleyicileri ile çalışmak için ASP.NET Core'u yapılandırın'](xref:host-and-deploy/proxy-load-balancer)a bakın.

### <a name="webconfig-file"></a>web.config dosyası

*web.config* dosyası ASP.NET [Çekirdek](xref:host-and-deploy/aspnet-core-module)Modül'ünyapılatır. *Web.config* dosyasının oluşturulması, dönüştürülmesi ve yayımlanması, proje`_TransformWebConfig`yayımlandığında bir MSBuild hedefi ( ) tarafından işlenir. Bu hedef Web SDK hedeflerinde`Microsoft.NET.Sdk.Web`mevcuttur ( ). SDK proje dosyasının en üstünde ayarlanır:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

Projede bir *web.config* dosyası yoksa, dosya doğru *işlemYolu* ve ASP.NET Çekirdek Modülyapılandırmak için *bağımsız değişkenler* ile oluşturulur ve [yayımlanmış çıktıya](xref:host-and-deploy/directory-structure)taşınır.

Projede bir *web.config* dosyası varsa, dosya doğru *işlemYolu* ve ASP.NET Çekirdek Modül'ünyapılandırılması için *bağımsız değişkenlerle* dönüştürülür ve yayımlanmış çıktıya taşınır. Dönüştürme, dosyadaki IIS yapılandırma ayarlarını değiştirmez.

*web.config* dosyası, etkin IIS modüllerini kontrol eden ek IIS yapılandırma ayarları sağlayabilir. ASP.NET Core uygulamalarıyla istekleri işleyebilen IIS modülleri hakkında bilgi için [IIS modülleri](xref:host-and-deploy/iis/modules) konusuna bakın.

Web SDK'nın *web.config* dosyasını dönüştürmesini önlemek için proje dosyasındaki ** \<IsTransformWebConfigDisabled>** özelliğini kullanın:

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

Web SDK'nın dosyayı dönüştürmesini devre dışı bırakırken, *processPath* ve *bağımsız değişkenler* geliştirici tarafından el ile ayarlanmalıdır. Daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module>.

### <a name="webconfig-file-location"></a>web.config dosya konumu

[ASP.NET Çekirdek Modülünü](xref:host-and-deploy/aspnet-core-module) doğru ayarlamak *için, web.config* dosyasının dağıtılan uygulamanın [içerik kök](xref:fundamentals/index#content-root) yolunda (genellikle uygulama temel yolu) bulunması gerekir. Burası, IIS'e sağlanan web sitesi fiziksel yolu ile aynı yerdir. Web Deploy kullanarak birden çok uygulamanın yayımlanmasını etkinleştirmek için uygulamanın kökünde *web.config* dosyası gereklidir.

Önemli dosyalar, uygulamanın fiziksel yolunda montaj * \<>.runtimeconfig.json,* * \<montaj>.xml* (XML Documentation comments) ve * \<montaj>.deps.json*gibi vardır. *web.config* dosyası hazır olduğunda ve site normal olarak başladığında, IIS bu hassas dosyaları istenirse sunmaz. *web.config* dosyası eksikse, yanlış adlandırılmışsa veya siteyi normal başlangıç için yapılandıramıyorsa, IIS hassas dosyaları herkese açık olarak sunabilir.

***web.config* dosyası dağıtımda her zaman bulunmalı, doğru adlandırılmış olmalı ve siteyi normal başlatma için yapılandırabilmeli. *Web.config* dosyasını asla üretim dağıtımından kaldırmayın.**

### <a name="transform-webconfig"></a>Web.config’i dönüştürme

Yayımlamada *web.config'i* dönüştürmeniz gerekiyorsa (örneğin, yapılandırma, profil veya ortama <xref:host-and-deploy/iis/transform-webconfig>göre ortam değişkenleri ayarlayın), bkz.

## <a name="iis-configuration"></a>IIS yapılandırması

**Windows Server işletim sistemleri**

Web **Sunucusu (IIS)** sunucu rolünü etkinleştirin ve rol hizmetleri kurun.

1. **Yönet** menüsünden veya **Server Manager'daki**bağlantıdan Rol ve **Özellikler Ekle** sihirbazını kullanın. Sunucu **Rolleri** adımında, Web **Sunucusu (IIS)** kutusunu işaretleyin.

   ![Select sunucu rolleri adımında Web Sunucusu IIS rolü seçilir.](index/_static/server-roles-ws2016.png)

1. **Özellikler** adımından sonra, Web Sunucusu (IIS) için **Rol hizmetleri** adımı yükler. İstenilen IIS rol hizmetlerini seçin veya sağlanan varsayılan rol hizmetlerini kabul edin.

   ![Varsayılan rol hizmetleri Seç rol hizmetleri adımında seçilir.](index/_static/role-services-ws2016.png)

   **Windows Kimlik Doğrulama (İsteğe Bağlı)**  
   Windows Kimlik Doğrulama'yı etkinleştirmek için aşağıdaki düğümleri genişletin: **Web Sunucusu** > **Güvenliği.** Windows **Kimlik Doğrulama** özelliğini seçin. Daha fazla bilgi için [Windows Kimlik Doğrulama \<windowsKimlik doğrulama>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) ve Windows kimlik [doğrulamasını yapılandırma](xref:security/authentication/windowsauth)ya da yapılandırma'ya bakın.

   **WebSockets (İsteğe Bağlı)**  
   WebSockets ASP.NET Core 1.1 veya daha sonra ile desteklenir. WebSockets etkinleştirmek için aşağıdaki düğümleri genişletin: **Web Server** > **Uygulama Geliştirme**. **WebSocket Protokolü** özelliğini seçin. Daha fazla bilgi için [Bkz. WebSockets.](xref:fundamentals/websockets)

1. Web sunucusu rolünü ve hizmetlerini yüklemek için **Onay** adımına devam edin. **Web Sunucusu (IIS)** rolünü yükledikten sonra sunucu/IIS yeniden başlatması gerekmez.

**Windows masaüstü işletim sistemleri**

**IIS Yönetim Konsolu** ve **World Wide Web Hizmetlerini**etkinleştirin.

1. Denetim **Masası** > **Programları** > **ve Özellikleri** > Windows özelliklerini açık veya kapalı (ekranın sol tarafında) **açın.**

1. Internet **Bilgi Hizmetleri** düğümlerini açın. Web **Yönetim Araçları** düğümlerini açın.

1. **IIS Yönetim Konsolu**için kutuyu işaretleyin.

1. World Wide **Web Services**için kutuyu işaretleyin.

1. **World Wide Web Services** için varsayılan özellikleri kabul edin veya IIS özelliklerini özelleştirin.

   **Windows Kimlik Doğrulama (İsteğe Bağlı)**  
   Windows Kimlik Doğrulama'yı etkinleştirmek için aşağıdaki düğümleri genişletin: **World Wide Web Services** > **Security.** Windows **Kimlik Doğrulama** özelliğini seçin. Daha fazla bilgi için [Windows Kimlik Doğrulama \<windowsKimlik doğrulama>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) ve Windows kimlik [doğrulamasını yapılandırma](xref:security/authentication/windowsauth)ya da yapılandırma'ya bakın.

   **WebSockets (İsteğe Bağlı)**  
   WebSockets ASP.NET Core 1.1 veya daha sonra ile desteklenir. WebSockets etkinleştirmek için aşağıdaki düğümleri genişletin: **World Wide Web Services** > **Uygulama Geliştirme Özellikleri.** **WebSocket Protokolü** özelliğini seçin. Daha fazla bilgi için [Bkz. WebSockets.](xref:fundamentals/websockets)

1. IIS yüklemesi yeniden başlatma gerektiriyorsa, sistemi yeniden başlatın.

![Windows Özellikleri'nde IIS Yönetim Konsolu ve World Wide Web Hizmetleri seçilir.](index/_static/windows-features-win10.png)

## <a name="install-the-net-core-hosting-bundle"></a>.NET Core Hosting Paketini yükleyin

*.NET Core Hosting Paketini* barındırma sistemine yükleyin. Pakette .NET Core Runtime, .NET Core Library ve [ASP.NET Çekirdek Modülü](xref:host-and-deploy/aspnet-core-module)yüklenir. Modül, ASP.NET Core uygulamalarının IIS'nin arkasında çalışmasını sağlar.

> [!IMPORTANT]
> Barındırma Paketi IIS'den önce yüklenmişse, paket yüklemesinin onarılması gerekir. IIS'yi yükledikten sonra Barındırma Paketi yükleyicisini yeniden çalıştırın.
>
> Barındırma Paketi .NET Core'un 64 bit (x64) sürümünü yükledikten sonra yüklenirse, SDK'lar eksik görünebilir[(No .NET Core SDK'lar algılandı).](xref:test/troubleshoot#no-net-core-sdks-were-detected) Sorunu çözmek için <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>bkz.

### <a name="direct-download-current-version"></a>Doğrudan indirme (geçerli sürüm)

Aşağıdaki bağlantıyı kullanarak yükleyiciindir:

[Geçerli .NET Çekirdek Hosting Paketi yükleyici (doğrudan indir)](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

### <a name="earlier-versions-of-the-installer"></a>Yükleyicinin önceki sürümleri

Yükleyicinin önceki bir sürümünü elde etmek için:

1. [İndir .NET Çekirdek](https://dotnet.microsoft.com/download/dotnet-core) sayfasına gidin.
1. İstenilen .NET Core sürümünü seçin.
1. Çalıştır **uygulamaları - Runtime** sütununda, istenen .NET Core çalışma zamanı sürümünün satırını bulun.
1. Yükleyiciyi Barındırma **Paketi** bağlantısını kullanarak indirin.

> [!WARNING]
> Bazı yükleyiciler, yaşam sürelerine (EOL) ulaşmış ve artık Microsoft tarafından desteklenmeyen sürüm sürümleri içerir. Daha fazla bilgi için [destek ilkesine](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)bakın.

### <a name="install-the-hosting-bundle"></a>Barındırma Paketini Yükleyin

1. Yükleyiciyi sunucuda çalıştırın. Yükleyiciyi bir yönetici komut uyruğundan çalıştırırken aşağıdaki parametreler kullanılabilir:

   * `OPT_NO_ANCM=1`&ndash; ASP.NET Çekirdek Modül'ASP.NET yükleyin.
   * `OPT_NO_RUNTIME=1`&ndash; .NET Core çalışma süresini yüklemeyi atlayın. Sunucu yalnızca bağımsız [dağıtımları (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd)barındırdığında kullanılır.
   * `OPT_NO_SHAREDFX=1`&ndash; Paylaşılan ÇerçeveASP.NET (çalışma süresi ASP.NET) yüklemeyi atlayın. Sunucu yalnızca bağımsız [dağıtımları (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd)barındırdığında kullanılır.
   * `OPT_NO_X86=1`&ndash; X86 çalışma sürelerini yüklemeyi atlayın. 32 bit uygulamaları barındırmayacağımı biliyorsanız bu parametreyi kullanın. Gelecekte hem 32 bit hem de 64 bit uygulamaları barındırma olasılığınız varsa, bu parametreyi kullanmayın ve her iki çalışma zamanını da yükleyin.
   * `OPT_NO_SHARED_CONFIG_CHECK=1`&ndash; Paylaşılan yapılandırma *(applicationHost.config)* IIS yüklemeyle aynı makinede olduğunda, IIS Paylaşılan Yapılandırması kullanma denetimini devre dışı kaldırın. *Yalnızca ASP.NET Core 2.2 veya daha sonra Hosting Bundler yükleyicileri için kullanılabilir.* Daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.
1. Sistemi yeniden başlatın veya komut kabuğunda aşağıdaki komutları çalıştırın:

   ```console
   net stop was /y
   net start w3svc
   ```
   IIS'yi yeniden başlatmak, yükleyici tarafından yapılan bir ortam değişkeni olan sistem PATH'inde bir değişiklik alır.

ASP.NET Core, paylaşılan çerçeve paketlerinin yama sürümleri için roll-forward davranışını benimsemez. Paylaşılan çerçeveyi yeni bir barındırma paketi yükleyerek yükselttikten sonra, sistemi yeniden başlatın veya aşağıdaki komutları bir komut kabuğunda çalıştırın:

```console
net stop was /y
net start w3svc
```

> [!NOTE]
> IIS Paylaşılan Yapılandırma hakkında daha fazla bilgi için, [IIS Paylaşılan Yapılandırması ile ASP.NET Çekirdek Modülü'ne](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration)bakın.

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a>Visual Studio ile yayımlarken Web Dağıtımı'nı yükleme

[Uygulamaları Web Dağıtımı'na](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later)sahip sunuculara dağıtırken, Web Dağıtımı'nın en son sürümünü sunucuya yükleyin. Web Dağıtımı'nı yüklemek için [Web Platform Yükleyicisini (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) kullanın veya doğrudan [Microsoft Download Center'dan](https://www.microsoft.com/download/details.aspx?id=43717)bir yükleyici edinin. Tercih edilen yöntem WebPI kullanmaktır. WebPI bağımsız bir kurulum ve barındırma sağlayıcıları için bir yapılandırma sunuyor.

## <a name="create-the-iis-site"></a>IIS sitesini oluşturma

1. Barındırma sisteminde, uygulamanın yayımlanmış klasörlerini ve dosyalarını içerecek bir klasör oluşturun. Aşağıdaki adımda, klasörün yolu uygulamanın fiziksel yolu olarak IIS'ye sağlanır. Bir uygulamanın dağıtım klasörü ve dosya düzeni <xref:host-and-deploy/directory-structure>hakkında daha fazla bilgi için bkz.

1. IIS Manager'da, **Bağlantılar** panelinde sunucu düğümlerini açın. **Siteler** klasörüne sağ tıklayın. Bağlamsal menüden **Web Sitesi Ekle'yi** seçin.

1. Bir **Site adı** sağlayın ve **Uygulamanın** dağıtım klasörüne Fiziksel yolu ayarlayın. **Bağlama** yapılandırmasını sağlayın ve **Tamam'ı**seçerek web sitesini oluşturun:

   ![Site Ekle adımında Site adını, fiziksel yolu ve Ana Bilgisayar adını tedarik edin.](index/_static/add-website-ws2016.png)

   > [!WARNING]
   > Üst düzey joker karakter`http://*:80/` bağlamaları ( ve `http://+:80`) **kullanılmamalıdır.** Üst düzey joker karakter bağlamaları uygulamanızı güvenlik açıklarına açabilir. Bu hem güçlü hem de zayıf joker karakterler için geçerlidir. Joker karakterler yerine açık ana bilgisayar adları kullanın. Alt etki alanı joker karakter `*.mysub.com`bağlama (örneğin, ) tüm üst etki alanını denetlerseniz (bunun `*.com`aksine, savunmasız dır) bu güvenlik riski yoktur. Daha fazla bilgi için [rfc7230 bölüm-5.4'e](https://tools.ietf.org/html/rfc7230#section-5.4) bakın.

1. Sunucudüğümün altında **Uygulama Havuzları'nı**seçin.

1. Sitenin uygulama havuzuna sağ tıklayın ve bağlamsal menüden **Temel Ayarlar'ı** seçin.

1. Uygulama **Havuzunu Edit** penceresinde **,.NET CLR sürümünü** **Yönetilen Kod Yok**olarak ayarlayın:

   ![.NET CLR sürümü için Yönetilen Kod Ayarlama.](index/_static/edit-apppool-ws2016.png)

    ASP.NET Core ayrı bir işlemde çalışır ve çalışma süresini yönetir. ASP.NET Core masaüstü CLR (.NET CLR)&mdash;Çekirdek Ortak Dil Runtime (CoreCLR) .NET Core için yükleme güvenmez alt sürecinde uygulama barındırmak için önyükleme. **.NET CLR sürümünü** **Yönetilen Kodu Yok** olarak ayarlamak isteğe bağlıdır, ancak önerilir.

1. *ASP.NET Core 2.2 veya sonraki*: [İşlem içi barındırma modelini](#in-process-hosting-model)kullanan 64 bit (x64) [bağımsız dağıtım](/dotnet/core/deploying/#self-contained-deployments-scd) için, uygulama havuzunu 32 bit (x86) işlemler için devre dışı açın.

   Uygulama **Havuzları>** IIS Yöneticisi'nin **Eylemler** kenar çubuğunda Uygulama Havuzu Varsayılanlarını veya Gelişmiş Ayarları **Ayarla'yı** seçin. **Advanced Settings** **32 Bit Uygulamaları Etkinleştir'i** bulun `False`ve değeri . Bu ayar, [işlem dışı barındırma](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model)için dağıtılan uygulamaları etkilemez.

1. İşlem modeli kimliğinin uygun izinlere sahip olduğunu onaylayın.

   Uygulama havuzunun varsayılan kimliği **(İşlem Modeli** > **Kimliği)** **ApplicationPoolIdentity'den** başka bir kimliğe değiştirilirse, yeni kimliğin uygulamanın klasörüne, veritabanına ve diğer gerekli kaynaklara erişmek için gerekli izinlere sahip olduğunu doğrulayın. Örneğin, uygulama havuzu, uygulamanın dosyaları okuyup yazdığı klasörlere okuma ve yazma erişimi gerektirir.

**Windows Kimlik Doğrulama yapılandırması (İsteğe bağlı)**  
Daha fazla bilgi için [bkz.](xref:security/authentication/windowsauth)

## <a name="deploy-the-app"></a>Uygulamayı dağıtma

Uygulamayı IIS site oluştur bölümünde oluşturulan IIS **Fiziksel yol** [klasörüne dağıtın.](#create-the-iis-site) [Web Dağıtımı](/iis/publish/using-web-deploy/introduction-to-web-deploy) dağıtım için önerilen mekanizmadır, ancak uygulamayı projenin *yayımlama* klasöründen barındırma sisteminin dağıtım klasörüne taşımak için çeşitli seçenekler vardır.

### <a name="web-deploy-with-visual-studio"></a>Visual Studio ile Web Dağıtımı

Web Dağıtımı'nda kullanılmak üzere bir yayımlama profili oluşturmayı öğrenmek için ASP.NET Core uygulama dağıtım konusu [için Visual Studio'nun profillerini yayımlayın.](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) Barındırma sağlayıcısı bir Yayımlama Profili veya bir oluşturma desteği sağlıyorsa, profillerini indirin ve Visual Studio Publish iletişim kutusunu kullanarak içe **aktarın:**

![İletişim sayfasını yayımlama](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a>Visual Studio dışında Web Dağıtım

[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) komut satırından Visual Studio dışında da kullanılabilir. Daha fazla bilgi için [Web Dağıtım Aracı'na](/iis/publish/using-web-deploy/use-the-web-deployment-tool)bakın.

### <a name="alternatives-to-web-deploy"></a>Web Dağıtımına Alternatifler

Uygulamayı manuel kopya, [Fotokopi,](/windows-server/administration/windows-commands/xcopy) [Robocopy](/windows-server/administration/windows-commands/robocopy)veya [PowerShell](/powershell/)gibi barındırma sistemine taşımak için çeşitli yöntemlerden herhangi birini kullanın.

IIS'ye ASP.NET Çekirdek dağıtımı hakkında daha fazla bilgi için, [IIS yöneticileri](#deployment-resources-for-iis-administrators) için Dağıtım kaynakları bölümüne bakın.

## <a name="browse-the-website"></a>Web sitesine göz atın

Uygulama barındırma sistemine dağıtıldıktan sonra, uygulamanın ortak uç noktalarından birine istekte bulunun.

Aşağıdaki örnekte, site `www.mysite.com` **Port'taki** `80`bir IIS **Host adına** bağlıdır. Bir istek `http://www.mysite.com`yapılır:

![Microsoft Edge tarayıcısı IIS başlangıç sayfasını yükledi.](index/_static/browsewebsite.png)

## <a name="locked-deployment-files"></a>Kilitli dağıtım dosyaları

Uygulama çalışırken dağıtım klasöründeki dosyalar kilitlenir. Kilitli dosyalar dağıtım sırasında üzerine yazılamaz. Kilitli dosyaları dağıtımda serbest bırakmak için aşağıdaki **yaklaşımlardan birini** kullanarak uygulama havuzunu durdurun:

* Proje dosyasında `Microsoft.NET.Sdk.Web` Web Dağıtımı ve başvuruyu kullanın. Web uygulaması dizininin köküne *bir app_offline.htm* dosyası yerleştirilir. Dosya bulunduğunda, ASP.NET Çekirdek Modülü uygulamayı zarif bir şekilde kapatır ve dağıtım sırasında *app_offline.htm* dosyasına hizmet verir. Daha fazla bilgi için [ASP.NET Core Module yapılandırma başvurusuna](xref:host-and-deploy/aspnet-core-module#app_offlinehtm)bakın.
* Sunucudaki IIS Manager'daki uygulama havuzunu el ile durdurun.
* *app_offline.htm'yi* düşürmek için PowerShell'i kullanın (PowerShell 5 veya daha sonra gerektirir):

  ```powershell
  $pathToApp = 'PATH_TO_APP'

  # Stop the AppPool
  New-Item -Path $pathToApp app_offline.htm

  # Provide script commands here to deploy the app

  # Restart the AppPool
  Remove-Item -Path $pathToApp app_offline.htm

  ```

## <a name="data-protection"></a>Veri koruma

[ASP.NET Çekirdek Veri Koruma yığını,](xref:security/data-protection/introduction) kimlik doğrulamasında kullanılan ara [yazılımlar](xref:fundamentals/middleware/index)da dahil olmak üzere birkaç ASP.NET Core ara yazılım tarafından kullanılır. Veri Koruma API'leri kullanıcı koduyla çağrılmasa bile, veri koruması kalıcı bir şifreleme anahtar [deposu](xref:security/data-protection/implementation/key-management)oluşturmak için bir dağıtım komut dosyası yla veya kullanıcı koduyla yapılandırılmalıdır. Veri koruması yapılandırılmamışsa, anahtarlar bellekte tutulur ve uygulama yeniden başlatıldığında atılır.

Uygulama yeniden başlatıldığında anahtarlık bellekte depolanırsa:

* Tüm çerez tabanlı kimlik doğrulama belirteçleri geçersiz kılındı. 
* Kullanıcıların bir sonraki istekleri üzerine yeniden oturum açmaları gerekmektedir. 
* Anahtarlıkla korunan tüm verilerin şifresi artık çözülemez. Buna [CSRF belirteçleri](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) ve [ASP.NET Core MVC TempData tanımlama bilgileri](xref:fundamentals/app-state#tempdata)de dahil olabilir.

Anahtarlığı sürdürmek için IIS altında veri korumasını yapılandırmak için aşağıdaki **yaklaşımlardan birini** kullanın:

* **Veri Koruma Kayıt Defteri Anahtarları Oluşturma**

  ASP.NET Core uygulamaları tarafından kullanılan veri koruma anahtarları, uygulamaların dışındaki kayıt defterinde depolanır. Belirli bir uygulamanın anahtarlarını devam ettirebilmek için uygulama havuzu için kayıt defteri anahtarları oluşturun.

  Bağımsız, webfarm olmayan IIS yüklemeleri için, [Veri Koruma Provision-AutoGenKeys.ps1 PowerShell komut dosyası](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) ASP.NET Core uygulaması ile kullanılan her uygulama havuzu için kullanılabilir. Bu komut dosyası, HKLM kayıt defterinde yalnızca uygulamanın uygulama havuzunun alt işlem hesabının erişebileceği bir kayıt defteri anahtarı oluşturur. Tuşlar makine çapında bir anahtarla DPAPI kullanılarak istirahatte şifrelenir.

  Web çİtleme senaryolarında, bir uygulama veri koruma anahtarlığını depolamak için bir UNC yolunu kullanacak şekilde yapılandırılabilir. Varsayılan olarak, veri koruma anahtarları şifrelenmez. Ağ paylaşımı için dosya izinlerinin uygulamanın altında çalıştığı Windows hesabıyla sınırlı olduğundan emin olun. Bir X509 sertifikası istirahat anahtarları korumak için kullanılabilir. Kullanıcıların sertifika yüklemesine izin verecek bir mekanizma düşünün: Sertifikaları kullanıcının güvenilen sertifika deposuna yerleştirin ve kullanıcının uygulamasının çalıştığı tüm makinelerde kullanılabilir olduğundan emin olun. Ayrıntılar için [ASP.NET Çekirdek Veri Korumayı Yapılandırma'ya](xref:security/data-protection/configuration/overview) bakın.

* **Kullanıcı profilini yüklemek için IIS Uygulama Havuzunu yapılandırın**

  Bu ayar, uygulama havuzuiçin **Gelişmiş Ayarlar'ın** altındaki **İşlem Modeli** bölümündedir. **Yük Kullanıcı** Profilini `True`' ye ayarlayın. `True`Ayarlandığında, anahtarlar kullanıcı profili dizininde depolanır ve kullanıcı hesabına özgü bir anahtarla DPAPI kullanılarak korunur. Anahtarlar *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* klasöründe kalıcıdır.

  Uygulama havuzunun [setProfileEnvironment özniteliği](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) de etkinleştirilmelidir. Varsayılan `setProfileEnvironment` değeri. `true` Bazı senaryolarda (örneğin, Windows `setProfileEnvironment` OS), `false`. Anahtarlar beklendiği gibi kullanıcı profili dizininde depolanmış değilse:

  1. *%windir%/system32/inetsrv/config* klasörüne gidin.
  1. *ApplicationHost.config* dosyasını açın.
  1. Öğeyi `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` bulun.
  1. Özniteliğin `setProfileEnvironment` mevcut olmadığını doğrulayın, bu da `true`değeri varsayılan olarak (veya özniteliğin `true`değerini açıkça ' ya göre ayarlar.

* **Dosya sistemini anahtarlık deposu olarak kullanma**

  [Dosya sistemini anahtarlık deposu olarak kullanmak](xref:security/data-protection/configuration/overview)için uygulama kodunu ayarlayın. Anahtarlık korumak ve sertifikanın güvenilir bir sertifika olduğundan emin olmak için X509 sertifikası kullanın. Sertifika kendi imzası varsa, sertifikayı Güvenilen Kök deposuna yerleştirin.

  Bir web çiftliğinde IIS kullanırken:

  * Tüm makinelerin erişebileceği bir dosya paylaşımı kullanın.
  * Her makineye bir X509 sertifikası dağıtın. [Kodda veri korumayı](xref:security/data-protection/configuration/overview)yapılandırın.

* **Veri koruması için makine çapında bir ilke ayarlama**

  Veri koruma sistemi, Veri Koruma API'lerini tüketen tüm uygulamalar için makine çapında varsayılan bir [ilke](xref:security/data-protection/configuration/machine-wide-policy) ayarlamak için sınırlı bir desteğe sahiptir. Daha fazla bilgi için bkz. <xref:security/data-protection/introduction>.

## <a name="virtual-directories"></a>Sanal Dizinler

[IIS Sanal Dizinleri](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) ASP.NET Core uygulamalarıyla desteklenmez. Bir uygulama [alt uygulama](#sub-applications)olarak barındırılabilir.

## <a name="sub-applications"></a>Alt uygulamalar

Bir ASP.NET Core uygulaması [IIS alt uygulaması (alt uygulama)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications)olarak barındırılabilir. Alt uygulamanın yolu kök uygulamanın URL'sinin bir parçası olur.

Alt uygulama içindeki statik varlık bağlantıları tilde-slash (`~/`) gösterimi kullanmalıdır. Tilde-slash gösterimi, alt uygulamanın yol tabanını işlenen göreli bağlantıya hazırlamak için bir [Etiket Yardımcısı'nı](xref:mvc/views/tag-helpers/intro) tetikler. Bir alt uygulama `/subapp_path`için , bir `src="~/image.png"` görüntü ile `src="/subapp_path/image.png"`bağlantılı olarak işlenir . Kök uygulamanın Statik Dosya Middleware statik dosya isteğini işlemez. İstek alt uygulamanın Statik Dosya Middleware tarafından işlenir.

Statik bir varlığın `src` özniteliği mutlak bir yola ayarlanmışsa (örneğin,), `src="/image.png"`bağlantı alt uygulamanın yol tabanı olmadan işlenir. Kök uygulamanın Statik Dosya Middleware kök [uygulamanın web kökünden](xref:fundamentals/index#web-root)varlık hizmet etmeye çalışır , hangi bir *404* sonuçlanır - Statik varlık kök uygulamasından kullanılabilir olmadıkça yanıt bulunamadı.

Başka bir ASP.NET Core uygulaması altında bir ASP.NET Core uygulamasını alt uygulama olarak barındırmak için:

1. Alt uygulama için bir uygulama havuzu kurun. .NET Core için Çekirdek Ortak Dil Çalışma Süresi (CoreCLR) masaüstü CLR (.NET CLR) değil, alt işlemde uygulamayı barındırmak için önyükleme olduğundan **.NET CLR Sürümünü** **Yönetilen Kod alet** e kümeleyin.

1. Kök sitenin altındaki bir klasörde alt uygulama ile IIS Manager kök sitesi ekleyin.

1. IIS Manager'daki alt uygulama klasörüne sağ tıklayın ve **Uygulamaya Dönüştür'ü**seçin.

1. Uygulama **Ekle** iletişim kutusunda, alt uygulama için oluşturduğunuz uygulama havuzunu atamak için **Uygulama Havuzu** için **Seç** düğmesini kullanın. **Tamam'ı**seçin.

İşlem içi barındırma modelini kullanırken alt uygulamaya ayrı bir uygulama havuzu atanması bir gerekliliktir.

Süreç içi barındırma modeli ve ASP.NET Çekirdek Modülü yapılandırma hakkında <xref:host-and-deploy/aspnet-core-module>daha fazla bilgi için bkz.

## <a name="configuration-of-iis-with-webconfig"></a>Web.config ile IIS yapılandırması

IIS yapılandırması, `<system.webServer>` ASP.NET Core Modülü ile ASP.NET Core uygulamaları için işlevsel olan IIS senaryoları için *web.config* bölümünden etkilenir. Örneğin, IIS yapılandırması dinamik sıkıştırma için işlevseldir. IIS dinamik sıkıştırma kullanmak üzere sunucu düzeyinde yapılandırılırsa, uygulamanın `<urlCompression>` *web.config* dosyasındaki öğe ASP.NET Core uygulaması için devre dışı kılabilir.

Daha fazla bilgi edinmek için aşağıdaki kaynaklara bakın:

* [system.webServer>için \<yapılandırma başvurusu](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

Yalıtılmış uygulama havuzlarında çalışan (IIS 10.0 veya sonrası için desteklenen) tek tek uygulamalar için ortam değişkenleri ayarlamak için, IIS başvuru belgelerinde [Çevre Değişkenleri \<ortamıdeğişken>lerinin](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) *AppCmd.exe komut* bölümüne bakın.

## <a name="configuration-sections-of-webconfig"></a>web.config'in yapılandırma bölümleri

*web.config'deki* ASP.NET 4.x uygulamalarının yapılandırma bölümleri ASP.NET Core uygulamaları tarafından yapılandırma için kullanılmaz:

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

ASP.NET Core uygulamaları diğer yapılandırma sağlayıcıları kullanılarak yapılandırılır. Daha fazla bilgi için [Configuration](xref:fundamentals/configuration/index)'a bakın.

## <a name="application-pools"></a>Uygulama Havuzları

Uygulama havuzu yalıtımı barındırma modeli tarafından belirlenir:

* Süreç içi &ndash; barındırma Uygulamalarının ayrı uygulama havuzlarında çalışması gerekir.
* İşlem dışı barındırma &ndash; Her uygulamayı kendi uygulama havuzunda çalıştırarak uygulamaları birbirinden yalıtmanızı öneririz.

IIS **Ekle Web Sitesi** iletişim kutusu varsayılan olarak uygulama başına tek bir uygulama havuzuna uğrama olur. Bir **Site adı** sağlandığında, metin otomatik olarak **Uygulama havuzu** metin kutusuna aktarılır. Site eklendiğinde site adı kullanılarak yeni bir uygulama havuzu oluşturulur.

## <a name="application-pool-identity"></a>Uygulama Havuzu Kimliği

Uygulama havuzu kimlik hesabı, bir uygulamanın etki alanları veya yerel hesaplar oluşturmak ve yönetmek zorunda kalmadan benzersiz bir hesap altında çalışmasına olanak tanır. IIS 8.0 veya sonraki işlemlerde, IIS Yönetici İşçi İşlemi (WAS) yeni uygulama havuzunun adını içeren sanal bir hesap oluşturur ve uygulama havuzunun alt işlemlerini varsayılan olarak bu hesap altında çalıştırın. Uygulama havuzu için **Gelişmiş Ayarlar** altındaki IIS Yönetim Konsolunda, **Kimliğin** **ApplicationPoolIdentity'i**kullanmak üzere ayarlandığından emin olun:

![Uygulama havuzu gelişmiş ayarlar iletişim kutusu](index/_static/apppool-identity.png)

IIS yönetim süreci, Windows Güvenlik Sistemi'ndeki uygulama havuzunun adını içeren güvenli bir tanımlayıcı oluşturur. Kaynaklar bu kimlik kullanılarak güvence altına alınabilir. Ancak, bu kimlik gerçek bir kullanıcı hesabı değildir ve Windows Kullanıcı Yönetimi Konsolu'nda görünmez.

IIS alt işlemi uygulamaya daha yüksek erişim gerektiriyorsa, uygulamayı içeren dizini için Erişim Denetim Listesi'ni (ACL) değiştirin:

1. Windows Gezgini'ni açın ve dizine gidin.

1. Dizinin üzerine sağ tıklayın ve **Özellikler'i**seçin.

1. **Güvenlik** sekmesinin **altında, Edit** düğmesini ve ardından **Ekle** düğmesini seçin.

1. **Konumlar** düğmesini seçin ve sistemin seçildiğinden emin olun.

1. Alanı **seçmek için nesne adlarını girin'e** **iIS AppPool\\<app_pool_name>** girin. Adları **Denetle** düğmesini seçin. *DefaultAppPool* için **IIS AppPool\DefaultAppPool**kullanarak isimleri kontrol edin. Adları **Denetle** düğmesi seçildiğinde, nesne adları alanında **DefaultAppPool** değeri gösterilir. Uygulama havuzu adını doğrudan nesne adları alanına girmek mümkün değildir. Nesne adını denetlerken **iIS AppPool\\<>** biçimini app_pool_name kullanın.

   ![Uygulama klasörü için kullanıcıları veya grupları seçin iletişim kutusunu seçin: "DefaultAppPool"un uygulama\" havuzu adı ,"Adları Kontrol Et"i seçmeden önce nesne adları alanında "IIS AppPool" olarak eklenir.](index/_static/select-users-or-groups-1.png)

1. **Tamam'ı**seçin.

   ![Uygulama klasörü için kullanıcıları veya grupları seçin iletişim kutusunu seçin: "Adları Denetle"yi seçtikten sonra nesne adı "DefaultAppPool" nesne adları alanında gösterilir.](index/_static/select-users-or-groups-2.png)

1. Okuma &amp; yürütme izinleri varsayılan olarak verilmelidir. Gerektiğinde ek izinler sağlayın.

Erişim, **ICACLS** aracını kullanarak komut istemiyle de verilebilir. *DefaultAppPool'u* örnek olarak kullanarak aşağıdaki komut kullanılır:

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

Daha fazla bilgi için [icacls](/windows-server/administration/windows-commands/icacls) konusuna bakın.

## <a name="http2-support"></a>HTTP/2 desteği

[HTTP/2,](https://httpwg.org/specs/rfc7540.html) aşağıdaki IIS dağıtım senaryolarında ASP.NET Core ile desteklenir:

* İşlem içi
  * Windows Server 2016/Windows 10 veya sonrası; IIS 10 veya sonrası
  * TLS 1.2 veya daha sonra bağlantı
* İşlem dışı
  * Windows Server 2016/Windows 10 veya sonrası; IIS 10 veya sonrası
  * Genel kullanıma dönük kenar sunucusu bağlantıları HTTP/2'yi kullanır, ancak [Kestrel sunucusuna](xref:fundamentals/servers/kestrel) ters proxy bağlantısı HTTP/1.1 kullanır.
  * TLS 1.2 veya daha sonra bağlantı

Http/2 bağlantısı kurulduğunda işlem içi dağıtım için [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) raporları. `HTTP/2` Http/2 bağlantısı kurulduğunda, işlem dışı bir dağıtım için [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) raporları. `HTTP/1.1`

Süreç içi ve süreç dışı barındırma modelleri hakkında daha <xref:host-and-deploy/aspnet-core-module>fazla bilgi için bkz.

HTTP/2 varsayılan olarak etkinleştirilir. Http/2 bağlantısı kurulmazsa bağlantılar HTTP/1.1'e geri döner. IIS dağıtımları ile HTTP/2 yapılandırması hakkında daha fazla bilgi için [IIS'deki HTTP/2'ye](/iis/get-started/whats-new-in-iis-10/http2-on-iis)bakın.

## <a name="cors-preflight-requests"></a>CORS ön uçuş istekleri

*Bu bölüm yalnızca .NET Framework'ASP.NET hedefleyen ASP.NET Core uygulamaları için geçerlidir.*

.NET Framework'u hedefleyen bir ASP.NET Core uygulaması için, OPTIONS istekleri Varsayılan olarak IIS'de uygulamaya geçirilemez. UYGULAMANIN IIS işleyicilerini OPTIONS isteklerini geçmek üzere *web.config'de* nasıl yapılandırılabildiğini öğrenmek için [ASP.NET](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works)bkz.

## <a name="application-initialization-module-and-idle-timeout"></a>Uygulama Başlatma Modülü ve Boşta Zaman Aşım

ASP.NET Core Module sürüm 2 tarafından IIS'de barındırıldığında:

* [Uygulama Başlatma Modülü](#application-initialization-module) &ndash; Uygulamasının barındırılan [işlem içi](#in-process-hosting-model) veya [iş dışı](#out-of-process-hosting-model) işlemi, bir alt işlem yeniden başlatma veya sunucu yeniden başlatma da otomatik olarak başlanacak şekilde yapılandırılabilir.
* [Boşta Zaman Çıkış](#idle-timeout) &ndash; Uygulaması'nın barındırılan [işlem içi,](#in-process-hosting-model) hareketsizlik dönemlerinde zaman ayarı yapmamaya göre yapılandırılabilir.

### <a name="application-initialization-module"></a>Uygulama Başlatma Modülü

*Süreç içinde ve iş lenmemiş olarak barındırılan uygulamalar için geçerlidir.*

[IIS Application Initialization,](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) uygulama havuzu başlatıldığında veya geri dönüştürüldüğünde uygulamaya BIR HTTP isteği gönderen bir IIS özelliğidir. İstek, uygulamanın başlatılmasını tetikler. Varsayılan olarak, IIS uygulamayı başlatmaisteği`/`() için uygulamanın kök URL'sine bir istek yayınlar (yapılandırma hakkında daha fazla ayrıntı için ek [kaynaklara](#application-initialization-module-and-idle-timeout-additional-resources) bakın).

IIS Application Initialization rol özelliğinin etkin olduğunu doğrulayın:

IIS'yi yerel olarak kullanırken Windows 7 veya daha sonraki masaüstü sistemlerinde:

1. Denetim **Masası** > **Programları** > **ve Özellikleri** > Windows özelliklerini açık veya kapalı (ekranın sol tarafında) **açın.**
1. Açık **İnternet Bilgi Hizmetleri** > **World Wide Web Services** > **Uygulama Geliştirme Özellikleri**.
1. **Uygulama Başlatma**için onay kutusunu seçin.

Windows Server 2008 R2 veya sonraki konularda:

1. Rol **ve Özellikler Ekle**Sihirbazı'nı açın.
1. Rol **hizmetleri seç** panelinde, **Uygulama Geliştirme** düğümünü açın.
1. **Uygulama Başlatma**için onay kutusunu seçin.

Site için Uygulama Başlatma Modülasyonunu etkinleştirmek için aşağıdaki yaklaşımlardan birini kullanın:

* IIS Yöneticisi'ni kullanma:

  1. **Bağlantılar** panelinde **Uygulama Havuzları'nı** seçin.
  1. Listede uygulamanın uygulama havuzuna sağ tıklayın ve **Gelişmiş Ayarlar'ı**seçin.
  1. Varsayılan **Başlangıç Modu** **OnDemand'dir.** Başlangıç **Modunu** **AlwaysRunning**olarak ayarlayın. **Tamam'ı**seçin.
  1. **Bağlantılar** panelinde **Siteler** düğümlerini açın.
  1. Uygulamaya sağ tıklayın ve Web Sitesi **Gelişmiş Ayarları** **Yönet'i** > seçin.
  1. Varsayılan **Önyükleme Etkin** ayarı **False'dur.** **Ön Yüklemeyi** **Doğru**olarak ayarlayın. **Tamam'ı**seçin.

* *web.config*kullanarak, `<applicationInitialization>` uygulamanın `doAppInitAfterRestart` `true` *web.config* dosyasındaki `<system.webServer>` öğelere ayarlı öğeyi ekleyin:

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <configuration>
    <location path="." inheritInChildApplications="false">
      <system.webServer>
        <applicationInitialization doAppInitAfterRestart="true" />
      </system.webServer>
    </location>
  </configuration>
  ```

### <a name="idle-timeout"></a>Boşta Kalma Süresi Zaman Aşımı

*Yalnızca işlem aşamasında barındırılan uygulamalar için geçerlidir.*

Uygulamanın rölantide olmasını önlemek için, Uygulama Havuzunun boşta zaman ödemesini IIS Manager'ı kullanarak ayarlayın:

1. **Bağlantılar** panelinde **Uygulama Havuzları'nı** seçin.
1. Listede uygulamanın uygulama havuzuna sağ tıklayın ve **Gelişmiş Ayarlar'ı**seçin.
1. Varsayılan **Boşta Zaman-Out (dakika)** **20** dakikadır. **Boşta Zaman Dışarı (dakika)** **0** (sıfır) olarak ayarlayın. **Tamam'ı**seçin.
1. İşçi işlemini geri dönüştürün.

Barındırılan uygulamaların [zamanlamasını](#out-of-process-hosting-model) önlemek için aşağıdaki yaklaşımlardan birini kullanın:

* Uygulamayı çalışır durumda tutmak için harici bir hizmetten pingleme.
* Uygulama yalnızca arka plan hizmetlerine ev sahipliği yapıyorsa, IIS barındırmaktan kaçının ve [ASP.NET Core uygulamasını barındırmak için](xref:host-and-deploy/windows-service)bir Windows Hizmeti kullanın.

### <a name="application-initialization-module-and-idle-timeout-additional-resources"></a>Uygulama Başlatma Modülü ve Boşta Zaman Aşım ek kaynakları

* [IIS 8.0 Uygulama Başlatma](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)
* [Uygulama Başlatma \<uygulamasıInitialization>](/iis/configuration/system.webserver/applicationinitialization/).
* [Bir Uygulama Havuzu \<işlemi için İşlem Modeli AyarlarıModel>. ](/iis/configuration/system.applicationhost/applicationpools/add/processmodel)

## <a name="deployment-resources-for-iis-administrators"></a>IIS yöneticileri için dağıtım kaynakları

* [IIS belgeleri](/iis)
* [IIS'de IIS Yöneticisi ile Başlarken](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [.NET Core uygulama dağıtımı](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:test/troubleshoot>
* <xref:index>
* [Resmi Microsoft IIS Sitesi](https://www.iis.net/)
* [Windows Server teknik içerik kitaplığı](/windows-server/windows-server)
* [HTTP/2 Üzerinde IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
* <xref:host-and-deploy/iis/transform-webconfig>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Bir ASP.NET Core uygulamasını bir IIS sunucusuna yayımlama konusunda öğretici bir deneyim için bkz. <xref:tutorials/publish-to-iis>

[.NET Core Hosting Paketini yükleyin](#install-the-net-core-hosting-bundle)

## <a name="supported-operating-systems"></a>Desteklenen işletim sistemleri

Aşağıdaki işletim sistemleri desteklenmektedir:

* Windows 7 veya sonraki
* Windows Server 2008 R2 veya sonraki sürümü

[HTTP.sys sunucusu](xref:fundamentals/servers/httpsys) (eski adıyla WebListener) IIS ile ters proxy yapılandırmasında çalışmaz. [Kerkenez sunucusunu](xref:fundamentals/servers/kestrel)kullanın.

Azure'da barındırma hakkında <xref:host-and-deploy/azure-apps/index>daha fazla bilgi için bkz.

Sorun giderme kılavuzu <xref:test/troubleshoot>için bkz.

## <a name="supported-platforms"></a>Desteklenen platformlar

32 bit (x86) veya 64 bit (x64) dağıtım için yayınlanan uygulamalar desteklenir. Uygulama olmadıkça 32 bit (x86) .NET Core SDK içeren bir 32 bit uygulaması dağıtın:

* 64 bit uygulama için kullanılabilen daha büyük sanal bellek adresi alanı gerektirir.
* Büyük IIS yığın boyutunu gerektirir.
* 64 bit yerel bağımlılıkları vardır.

64 bit lik bir uygulama yayınlamak için 64 bit (x64) .NET Core SDK kullanın. Ana bilgisayar sisteminde 64 bit çalışma süresi bulunmalıdır.

## <a name="hosting-models"></a>Barındırma modelleri

### <a name="in-process-hosting-model"></a>Süreç içi barındırma modeli

ASP.NET Core uygulaması, süreç içi barındırmayı kullanarak IIS alt işlemiyle aynı işlemde çalışır. İstekler giden ağ trafiğini aynı makineye geri döndüren bir ağ arabirimi olan döngü bağdaştırıcısı üzerinden yakınlamadığından, işlem dışı barındırma işlemi barındırma üzerinde gelişmiş performans sağlar. IIS, Windows Process [Etkinleştirme Hizmeti (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was)ile işlem yönetimini işler.

[ASP.NET Çekirdek Modülü](xref:host-and-deploy/aspnet-core-module):

* Uygulama başlatma gerçekleştirir.
  * [CoreCLR](/dotnet/standard/glossary#coreclr)yükler.
  * Aramalar `Program.Main`.
* IIS yerel isteğinin kullanım ömrünü yönetir.

İşlem içi barındırma modeli,.NET Framework'ASP.NET hedefleyen ASP.NET Core uygulamaları için desteklenmez.

Aşağıdaki diyagram, IIS, ASP.NET Çekirdek Modülü ve işlem içinde barındırılan bir uygulama arasındaki ilişkiyi göstermektedir:

![süreç içi barındırma senaryosunda ASP.NET Çekirdek Modülü](index/_static/ancm-inprocess.png)

Web'den çekirdek modu HTTP.sys sürücüsüne bir istek gelir. Sürücü, genellikle 80 (HTTP) veya 443 (HTTPS) olarak web sitesinin yapılandırılmış bağlantı noktasında yerel isteği IIS'ye yönlendirir. ASP.NET Çekirdek Modülü yerel isteği alır ve IIS`IISHttpServer`HTTP Server 'a iletir ( ). IIS HTTP Server, IIS için isteği yerelden yönetilene dönüştüren bir işlem sunucusu uygulamasıdır.

IIS HTTP Server isteği işledikten sonra, istek ASP.NET Core ara yazılım boru hattına itilir. Ara yazılım ardışık alanı isteği işler ve `HttpContext` uygulamanın mantığına örnek olarak aktarın. Uygulamanın yanıtı IIS HTTP Server üzerinden IIS'e geri aktarılır. IIS yanıtı isteği başlatan istemciye gönderir.

Süreç içi barındırma mevcut uygulamalar için tercih edilir, ancak [dotnet yeni](/dotnet/core/tools/dotnet-new) şablonlar tüm IIS ve IIS Express senaryoları için süreç içi barındırma modeli varsayılan.

`CreateDefaultBuilder`[CoreCLR](/dotnet/standard/glossary#coreclr) önyükleme <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> ve IIS işçi sürecinin içinde uygulama barındırmak için yöntemi arayarak bir <xref:Microsoft.AspNetCore.Hosting.Server.IServer> örnek ekler (*w3wp.exe* veya *iisexpress.exe).* Performans testleri, bir .NET Core uygulamasının iş aşamasında barındırılmaya karşı, uygulamanın işlem dışı barındırma ve [Kerkenez](xref:fundamentals/servers/kestrel) sunucusuna proxy isteklerinin proxy edilmesiyle karşılaştırıldığında önemli ölçüde daha yüksek istek verimi sağladığını gösterir.

### <a name="out-of-process-hosting-model"></a>İşlem dışı barındırma modeli

Core uygulamaları ASP.NET IIS alt işleminden ayrı bir işlemde çalıştığı için, ASP.NET Çekirdek Modülü işlem yönetimini işler. Modül, ilk istek geldiğinde ASP.NET Core uygulaması için süreci başlatır ve kapanDığında veya çökerse uygulamayı yeniden başlatır. Bu aslında [Windows Process Etkinleştirme Hizmeti (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was)tarafından yönetilen süreç içinde çalışan uygulamalarda görülen aynı davranıştır.

Aşağıdaki diyagram, IIS, ASP.NET Çekirdek Modülü ve işlem dışı barındırılan bir uygulama arasındaki ilişkiyi göstermektedir:

![ASP.NET Çekirdek Modülü, işlem dışı barındırma senaryosunda](index/_static/ancm-outofprocess.png)

İstekler web'den çekirdek modu HTTP.sys sürücüsüne gelir. Sürücü, istekleri genellikle 80 (HTTP) veya 443 (HTTPS) olarak web sitesinin yapılandırılmış bağlantı noktasında IIS'ye yönlendirir. Modül, 80 veya 443 bağlantı noktası olmayan uygulama için rastgele bir bağlantı noktası üzerindeki istekleri Kestrel'e iletiyor.

Modül, başlangıçta bir ortam değişkeni üzerinden bağlantı <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> noktasını belirtir ve uzantı `http://localhost:{PORT}`sunucuyu dinleyecek şekilde yapılandırır. Ek denetimler gerçekleştirilir ve modülden kaynaklanmıyor istekleri reddedilir. Modül HTTPS iletmesini desteklemez, bu nedenle iIS tarafından HTTPS üzerinden alınsa bile istekler HTTP üzerinden iletilir.

Kestrel modülden isteği aldıktan sonra, istek ASP.NET Core ara yazılım boru hattına itilir. Ara yazılım ardışık alanı isteği işler ve `HttpContext` uygulamanın mantığına örnek olarak aktarın. IIS Integration tarafından eklenen ara yazılım, isteği Kestrel'e iletmek için hesap vermek üzere şemayı, uzak IP'yi ve yol tabanını güncelleştirir. Uygulamanın yanıtı IIS'ye geri aktarılır ve bu da isteği başlatan HTTP istemcisine geri iter.

ASP.NET Çekirdek Modülü yapılandırma <xref:host-and-deploy/aspnet-core-module>kılavuzu için bkz.

Barındırma hakkında daha fazla bilgi için, [ASP.NET Core'da Host'a](xref:fundamentals/index#host)bakın.

## <a name="application-configuration"></a>Uygulama yapılandırması

### <a name="enable-the-iisintegration-components"></a>IISIntegration bileşenlerini etkinleştirme

(Program.cs) içinde `CreateWebHostBuilder` *Program.cs*bir ev <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> sahibi kurarken, IIS entegrasyonunu etkinleştirmek için çağrı:

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        ...
```

Daha fazla `CreateDefaultBuilder`bilgi <xref:fundamentals/host/web-host#set-up-a-host>için bkz.

### <a name="iis-options"></a>IIS seçenekleri

**Süreç içi barındırma modeli**

IIS Server seçeneklerini yapılandırmak için, <xref:Microsoft.AspNetCore.Builder.IISServerOptions> 'de için bir hizmet yapılandırması <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>ekleyin. Aşağıdaki örnek Otomatik Kimlik Doğrulamasını devre dışı bırakmaz:

```csharp
services.Configure<IISServerOptions>(options => 
{
    options.AutomaticAuthentication = false;
});
```

| Seçenek                         | Varsayılan | Ayar |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | IIS `true`Server, Windows `HttpContext.User` Kimlik Doğrulama sı tarafından kimlik doğrulaması yapılandırışlarını [ayarlarsa.](xref:security/authentication/windowsauth) Sunucu `false`yalnızca bir kimlik sağlar `HttpContext.User` ve açıkça tarafından istendiğinde zorluklara `AuthenticationScheme`yanıt verir. Windows Kimlik Doğrulama'nın çalışması `AutomaticAuthentication` için IIS'de etkinleştirilmesi gerekir. Daha fazla bilgi için [Windows Kimlik Doğrulama'ya](xref:security/authentication/windowsauth)bakın. |
| `AuthenticationDisplayName`    | `null`  | Oturum açma sayfalarında kullanıcılara gösterilen görüntü adını ayarlar. |

**İşlem dışı barındırma modeli**

IIS seçeneklerini yapılandırmak için, <xref:Microsoft.AspNetCore.Builder.IISOptions> için <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>bir hizmet yapılandırması ekleyin. Aşağıdaki örnek, uygulamanın doldurmasını `HttpContext.Connection.ClientCertificate`engeller:

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| Seçenek                         | Varsayılan | Ayar |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | `true`IIS [Integration Middleware,](#enable-the-iisintegration-components) `HttpContext.User` Windows Kimlik Doğrulaması tarafından kimlik doğrulaması yapılan aracı [ayarlarsa.](xref:security/authentication/windowsauth) Eğer, `false`ara yazılım sadece bir `HttpContext.User` kimlik sağlar ve açıkça tarafından talep `AuthenticationScheme`edildiğinde zorluklara yanıt verir. Windows Kimlik Doğrulama'nın çalışması `AutomaticAuthentication` için IIS'de etkinleştirilmesi gerekir. Daha fazla bilgi için [Windows Kimlik Doğrulama](xref:security/authentication/windowsauth) konusuna bakın. |
| `AuthenticationDisplayName`    | `null`  | Oturum açma sayfalarında kullanıcılara gösterilen görüntü adını ayarlar. |
| `ForwardClientCertificate`     | `true`  | İstek `true` üstbilgisi varsa, `MS-ASPNETCORE-CLIENTCERT` `HttpContext.Connection.ClientCertificate` doldurulur. |

### <a name="proxy-server-and-load-balancer-scenarios"></a>Proxy sunucusu ve yük dengeleyici senaryoları

İleriye Dönük Üstbilgileri Ara Yazılım'ı yapılandıran [IIS Tümleştirme Aracı](#enable-the-iisintegration-components)ve ASP.NET Çekirdek Modülü, düzeni (HTTP/HTTPS) ve isteğin kaynağı olduğu uzak IP adresini iletecek şekilde yapılandırılır. Ek proxy sunucuları ve yük dengeleyicileri arkasında barındırılan uygulamalar için ek yapılandırma gerekebilir. Daha fazla bilgi için proxy [sunucuları ve yük dengeleyicileri ile çalışmak için ASP.NET Core'u yapılandırın'](xref:host-and-deploy/proxy-load-balancer)a bakın.

### <a name="webconfig-file"></a>web.config dosyası

*web.config* dosyası ASP.NET [Çekirdek](xref:host-and-deploy/aspnet-core-module)Modül'ünyapılatır. *Web.config* dosyasının oluşturulması, dönüştürülmesi ve yayımlanması, proje`_TransformWebConfig`yayımlandığında bir MSBuild hedefi ( ) tarafından işlenir. Bu hedef Web SDK hedeflerinde`Microsoft.NET.Sdk.Web`mevcuttur ( ). SDK proje dosyasının en üstünde ayarlanır:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

Projede bir *web.config* dosyası yoksa, dosya doğru *işlemYolu* ve ASP.NET Çekirdek Modülyapılandırmak için *bağımsız değişkenler* ile oluşturulur ve [yayımlanmış çıktıya](xref:host-and-deploy/directory-structure)taşınır.

Projede bir *web.config* dosyası varsa, dosya doğru *işlemYolu* ve ASP.NET Çekirdek Modül'ünyapılandırılması için *bağımsız değişkenlerle* dönüştürülür ve yayımlanmış çıktıya taşınır. Dönüştürme, dosyadaki IIS yapılandırma ayarlarını değiştirmez.

*web.config* dosyası, etkin IIS modüllerini kontrol eden ek IIS yapılandırma ayarları sağlayabilir. ASP.NET Core uygulamalarıyla istekleri işleyebilen IIS modülleri hakkında bilgi için [IIS modülleri](xref:host-and-deploy/iis/modules) konusuna bakın.

Web SDK'nın *web.config* dosyasını dönüştürmesini önlemek için proje dosyasındaki ** \<IsTransformWebConfigDisabled>** özelliğini kullanın:

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

Web SDK'nın dosyayı dönüştürmesini devre dışı bırakırken, *processPath* ve *bağımsız değişkenler* geliştirici tarafından el ile ayarlanmalıdır. Daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module>.

### <a name="webconfig-file-location"></a>web.config dosya konumu

[ASP.NET Çekirdek Modülünü](xref:host-and-deploy/aspnet-core-module) doğru ayarlamak *için, web.config* dosyasının dağıtılan uygulamanın [içerik kök](xref:fundamentals/index#content-root) yolunda (genellikle uygulama temel yolu) bulunması gerekir. Burası, IIS'e sağlanan web sitesi fiziksel yolu ile aynı yerdir. Web Deploy kullanarak birden çok uygulamanın yayımlanmasını etkinleştirmek için uygulamanın kökünde *web.config* dosyası gereklidir.

Önemli dosyalar, uygulamanın fiziksel yolunda montaj * \<>.runtimeconfig.json,* * \<montaj>.xml* (XML Documentation comments) ve * \<montaj>.deps.json*gibi vardır. *web.config* dosyası hazır olduğunda ve site normal olarak başladığında, IIS bu hassas dosyaları istenirse sunmaz. *web.config* dosyası eksikse, yanlış adlandırılmışsa veya siteyi normal başlangıç için yapılandıramıyorsa, IIS hassas dosyaları herkese açık olarak sunabilir.

***web.config* dosyası dağıtımda her zaman bulunmalı, doğru adlandırılmış olmalı ve siteyi normal başlatma için yapılandırabilmeli. *Web.config* dosyasını asla üretim dağıtımından kaldırmayın.**

### <a name="transform-webconfig"></a>Web.config’i dönüştürme

Yayımlamada *web.config'i* dönüştürmeniz gerekiyorsa (örneğin, yapılandırma, profil veya ortama <xref:host-and-deploy/iis/transform-webconfig>göre ortam değişkenleri ayarlayın), bkz.

## <a name="iis-configuration"></a>IIS yapılandırması

**Windows Server işletim sistemleri**

Web **Sunucusu (IIS)** sunucu rolünü etkinleştirin ve rol hizmetleri kurun.

1. **Yönet** menüsünden veya **Server Manager'daki**bağlantıdan Rol ve **Özellikler Ekle** sihirbazını kullanın. Sunucu **Rolleri** adımında, Web **Sunucusu (IIS)** kutusunu işaretleyin.

   ![Select sunucu rolleri adımında Web Sunucusu IIS rolü seçilir.](index/_static/server-roles-ws2016.png)

1. **Özellikler** adımından sonra, Web Sunucusu (IIS) için **Rol hizmetleri** adımı yükler. İstenilen IIS rol hizmetlerini seçin veya sağlanan varsayılan rol hizmetlerini kabul edin.

   ![Varsayılan rol hizmetleri Seç rol hizmetleri adımında seçilir.](index/_static/role-services-ws2016.png)

   **Windows Kimlik Doğrulama (İsteğe Bağlı)**  
   Windows Kimlik Doğrulama'yı etkinleştirmek için aşağıdaki düğümleri genişletin: **Web Sunucusu** > **Güvenliği.** Windows **Kimlik Doğrulama** özelliğini seçin. Daha fazla bilgi için [Windows Kimlik Doğrulama \<windowsKimlik doğrulama>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) ve Windows kimlik [doğrulamasını yapılandırma](xref:security/authentication/windowsauth)ya da yapılandırma'ya bakın.

   **WebSockets (İsteğe Bağlı)**  
   WebSockets ASP.NET Core 1.1 veya daha sonra ile desteklenir. WebSockets etkinleştirmek için aşağıdaki düğümleri genişletin: **Web Server** > **Uygulama Geliştirme**. **WebSocket Protokolü** özelliğini seçin. Daha fazla bilgi için [Bkz. WebSockets.](xref:fundamentals/websockets)

1. Web sunucusu rolünü ve hizmetlerini yüklemek için **Onay** adımına devam edin. **Web Sunucusu (IIS)** rolünü yükledikten sonra sunucu/IIS yeniden başlatması gerekmez.

**Windows masaüstü işletim sistemleri**

**IIS Yönetim Konsolu** ve **World Wide Web Hizmetlerini**etkinleştirin.

1. Denetim **Masası** > **Programları** > **ve Özellikleri** > Windows özelliklerini açık veya kapalı (ekranın sol tarafında) **açın.**

1. Internet **Bilgi Hizmetleri** düğümlerini açın. Web **Yönetim Araçları** düğümlerini açın.

1. **IIS Yönetim Konsolu**için kutuyu işaretleyin.

1. World Wide **Web Services**için kutuyu işaretleyin.

1. **World Wide Web Services** için varsayılan özellikleri kabul edin veya IIS özelliklerini özelleştirin.

   **Windows Kimlik Doğrulama (İsteğe Bağlı)**  
   Windows Kimlik Doğrulama'yı etkinleştirmek için aşağıdaki düğümleri genişletin: **World Wide Web Services** > **Security.** Windows **Kimlik Doğrulama** özelliğini seçin. Daha fazla bilgi için [Windows Kimlik Doğrulama \<windowsKimlik doğrulama>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) ve Windows kimlik [doğrulamasını yapılandırma](xref:security/authentication/windowsauth)ya da yapılandırma'ya bakın.

   **WebSockets (İsteğe Bağlı)**  
   WebSockets ASP.NET Core 1.1 veya daha sonra ile desteklenir. WebSockets etkinleştirmek için aşağıdaki düğümleri genişletin: **World Wide Web Services** > **Uygulama Geliştirme Özellikleri.** **WebSocket Protokolü** özelliğini seçin. Daha fazla bilgi için [Bkz. WebSockets.](xref:fundamentals/websockets)

1. IIS yüklemesi yeniden başlatma gerektiriyorsa, sistemi yeniden başlatın.

![Windows Özellikleri'nde IIS Yönetim Konsolu ve World Wide Web Hizmetleri seçilir.](index/_static/windows-features-win10.png)

## <a name="install-the-net-core-hosting-bundle"></a>.NET Core Hosting Paketini yükleyin

*.NET Core Hosting Paketini* barındırma sistemine yükleyin. Pakette .NET Core Runtime, .NET Core Library ve [ASP.NET Çekirdek Modülü](xref:host-and-deploy/aspnet-core-module)yüklenir. Modül, ASP.NET Core uygulamalarının IIS'nin arkasında çalışmasını sağlar.

> [!IMPORTANT]
> Barındırma Paketi IIS'den önce yüklenmişse, paket yüklemesinin onarılması gerekir. IIS'yi yükledikten sonra Barındırma Paketi yükleyicisini yeniden çalıştırın.
>
> Barındırma Paketi .NET Core'un 64 bit (x64) sürümünü yükledikten sonra yüklenirse, SDK'lar eksik görünebilir[(No .NET Core SDK'lar algılandı).](xref:test/troubleshoot#no-net-core-sdks-were-detected) Sorunu çözmek için <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>bkz.

### <a name="download"></a>İndirme

1. [İndir .NET Çekirdek](https://dotnet.microsoft.com/download/dotnet-core) sayfasına gidin.
1. İstenilen .NET Core sürümünü seçin.
1. Çalıştır **uygulamaları - Runtime** sütununda, istenen .NET Core çalışma zamanı sürümünün satırını bulun.
1. Yükleyiciyi Barındırma **Paketi** bağlantısını kullanarak indirin.

> [!WARNING]
> Bazı yükleyiciler, yaşam sürelerine (EOL) ulaşmış ve artık Microsoft tarafından desteklenmeyen sürüm sürümleri içerir. Daha fazla bilgi için [destek ilkesine](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)bakın.

### <a name="install-the-hosting-bundle"></a>Barındırma Paketini Yükleyin

1. Yükleyiciyi sunucuda çalıştırın. Yükleyiciyi bir yönetici komut uyruğundan çalıştırırken aşağıdaki parametreler kullanılabilir:

   * `OPT_NO_ANCM=1`&ndash; ASP.NET Çekirdek Modül'ASP.NET yükleyin.
   * `OPT_NO_RUNTIME=1`&ndash; .NET Core çalışma süresini yüklemeyi atlayın. Sunucu yalnızca bağımsız [dağıtımları (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd)barındırdığında kullanılır.
   * `OPT_NO_SHAREDFX=1`&ndash; Paylaşılan ÇerçeveASP.NET (çalışma süresi ASP.NET) yüklemeyi atlayın. Sunucu yalnızca bağımsız [dağıtımları (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd)barındırdığında kullanılır.
   * `OPT_NO_X86=1`&ndash; X86 çalışma sürelerini yüklemeyi atlayın. 32 bit uygulamaları barındırmayacağımı biliyorsanız bu parametreyi kullanın. Gelecekte hem 32 bit hem de 64 bit uygulamaları barındırma olasılığınız varsa, bu parametreyi kullanmayın ve her iki çalışma zamanını da yükleyin.
   * `OPT_NO_SHARED_CONFIG_CHECK=1`&ndash; Paylaşılan yapılandırma *(applicationHost.config)* IIS yüklemeyle aynı makinede olduğunda, IIS Paylaşılan Yapılandırması kullanma denetimini devre dışı kaldırın. *Yalnızca ASP.NET Core 2.2 veya daha sonra Hosting Bundler yükleyicileri için kullanılabilir.* Daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.
1. Sistemi yeniden başlatın veya komut kabuğunda aşağıdaki komutları çalıştırın:

   ```console
   net stop was /y
   net start w3svc
   ```
   IIS'yi yeniden başlatmak, yükleyici tarafından yapılan bir ortam değişkeni olan sistem PATH'inde bir değişiklik alır.

Barındırma Paketini yüklerken IIS'deki tek tek siteleri el ile durdurmaya gerek yoktur. Barındırılan uygulamalar (IIS siteleri), IIS yeniden başlatıldığında yeniden başlatılır. [Uygulamalar, Uygulama Başlatma Modülü](#application-initialization-module-and-idle-timeout)de dahil olmak üzere ilk isteklerini aldıklarında yeniden başlar.

ASP.NET Core, paylaşılan çerçeve paketlerinin yama sürümleri için roll-forward davranışını benimser. IIS tarafından barındırılan uygulamalar IIS ile yeniden başlatıldığında, uygulamalar ilk isteklerini aldıklarında başvurulan paketlerinin en son yama sürümleriyle yüklenir. IIS yeniden başlatılmazsa, uygulamalar yeniden başlatılır ve çalışan işlemleri geri dönüştürüldüğünde ve ilk isteklerini aldıklarında yuvarlama davranışı sergiler.

> [!NOTE]
> IIS Paylaşılan Yapılandırma hakkında daha fazla bilgi için, [IIS Paylaşılan Yapılandırması ile ASP.NET Çekirdek Modülü'ne](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration)bakın.

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a>Visual Studio ile yayımlarken Web Dağıtımı'nı yükleme

[Uygulamaları Web Dağıtımı'na](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later)sahip sunuculara dağıtırken, Web Dağıtımı'nın en son sürümünü sunucuya yükleyin. Web Dağıtımı'nı yüklemek için [Web Platform Yükleyicisini (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) kullanın veya doğrudan [Microsoft Download Center'dan](https://www.microsoft.com/download/details.aspx?id=43717)bir yükleyici edinin. Tercih edilen yöntem WebPI kullanmaktır. WebPI bağımsız bir kurulum ve barındırma sağlayıcıları için bir yapılandırma sunuyor.

## <a name="create-the-iis-site"></a>IIS sitesini oluşturma

1. Barındırma sisteminde, uygulamanın yayımlanmış klasörlerini ve dosyalarını içerecek bir klasör oluşturun. Aşağıdaki adımda, klasörün yolu uygulamanın fiziksel yolu olarak IIS'ye sağlanır. Bir uygulamanın dağıtım klasörü ve dosya düzeni <xref:host-and-deploy/directory-structure>hakkında daha fazla bilgi için bkz.

1. IIS Manager'da, **Bağlantılar** panelinde sunucu düğümlerini açın. **Siteler** klasörüne sağ tıklayın. Bağlamsal menüden **Web Sitesi Ekle'yi** seçin.

1. Bir **Site adı** sağlayın ve **Uygulamanın** dağıtım klasörüne Fiziksel yolu ayarlayın. **Bağlama** yapılandırmasını sağlayın ve **Tamam'ı**seçerek web sitesini oluşturun:

   ![Site Ekle adımında Site adını, fiziksel yolu ve Ana Bilgisayar adını tedarik edin.](index/_static/add-website-ws2016.png)

   > [!WARNING]
   > Üst düzey joker karakter`http://*:80/` bağlamaları ( ve `http://+:80`) **kullanılmamalıdır.** Üst düzey joker karakter bağlamaları uygulamanızı güvenlik açıklarına açabilir. Bu hem güçlü hem de zayıf joker karakterler için geçerlidir. Joker karakterler yerine açık ana bilgisayar adları kullanın. Alt etki alanı joker karakter `*.mysub.com`bağlama (örneğin, ) tüm üst etki alanını denetlerseniz (bunun `*.com`aksine, savunmasız dır) bu güvenlik riski yoktur. Daha fazla bilgi için [rfc7230 bölüm-5.4'e](https://tools.ietf.org/html/rfc7230#section-5.4) bakın.

1. Sunucudüğümün altında **Uygulama Havuzları'nı**seçin.

1. Sitenin uygulama havuzuna sağ tıklayın ve bağlamsal menüden **Temel Ayarlar'ı** seçin.

1. Uygulama **Havuzunu Edit** penceresinde **,.NET CLR sürümünü** **Yönetilen Kod Yok**olarak ayarlayın:

   ![.NET CLR sürümü için Yönetilen Kod Ayarlama.](index/_static/edit-apppool-ws2016.png)

    ASP.NET Core ayrı bir işlemde çalışır ve çalışma süresini yönetir. ASP.NET Core masaüstü CLR (.NET CLR)&mdash;Çekirdek Ortak Dil Runtime (CoreCLR) .NET Core için yükleme güvenmez alt sürecinde uygulama barındırmak için önyükleme. **.NET CLR sürümünü** **Yönetilen Kodu Yok** olarak ayarlamak isteğe bağlıdır, ancak önerilir.

1. *ASP.NET Core 2.2 veya sonraki*: [İşlem içi barındırma modelini](#in-process-hosting-model)kullanan 64 bit (x64) [bağımsız dağıtım](/dotnet/core/deploying/#self-contained-deployments-scd) için, uygulama havuzunu 32 bit (x86) işlemler için devre dışı açın.

   Uygulama **Havuzları>** IIS Yöneticisi'nin **Eylemler** kenar çubuğunda Uygulama Havuzu Varsayılanlarını veya Gelişmiş Ayarları **Ayarla'yı** seçin. **Advanced Settings** **32 Bit Uygulamaları Etkinleştir'i** bulun `False`ve değeri . Bu ayar, [işlem dışı barındırma](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model)için dağıtılan uygulamaları etkilemez.

1. İşlem modeli kimliğinin uygun izinlere sahip olduğunu onaylayın.

   Uygulama havuzunun varsayılan kimliği **(İşlem Modeli** > **Kimliği)** **ApplicationPoolIdentity'den** başka bir kimliğe değiştirilirse, yeni kimliğin uygulamanın klasörüne, veritabanına ve diğer gerekli kaynaklara erişmek için gerekli izinlere sahip olduğunu doğrulayın. Örneğin, uygulama havuzu, uygulamanın dosyaları okuyup yazdığı klasörlere okuma ve yazma erişimi gerektirir.

**Windows Kimlik Doğrulama yapılandırması (İsteğe bağlı)**  
Daha fazla bilgi için [bkz.](xref:security/authentication/windowsauth)

## <a name="deploy-the-app"></a>Uygulamayı dağıtma

Uygulamayı IIS site oluştur bölümünde oluşturulan IIS **Fiziksel yol** [klasörüne dağıtın.](#create-the-iis-site) [Web Dağıtımı](/iis/publish/using-web-deploy/introduction-to-web-deploy) dağıtım için önerilen mekanizmadır, ancak uygulamayı projenin *yayımlama* klasöründen barındırma sisteminin dağıtım klasörüne taşımak için çeşitli seçenekler vardır.

### <a name="web-deploy-with-visual-studio"></a>Visual Studio ile Web Dağıtımı

Web Dağıtımı'nda kullanılmak üzere bir yayımlama profili oluşturmayı öğrenmek için ASP.NET Core uygulama dağıtım konusu [için Visual Studio'nun profillerini yayımlayın.](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) Barındırma sağlayıcısı bir Yayımlama Profili veya bir oluşturma desteği sağlıyorsa, profillerini indirin ve Visual Studio Publish iletişim kutusunu kullanarak içe **aktarın:**

![İletişim sayfasını yayımlama](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a>Visual Studio dışında Web Dağıtım

[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) komut satırından Visual Studio dışında da kullanılabilir. Daha fazla bilgi için [Web Dağıtım Aracı'na](/iis/publish/using-web-deploy/use-the-web-deployment-tool)bakın.

### <a name="alternatives-to-web-deploy"></a>Web Dağıtımına Alternatifler

Uygulamayı manuel kopya, [Fotokopi,](/windows-server/administration/windows-commands/xcopy) [Robocopy](/windows-server/administration/windows-commands/robocopy)veya [PowerShell](/powershell/)gibi barındırma sistemine taşımak için çeşitli yöntemlerden herhangi birini kullanın.

IIS'ye ASP.NET Çekirdek dağıtımı hakkında daha fazla bilgi için, [IIS yöneticileri](#deployment-resources-for-iis-administrators) için Dağıtım kaynakları bölümüne bakın.

## <a name="browse-the-website"></a>Web sitesine göz atın

Uygulama barındırma sistemine dağıtıldıktan sonra, uygulamanın ortak uç noktalarından birine istekte bulunun.

Aşağıdaki örnekte, site `www.mysite.com` **Port'taki** `80`bir IIS **Host adına** bağlıdır. Bir istek `http://www.mysite.com`yapılır:

![Microsoft Edge tarayıcısı IIS başlangıç sayfasını yükledi.](index/_static/browsewebsite.png)

## <a name="locked-deployment-files"></a>Kilitli dağıtım dosyaları

Uygulama çalışırken dağıtım klasöründeki dosyalar kilitlenir. Kilitli dosyalar dağıtım sırasında üzerine yazılamaz. Kilitli dosyaları dağıtımda serbest bırakmak için aşağıdaki **yaklaşımlardan birini** kullanarak uygulama havuzunu durdurun:

* Proje dosyasında `Microsoft.NET.Sdk.Web` Web Dağıtımı ve başvuruyu kullanın. Web uygulaması dizininin köküne *bir app_offline.htm* dosyası yerleştirilir. Dosya bulunduğunda, ASP.NET Çekirdek Modülü uygulamayı zarif bir şekilde kapatır ve dağıtım sırasında *app_offline.htm* dosyasına hizmet verir. Daha fazla bilgi için [ASP.NET Core Module yapılandırma başvurusuna](xref:host-and-deploy/aspnet-core-module#app_offlinehtm)bakın.
* Sunucudaki IIS Manager'daki uygulama havuzunu el ile durdurun.
* *app_offline.htm'yi* düşürmek için PowerShell'i kullanın (PowerShell 5 veya daha sonra gerektirir):

  ```powershell
  $pathToApp = 'PATH_TO_APP'

  # Stop the AppPool
  New-Item -Path $pathToApp app_offline.htm

  # Provide script commands here to deploy the app

  # Restart the AppPool
  Remove-Item -Path $pathToApp app_offline.htm

  ```

## <a name="data-protection"></a>Veri koruma

[ASP.NET Çekirdek Veri Koruma yığını,](xref:security/data-protection/introduction) kimlik doğrulamasında kullanılan ara [yazılımlar](xref:fundamentals/middleware/index)da dahil olmak üzere birkaç ASP.NET Core ara yazılım tarafından kullanılır. Veri Koruma API'leri kullanıcı koduyla çağrılmasa bile, veri koruması kalıcı bir şifreleme anahtar [deposu](xref:security/data-protection/implementation/key-management)oluşturmak için bir dağıtım komut dosyası yla veya kullanıcı koduyla yapılandırılmalıdır. Veri koruması yapılandırılmamışsa, anahtarlar bellekte tutulur ve uygulama yeniden başlatıldığında atılır.

Uygulama yeniden başlatıldığında anahtarlık bellekte depolanırsa:

* Tüm çerez tabanlı kimlik doğrulama belirteçleri geçersiz kılındı. 
* Kullanıcıların bir sonraki istekleri üzerine yeniden oturum açmaları gerekmektedir. 
* Anahtarlıkla korunan tüm verilerin şifresi artık çözülemez. Buna [CSRF belirteçleri](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) ve [ASP.NET Core MVC TempData tanımlama bilgileri](xref:fundamentals/app-state#tempdata)de dahil olabilir.

Anahtarlığı sürdürmek için IIS altında veri korumasını yapılandırmak için aşağıdaki **yaklaşımlardan birini** kullanın:

* **Veri Koruma Kayıt Defteri Anahtarları Oluşturma**

  ASP.NET Core uygulamaları tarafından kullanılan veri koruma anahtarları, uygulamaların dışındaki kayıt defterinde depolanır. Belirli bir uygulamanın anahtarlarını devam ettirebilmek için uygulama havuzu için kayıt defteri anahtarları oluşturun.

  Bağımsız, webfarm olmayan IIS yüklemeleri için, [Veri Koruma Provision-AutoGenKeys.ps1 PowerShell komut dosyası](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) ASP.NET Core uygulaması ile kullanılan her uygulama havuzu için kullanılabilir. Bu komut dosyası, HKLM kayıt defterinde yalnızca uygulamanın uygulama havuzunun alt işlem hesabının erişebileceği bir kayıt defteri anahtarı oluşturur. Tuşlar makine çapında bir anahtarla DPAPI kullanılarak istirahatte şifrelenir.

  Web çİtleme senaryolarında, bir uygulama veri koruma anahtarlığını depolamak için bir UNC yolunu kullanacak şekilde yapılandırılabilir. Varsayılan olarak, veri koruma anahtarları şifrelenmez. Ağ paylaşımı için dosya izinlerinin uygulamanın altında çalıştığı Windows hesabıyla sınırlı olduğundan emin olun. Bir X509 sertifikası istirahat anahtarları korumak için kullanılabilir. Kullanıcıların sertifika yüklemesine izin verecek bir mekanizma düşünün: Sertifikaları kullanıcının güvenilen sertifika deposuna yerleştirin ve kullanıcının uygulamasının çalıştığı tüm makinelerde kullanılabilir olduğundan emin olun. Ayrıntılar için [ASP.NET Çekirdek Veri Korumayı Yapılandırma'ya](xref:security/data-protection/configuration/overview) bakın.

* **Kullanıcı profilini yüklemek için IIS Uygulama Havuzunu yapılandırın**

  Bu ayar, uygulama havuzuiçin **Gelişmiş Ayarlar'ın** altındaki **İşlem Modeli** bölümündedir. **Yük Kullanıcı** Profilini `True`' ye ayarlayın. `True`Ayarlandığında, anahtarlar kullanıcı profili dizininde depolanır ve kullanıcı hesabına özgü bir anahtarla DPAPI kullanılarak korunur. Anahtarlar *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* klasöründe kalıcıdır.

  Uygulama havuzunun [setProfileEnvironment özniteliği](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) de etkinleştirilmelidir. Varsayılan `setProfileEnvironment` değeri. `true` Bazı senaryolarda (örneğin, Windows `setProfileEnvironment` OS), `false`. Anahtarlar beklendiği gibi kullanıcı profili dizininde depolanmış değilse:

  1. *%windir%/system32/inetsrv/config* klasörüne gidin.
  1. *ApplicationHost.config* dosyasını açın.
  1. Öğeyi `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` bulun.
  1. Özniteliğin `setProfileEnvironment` mevcut olmadığını doğrulayın, bu da `true`değeri varsayılan olarak (veya özniteliğin `true`değerini açıkça ' ya göre ayarlar.

* **Dosya sistemini anahtarlık deposu olarak kullanma**

  [Dosya sistemini anahtarlık deposu olarak kullanmak](xref:security/data-protection/configuration/overview)için uygulama kodunu ayarlayın. Anahtarlık korumak ve sertifikanın güvenilir bir sertifika olduğundan emin olmak için X509 sertifikası kullanın. Sertifika kendi imzası varsa, sertifikayı Güvenilen Kök deposuna yerleştirin.

  Bir web çiftliğinde IIS kullanırken:

  * Tüm makinelerin erişebileceği bir dosya paylaşımı kullanın.
  * Her makineye bir X509 sertifikası dağıtın. [Kodda veri korumayı](xref:security/data-protection/configuration/overview)yapılandırın.

* **Veri koruması için makine çapında bir ilke ayarlama**

  Veri koruma sistemi, Veri Koruma API'lerini tüketen tüm uygulamalar için makine çapında varsayılan bir [ilke](xref:security/data-protection/configuration/machine-wide-policy) ayarlamak için sınırlı bir desteğe sahiptir. Daha fazla bilgi için bkz. <xref:security/data-protection/introduction>.

## <a name="virtual-directories"></a>Sanal Dizinler

[IIS Sanal Dizinleri](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) ASP.NET Core uygulamalarıyla desteklenmez. Bir uygulama [alt uygulama](#sub-applications)olarak barındırılabilir.

## <a name="sub-applications"></a>Alt uygulamalar

Bir ASP.NET Core uygulaması [IIS alt uygulaması (alt uygulama)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications)olarak barındırılabilir. Alt uygulamanın yolu kök uygulamanın URL'sinin bir parçası olur.

Alt uygulama içindeki statik varlık bağlantıları tilde-slash (`~/`) gösterimi kullanmalıdır. Tilde-slash gösterimi, alt uygulamanın yol tabanını işlenen göreli bağlantıya hazırlamak için bir [Etiket Yardımcısı'nı](xref:mvc/views/tag-helpers/intro) tetikler. Bir alt uygulama `/subapp_path`için , bir `src="~/image.png"` görüntü ile `src="/subapp_path/image.png"`bağlantılı olarak işlenir . Kök uygulamanın Statik Dosya Middleware statik dosya isteğini işlemez. İstek alt uygulamanın Statik Dosya Middleware tarafından işlenir.

Statik bir varlığın `src` özniteliği mutlak bir yola ayarlanmışsa (örneğin,), `src="/image.png"`bağlantı alt uygulamanın yol tabanı olmadan işlenir. Kök uygulamanın Statik Dosya Middleware kök [uygulamanın web kökünden](xref:fundamentals/index#web-root)varlık hizmet etmeye çalışır , hangi bir *404* sonuçlanır - Statik varlık kök uygulamasından kullanılabilir olmadıkça yanıt bulunamadı.

Başka bir ASP.NET Core uygulaması altında bir ASP.NET Core uygulamasını alt uygulama olarak barındırmak için:

1. Alt uygulama için bir uygulama havuzu kurun. .NET Core için Çekirdek Ortak Dil Çalışma Süresi (CoreCLR) masaüstü CLR (.NET CLR) değil, alt işlemde uygulamayı barındırmak için önyükleme olduğundan **.NET CLR Sürümünü** **Yönetilen Kod alet** e kümeleyin.

1. Kök sitenin altındaki bir klasörde alt uygulama ile IIS Manager kök sitesi ekleyin.

1. IIS Manager'daki alt uygulama klasörüne sağ tıklayın ve **Uygulamaya Dönüştür'ü**seçin.

1. Uygulama **Ekle** iletişim kutusunda, alt uygulama için oluşturduğunuz uygulama havuzunu atamak için **Uygulama Havuzu** için **Seç** düğmesini kullanın. **Tamam'ı**seçin.

İşlem içi barındırma modelini kullanırken alt uygulamaya ayrı bir uygulama havuzu atanması bir gerekliliktir.

Süreç içi barındırma modeli ve ASP.NET Çekirdek Modülü yapılandırma hakkında <xref:host-and-deploy/aspnet-core-module>daha fazla bilgi için bkz.

## <a name="configuration-of-iis-with-webconfig"></a>Web.config ile IIS yapılandırması

IIS yapılandırması, `<system.webServer>` ASP.NET Core Modülü ile ASP.NET Core uygulamaları için işlevsel olan IIS senaryoları için *web.config* bölümünden etkilenir. Örneğin, IIS yapılandırması dinamik sıkıştırma için işlevseldir. IIS dinamik sıkıştırma kullanmak üzere sunucu düzeyinde yapılandırılırsa, uygulamanın `<urlCompression>` *web.config* dosyasındaki öğe ASP.NET Core uygulaması için devre dışı kılabilir.

Daha fazla bilgi edinmek için aşağıdaki kaynaklara bakın:

* [system.webServer>için \<yapılandırma başvurusu](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

Yalıtılmış uygulama havuzlarında çalışan (IIS 10.0 veya sonrası için desteklenen) tek tek uygulamalar için ortam değişkenleri ayarlamak için, IIS başvuru belgelerinde [Çevre Değişkenleri \<ortamıdeğişken>lerinin](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) *AppCmd.exe komut* bölümüne bakın.

## <a name="configuration-sections-of-webconfig"></a>web.config'in yapılandırma bölümleri

*web.config'deki* ASP.NET 4.x uygulamalarının yapılandırma bölümleri ASP.NET Core uygulamaları tarafından yapılandırma için kullanılmaz:

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

ASP.NET Core uygulamaları diğer yapılandırma sağlayıcıları kullanılarak yapılandırılır. Daha fazla bilgi için [Configuration](xref:fundamentals/configuration/index)'a bakın.

## <a name="application-pools"></a>Uygulama Havuzları

Uygulama havuzu yalıtımı barındırma modeli tarafından belirlenir:

* Süreç içi &ndash; barındırma Uygulamalarının ayrı uygulama havuzlarında çalışması gerekir.
* İşlem dışı barındırma &ndash; Her uygulamayı kendi uygulama havuzunda çalıştırarak uygulamaları birbirinden yalıtmanızı öneririz.

IIS **Ekle Web Sitesi** iletişim kutusu varsayılan olarak uygulama başına tek bir uygulama havuzuna uğrama olur. Bir **Site adı** sağlandığında, metin otomatik olarak **Uygulama havuzu** metin kutusuna aktarılır. Site eklendiğinde site adı kullanılarak yeni bir uygulama havuzu oluşturulur.

## <a name="application-pool-identity"></a>Uygulama Havuzu Kimliği

Uygulama havuzu kimlik hesabı, bir uygulamanın etki alanları veya yerel hesaplar oluşturmak ve yönetmek zorunda kalmadan benzersiz bir hesap altında çalışmasına olanak tanır. IIS 8.0 veya sonraki işlemlerde, IIS Yönetici İşçi İşlemi (WAS) yeni uygulama havuzunun adını içeren sanal bir hesap oluşturur ve uygulama havuzunun alt işlemlerini varsayılan olarak bu hesap altında çalıştırın. Uygulama havuzu için **Gelişmiş Ayarlar** altındaki IIS Yönetim Konsolunda, **Kimliğin** **ApplicationPoolIdentity'i**kullanmak üzere ayarlandığından emin olun:

![Uygulama havuzu gelişmiş ayarlar iletişim kutusu](index/_static/apppool-identity.png)

IIS yönetim süreci, Windows Güvenlik Sistemi'ndeki uygulama havuzunun adını içeren güvenli bir tanımlayıcı oluşturur. Kaynaklar bu kimlik kullanılarak güvence altına alınabilir. Ancak, bu kimlik gerçek bir kullanıcı hesabı değildir ve Windows Kullanıcı Yönetimi Konsolu'nda görünmez.

IIS alt işlemi uygulamaya daha yüksek erişim gerektiriyorsa, uygulamayı içeren dizini için Erişim Denetim Listesi'ni (ACL) değiştirin:

1. Windows Gezgini'ni açın ve dizine gidin.

1. Dizinin üzerine sağ tıklayın ve **Özellikler'i**seçin.

1. **Güvenlik** sekmesinin **altında, Edit** düğmesini ve ardından **Ekle** düğmesini seçin.

1. **Konumlar** düğmesini seçin ve sistemin seçildiğinden emin olun.

1. Alanı **seçmek için nesne adlarını girin'e** **iIS AppPool\\<app_pool_name>** girin. Adları **Denetle** düğmesini seçin. *DefaultAppPool* için **IIS AppPool\DefaultAppPool**kullanarak isimleri kontrol edin. Adları **Denetle** düğmesi seçildiğinde, nesne adları alanında **DefaultAppPool** değeri gösterilir. Uygulama havuzu adını doğrudan nesne adları alanına girmek mümkün değildir. Nesne adını denetlerken **iIS AppPool\\<>** biçimini app_pool_name kullanın.

   ![Uygulama klasörü için kullanıcıları veya grupları seçin iletişim kutusunu seçin: "DefaultAppPool"un uygulama\" havuzu adı ,"Adları Kontrol Et"i seçmeden önce nesne adları alanında "IIS AppPool" olarak eklenir.](index/_static/select-users-or-groups-1.png)

1. **Tamam'ı**seçin.

   ![Uygulama klasörü için kullanıcıları veya grupları seçin iletişim kutusunu seçin: "Adları Denetle"yi seçtikten sonra nesne adı "DefaultAppPool" nesne adları alanında gösterilir.](index/_static/select-users-or-groups-2.png)

1. Okuma &amp; yürütme izinleri varsayılan olarak verilmelidir. Gerektiğinde ek izinler sağlayın.

Erişim, **ICACLS** aracını kullanarak komut istemiyle de verilebilir. *DefaultAppPool'u* örnek olarak kullanarak aşağıdaki komut kullanılır:

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

Daha fazla bilgi için [icacls](/windows-server/administration/windows-commands/icacls) konusuna bakın.

## <a name="http2-support"></a>HTTP/2 desteği

[HTTP/2,](https://httpwg.org/specs/rfc7540.html) aşağıdaki IIS dağıtım senaryolarında ASP.NET Core ile desteklenir:

* İşlem içi
  * Windows Server 2016/Windows 10 veya sonrası; IIS 10 veya sonrası
  * TLS 1.2 veya daha sonra bağlantı
* İşlem dışı
  * Windows Server 2016/Windows 10 veya sonrası; IIS 10 veya sonrası
  * Genel kullanıma dönük kenar sunucusu bağlantıları HTTP/2'yi kullanır, ancak [Kestrel sunucusuna](xref:fundamentals/servers/kestrel) ters proxy bağlantısı HTTP/1.1 kullanır.
  * TLS 1.2 veya daha sonra bağlantı

Http/2 bağlantısı kurulduğunda işlem içi dağıtım için [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) raporları. `HTTP/2` Http/2 bağlantısı kurulduğunda, işlem dışı bir dağıtım için [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) raporları. `HTTP/1.1`

Süreç içi ve süreç dışı barındırma modelleri hakkında daha <xref:host-and-deploy/aspnet-core-module>fazla bilgi için bkz.

HTTP/2 varsayılan olarak etkinleştirilir. Http/2 bağlantısı kurulmazsa bağlantılar HTTP/1.1'e geri döner. IIS dağıtımları ile HTTP/2 yapılandırması hakkında daha fazla bilgi için [IIS'deki HTTP/2'ye](/iis/get-started/whats-new-in-iis-10/http2-on-iis)bakın.

## <a name="cors-preflight-requests"></a>CORS ön uçuş istekleri

*Bu bölüm yalnızca .NET Framework'ASP.NET hedefleyen ASP.NET Core uygulamaları için geçerlidir.*

.NET Framework'u hedefleyen bir ASP.NET Core uygulaması için, OPTIONS istekleri Varsayılan olarak IIS'de uygulamaya geçirilemez. UYGULAMANIN IIS işleyicilerini OPTIONS isteklerini geçmek üzere *web.config'de* nasıl yapılandırılabildiğini öğrenmek için [ASP.NET](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works)bkz.

## <a name="application-initialization-module-and-idle-timeout"></a>Uygulama Başlatma Modülü ve Boşta Zaman Aşım

ASP.NET Core Module sürüm 2 tarafından IIS'de barındırıldığında:

* [Uygulama Başlatma Modülü](#application-initialization-module) &ndash; Uygulamasının barındırılan [işlem içi](#in-process-hosting-model) veya [iş dışı](#out-of-process-hosting-model) işlemi, bir alt işlem yeniden başlatma veya sunucu yeniden başlatma da otomatik olarak başlanacak şekilde yapılandırılabilir.
* [Boşta Zaman Çıkış](#idle-timeout) &ndash; Uygulaması'nın barındırılan [işlem içi,](#in-process-hosting-model) hareketsizlik dönemlerinde zaman ayarı yapmamaya göre yapılandırılabilir.

### <a name="application-initialization-module"></a>Uygulama Başlatma Modülü

*Süreç içinde ve iş lenmemiş olarak barındırılan uygulamalar için geçerlidir.*

[IIS Application Initialization,](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) uygulama havuzu başlatıldığında veya geri dönüştürüldüğünde uygulamaya BIR HTTP isteği gönderen bir IIS özelliğidir. İstek, uygulamanın başlatılmasını tetikler. Varsayılan olarak, IIS uygulamayı başlatmaisteği`/`() için uygulamanın kök URL'sine bir istek yayınlar (yapılandırma hakkında daha fazla ayrıntı için ek [kaynaklara](#application-initialization-module-and-idle-timeout-additional-resources) bakın).

IIS Application Initialization rol özelliğinin etkin olduğunu doğrulayın:

IIS'yi yerel olarak kullanırken Windows 7 veya daha sonraki masaüstü sistemlerinde:

1. Denetim **Masası** > **Programları** > **ve Özellikleri** > Windows özelliklerini açık veya kapalı (ekranın sol tarafında) **açın.**
1. Açık **İnternet Bilgi Hizmetleri** > **World Wide Web Services** > **Uygulama Geliştirme Özellikleri**.
1. **Uygulama Başlatma**için onay kutusunu seçin.

Windows Server 2008 R2 veya sonraki konularda:

1. Rol **ve Özellikler Ekle**Sihirbazı'nı açın.
1. Rol **hizmetleri seç** panelinde, **Uygulama Geliştirme** düğümünü açın.
1. **Uygulama Başlatma**için onay kutusunu seçin.

Site için Uygulama Başlatma Modülasyonunu etkinleştirmek için aşağıdaki yaklaşımlardan birini kullanın:

* IIS Yöneticisi'ni kullanma:

  1. **Bağlantılar** panelinde **Uygulama Havuzları'nı** seçin.
  1. Listede uygulamanın uygulama havuzuna sağ tıklayın ve **Gelişmiş Ayarlar'ı**seçin.
  1. Varsayılan **Başlangıç Modu** **OnDemand'dir.** Başlangıç **Modunu** **AlwaysRunning**olarak ayarlayın. **Tamam'ı**seçin.
  1. **Bağlantılar** panelinde **Siteler** düğümlerini açın.
  1. Uygulamaya sağ tıklayın ve Web Sitesi **Gelişmiş Ayarları** **Yönet'i** > seçin.
  1. Varsayılan **Önyükleme Etkin** ayarı **False'dur.** **Ön Yüklemeyi** **Doğru**olarak ayarlayın. **Tamam'ı**seçin.

* *web.config*kullanarak, `<applicationInitialization>` uygulamanın `doAppInitAfterRestart` `true` *web.config* dosyasındaki `<system.webServer>` öğelere ayarlı öğeyi ekleyin:

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <configuration>
    <location path="." inheritInChildApplications="false">
      <system.webServer>
        <applicationInitialization doAppInitAfterRestart="true" />
      </system.webServer>
    </location>
  </configuration>
  ```

### <a name="idle-timeout"></a>Boşta Kalma Süresi Zaman Aşımı

*Yalnızca işlem aşamasında barındırılan uygulamalar için geçerlidir.*

Uygulamanın rölantide olmasını önlemek için, Uygulama Havuzunun boşta zaman ödemesini IIS Manager'ı kullanarak ayarlayın:

1. **Bağlantılar** panelinde **Uygulama Havuzları'nı** seçin.
1. Listede uygulamanın uygulama havuzuna sağ tıklayın ve **Gelişmiş Ayarlar'ı**seçin.
1. Varsayılan **Boşta Zaman-Out (dakika)** **20** dakikadır. **Boşta Zaman Dışarı (dakika)** **0** (sıfır) olarak ayarlayın. **Tamam'ı**seçin.
1. İşçi işlemini geri dönüştürün.

Barındırılan uygulamaların [zamanlamasını](#out-of-process-hosting-model) önlemek için aşağıdaki yaklaşımlardan birini kullanın:

* Uygulamayı çalışır durumda tutmak için harici bir hizmetten pingleme.
* Uygulama yalnızca arka plan hizmetlerine ev sahipliği yapıyorsa, IIS barındırmaktan kaçının ve [ASP.NET Core uygulamasını barındırmak için](xref:host-and-deploy/windows-service)bir Windows Hizmeti kullanın.

### <a name="application-initialization-module-and-idle-timeout-additional-resources"></a>Uygulama Başlatma Modülü ve Boşta Zaman Aşım ek kaynakları

* [IIS 8.0 Uygulama Başlatma](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)
* [Uygulama Başlatma \<uygulamasıInitialization>](/iis/configuration/system.webserver/applicationinitialization/).
* [Bir Uygulama Havuzu \<işlemi için İşlem Modeli AyarlarıModel>. ](/iis/configuration/system.applicationhost/applicationpools/add/processmodel)

## <a name="deployment-resources-for-iis-administrators"></a>IIS yöneticileri için dağıtım kaynakları

* [IIS belgeleri](/iis)
* [IIS'de IIS Yöneticisi ile Başlarken](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [.NET Core uygulama dağıtımı](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:test/troubleshoot>
* <xref:index>
* [Resmi Microsoft IIS Sitesi](https://www.iis.net/)
* [Windows Server teknik içerik kitaplığı](/windows-server/windows-server)
* [HTTP/2 Üzerinde IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
* <xref:host-and-deploy/iis/transform-webconfig>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Bir ASP.NET Core uygulamasını bir IIS sunucusuna yayımlama konusunda öğretici bir deneyim için bkz. <xref:tutorials/publish-to-iis>

[.NET Core Hosting Paketini yükleyin](#install-the-net-core-hosting-bundle)

## <a name="supported-operating-systems"></a>Desteklenen işletim sistemleri

Aşağıdaki işletim sistemleri desteklenmektedir:

* Windows 7 veya sonraki
* Windows Server 2008 R2 veya sonraki sürümü

[HTTP.sys sunucusu](xref:fundamentals/servers/httpsys) (eski adıyla WebListener) IIS ile ters proxy yapılandırmasında çalışmaz. [Kerkenez sunucusunu](xref:fundamentals/servers/kestrel)kullanın.

Azure'da barındırma hakkında <xref:host-and-deploy/azure-apps/index>daha fazla bilgi için bkz.

Sorun giderme kılavuzu <xref:test/troubleshoot>için bkz.

## <a name="supported-platforms"></a>Desteklenen platformlar

32 bit (x86) veya 64 bit (x64) dağıtım için yayınlanan uygulamalar desteklenir. Uygulama olmadıkça 32 bit (x86) .NET Core SDK içeren bir 32 bit uygulaması dağıtın:

* 64 bit uygulama için kullanılabilen daha büyük sanal bellek adresi alanı gerektirir.
* Büyük IIS yığın boyutunu gerektirir.
* 64 bit yerel bağımlılıkları vardır.

64 bit lik bir uygulama yayınlamak için 64 bit (x64) .NET Core SDK kullanın. Ana bilgisayar sisteminde 64 bit çalışma süresi bulunmalıdır.

[Kestrel sunucusu](xref:fundamentals/servers/kestrel)ile ASP.NET Core gemileri , varsayılan, çapraz platform HTTP sunucusu.

[IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) veya [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)kullanırken, uygulama [Kestrel sunucusu](xref:fundamentals/servers/index#kestrel)ile IIS alt işleminden ayrı bir işlemde *(işlem dışı)* çalışır.

ASP.NET Core uygulamaları IIS alt işleminden ayrı bir işlemde çalıştığı için, modül işlem yönetimini işler. Modül, ilk istek geldiğinde ASP.NET Core uygulaması için süreci başlatır ve kapanDığında veya çökerse uygulamayı yeniden başlatır. Bu aslında [Windows Process Etkinleştirme Hizmeti (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was)tarafından yönetilen süreç içinde çalışan uygulamalarda görülen aynı davranıştır.

Aşağıdaki diyagram, IIS, ASP.NET Çekirdek Modülü ve işlem dışı barındırılan bir uygulama arasındaki ilişkiyi göstermektedir:

![ASP.NET Core Modülü](index/_static/ancm-outofprocess.png)

İstekler web'den çekirdek modu HTTP.sys sürücüsüne gelir. Sürücü, istekleri genellikle 80 (HTTP) veya 443 (HTTPS) olarak web sitesinin yapılandırılmış bağlantı noktasında IIS'ye yönlendirir. Modül, 80 veya 443 bağlantı noktası olmayan uygulama için rastgele bir bağlantı noktası üzerindeki istekleri Kestrel'e iletiyor.

Modül başlangıçta bir ortam değişkeni üzerinden bağlantı noktasını belirtir ve [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) sunucuyu dinleyecek şekilde `http://localhost:{port}`yapılandırır. Ek denetimler gerçekleştirilir ve modülden kaynaklanmıyor istekleri reddedilir. Modül HTTPS iletmesini desteklemez, bu nedenle iIS tarafından HTTPS üzerinden alınsa bile istekler HTTP üzerinden iletilir.

Kestrel modülden isteği aldıktan sonra, istek ASP.NET Core ara yazılım boru hattına itilir. Ara yazılım ardışık alanı isteği işler ve `HttpContext` uygulamanın mantığına örnek olarak aktarın. IIS Integration tarafından eklenen ara yazılım, isteği Kestrel'e iletmek için hesap vermek üzere şemayı, uzak IP'yi ve yol tabanını güncelleştirir. Uygulamanın yanıtı IIS'ye geri aktarılır ve bu da isteği başlatan HTTP istemcisine geri iter.

`CreateDefaultBuilder`[Kerkenez](xref:fundamentals/servers/kestrel) sunucusunu web sunucusu olarak yapılandırır ve [ASP.NET Çekirdek Modülü](xref:host-and-deploy/aspnet-core-module)için temel yolu ve bağlantı noktasını yapılandırarak IIS Entegrasyonusağlar.

ASP.NET Çekirdek Modülü arka uç işlemine atamak için dinamik bir bağlantı noktası oluşturur. `CreateDefaultBuilder`yöntemi <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> çağırır. `UseIISIntegration`localhost IP adresindeki dinamik bağlantı noktasını dinlemek için`127.0.0.1`Kerkenez'i yapılandırır ( ). Dinamik bağlantı noktası 1234 ise, Kerkenez `127.0.0.1:1234`. Bu yapılandırma, şu lar tarafından sağlanan diğer URL yapılandırmalarının yerini alır:

* `UseUrls`
* [Kerkenez's Dinle API](xref:fundamentals/servers/kestrel#endpoint-configuration)
* [Yapılandırma](xref:fundamentals/configuration/index) (veya [komut satırı --urls seçeneği)](xref:fundamentals/host/web-host#override-configuration)

Modülü `UseUrls` kullanırken Kestrel'in API'sine `Listen` yapılan aramalar gerekli değildir. Çağrıldığında `UseUrls` veya `Listen` çağrıldığında, Kestrel yalnızca uygulamayı IIS olmadan çalıştırırken belirtilen bağlantı noktasında dinler.

ASP.NET Çekirdek Modülü yapılandırma <xref:host-and-deploy/aspnet-core-module>kılavuzu için bkz.

Barındırma hakkında daha fazla bilgi için, [ASP.NET Core'da Host'a](xref:fundamentals/index#host)bakın.

## <a name="application-configuration"></a>Uygulama yapılandırması

### <a name="enable-the-iisintegration-components"></a>IISIntegration bileşenlerini etkinleştirme

(Program.cs) içinde `CreateWebHostBuilder` *Program.cs*bir ev <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> sahibi kurarken, IIS entegrasyonunu etkinleştirmek için çağrı:

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        ...
```

Daha fazla `CreateDefaultBuilder`bilgi <xref:fundamentals/host/web-host#set-up-a-host>için bkz.

### <a name="iis-options"></a>IIS seçenekleri

| Seçenek                         | Varsayılan | Ayar |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | IIS `true`Server, Windows `HttpContext.User` Kimlik Doğrulama sı tarafından kimlik doğrulaması yapılandırışlarını [ayarlarsa.](xref:security/authentication/windowsauth) Sunucu `false`yalnızca bir kimlik sağlar `HttpContext.User` ve açıkça tarafından istendiğinde zorluklara `AuthenticationScheme`yanıt verir. Windows Kimlik Doğrulama'nın çalışması `AutomaticAuthentication` için IIS'de etkinleştirilmesi gerekir. Daha fazla bilgi için [Windows Kimlik Doğrulama'ya](xref:security/authentication/windowsauth)bakın. |
| `AuthenticationDisplayName`    | `null`  | Oturum açma sayfalarında kullanıcılara gösterilen görüntü adını ayarlar. |

IIS seçeneklerini yapılandırmak için, <xref:Microsoft.AspNetCore.Builder.IISOptions> için <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>bir hizmet yapılandırması ekleyin. Aşağıdaki örnek, uygulamanın doldurmasını `HttpContext.Connection.ClientCertificate`engeller:

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| Seçenek                         | Varsayılan | Ayar |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | `true`IIS [Integration Middleware,](#enable-the-iisintegration-components) `HttpContext.User` Windows Kimlik Doğrulaması tarafından kimlik doğrulaması yapılan aracı [ayarlarsa.](xref:security/authentication/windowsauth) Eğer, `false`ara yazılım sadece bir `HttpContext.User` kimlik sağlar ve açıkça tarafından talep `AuthenticationScheme`edildiğinde zorluklara yanıt verir. Windows Kimlik Doğrulama'nın çalışması `AutomaticAuthentication` için IIS'de etkinleştirilmesi gerekir. Daha fazla bilgi için [Windows Kimlik Doğrulama](xref:security/authentication/windowsauth) konusuna bakın. |
| `AuthenticationDisplayName`    | `null`  | Oturum açma sayfalarında kullanıcılara gösterilen görüntü adını ayarlar. |
| `ForwardClientCertificate`     | `true`  | İstek `true` üstbilgisi varsa, `MS-ASPNETCORE-CLIENTCERT` `HttpContext.Connection.ClientCertificate` doldurulur. |

### <a name="proxy-server-and-load-balancer-scenarios"></a>Proxy sunucusu ve yük dengeleyici senaryoları

İleriye Dönük Üstbilgileri Ara Yazılım'ı yapılandıran [IIS Tümleştirme Aracı](#enable-the-iisintegration-components)ve ASP.NET Çekirdek Modülü, düzeni (HTTP/HTTPS) ve isteğin kaynağı olduğu uzak IP adresini iletecek şekilde yapılandırılır. Ek proxy sunucuları ve yük dengeleyicileri arkasında barındırılan uygulamalar için ek yapılandırma gerekebilir. Daha fazla bilgi için proxy [sunucuları ve yük dengeleyicileri ile çalışmak için ASP.NET Core'u yapılandırın'](xref:host-and-deploy/proxy-load-balancer)a bakın.

### <a name="webconfig-file"></a>web.config dosyası

*web.config* dosyası ASP.NET [Çekirdek](xref:host-and-deploy/aspnet-core-module)Modül'ünyapılatır. *Web.config* dosyasının oluşturulması, dönüştürülmesi ve yayımlanması, proje`_TransformWebConfig`yayımlandığında bir MSBuild hedefi ( ) tarafından işlenir. Bu hedef Web SDK hedeflerinde`Microsoft.NET.Sdk.Web`mevcuttur ( ). SDK proje dosyasının en üstünde ayarlanır:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

Projede bir *web.config* dosyası yoksa, dosya doğru *işlemYolu* ve ASP.NET Çekirdek Modülyapılandırmak için *bağımsız değişkenler* ile oluşturulur ve [yayımlanmış çıktıya](xref:host-and-deploy/directory-structure)taşınır.

Projede bir *web.config* dosyası varsa, dosya doğru *işlemYolu* ve ASP.NET Çekirdek Modül'ünyapılandırılması için *bağımsız değişkenlerle* dönüştürülür ve yayımlanmış çıktıya taşınır. Dönüştürme, dosyadaki IIS yapılandırma ayarlarını değiştirmez.

*web.config* dosyası, etkin IIS modüllerini kontrol eden ek IIS yapılandırma ayarları sağlayabilir. ASP.NET Core uygulamalarıyla istekleri işleyebilen IIS modülleri hakkında bilgi için [IIS modülleri](xref:host-and-deploy/iis/modules) konusuna bakın.

Web SDK'nın *web.config* dosyasını dönüştürmesini önlemek için proje dosyasındaki ** \<IsTransformWebConfigDisabled>** özelliğini kullanın:

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

Web SDK'nın dosyayı dönüştürmesini devre dışı bırakırken, *processPath* ve *bağımsız değişkenler* geliştirici tarafından el ile ayarlanmalıdır. Daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module>.

### <a name="webconfig-file-location"></a>web.config dosya konumu

[ASP.NET Çekirdek Modülünü](xref:host-and-deploy/aspnet-core-module) doğru ayarlamak *için, web.config* dosyasının dağıtılan uygulamanın [içerik kök](xref:fundamentals/index#content-root) yolunda (genellikle uygulama temel yolu) bulunması gerekir. Burası, IIS'e sağlanan web sitesi fiziksel yolu ile aynı yerdir. Web Deploy kullanarak birden çok uygulamanın yayımlanmasını etkinleştirmek için uygulamanın kökünde *web.config* dosyası gereklidir.

Önemli dosyalar, uygulamanın fiziksel yolunda montaj * \<>.runtimeconfig.json,* * \<montaj>.xml* (XML Documentation comments) ve * \<montaj>.deps.json*gibi vardır. *web.config* dosyası hazır olduğunda ve site normal olarak başladığında, IIS bu hassas dosyaları istenirse sunmaz. *web.config* dosyası eksikse, yanlış adlandırılmışsa veya siteyi normal başlangıç için yapılandıramıyorsa, IIS hassas dosyaları herkese açık olarak sunabilir.

***web.config* dosyası dağıtımda her zaman bulunmalı, doğru adlandırılmış olmalı ve siteyi normal başlatma için yapılandırabilmeli. *Web.config* dosyasını asla üretim dağıtımından kaldırmayın.**

### <a name="transform-webconfig"></a>Web.config’i dönüştürme

Yayımlamada *web.config'i* dönüştürmeniz gerekiyorsa (örneğin, yapılandırma, profil veya ortama <xref:host-and-deploy/iis/transform-webconfig>göre ortam değişkenleri ayarlayın), bkz.

## <a name="iis-configuration"></a>IIS yapılandırması

**Windows Server işletim sistemleri**

Web **Sunucusu (IIS)** sunucu rolünü etkinleştirin ve rol hizmetleri kurun.

1. **Yönet** menüsünden veya **Server Manager'daki**bağlantıdan Rol ve **Özellikler Ekle** sihirbazını kullanın. Sunucu **Rolleri** adımında, Web **Sunucusu (IIS)** kutusunu işaretleyin.

   ![Select sunucu rolleri adımında Web Sunucusu IIS rolü seçilir.](index/_static/server-roles-ws2016.png)

1. **Özellikler** adımından sonra, Web Sunucusu (IIS) için **Rol hizmetleri** adımı yükler. İstenilen IIS rol hizmetlerini seçin veya sağlanan varsayılan rol hizmetlerini kabul edin.

   ![Varsayılan rol hizmetleri Seç rol hizmetleri adımında seçilir.](index/_static/role-services-ws2016.png)

   **Windows Kimlik Doğrulama (İsteğe Bağlı)**  
   Windows Kimlik Doğrulama'yı etkinleştirmek için aşağıdaki düğümleri genişletin: **Web Sunucusu** > **Güvenliği.** Windows **Kimlik Doğrulama** özelliğini seçin. Daha fazla bilgi için [Windows Kimlik Doğrulama \<windowsKimlik doğrulama>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) ve Windows kimlik [doğrulamasını yapılandırma](xref:security/authentication/windowsauth)ya da yapılandırma'ya bakın.

   **WebSockets (İsteğe Bağlı)**  
   WebSockets ASP.NET Core 1.1 veya daha sonra ile desteklenir. WebSockets etkinleştirmek için aşağıdaki düğümleri genişletin: **Web Server** > **Uygulama Geliştirme**. **WebSocket Protokolü** özelliğini seçin. Daha fazla bilgi için [Bkz. WebSockets.](xref:fundamentals/websockets)

1. Web sunucusu rolünü ve hizmetlerini yüklemek için **Onay** adımına devam edin. **Web Sunucusu (IIS)** rolünü yükledikten sonra sunucu/IIS yeniden başlatması gerekmez.

**Windows masaüstü işletim sistemleri**

**IIS Yönetim Konsolu** ve **World Wide Web Hizmetlerini**etkinleştirin.

1. Denetim **Masası** > **Programları** > **ve Özellikleri** > Windows özelliklerini açık veya kapalı (ekranın sol tarafında) **açın.**

1. Internet **Bilgi Hizmetleri** düğümlerini açın. Web **Yönetim Araçları** düğümlerini açın.

1. **IIS Yönetim Konsolu**için kutuyu işaretleyin.

1. World Wide **Web Services**için kutuyu işaretleyin.

1. **World Wide Web Services** için varsayılan özellikleri kabul edin veya IIS özelliklerini özelleştirin.

   **Windows Kimlik Doğrulama (İsteğe Bağlı)**  
   Windows Kimlik Doğrulama'yı etkinleştirmek için aşağıdaki düğümleri genişletin: **World Wide Web Services** > **Security.** Windows **Kimlik Doğrulama** özelliğini seçin. Daha fazla bilgi için [Windows Kimlik Doğrulama \<windowsKimlik doğrulama>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) ve Windows kimlik [doğrulamasını yapılandırma](xref:security/authentication/windowsauth)ya da yapılandırma'ya bakın.

   **WebSockets (İsteğe Bağlı)**  
   WebSockets ASP.NET Core 1.1 veya daha sonra ile desteklenir. WebSockets etkinleştirmek için aşağıdaki düğümleri genişletin: **World Wide Web Services** > **Uygulama Geliştirme Özellikleri.** **WebSocket Protokolü** özelliğini seçin. Daha fazla bilgi için [Bkz. WebSockets.](xref:fundamentals/websockets)

1. IIS yüklemesi yeniden başlatma gerektiriyorsa, sistemi yeniden başlatın.

![Windows Özellikleri'nde IIS Yönetim Konsolu ve World Wide Web Hizmetleri seçilir.](index/_static/windows-features-win10.png)

## <a name="install-the-net-core-hosting-bundle"></a>.NET Core Hosting Paketini yükleyin

*.NET Core Hosting Paketini* barındırma sistemine yükleyin. Pakette .NET Core Runtime, .NET Core Library ve [ASP.NET Çekirdek Modülü](xref:host-and-deploy/aspnet-core-module)yüklenir. Modül, ASP.NET Core uygulamalarının IIS'nin arkasında çalışmasını sağlar.

> [!IMPORTANT]
> Barındırma Paketi IIS'den önce yüklenmişse, paket yüklemesinin onarılması gerekir. IIS'yi yükledikten sonra Barındırma Paketi yükleyicisini yeniden çalıştırın.
>
> Barındırma Paketi .NET Core'un 64 bit (x64) sürümünü yükledikten sonra yüklenirse, SDK'lar eksik görünebilir[(No .NET Core SDK'lar algılandı).](xref:test/troubleshoot#no-net-core-sdks-were-detected) Sorunu çözmek için <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>bkz.

### <a name="download"></a>İndirme

1. [İndir .NET Çekirdek](https://dotnet.microsoft.com/download/dotnet-core) sayfasına gidin.
1. İstenilen .NET Core sürümünü seçin.
1. Çalıştır **uygulamaları - Runtime** sütununda, istenen .NET Core çalışma zamanı sürümünün satırını bulun.
1. Yükleyiciyi Barındırma **Paketi** bağlantısını kullanarak indirin.

> [!WARNING]
> Bazı yükleyiciler, yaşam sürelerine (EOL) ulaşmış ve artık Microsoft tarafından desteklenmeyen sürüm sürümleri içerir. Daha fazla bilgi için [destek ilkesine](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)bakın.

### <a name="install-the-hosting-bundle"></a>Barındırma Paketini Yükleyin

1. Yükleyiciyi sunucuda çalıştırın. Yükleyiciyi bir yönetici komut uyruğundan çalıştırırken aşağıdaki parametreler kullanılabilir:

   * `OPT_NO_ANCM=1`&ndash; ASP.NET Çekirdek Modül'ASP.NET yükleyin.
   * `OPT_NO_RUNTIME=1`&ndash; .NET Core çalışma süresini yüklemeyi atlayın. Sunucu yalnızca bağımsız [dağıtımları (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd)barındırdığında kullanılır.
   * `OPT_NO_SHAREDFX=1`&ndash; Paylaşılan ÇerçeveASP.NET (çalışma süresi ASP.NET) yüklemeyi atlayın. Sunucu yalnızca bağımsız [dağıtımları (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd)barındırdığında kullanılır.
   * `OPT_NO_X86=1`&ndash; X86 çalışma sürelerini yüklemeyi atlayın. 32 bit uygulamaları barındırmayacağımı biliyorsanız bu parametreyi kullanın. Gelecekte hem 32 bit hem de 64 bit uygulamaları barındırma olasılığınız varsa, bu parametreyi kullanmayın ve her iki çalışma zamanını da yükleyin.
   * `OPT_NO_SHARED_CONFIG_CHECK=1`&ndash; Paylaşılan yapılandırma *(applicationHost.config)* IIS yüklemeyle aynı makinede olduğunda, IIS Paylaşılan Yapılandırması kullanma denetimini devre dışı kaldırın. *Yalnızca ASP.NET Core 2.2 veya daha sonra Hosting Bundler yükleyicileri için kullanılabilir.* Daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.
1. Sistemi yeniden başlatın veya komut kabuğunda aşağıdaki komutları çalıştırın:

   ```console
   net stop was /y
   net start w3svc
   ```
   IIS'yi yeniden başlatmak, yükleyici tarafından yapılan bir ortam değişkeni olan sistem PATH'inde bir değişiklik alır.

Barındırma Paketini yüklerken IIS'deki tek tek siteleri el ile durdurmaya gerek yoktur. Barındırılan uygulamalar (IIS siteleri), IIS yeniden başlatıldığında yeniden başlatılır. [Uygulamalar, Uygulama Başlatma Modülü](#application-initialization-module-and-idle-timeout)de dahil olmak üzere ilk isteklerini aldıklarında yeniden başlar.

ASP.NET Core, paylaşılan çerçeve paketlerinin yama sürümleri için roll-forward davranışını benimser. IIS tarafından barındırılan uygulamalar IIS ile yeniden başlatıldığında, uygulamalar ilk isteklerini aldıklarında başvurulan paketlerinin en son yama sürümleriyle yüklenir. IIS yeniden başlatılmazsa, uygulamalar yeniden başlatılır ve çalışan işlemleri geri dönüştürüldüğünde ve ilk isteklerini aldıklarında yuvarlama davranışı sergiler.

> [!NOTE]
> IIS Paylaşılan Yapılandırma hakkında daha fazla bilgi için, [IIS Paylaşılan Yapılandırması ile ASP.NET Çekirdek Modülü'ne](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration)bakın.

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a>Visual Studio ile yayımlarken Web Dağıtımı'nı yükleme

[Uygulamaları Web Dağıtımı'na](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later)sahip sunuculara dağıtırken, Web Dağıtımı'nın en son sürümünü sunucuya yükleyin. Web Dağıtımı'nı yüklemek için [Web Platform Yükleyicisini (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) kullanın veya doğrudan [Microsoft Download Center'dan](https://www.microsoft.com/download/details.aspx?id=43717)bir yükleyici edinin. Tercih edilen yöntem WebPI kullanmaktır. WebPI bağımsız bir kurulum ve barındırma sağlayıcıları için bir yapılandırma sunuyor.

## <a name="create-the-iis-site"></a>IIS sitesini oluşturma

1. Barındırma sisteminde, uygulamanın yayımlanmış klasörlerini ve dosyalarını içerecek bir klasör oluşturun. Aşağıdaki adımda, klasörün yolu uygulamanın fiziksel yolu olarak IIS'ye sağlanır. Bir uygulamanın dağıtım klasörü ve dosya düzeni <xref:host-and-deploy/directory-structure>hakkında daha fazla bilgi için bkz.

1. IIS Manager'da, **Bağlantılar** panelinde sunucu düğümlerini açın. **Siteler** klasörüne sağ tıklayın. Bağlamsal menüden **Web Sitesi Ekle'yi** seçin.

1. Bir **Site adı** sağlayın ve **Uygulamanın** dağıtım klasörüne Fiziksel yolu ayarlayın. **Bağlama** yapılandırmasını sağlayın ve **Tamam'ı**seçerek web sitesini oluşturun:

   ![Site Ekle adımında Site adını, fiziksel yolu ve Ana Bilgisayar adını tedarik edin.](index/_static/add-website-ws2016.png)

   > [!WARNING]
   > Üst düzey joker karakter`http://*:80/` bağlamaları ( ve `http://+:80`) **kullanılmamalıdır.** Üst düzey joker karakter bağlamaları uygulamanızı güvenlik açıklarına açabilir. Bu hem güçlü hem de zayıf joker karakterler için geçerlidir. Joker karakterler yerine açık ana bilgisayar adları kullanın. Alt etki alanı joker karakter `*.mysub.com`bağlama (örneğin, ) tüm üst etki alanını denetlerseniz (bunun `*.com`aksine, savunmasız dır) bu güvenlik riski yoktur. Daha fazla bilgi için [rfc7230 bölüm-5.4'e](https://tools.ietf.org/html/rfc7230#section-5.4) bakın.

1. Sunucudüğümün altında **Uygulama Havuzları'nı**seçin.

1. Sitenin uygulama havuzuna sağ tıklayın ve bağlamsal menüden **Temel Ayarlar'ı** seçin.

1. Uygulama **Havuzunu Edit** penceresinde **,.NET CLR sürümünü** **Yönetilen Kod Yok**olarak ayarlayın:

   ![.NET CLR sürümü için Yönetilen Kod Ayarlama.](index/_static/edit-apppool-ws2016.png)

    ASP.NET Core ayrı bir işlemde çalışır ve çalışma süresini yönetir. ASP.NET Core masaüstü CLR (.NET CLR)&mdash;Çekirdek Ortak Dil Runtime (CoreCLR) .NET Core için yükleme güvenmez alt sürecinde uygulama barındırmak için önyükleme. **.NET CLR sürümünü** **Yönetilen Kodu Yok** olarak ayarlamak isteğe bağlıdır, ancak önerilir.

1. *ASP.NET Core 2.2 veya sonraki*: [İşlem içi barındırma modelini](#in-process-hosting-model)kullanan 64 bit (x64) [bağımsız dağıtım](/dotnet/core/deploying/#self-contained-deployments-scd) için, uygulama havuzunu 32 bit (x86) işlemler için devre dışı açın.

   Uygulama **Havuzları>** IIS Yöneticisi'nin **Eylemler** kenar çubuğunda Uygulama Havuzu Varsayılanlarını veya Gelişmiş Ayarları **Ayarla'yı** seçin. **Advanced Settings** **32 Bit Uygulamaları Etkinleştir'i** bulun `False`ve değeri . Bu ayar, [işlem dışı barındırma](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model)için dağıtılan uygulamaları etkilemez.

1. İşlem modeli kimliğinin uygun izinlere sahip olduğunu onaylayın.

   Uygulama havuzunun varsayılan kimliği **(İşlem Modeli** > **Kimliği)** **ApplicationPoolIdentity'den** başka bir kimliğe değiştirilirse, yeni kimliğin uygulamanın klasörüne, veritabanına ve diğer gerekli kaynaklara erişmek için gerekli izinlere sahip olduğunu doğrulayın. Örneğin, uygulama havuzu, uygulamanın dosyaları okuyup yazdığı klasörlere okuma ve yazma erişimi gerektirir.

**Windows Kimlik Doğrulama yapılandırması (İsteğe bağlı)**  
Daha fazla bilgi için [bkz.](xref:security/authentication/windowsauth)

## <a name="deploy-the-app"></a>Uygulamayı dağıtma

Uygulamayı IIS site oluştur bölümünde oluşturulan IIS **Fiziksel yol** [klasörüne dağıtın.](#create-the-iis-site) [Web Dağıtımı](/iis/publish/using-web-deploy/introduction-to-web-deploy) dağıtım için önerilen mekanizmadır, ancak uygulamayı projenin *yayımlama* klasöründen barındırma sisteminin dağıtım klasörüne taşımak için çeşitli seçenekler vardır.

### <a name="web-deploy-with-visual-studio"></a>Visual Studio ile Web Dağıtımı

Web Dağıtımı'nda kullanılmak üzere bir yayımlama profili oluşturmayı öğrenmek için ASP.NET Core uygulama dağıtım konusu [için Visual Studio'nun profillerini yayımlayın.](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) Barındırma sağlayıcısı bir Yayımlama Profili veya bir oluşturma desteği sağlıyorsa, profillerini indirin ve Visual Studio Publish iletişim kutusunu kullanarak içe **aktarın:**

![İletişim sayfasını yayımlama](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a>Visual Studio dışında Web Dağıtım

[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) komut satırından Visual Studio dışında da kullanılabilir. Daha fazla bilgi için [Web Dağıtım Aracı'na](/iis/publish/using-web-deploy/use-the-web-deployment-tool)bakın.

### <a name="alternatives-to-web-deploy"></a>Web Dağıtımına Alternatifler

Uygulamayı manuel kopya, [Fotokopi,](/windows-server/administration/windows-commands/xcopy) [Robocopy](/windows-server/administration/windows-commands/robocopy)veya [PowerShell](/powershell/)gibi barındırma sistemine taşımak için çeşitli yöntemlerden herhangi birini kullanın.

IIS'ye ASP.NET Çekirdek dağıtımı hakkında daha fazla bilgi için, [IIS yöneticileri](#deployment-resources-for-iis-administrators) için Dağıtım kaynakları bölümüne bakın.

## <a name="browse-the-website"></a>Web sitesine göz atın

Uygulama barındırma sistemine dağıtıldıktan sonra, uygulamanın ortak uç noktalarından birine istekte bulunun.

Aşağıdaki örnekte, site `www.mysite.com` **Port'taki** `80`bir IIS **Host adına** bağlıdır. Bir istek `http://www.mysite.com`yapılır:

![Microsoft Edge tarayıcısı IIS başlangıç sayfasını yükledi.](index/_static/browsewebsite.png)

## <a name="locked-deployment-files"></a>Kilitli dağıtım dosyaları

Uygulama çalışırken dağıtım klasöründeki dosyalar kilitlenir. Kilitli dosyalar dağıtım sırasında üzerine yazılamaz. Kilitli dosyaları dağıtımda serbest bırakmak için aşağıdaki **yaklaşımlardan birini** kullanarak uygulama havuzunu durdurun:

* Proje dosyasında `Microsoft.NET.Sdk.Web` Web Dağıtımı ve başvuruyu kullanın. Web uygulaması dizininin köküne *bir app_offline.htm* dosyası yerleştirilir. Dosya bulunduğunda, ASP.NET Çekirdek Modülü uygulamayı zarif bir şekilde kapatır ve dağıtım sırasında *app_offline.htm* dosyasına hizmet verir. Daha fazla bilgi için [ASP.NET Core Module yapılandırma başvurusuna](xref:host-and-deploy/aspnet-core-module#app_offlinehtm)bakın.
* Sunucudaki IIS Manager'daki uygulama havuzunu el ile durdurun.
* *app_offline.htm'yi* düşürmek için PowerShell'i kullanın (PowerShell 5 veya daha sonra gerektirir):

  ```powershell
  $pathToApp = 'PATH_TO_APP'

  # Stop the AppPool
  New-Item -Path $pathToApp app_offline.htm

  # Provide script commands here to deploy the app

  # Restart the AppPool
  Remove-Item -Path $pathToApp app_offline.htm

  ```

## <a name="data-protection"></a>Veri koruma

[ASP.NET Çekirdek Veri Koruma yığını,](xref:security/data-protection/introduction) kimlik doğrulamasında kullanılan ara [yazılımlar](xref:fundamentals/middleware/index)da dahil olmak üzere birkaç ASP.NET Core ara yazılım tarafından kullanılır. Veri Koruma API'leri kullanıcı koduyla çağrılmasa bile, veri koruması kalıcı bir şifreleme anahtar [deposu](xref:security/data-protection/implementation/key-management)oluşturmak için bir dağıtım komut dosyası yla veya kullanıcı koduyla yapılandırılmalıdır. Veri koruması yapılandırılmamışsa, anahtarlar bellekte tutulur ve uygulama yeniden başlatıldığında atılır.

Uygulama yeniden başlatıldığında anahtarlık bellekte depolanırsa:

* Tüm çerez tabanlı kimlik doğrulama belirteçleri geçersiz kılındı. 
* Kullanıcıların bir sonraki istekleri üzerine yeniden oturum açmaları gerekmektedir. 
* Anahtarlıkla korunan tüm verilerin şifresi artık çözülemez. Buna [CSRF belirteçleri](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) ve [ASP.NET Core MVC TempData tanımlama bilgileri](xref:fundamentals/app-state#tempdata)de dahil olabilir.

Anahtarlığı sürdürmek için IIS altında veri korumasını yapılandırmak için aşağıdaki **yaklaşımlardan birini** kullanın:

* **Veri Koruma Kayıt Defteri Anahtarları Oluşturma**

  ASP.NET Core uygulamaları tarafından kullanılan veri koruma anahtarları, uygulamaların dışındaki kayıt defterinde depolanır. Belirli bir uygulamanın anahtarlarını devam ettirebilmek için uygulama havuzu için kayıt defteri anahtarları oluşturun.

  Bağımsız, webfarm olmayan IIS yüklemeleri için, [Veri Koruma Provision-AutoGenKeys.ps1 PowerShell komut dosyası](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) ASP.NET Core uygulaması ile kullanılan her uygulama havuzu için kullanılabilir. Bu komut dosyası, HKLM kayıt defterinde yalnızca uygulamanın uygulama havuzunun alt işlem hesabının erişebileceği bir kayıt defteri anahtarı oluşturur. Tuşlar makine çapında bir anahtarla DPAPI kullanılarak istirahatte şifrelenir.

  Web çİtleme senaryolarında, bir uygulama veri koruma anahtarlığını depolamak için bir UNC yolunu kullanacak şekilde yapılandırılabilir. Varsayılan olarak, veri koruma anahtarları şifrelenmez. Ağ paylaşımı için dosya izinlerinin uygulamanın altında çalıştığı Windows hesabıyla sınırlı olduğundan emin olun. Bir X509 sertifikası istirahat anahtarları korumak için kullanılabilir. Kullanıcıların sertifika yüklemesine izin verecek bir mekanizma düşünün: Sertifikaları kullanıcının güvenilen sertifika deposuna yerleştirin ve kullanıcının uygulamasının çalıştığı tüm makinelerde kullanılabilir olduğundan emin olun. Ayrıntılar için [ASP.NET Çekirdek Veri Korumayı Yapılandırma'ya](xref:security/data-protection/configuration/overview) bakın.

* **Kullanıcı profilini yüklemek için IIS Uygulama Havuzunu yapılandırın**

  Bu ayar, uygulama havuzuiçin **Gelişmiş Ayarlar'ın** altındaki **İşlem Modeli** bölümündedir. **Yük Kullanıcı** Profilini `True`' ye ayarlayın. `True`Ayarlandığında, anahtarlar kullanıcı profili dizininde depolanır ve kullanıcı hesabına özgü bir anahtarla DPAPI kullanılarak korunur. Anahtarlar *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* klasöründe kalıcıdır.

  Uygulama havuzunun [setProfileEnvironment özniteliği](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) de etkinleştirilmelidir. Varsayılan `setProfileEnvironment` değeri. `true` Bazı senaryolarda (örneğin, Windows `setProfileEnvironment` OS), `false`. Anahtarlar beklendiği gibi kullanıcı profili dizininde depolanmış değilse:

  1. *%windir%/system32/inetsrv/config* klasörüne gidin.
  1. *ApplicationHost.config* dosyasını açın.
  1. Öğeyi `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` bulun.
  1. Özniteliğin `setProfileEnvironment` mevcut olmadığını doğrulayın, bu da `true`değeri varsayılan olarak (veya özniteliğin `true`değerini açıkça ' ya göre ayarlar.

* **Dosya sistemini anahtarlık deposu olarak kullanma**

  [Dosya sistemini anahtarlık deposu olarak kullanmak](xref:security/data-protection/configuration/overview)için uygulama kodunu ayarlayın. Anahtarlık korumak ve sertifikanın güvenilir bir sertifika olduğundan emin olmak için X509 sertifikası kullanın. Sertifika kendi imzası varsa, sertifikayı Güvenilen Kök deposuna yerleştirin.

  Bir web çiftliğinde IIS kullanırken:

  * Tüm makinelerin erişebileceği bir dosya paylaşımı kullanın.
  * Her makineye bir X509 sertifikası dağıtın. [Kodda veri korumayı](xref:security/data-protection/configuration/overview)yapılandırın.

* **Veri koruması için makine çapında bir ilke ayarlama**

  Veri koruma sistemi, Veri Koruma API'lerini tüketen tüm uygulamalar için makine çapında varsayılan bir [ilke](xref:security/data-protection/configuration/machine-wide-policy) ayarlamak için sınırlı bir desteğe sahiptir. Daha fazla bilgi için bkz. <xref:security/data-protection/introduction>.

## <a name="virtual-directories"></a>Sanal Dizinler

[IIS Sanal Dizinleri](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) ASP.NET Core uygulamalarıyla desteklenmez. Bir uygulama [alt uygulama](#sub-applications)olarak barındırılabilir.

## <a name="sub-applications"></a>Alt uygulamalar

Bir ASP.NET Core uygulaması [IIS alt uygulaması (alt uygulama)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications)olarak barındırılabilir. Alt uygulamanın yolu kök uygulamanın URL'sinin bir parçası olur.

Bir alt uygulama, ASP.NET Çekirdek Modül'ASP.NET işleyici olarak içermemelidir. Modül bir alt uygulamanın *web.config* dosyasına işleyici olarak eklenirse, alt uygulamaya göz atmaya çalışırken hatalı config dosyasına başvuran *500.19 Dahili Sunucu Hatası* alınır.

Aşağıdaki örnek, ASP.NET Core alt uygulaması için yayınlanan bir *web.config* dosyasını gösterir:

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <aspNetCore processPath="dotnet" 
      arguments=".\MyApp.dll" 
      stdoutLogEnabled="false" 
      stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

bir ASP.NET Core uygulamasının altında bir non-ASP.NET Core alt uygulaması barındırırken, alt uygulamanın *web.config* dosyasındaki devralınan işleyiciyi açıkça kaldırın:

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <remove name="aspNetCore" />
    </handlers>
    <aspNetCore processPath="dotnet" 
      arguments=".\MyApp.dll" 
      stdoutLogEnabled="false" 
      stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

Alt uygulama içindeki statik varlık bağlantıları tilde-slash (`~/`) gösterimi kullanmalıdır. Tilde-slash gösterimi, alt uygulamanın yol tabanını işlenen göreli bağlantıya hazırlamak için bir [Etiket Yardımcısı'nı](xref:mvc/views/tag-helpers/intro) tetikler. Bir alt uygulama `/subapp_path`için , bir `src="~/image.png"` görüntü ile `src="/subapp_path/image.png"`bağlantılı olarak işlenir . Kök uygulamanın Statik Dosya Middleware statik dosya isteğini işlemez. İstek alt uygulamanın Statik Dosya Middleware tarafından işlenir.

Statik bir varlığın `src` özniteliği mutlak bir yola ayarlanmışsa (örneğin,), `src="/image.png"`bağlantı alt uygulamanın yol tabanı olmadan işlenir. Kök uygulamanın Statik Dosya Middleware kök [uygulamanın web kökünden](xref:fundamentals/index#web-root)varlık hizmet etmeye çalışır , hangi bir *404* sonuçlanır - Statik varlık kök uygulamasından kullanılabilir olmadıkça yanıt bulunamadı.

Başka bir ASP.NET Core uygulaması altında bir ASP.NET Core uygulamasını alt uygulama olarak barındırmak için:

1. Alt uygulama için bir uygulama havuzu kurun. .NET Core için Çekirdek Ortak Dil Çalışma Süresi (CoreCLR) masaüstü CLR (.NET CLR) değil, alt işlemde uygulamayı barındırmak için önyükleme olduğundan **.NET CLR Sürümünü** **Yönetilen Kod alet** e kümeleyin.

1. Kök sitenin altındaki bir klasörde alt uygulama ile IIS Manager kök sitesi ekleyin.

1. IIS Manager'daki alt uygulama klasörüne sağ tıklayın ve **Uygulamaya Dönüştür'ü**seçin.

1. Uygulama **Ekle** iletişim kutusunda, alt uygulama için oluşturduğunuz uygulama havuzunu atamak için **Uygulama Havuzu** için **Seç** düğmesini kullanın. **Tamam'ı**seçin.

İşlem içi barındırma modelini kullanırken alt uygulamaya ayrı bir uygulama havuzu atanması bir gerekliliktir.

Süreç içi barındırma modeli ve ASP.NET Çekirdek Modülü yapılandırma hakkında <xref:host-and-deploy/aspnet-core-module>daha fazla bilgi için bkz.

## <a name="configuration-of-iis-with-webconfig"></a>Web.config ile IIS yapılandırması

IIS yapılandırması, `<system.webServer>` ASP.NET Core Modülü ile ASP.NET Core uygulamaları için işlevsel olan IIS senaryoları için *web.config* bölümünden etkilenir. Örneğin, IIS yapılandırması dinamik sıkıştırma için işlevseldir. IIS dinamik sıkıştırma kullanmak üzere sunucu düzeyinde yapılandırılırsa, uygulamanın `<urlCompression>` *web.config* dosyasındaki öğe ASP.NET Core uygulaması için devre dışı kılabilir.

Daha fazla bilgi edinmek için aşağıdaki kaynaklara bakın:

* [system.webServer>için \<yapılandırma başvurusu](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

Yalıtılmış uygulama havuzlarında çalışan (IIS 10.0 veya sonrası için desteklenen) tek tek uygulamalar için ortam değişkenleri ayarlamak için, IIS başvuru belgelerinde [Çevre Değişkenleri \<ortamıdeğişken>lerinin](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) *AppCmd.exe komut* bölümüne bakın.

## <a name="configuration-sections-of-webconfig"></a>web.config'in yapılandırma bölümleri

*web.config'deki* ASP.NET 4.x uygulamalarının yapılandırma bölümleri ASP.NET Core uygulamaları tarafından yapılandırma için kullanılmaz:

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

ASP.NET Core uygulamaları diğer yapılandırma sağlayıcıları kullanılarak yapılandırılır. Daha fazla bilgi için [Configuration](xref:fundamentals/configuration/index)'a bakın.

## <a name="application-pools"></a>Uygulama Havuzları

Bir sunucuda birden çok web sitesi barındırırken, her uygulamayı kendi uygulama havuzunda çalıştırarak uygulamaları birbirinden yalıtmanızı öneririz. IIS **Web Sitesi Ekle** iletişim kutusu bu yapılandırmaiçin varsayılan dır. Bir **Site adı** sağlandığında, metin otomatik olarak **Uygulama havuzu** metin kutusuna aktarılır. Site eklendiğinde site adı kullanılarak yeni bir uygulama havuzu oluşturulur.

## <a name="application-pool-identity"></a>Uygulama Havuzu Kimliği

Uygulama havuzu kimlik hesabı, bir uygulamanın etki alanları veya yerel hesaplar oluşturmak ve yönetmek zorunda kalmadan benzersiz bir hesap altında çalışmasına olanak tanır. IIS 8.0 veya sonraki işlemlerde, IIS Yönetici İşçi İşlemi (WAS) yeni uygulama havuzunun adını içeren sanal bir hesap oluşturur ve uygulama havuzunun alt işlemlerini varsayılan olarak bu hesap altında çalıştırın. Uygulama havuzu için **Gelişmiş Ayarlar** altındaki IIS Yönetim Konsolunda, **Kimliğin** **ApplicationPoolIdentity'i**kullanmak üzere ayarlandığından emin olun:

![Uygulama havuzu gelişmiş ayarlar iletişim kutusu](index/_static/apppool-identity.png)

IIS yönetim süreci, Windows Güvenlik Sistemi'ndeki uygulama havuzunun adını içeren güvenli bir tanımlayıcı oluşturur. Kaynaklar bu kimlik kullanılarak güvence altına alınabilir. Ancak, bu kimlik gerçek bir kullanıcı hesabı değildir ve Windows Kullanıcı Yönetimi Konsolu'nda görünmez.

IIS alt işlemi uygulamaya daha yüksek erişim gerektiriyorsa, uygulamayı içeren dizini için Erişim Denetim Listesi'ni (ACL) değiştirin:

1. Windows Gezgini'ni açın ve dizine gidin.

1. Dizinin üzerine sağ tıklayın ve **Özellikler'i**seçin.

1. **Güvenlik** sekmesinin **altında, Edit** düğmesini ve ardından **Ekle** düğmesini seçin.

1. **Konumlar** düğmesini seçin ve sistemin seçildiğinden emin olun.

1. Alanı **seçmek için nesne adlarını girin'e** **iIS AppPool\\<app_pool_name>** girin. Adları **Denetle** düğmesini seçin. *DefaultAppPool* için **IIS AppPool\DefaultAppPool**kullanarak isimleri kontrol edin. Adları **Denetle** düğmesi seçildiğinde, nesne adları alanında **DefaultAppPool** değeri gösterilir. Uygulama havuzu adını doğrudan nesne adları alanına girmek mümkün değildir. Nesne adını denetlerken **iIS AppPool\\<>** biçimini app_pool_name kullanın.

   ![Uygulama klasörü için kullanıcıları veya grupları seçin iletişim kutusunu seçin: "DefaultAppPool"un uygulama\" havuzu adı ,"Adları Kontrol Et"i seçmeden önce nesne adları alanında "IIS AppPool" olarak eklenir.](index/_static/select-users-or-groups-1.png)

1. **Tamam'ı**seçin.

   ![Uygulama klasörü için kullanıcıları veya grupları seçin iletişim kutusunu seçin: "Adları Denetle"yi seçtikten sonra nesne adı "DefaultAppPool" nesne adları alanında gösterilir.](index/_static/select-users-or-groups-2.png)

1. Okuma &amp; yürütme izinleri varsayılan olarak verilmelidir. Gerektiğinde ek izinler sağlayın.

Erişim, **ICACLS** aracını kullanarak komut istemiyle de verilebilir. *DefaultAppPool'u* örnek olarak kullanarak aşağıdaki komut kullanılır:

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

Daha fazla bilgi için [icacls](/windows-server/administration/windows-commands/icacls) konusuna bakın.

## <a name="http2-support"></a>HTTP/2 desteği

[HTTP/2,](https://httpwg.org/specs/rfc7540.html) aşağıdaki temel gereksinimleri karşılayan işlem dışı dağıtımlar için desteklenir:

* Windows Server 2016/Windows 10 veya sonrası; IIS 10 veya sonrası
* Genel kullanıma dönük kenar sunucusu bağlantıları HTTP/2'yi kullanır, ancak [Kestrel sunucusuna](xref:fundamentals/servers/kestrel) ters proxy bağlantısı HTTP/1.1 kullanır.
* Hedef çerçeve: HTTP/2 bağlantısı tamamen IIS tarafından işlenir, çünkü işlem dışı dağıtımlar için geçerli değildir.
* TLS 1.2 veya daha sonra bağlantı

Bir HTTP/2 bağlantısı kuruluysa, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) bildirir. `HTTP/1.1`

HTTP/2 varsayılan olarak etkinleştirilir. Http/2 bağlantısı kurulmazsa bağlantılar HTTP/1.1'e geri döner. IIS dağıtımları ile HTTP/2 yapılandırması hakkında daha fazla bilgi için [IIS'deki HTTP/2'ye](/iis/get-started/whats-new-in-iis-10/http2-on-iis)bakın.

## <a name="cors-preflight-requests"></a>CORS ön uçuş istekleri

*Bu bölüm yalnızca .NET Framework'ASP.NET hedefleyen ASP.NET Core uygulamaları için geçerlidir.*

.NET Framework'u hedefleyen bir ASP.NET Core uygulaması için, OPTIONS istekleri Varsayılan olarak IIS'de uygulamaya geçirilemez. UYGULAMANIN IIS işleyicilerini OPTIONS isteklerini geçmek üzere *web.config'de* nasıl yapılandırılabildiğini öğrenmek için [ASP.NET](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works)bkz.

## <a name="deployment-resources-for-iis-administrators"></a>IIS yöneticileri için dağıtım kaynakları

* [IIS belgeleri](/iis)
* [IIS'de IIS Yöneticisi ile Başlarken](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [.NET Core uygulama dağıtımı](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:test/troubleshoot>
* <xref:index>
* [Resmi Microsoft IIS Sitesi](https://www.iis.net/)
* [Windows Server teknik içerik kitaplığı](/windows-server/windows-server)
* [HTTP/2 Üzerinde IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
* <xref:host-and-deploy/iis/transform-webconfig>

::: moniker-end
