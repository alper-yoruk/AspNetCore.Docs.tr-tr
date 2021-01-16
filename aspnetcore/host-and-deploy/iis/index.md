---
title: IIS ile Windows üzerinde ASP.NET Core barındırma
author: rick-anderson
description: Windows Server Internet Information Services (IIS) üzerinde ASP.NET Core uygulamalarını nasıl barındıralabileceğinizi öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/7/2020
no-loc:
- appsettings.json
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
uid: host-and-deploy/iis/index
ms.openlocfilehash: e0354859b08dc5d8a3e1f4b8a8530d61de2e78cf
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253169"
---
# <a name="host-aspnet-core-on-windows-with-iis"></a>IIS ile Windows üzerinde ASP.NET Core barındırma

::: moniker range=">= aspnetcore-5.0"

Internet Information Services (IIS), Web uygulamalarını barındırmak için ASP.NET Core dahil esnek, güvenli ve yönetilebilir bir Web sunucusudur.

## <a name="supported-platforms"></a>Desteklenen platformlar

Aşağıdaki işletim sistemleri desteklenmektedir:

* Windows 7 veya üzeri
* Windows Server 2012 R2 veya üzeri

32-bit (x86) veya 64 bit (x64) dağıtımı için yayımlanan uygulamalar desteklenir. Uygulama dışında 32 bitlik bir uygulamayı 32 bit (x86) .NET Core SDK dağıtma:

* 64 bitlik bir uygulama için kullanılabilir daha büyük sanal bellek adres alanını gerektirir.
* Daha büyük IIS yığın boyutunu gerektirir.
* 64 bitlik yerel bağımlılıklara sahiptir.

## <a name="install-the-aspnet-core-modulehosting-bundle"></a>ASP.NET Core modülünü/barındırma paketini yükler

Aşağıdaki bağlantıyı kullanarak yükleyiciyi indirin:

[Geçerli .NET Core barındırma paketi yükleyicisi (doğrudan indirme)](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

ASP.NET Core modülünü yükleme veya farklı sürümleri yükleme hakkında daha fazla bilgi için bkz. [.NET Core barındırma paketi yükleme](xref:host-and-deploy/iis/hosting-bundle).

## <a name="get-started"></a>başlarken

IIS 'de bir Web sitesi barındırılmasına başlamak için Başlarken [kılavuzumuza](xref:tutorials/publish-to-iis)bakın.

Azure Uygulama Hizmetleri 'nde bir Web sitesi barındırmaya başlamak için bkz. [dağıtım Azure App Service Kılavuzu](xref:host-and-deploy/azure-apps/index).

## <a name="deployment-resources-for-iis-administrators"></a>IIS yöneticileri için dağıtım kaynakları

* [IIS belgeleri](/iis)
* [IIS 'de IIS Yöneticisi 'Ni kullanmaya başlama](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [.NET Core uygulama dağıtımı](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

## <a name="overlapped-recycle"></a>Çakışan geri dönüşüm

Genel olarak, sıfır kesinti dağıtımları için [mavi yeşil dağıtımlar](https://www.martinfowler.com/bliki/BlueGreenDeployment.html) gibi bir model kullanmanızı öneririz. Çakışan geri dönüşüm yardımı gibi özellikler, ancak sıfır kapalı kalma süresi ile ilgili bir dağıtım gerçekleştirebileceğinizi garanti etmez. Daha fazla bilgi için [Bu GitHub sorununa](https://github.com/dotnet/aspnetcore/issues/10117)bakın.

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:test/troubleshoot>
* <xref:index>
* [Resmi Microsoft IIS sitesi](https://www.iis.net/)
* [Windows Server teknik içerik kitaplığı](/windows-server/windows-server)
* [IIS üzerinde HTTP/2](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
* <xref:host-and-deploy/iis/transform-webconfig>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

ASP.NET Core uygulamasını bir IIS sunucusuna yayımlamaya yönelik bir öğretici deneyimi için, bkz <xref:tutorials/publish-to-iis> ..

[.NET Core barındırma paketi 'ni yükler](#install-the-net-core-hosting-bundle)

## <a name="supported-operating-systems"></a>Desteklenen işletim sistemleri

Aşağıdaki işletim sistemleri desteklenmektedir:

* Windows 7 veya üzeri
* Windows Server 2012 R2 veya üzeri

[HTTP.sys sunucusu](xref:fundamentals/servers/httpsys) (eskiden webListener olarak adlandırılır), IIS ile ters proxy yapılandırmasında çalışmaz. [Kestrel sunucusunu](xref:fundamentals/servers/kestrel)kullanın.

Azure 'da barındırma hakkında daha fazla bilgi için bkz <xref:host-and-deploy/azure-apps/index> ..

Sorun giderme kılavuzu için bkz <xref:test/troubleshoot> ..

## <a name="supported-platforms"></a>Desteklenen platformlar

32-bit (x86) veya 64 bit (x64) dağıtımı için yayımlanan uygulamalar desteklenir. Uygulama dışında 32 bitlik bir uygulamayı 32 bit (x86) .NET Core SDK dağıtma:

* 64 bitlik bir uygulama için kullanılabilir daha büyük sanal bellek adres alanını gerektirir.
* Daha büyük IIS yığın boyutunu gerektirir.
* 64 bitlik yerel bağımlılıklara sahiptir.

32 bit (x86) için yayımlanan uygulamalarda IIS uygulama havuzları için 32 bit etkinleştirilmiş olmalıdır. Daha fazla bilgi için [IIS sitesini oluşturma](#create-the-iis-site) bölümüne bakın.

64 bit uygulama yayımlamak için 64 bit (x64) .NET Core SDK kullanın. Ana bilgisayar sisteminde 64 bit çalışma zamanı bulunmalıdır.

## <a name="hosting-models"></a>Barındırma modelleri

### <a name="in-process-hosting-model"></a>İşlem içi barındırma modeli

ASP.NET Core bir uygulama, işlem içi barındırma kullanarak IIS çalışan işlemiyle aynı işlemde çalışır. İşlem içi barındırma, istek dışı barındırmak için gelişmiş performans sağlar çünkü istekler, giden ağ trafiği ile aynı makineye geri döndürülen bir ağ arabirimidir. IIS, [Windows Işlem etkinleştirme hizmeti (was)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was)ile işlem yönetimini işler.

[ASP.NET Core modülü](xref:host-and-deploy/aspnet-core-module):

* Uygulama başlatmayı gerçekleştirir.
  * [CoreCLR](/dotnet/standard/glossary#coreclr)'yi yükler.
  * Çağırır `Program.Main` .
* IIS yerel isteğinin ömrünü işler.

Aşağıdaki diyagramda IIS, ASP.NET Core modülü ve süreçte barındırılan bir uygulama arasındaki ilişki gösterilmektedir:

![İşlem içi barındırma senaryosunda modül ASP.NET Core](index/_static/ancm-inprocess.png)

1. Web 'den çekirdek modu HTTP.sys sürücüsüne bir istek ulaşır.
1. Sürücü, yerel isteği Web sitesinin yapılandırılmış bağlantı noktasında IIS 'ye yönlendirir, genellikle 80 (HTTP) veya 443 (HTTPS).
1. ASP.NET Core modülü yerel isteği alır ve IIS HTTP sunucusuna ( `IISHttpServer` ) geçirir. IIS HTTP sunucusu, isteği yerelden yönetilene dönüştüren bir IIS için işlem içi sunucu uygulamasıdır.

IIS HTTP sunucusu isteği tamamladıktan sonra:

1. İstek ASP.NET Core ara yazılım ardışık düzenine gönderilir.
1. Ara yazılım ardışık düzeni isteği işler ve `HttpContext` uygulamanın mantığına bir örnek olarak geçirir.
1. Uygulamanın yanıtı IIS HTTP sunucusu aracılığıyla IIS 'e geri geçirilir.
1. IIS yanıtı, isteği başlatan istemciye gönderir.

İşlem içi barındırma, mevcut uygulamalar için kabul edilir. ASP.NET Core Web şablonları, işlem içi barındırma modelini kullanır.

`CreateDefaultBuilder`<xref:Microsoft.AspNetCore.Hosting.Server.IServer> <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS%2A> [CoreCLR](/dotnet/standard/glossary#coreclr) 'YI önyüklemek ve uygulamayı IIS çalışan işleminin (veya) içinde barındırmak için yöntemini çağırarak bir örnek ekler `w3wp.exe` `iisexpress.exe` . Performans testleri, bir .NET Core uygulamasını işlem içinde barındırmanın, uygulamayı işlem dışı ve [Kestrel](xref:fundamentals/servers/kestrel)'e yönelik proxy istekleri barındırmakla karşılaştırıldığında önemli ölçüde daha yüksek istek aktarım hızı sağladığını gösterir.

Tek bir dosya yürütülebilir dosyası olarak yayınlanan uygulamalar, işlem içi barındırma modeliyle yüklenemez.

### <a name="out-of-process-hosting-model"></a>İşlem dışı barındırma modeli

ASP.NET Core uygulamalar IIS çalışan işleminden ayrı bir işlemde çalıştığından, ASP.NET Core modülü işlem yönetimini işler. Modül, ilk istek ulaştığında ASP.NET Core uygulama için işlemi başlatır ve kapanırsa veya kilitlenirse uygulamayı yeniden başlatır. Bu aslında, [Windows Işlem etkinleştirme hizmeti (was)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was)tarafından yönetilen işlem içi uygulamalarla birlikte görülen davranışdır.

Aşağıdaki diyagramda IIS, ASP.NET Core modülü ve işlem dışı barındırılan bir uygulama arasındaki ilişki gösterilmektedir:

![İşlem dışı barındırma senaryosunda modül ASP.NET Core](index/_static/ancm-outofprocess.png)

1. İstekler Web 'den çekirdek modu HTTP.sys sürücüsüne ulaşır.
1. Sürücü, Web sitesinin yapılandırılmış bağlantı noktasında istekleri IIS 'ye yönlendirir. Yapılandırılmış bağlantı noktası genellikle 80 (HTTP) veya 443 (HTTPS).
1. Modül, isteği uygulama için rastgele bir bağlantı noktasında Kestrel 'e iletir. Rastgele bağlantı noktası 80 veya 443 değildir.

<!-- make this a bullet list -->
ASP.NET Core modülü başlatma sırasında bir ortam değişkeni aracılığıyla bağlantı noktasını belirtir. <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration%2A>Uzantı, sunucuyu dinlemek üzere yapılandırır `http://localhost:{PORT}` . Ek denetimler gerçekleştirilir ve modülünden kaynaklanmayan istekler reddedilir. Modül HTTPS iletmeyi desteklemiyor. İstekler, HTTPS üzerinden IIS tarafından alınsa bile HTTP üzerinden iletilir.

Kestrel, isteği modülden başlattıktan sonra, istek ASP.NET Core ara yazılım ardışık düzenine iletilir. Ara yazılım ardışık düzeni isteği işler ve `HttpContext` uygulamanın mantığına bir örnek olarak geçirir. IIS tümleştirmesi tarafından eklenen ara yazılım, isteği Kestrel iletmek için düzen, uzak IP ve pathbase 'i hesaba göre güncelleştirir. Uygulamanın yanıtı IIS 'e geri geçirilir ve bu, isteği başlatan HTTP istemcisine geri iletilir.

ASP.NET Core modülü yapılandırma kılavuzu için bkz <xref:host-and-deploy/aspnet-core-module> ..

Barındırma hakkında daha fazla bilgi için bkz. [ASP.NET Core ana bilgisayar](xref:fundamentals/index#host).

## <a name="application-configuration"></a>Uygulama yapılandırması

### <a name="enable-the-iisintegration-components"></a>Iısıntegration bileşenlerini etkinleştirin

() İçinde bir konak `CreateHostBuilder` oluştururken `Program.cs` <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> IIS tümleştirmesini etkinleştirmek için çağırın:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        ...
```

Hakkında daha fazla bilgi için `CreateDefaultBuilder` bkz <xref:fundamentals/host/generic-host#default-builder-settings> ..

### <a name="iis-options"></a>IIS seçenekleri

**İşlem içi barındırma modeli**

IIS sunucu seçeneklerini yapılandırmak için, içinde için bir hizmet yapılandırması <xref:Microsoft.AspNetCore.Builder.IISServerOptions> ekleyin <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A> . Aşağıdaki örnek, Automatıcauthentication 'ı devre dışı bırakır:

```csharp
services.Configure<IISServerOptions>(options => 
{
    options.AutomaticAuthentication = false;
});
```

| Seçenek                         | Varsayılan | Ayar |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | `true`IIS sunucusu, `HttpContext.User` [Windows kimlik doğrulaması](xref:security/authentication/windowsauth)tarafından kimliği doğrulanmış olarak ayarlanır. İse `false` , sunucu yalnızca bir kimlik sağlar `HttpContext.User` ve tarafından açıkça istendiğinde sorunları yanıtlar `AuthenticationScheme` . ' Nin çalışması için IIS 'de Windows kimlik doğrulamasının etkinleştirilmesi gerekir `AutomaticAuthentication` . Daha fazla bilgi için bkz. [Windows kimlik doğrulaması](xref:security/authentication/windowsauth). |
| `AuthenticationDisplayName`    | `null`  | Oturum açma sayfalarındaki kullanıcılara gösterilen görünen adı ayarlar. |
| `AllowSynchronousIO`           | `false` | Ve için zaman uyumlu g/ç 'ye izin verilip verilmeyeceğini belirtir `HttpContext.Request` `HttpContext.Response` . |
| `MaxRequestBodySize`           | `30000000`  | İçin en büyük istek gövdesi boyutunu alır veya ayarlar `HttpRequest` . IIS 'nin `maxAllowedContentLength` , içindeki kümesinden önce işlenecek sınıra sahip olduğunu unutmayın `MaxRequestBodySize` `IISServerOptions` . ' Nin değiştirilmesi `MaxRequestBodySize` , etkilemez `maxAllowedContentLength` . Arttırmak için `maxAllowedContentLength` ' de bir giriş ekleyerek `web.config` `maxAllowedContentLength` daha yüksek bir değere ayarlayın. Daha fazla ayrıntı için bkz. [yapılandırma](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/#configuration). |

**İşlem dışı barındırma modeli**

IIS seçeneklerini yapılandırmak için, içinde için bir hizmet yapılandırması <xref:Microsoft.AspNetCore.Builder.IISOptions> ekleyin <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A> . Aşağıdaki örnek, uygulamanın doldurulmasını önler `HttpContext.Connection.ClientCertificate` :

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| Seçenek                         | Varsayılan | Ayar |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | `true` [IIS tümleştirme ara yazılımı](#enable-the-iisintegration-components) , `HttpContext.User` kimliği doğrulanmış [Windows kimlik doğrulaması](xref:security/authentication/windowsauth)tarafından ayarlanır. İse `false` , ara yazılım için yalnızca bir kimlik sağlar `HttpContext.User` ve tarafından açıkça istendiğinde sorunları yanıtlar `AuthenticationScheme` . ' Nin çalışması için IIS 'de Windows kimlik doğrulamasının etkinleştirilmesi gerekir `AutomaticAuthentication` . Daha fazla bilgi için [Windows kimlik doğrulaması](xref:security/authentication/windowsauth) konusuna bakın. |
| `AuthenticationDisplayName`    | `null`  | Oturum açma sayfalarındaki kullanıcılara gösterilen görünen adı ayarlar. |
| `ForwardClientCertificate`     | `true`  | `true`Ve `MS-ASPNETCORE-CLIENTCERT` istek üst bilgisi varsa, `HttpContext.Connection.ClientCertificate` doldurulur. |

### <a name="proxy-server-and-load-balancer-scenarios"></a>Proxy sunucusu ve yük dengeleyici senaryoları

[IIS tümleştirme ara yazılımı](#enable-the-iisintegration-components) ve ASP.NET Core modülü, iletmek üzere yapılandırılır:

* Düzen (HTTP/HTTPS).
* İsteğin kaynaklandığı uzak IP adresi.

[IIS tümleştirme ara yazılımı](#enable-the-iisintegration-components) , Iletilen üstbilgiler ara yazılımını yapılandırır.

Ek proxy sunucularının ve yük dengeleyiciler arkasında barındırılan uygulamalar için ek yapılandırma gerekebilir. Daha fazla bilgi için bkz. [proxy sunucularıyla ve yük dengeleyicilerle çalışacak ASP.NET Core yapılandırma](xref:host-and-deploy/proxy-load-balancer).

### <a name="webconfig-file"></a>`web.config` dosyasýný

`web.config`Dosya [ASP.NET Core modülünü](xref:host-and-deploy/aspnet-core-module)yapılandırır. Proje yayımlandığında dosyanın oluşturulması, dönüştürülmesi ve yayımlanması `web.config` bir MSBuild hedefi () tarafından işlenir `_TransformWebConfig` . Bu hedef, Web SDK hedeflerinde () bulunur `Microsoft.NET.Sdk.Web` . SDK proje dosyasının en üstünde ayarlanır:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

Projede bir `web.config` Dosya yoksa, dosya doğru şekilde oluşturulur `processPath` ve `arguments` ASP.NET Core modülünü yapılandırıp [yayımlanan çıktıya](xref:host-and-deploy/directory-structure)taşınır.

Projede bir `web.config` dosya varsa, dosya doğru şekilde dönüştürülür `processPath` ve `arguments` ASP.NET Core modülünü yapılandırıp yayımlanan çıktıya taşınır. Dönüştürme, dosyadaki IIS yapılandırma ayarlarını değiştirmez.

`web.config`Dosya, ETKIN IIS modüllerini denetleyen ek IIS yapılandırma ayarları sağlayabilir. ASP.NET Core uygulamalarla istekleri işleyebilen IIS modülleri hakkında daha fazla bilgi için bkz. [IIS modules](xref:host-and-deploy/iis/modules) konusu.

Web SDK 'sının dosyayı dönüştürülmesini engellemek için `web.config` , `<IsTransformWebConfigDisabled>` Proje dosyasındaki özelliğini kullanın:

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

Web SDK 'sının dosyayı dönüştürmesiyle devre dışı bırakılırken, `processPath` ve `arguments` geliştiricisi tarafından el ile ayarlanması gerekir. Daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module>.

### <a name="webconfig-file-location"></a>`web.config` dosya konumu

[ASP.NET Core modülünü](xref:host-and-deploy/aspnet-core-module) doğru bir şekilde ayarlamak için, `web.config` dağıtılan uygulamanın [içerik kök](xref:fundamentals/index#content-root) yolunda (genellikle uygulama temel yolu), dosyanın mevcut olması gerekir. Bu, IIS 'ye sunulan Web sitesi fiziksel yoluyla aynı konumdadır. `web.config`Web dağıtımı kullanarak birden çok uygulama yayımlamayı etkinleştirmek için uygulamanın kökünde dosya gereklidir.

Gizli dosyalar, uygulamanın fiziksel yolunda `{ASSEMBLY}.runtimeconfig.json` `{ASSEMBLY}.xml` (XML belge açıklamaları) bulunur ve `{ASSEMBLY}.deps.json` burada yer tutucunun `{ASSEMBLY}` derleme adıdır. `web.config`Dosya mevcut olduğunda ve site normal olarak başlatıldığında, ISTENIRSE IIS bu hassas dosyaları sunmaz. `web.config`Dosya eksikse, yanlış şekilde adlandırılmış veya site normal başlatma için YAPıLANDıRıMıŞSA IIS hassas dosyalara genel olarak sunabilir.

**`web.config`Dosyanın her zaman dağıtımda mevcut olması, doğru şekilde adlandırılması ve siteyi normal başlangıç için yapılandırabiliyor olması gerekir. `web.config`Dosyayı bir üretim dağıtımından hiçbir şekilde kaldırmayın.**

### <a name="transform-webconfig"></a>Web.config’i dönüştürme

`web.config`Yayımlama sırasında dönüştürmeniz gerekiyorsa bkz <xref:host-and-deploy/iis/transform-webconfig> .. `web.config`Yapılandırma, profil veya ortama göre ortam değişkenlerini ayarlamak için Yayımla ' ya dönüştürmeniz gerekebilir.

## <a name="iis-configuration"></a>IIS yapılandırması

**Windows Server işletim sistemleri**

**Web sunucusu (IIS)** sunucu rolünü etkinleştirin ve rol hizmetleri oluşturun.

1. **Yönet** menüsündeki **rol ve özellik ekleme** sihirbazı ' nı veya **Sunucu Yöneticisi** bağlantısındaki bağlantıyı kullanın. **Sunucu rolleri** adımında, **Web sunucusu (IIS)** kutusunu işaretleyin.

   ![Sunucu rollerini seçin adımında Web sunucusu IIS rolü seçilidir.](index/_static/server-roles-ws2016.png)

1. **Özellikler** adımından sonra, Web sunucusu (IIS) için **rol hizmetleri** adımı yüklenir. İstenen IIS rol hizmetlerini seçin veya varsayılan rol hizmetlerini kabul edin.

   ![Rol hizmetlerini seçin adımında varsayılan rol hizmetleri seçilidir.](index/_static/role-services-ws2016.png)

   **Windows kimlik doğrulaması (Isteğe bağlı)**  
   Windows kimlik doğrulamasını etkinleştirmek için şu düğümleri genişletin: **Web sunucusu**  >  **güvenliği**. **Windows kimlik doğrulama** özelliğini seçin. Daha fazla bilgi için bkz. [Windows `<windowsAuthentication>` kimlik doğrulaması](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) ve [Windows kimlik doğrulamasını yapılandırma](xref:security/authentication/windowsauth).

   **WebSockets (Isteğe bağlı)**  
   WebSockets ASP.NET Core 1,1 veya üzeri bir sürümde desteklenir. WebSockets etkinleştirmek için şu düğümleri genişletin: **Web sunucusu**  >  **uygulama geliştirme**. **WebSocket protokolü** özelliğini seçin. Daha fazla bilgi için bkz. [WebSockets](xref:fundamentals/websockets).

1. Web sunucusu rolü ve hizmetlerini yüklemek için **onay** adımına ilerleyin. **Web sunucusu (IIS)** rolü yüklendikten sonra sunucu/IIS yeniden başlatması gerekli değildir.

**Windows masaüstü işletim sistemleri**

**IIS Yönetim Konsolu** ve **World Wide Web hizmetlerini** etkinleştirin.

1. **Denetim Masası** > **programları** > **Programlar ve Özellikler** > **Windows özelliklerini aç veya kapat** (ekranın sol tarafında).

1. **Internet Information Services** düğümünü açın. **Web yönetimi araçları** düğümünü açın.

1. **IIS Yönetim Konsolu** kutusunu işaretleyin.

1. **World Wide Web Hizmetleri** kutusunu işaretleyin.

1. **World Wide Web Hizmetleri** için varsayılan özellikleri kabul edın veya IIS özelliklerini özelleştirin.

   **Windows kimlik doğrulaması (Isteğe bağlı)**  
   Windows kimlik doğrulamasını etkinleştirmek için şu düğümleri genişletin: **World Wide Web Hizmetleri**  >  **güvenliği**. **Windows kimlik doğrulama** özelliğini seçin. Daha fazla bilgi için bkz. [Windows \<windowsAuthentication> kimlik doğrulaması](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) ve [Windows kimlik doğrulamasını yapılandırma](xref:security/authentication/windowsauth).

   **WebSockets (Isteğe bağlı)**  
   WebSockets ASP.NET Core 1,1 veya üzeri bir sürümde desteklenir. WebSockets etkinleştirmek için şu düğümleri genişletin: **World Wide Web Services**  >  **uygulaması geliştirme özellikleri**. **WebSocket protokolü** özelliğini seçin. Daha fazla bilgi için bkz. [WebSockets](xref:fundamentals/websockets).

1. IIS yüklemesi için yeniden başlatma gerekiyorsa, sistemi yeniden başlatın.

![IIS Yönetim Konsolu ve World Wide Web Hizmetleri Windows Özellikleri ' nde seçilidir.](index/_static/windows-features-win10.png)

## <a name="install-the-net-core-hosting-bundle"></a>.NET Core barındırma paketi 'ni yükler

*.NET Core barındırma paketi* 'ni barındırma sistemine yükler. Paket, .NET Core çalışma zamanı, .NET Core kitaplığı ve [ASP.NET Core modülünü](xref:host-and-deploy/aspnet-core-module)de yüklüyor. Modül ASP.NET Core uygulamaların IIS 'nin arkasında çalışmasına izin verir.

> [!IMPORTANT]
> Barındırma paketi IIS 'den önce yüklendiyse, paket yüklemesi onarılması gerekir. IIS yükledikten sonra barındırma paketi yükleyicisini yeniden çalıştırın.
>
> .NET Core 'un 64 bit (x64) sürümünü yükledikten sonra barındırma paketi yüklenirse, SDK 'lar eksik gibi görünebilir ([hiçbir .NET Core SDK 'sı algılanmadı](xref:test/troubleshoot#no-net-core-sdks-were-detected)). Sorunu çözmek için bkz <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle> ..

### <a name="direct-download-current-version"></a>Doğrudan indirme (geçerli sürüm)

Aşağıdaki bağlantıyı kullanarak yükleyiciyi indirin:

[Geçerli .NET Core barındırma paketi yükleyicisi (doğrudan indirme)](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

### <a name="earlier-versions-of-the-installer"></a>Yükleyicinin önceki sürümleri

Yükleyicinin önceki bir sürümünü elde etmek için:

1. [.NET Core 'U indir](https://dotnet.microsoft.com/download/dotnet-core) sayfasına gidin.
1. İstediğiniz .NET Core sürümünü seçin.
1. **Uygulamaları Çalıştır-çalışma zamanı** sütununda, Istenen .NET Core çalışma zamanı sürümünün satırını bulun.
1. **Barındırma paketi** bağlantısını kullanarak yükleyiciyi indirin.

> [!WARNING]
> Bazı yükleyicilerle yaşam süresi (EOL) gelmiş olan ve artık Microsoft tarafından desteklenmeyen yayın sürümlerini içerir. Daha fazla bilgi için bkz. [destek ilkesi](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).

### <a name="install-the-hosting-bundle"></a>Barındırma paketini yükler

1. Yükleyiciyi sunucuda çalıştırın. Aşağıdaki parametreler, yükleyiciyi yönetici komut kabuğu 'ndan çalıştırırken kullanılabilir:

   * `OPT_NO_ANCM=1`: ASP.NET Core modülünü yüklemeyi atlayın.
   * `OPT_NO_RUNTIME=1`: .NET Core çalışma zamanını yüklemeyi atlayın. Sunucu yalnızca [kendi kendine içerilen dağıtımları (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd)barındırdığınızda kullanılır.
   * `OPT_NO_SHAREDFX=1`: ASP.NET paylaşılan çerçevesini (ASP.NET çalışma zamanı) yüklemeyi atlayın. Sunucu yalnızca [kendi kendine içerilen dağıtımları (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd)barındırdığınızda kullanılır.
   * `OPT_NO_X86=1`: X86 çalışma zamanlarını yüklemeyi atlayın. 32 bitlik uygulamalar barındırmayabildiğinizi bildiğiniz durumlarda bu parametreyi kullanın. Gelecekte 32-bit ve 64 bit uygulamaları barındırabilmeniz gereken herhangi bir şansınız varsa, bu parametreyi kullanmayın ve her iki çalışma zamanını da yüklemeyin.
   * `OPT_NO_SHARED_CONFIG_CHECK=1`: Paylaşılan yapılandırma ( `applicationHost.config` ), IIS yüklemesiyle aynı makineli olduğunda IIS paylaşılan yapılandırması kullanma denetimini devre dışı bırakın. *Yalnızca ASP.NET Core 2,2 veya sonraki bir sürümü Paketcisi yükleyicilerini barındırmak için kullanılabilir.* Daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.
1. Sistemi yeniden başlatın veya komut kabuğu 'nda aşağıdaki komutları yürütün:

   ```console
   net stop was /y
   net start w3svc
   ```
   IIS 'nin yeniden başlatılması, yükleyici tarafından oluşturulan bir ortam değişkeni olan sistem yolunda bir değişiklik seçer.

ASP.NET Core, paylaşılan çerçeve paketlerinin yama sürümleri için geri iletme davranışını benimsemez. Yeni bir barındırma paketi yükleyerek paylaşılan çerçeveyi yükselttikten sonra, sistemi yeniden başlatın veya komut kabuğu 'nda aşağıdaki komutları yürütün:

```console
net stop was /y
net start w3svc
```

> [!NOTE]
> IIS paylaşılan Yapılandırması hakkında bilgi için bkz. [IIS paylaşılan yapılandırması ile ASP.NET Core modülü](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a>Visual Studio ile yayımlarken Web Dağıtımı yüklemesi

Uygulamaları [Web dağıtımı](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later)olan sunuculara dağıttığınızda, en son Web dağıtımı sürümünü sunucuya yüklersiniz. Web Dağıtımı yüklemek için [Web Platformu Yükleyicisi 'ni (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) kullanın veya doğrudan [Microsoft İndirme Merkezi](https://www.microsoft.com/download/details.aspx?id=43717)'nden bir yükleyici edinin. Tercih edilen yöntem, WebPI kullanmaktır. WebPI, barındırma sağlayıcıları için tek başına kurulum ve yapılandırma sağlar.

## <a name="create-the-iis-site"></a>IIS sitesini oluşturma

1. Barındırma sisteminde, uygulamanın yayımlanan klasörlerini ve dosyalarını içeren bir klasör oluşturun. Aşağıdaki adımda, klasörün yolu, uygulamanın fiziksel yolu olarak IIS 'ye sağlanır. Uygulamanın dağıtım klasörü ve dosya düzeni hakkında daha fazla bilgi için bkz <xref:host-and-deploy/directory-structure> ..

1. IIS Yöneticisi 'nde, **Bağlantılar** panelinde sunucunun düğümünü açın. **Siteler** klasörüne sağ tıklayın. Bağlamsal menüden **Web sitesi Ekle** ' yi seçin.

1. Bir **site adı** belirtin ve **fiziksel yolu** uygulamanın dağıtım klasörüne ayarlayın. **Bağlama** yapılandırmasını sağlayın ve **Tamam**' ı seçerek Web sitesini oluşturun:

   ![Web sitesi Ekle adımında site adı, fiziksel yol ve ana bilgisayar adını sağlayın.](index/_static/add-website-ws2016.png)

   > [!WARNING]
   > Üst düzey joker karakter bağlamaları ( `http://*:80/` ve `http://+:80` ) kullanılmamalıdır  . Üst düzey joker karakter bağlamaları, uygulamanızı güvenlik açıklarına açabilir. Bu hem güçlü hem de zayıf Joker karakterlere yöneliktir. Joker karakterler yerine açık ana bilgisayar adları kullanın. Alt etki alanı joker bağlantısı (örneğin, `*.mysub.com` ), tüm üst etki alanını (Bu güvenlik açığı olan aksine) kontrol ediyorsanız bu güvenlik riskine sahip değildir `*.com` . Daha fazla bilgi için bkz. [rfc7230 Section-5,4](https://tools.ietf.org/html/rfc7230#section-5.4) .

1. Sunucu düğümünün altında **uygulama havuzları**' nı seçin.

1. Sitenin uygulama havuzuna sağ tıklayın ve bağlam menüsünden **temel ayarlar** ' ı seçin.

1. **Uygulama havuzunu Düzenle** penceresinde, **.NET CLR sürümünü** **yönetilen kod olmadan** ayarlayın:

   ![.NET CLR sürümü için yönetilen kod ayarlama.](index/_static/edit-apppool-ws2016.png)

    ASP.NET Core ayrı bir işlemde çalışır ve çalışma zamanını yönetir. ASP.NET Core, masaüstü CLR 'yi (.NET CLR) yüklemeye bağlı değildir. .NET Core için çekirdek ortak dil çalışma zamanı (CoreCLR), uygulamayı çalışan işlemde barındırmak için önyüklenir. **.NET CLR sürümünün** **yönetilen kod olmadan** ayarlanması isteğe bağlıdır, ancak önerilir.

1. *ASP.NET Core 2,2 veya üzeri*:

   * [İşlem içi barındırma modelini](#in-process-hosting-model)kullanan 32 bit SDK ile yayımlanmış 32-bit (x86) bir [dağıtımı](/dotnet/core/deploying/#self-contained-deployments-scd) için, uygulama havuzunu 32 bit olarak etkinleştirin. IIS Yöneticisi 'nde **Bağlantılar** kenar çubuğunda **uygulama havuzları** ' na gidin. Uygulamanın uygulama havuzunu seçin. **Eylemler** kenar çubuğunda **Gelişmiş ayarlar**' ı seçin. **32 bitlik uygulamaları etkinleştir** olarak ayarlayın `True` . 

   * [İşlem içi barındırma modelini](#in-process-hosting-model)kullanan 64 bitlik (x64) bir [dağıtım](/dotnet/core/deploying/#self-contained-deployments-scd) için, 32 bit (x86) işlemleri için uygulama havuzunu devre dışı bırakın. IIS Yöneticisi 'nde **Bağlantılar** kenar çubuğunda **uygulama havuzları** ' na gidin. Uygulamanın uygulama havuzunu seçin. **Eylemler** kenar çubuğunda **Gelişmiş ayarlar**' ı seçin. **32 bitlik uygulamaları etkinleştir** olarak ayarlayın `False` . 

1. İşlem modeli kimliğinin uygun izinlere sahip olduğunu doğrulayın.

   Uygulama havuzunun varsayılan kimliği (**işlem modeli**  >  **Identity** ) **applicationPool Identity** 'den başka bir kimliğe değiştirilirse, yeni kimliğin uygulamanın klasörüne, veritabanına ve diğer gerekli kaynaklara erişmek için gerekli izinlere sahip olduğunu doğrulayın. Örneğin, uygulama havuzu, uygulamanın dosyaları okuduğu ve yazdığı klasörlere okuma ve yazma erişimi gerektirir.

**Windows kimlik doğrulama yapılandırması (Isteğe bağlı)**  
Daha fazla bilgi için bkz. [Windows kimlik doğrulamasını yapılandırma](xref:security/authentication/windowsauth).

## <a name="deploy-the-app"></a>Uygulamayı dağıtma

Uygulamayı [IIS sitesi oluşturma](#create-the-iis-site) bölümünde oluşturulan IIS **fiziksel yol** klasörüne dağıtın. [Web dağıtımı](/iis/publish/using-web-deploy/introduction-to-web-deploy) dağıtım için önerilen mekanizmadır, ancak uygulamayı projenin `publish` klasöründen barındırma sisteminin dağıtım klasörüne taşımak için çeşitli seçenekler mevcuttur.

### <a name="web-deploy-with-visual-studio"></a>Visual Studio ile Web Dağıtımı

Web Dağıtımı kullanılacak bir yayımlama profili oluşturma hakkında bilgi edinmek için bkz. [ASP.NET Core App Deployment Için Visual Studio yayımlama profilleri](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) konusuna bakın. Barındırma sağlayıcısı, bir yayımlama profili veya oluşturma desteği sağlıyorsa, profilini indirin ve Visual Studio **Yayımlama** iletişim kutusunu kullanarak içeri aktarın:

![Yayımla iletişim sayfası](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a>Visual Studio dışında Web Dağıtımı

[Web dağıtımı](/iis/publish/using-web-deploy/introduction-to-web-deploy) , komut satırından Visual Studio dışında da kullanılabilir. Daha fazla bilgi için bkz. [Web Dağıtım aracı](/iis/publish/using-web-deploy/use-the-web-deployment-tool).

### <a name="alternatives-to-web-deploy"></a>Web Dağıtımı alternatifleri

Uygulamayı barındırma sistemine taşımak için el ile kopyalama, [xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy)veya [PowerShell](/powershell/)gibi çeşitli yöntemlerden birini kullanın.

IIS 'ye dağıtım ASP.NET Core hakkında daha fazla bilgi için, [IIS yöneticileri Için dağıtım kaynakları](#deployment-resources-for-iis-administrators) bölümüne bakın.

## <a name="browse-the-website"></a>Web sitesine gidin

Uygulama barındırma sistemine dağıtıldıktan sonra, uygulamanın genel uç noktalarından birine bir istek oluşturun.

Aşağıdaki örnekte, site bağlantı noktasındaki IIS **ana bilgisayar adına** bağlıdır `www.mysite.com`  `80` . Bir istek şu şekilde yapılır `http://www.mysite.com` :

![Microsoft Edge tarayıcısı, IIS başlangıç sayfasını yükledi.](index/_static/browsewebsite.png)

## <a name="locked-deployment-files"></a>Kilitli dağıtım dosyaları

Dağıtım klasöründeki dosyalar, uygulama çalışırken kilitlenir. Dağıtım sırasında kilitli dosyaların üzerine yazılamaz. Kilitli dosyaları bir dağıtımda serbest bırakmak için aşağıdaki yaklaşımlardan **birini** kullanarak uygulama havuzunu durdurun:

* Proje dosyasında Web Dağıtımı ve başvurusunu kullanın `Microsoft.NET.Sdk.Web` . `app_offline.htm`Web uygulaması dizininin köküne bir dosya yerleştirilir. Dosya olduğunda, ASP.NET Core modülü uygulamayı düzgün bir şekilde kapatır ve `app_offline.htm` dağıtım sırasında dosyayı hizmet eder. Daha fazla bilgi için [ASP.NET Core modülü yapılandırma başvurusuna](xref:host-and-deploy/aspnet-core-module#app_offlinehtm)bakın.
* Sunucu üzerindeki IIS Yöneticisi 'nde uygulama havuzunu el ile durdurun.
* Atmak için PowerShell 'i kullanın `app_offline.htm` (PowerShell 5 veya üzerini gerektirir):

  ```powershell
  $pathToApp = 'PATH_TO_APP'

  # Stop the AppPool
  New-Item -Path $pathToApp app_offline.htm

  # Provide script commands here to deploy the app

  # Restart the AppPool
  Remove-Item -Path $pathToApp app_offline.htm
  ```

## <a name="data-protection"></a>Veri koruma

[ASP.NET Core veri koruma yığını](xref:security/data-protection/introduction) , kimlik doğrulamasında kullanılan ara yazılımlar dahil olmak üzere birkaç ASP.NET Core [middlewares](xref:fundamentals/middleware/index)tarafından kullanılır. Veri koruma API 'Leri Kullanıcı kodu tarafından çağrılmasa bile, kalıcı bir şifreleme [anahtarı deposu](xref:security/data-protection/implementation/key-management)oluşturmak için veri koruma bir dağıtım betiği veya Kullanıcı kodu ile yapılandırılmalıdır. Veri koruması yapılandırılmamışsa, anahtarlar bellekte tutulur ve uygulama yeniden başlatıldığında atılır.

Uygulama yeniden başlatıldığında anahtar halkası bellekte depolanıyorsa:

* Tüm cookie tabanlı kimlik doğrulama belirteçleri geçersiz kılındı. 
* Kullanıcıların bir sonraki isteğinde yeniden oturum açması gerekir. 
* Anahtar halkası ile korunan tüm veriler artık çözülemez. Bu, [CSRF belirteçlerini](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) ve [ASP.NET Core MVC TempData cookie s](xref:fundamentals/app-state#tempdata)öğesini içerebilir.

Anahtar halkasını sürdürmek için IIS altındaki veri korumasını yapılandırmak için aşağıdaki yaklaşımlardan **birini** kullanın:

* **Veri koruma kayıt defteri anahtarları oluşturma**

  ASP.NET Core uygulamalar tarafından kullanılan veri koruma anahtarları, uygulamaların dış kayıt defterinde saklanır. Belirli bir uygulamanın anahtarlarını kalıcı hale getirmek için, uygulama havuzu için kayıt defteri anahtarları oluşturun.

  Tek başına, Web grubu olmayan IIS yüklemeleri için, [veri koruma Provision-AutoGenKeys.ps1 PowerShell betiği](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) , bir ASP.NET Core uygulamasıyla kullanılan her uygulama havuzu için kullanılabilir. Bu betik, yalnızca uygulamanın uygulama havuzunun çalışan işlem hesabına erişilebilen HKLM Kayıt defterinde bir kayıt defteri anahtarı oluşturur. Anahtarlar, makine genelindeki bir anahtarla DPAPI kullanılarak şifrelenir.

  Web grubu senaryolarında bir uygulama, veri koruma anahtar halkasını depolamak için bir UNC yolu kullanacak şekilde yapılandırılabilir. Varsayılan olarak, veri koruma anahtarları şifrelenmez. Ağ paylaşımının dosya izinlerinin, uygulamanın çalıştırıldığı Windows hesabıyla sınırlı olduğundan emin olun. Bir x509 sertifikası, bekleyen anahtarları korumak için kullanılabilir. Kullanıcıların sertifikaları karşıya yüklemesine izin vermek için bir mekanizma düşünün: sertifikaları kullanıcının güvenilen sertifika deposuna yerleştir ve kullanıcının uygulamasının çalıştığı tüm makinelerde kullanılabilir olduklarından emin olun. Ayrıntılar için bkz. [ASP.NET Core veri korumasını yapılandırma](xref:security/data-protection/configuration/overview) .

* **Kullanıcı profilini yüklemek için IIS uygulama havuzunu yapılandırma**

  Bu ayar, uygulama havuzunun **Gelişmiş ayarları** altındaki **işlem modeli** bölümünde bulunur. **Kullanıcı profilini yükle** ' ye ayarlayın `True` . Olarak ayarlandığında `True` anahtarlar Kullanıcı profili dizininde depolanır ve Kullanıcı hesabına özgü bir ANAHTARLA DPAPI kullanılarak korunur. Anahtarlar *% LocalAppData%/ASP.exe. net/DataProtection-Keys* klasörüne kalıcıdır.

  Uygulama havuzunun [Setprofileenvironment özniteliğinin](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) de etkinleştirilmesi gerekir. Varsayılan değeri `setProfileEnvironment` `true` . Bazı senaryolarda (örneğin, Windows işletim sistemi), `setProfileEnvironment` olarak ayarlanır `false` . Anahtarlar beklenen şekilde Kullanıcı profili dizininde depolanmıyorsa:

  1. *% Windir%/system32/inetsrv/config* klasörüne gidin.
  1. *applicationHost.config* dosyasını açın.
  1. Öğesini bulun `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` .
  1. `setProfileEnvironment`Özniteliğinin mevcut olmadığını, değeri varsayılan olarak değerini, `true` veya özniteliğin değerini olarak olarak ayarlandığını doğrulayın `true` .

* **Dosya sistemini anahtar halka deposu olarak kullanma**

  [Dosya sistemini anahtar halka deposu olarak kullanmak](xref:security/data-protection/configuration/overview)için uygulama kodunu ayarlayın. Anahtar halkasını korumak ve sertifikanın güvenilen bir sertifika olduğundan emin olmak için bir x509 sertifikası kullanın. Sertifika kendinden imzalı ise, sertifikayı güvenilen kök depoya yerleştirin.

  IIS 'yi bir Web grubunda kullanırken:

  * Tüm makinelerin erişebileceği bir dosya paylaşma kullanın.
  * Her makineye bir x509 sertifikası dağıtın. [Kodda veri korumasını](xref:security/data-protection/configuration/overview)yapılandırın.

* **Veri koruma için makineye özel bir ilke ayarlama**

  Veri koruma sistemi, veri koruma API 'Lerini kullanan tüm uygulamalar için varsayılan [makine genelinde bir ilke](xref:security/data-protection/configuration/machine-wide-policy) ayarlamak için sınırlı destek içerir. Daha fazla bilgi için bkz. <xref:security/data-protection/introduction>.

## <a name="virtual-directories"></a>Sanal Dizinler

[IIS sanal dizinleri](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) ASP.NET Core uygulamalarla desteklenmez. Bir uygulama, bir [alt uygulama](#sub-applications)olarak barındırılabilir.

## <a name="sub-applications"></a>Alt uygulamalar

ASP.NET Core bir uygulama, bir [IIS alt uygulaması (alt uygulama)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications)olarak barındırılabilir. Alt uygulamanın yolu, kök uygulamanın URL 'sinin bir parçası haline gelir.

Alt uygulama içindeki statik varlık bağlantıları, tilde işareti ( `~/` ) gösterimini kullanmalıdır. Tilde işareti gösterimi, alt uygulamanın pathbase 'i işlenmiş göreli bağlantıya eklemek için bir [etiket yardımcısını](xref:mvc/views/tag-helpers/intro) tetikler. Üzerindeki bir alt uygulama için `/subapp_path` ile bağlantılı bir görüntü `src="~/image.png"` olarak işlenir `src="/subapp_path/image.png"` . Kök uygulamanın statik dosya ara yazılımı statik dosya isteğini işlemez. İstek, alt uygulamanın statik dosya ara yazılımı tarafından işlenir.

Statik bir varlığın `src` özniteliği mutlak bir yola ayarlandıysa (örneğin, `src="/image.png"` ), bağlantı alt uygulamanın pathbase olmadan işlenir. Kök uygulamanın statik dosya ara yazılımı, statik varlık kök uygulamadan kullanılabilir olmadığı takdirde *404-bulunamayan* bir Yanıt ile sonuçlanır. Bu, kök uygulamanın [Web kökünden](xref:fundamentals/index#web-root)varlık sunmaya çalışır.

Bir ASP.NET Core uygulamasını başka bir ASP.NET Core uygulaması altında alt uygulama olarak barındırmak için:

1. Alt uygulama için bir uygulama havuzu oluşturun. .NET Core için çekirdek ortak dil çalışma zamanı (CoreCLR), uygulamayı masaüstü CLR (.NET CLR) değil, çalışan işlemde barındırmak için önyüklenirken **.NET CLR sürümünü** **yönetilen kod olmadan** ayarlayın.

1. Kök siteyi, kök sitenin altındaki bir klasörde bulunan alt uygulamayla IIS Yöneticisi 'ne ekleyin.

1. IIS Yöneticisi 'ndeki alt uygulama klasörüne sağ tıklayın ve **uygulamaya Dönüştür**' ü seçin.

1. **Uygulama Ekle** iletişim kutusunda, alt uygulama için oluşturduğunuz uygulama havuzunu atamak üzere **uygulama havuzunun** **Seç** düğmesini kullanın. **Tamam**’ı seçin.

Ayrı bir uygulama havuzunun alt uygulamaya atanması, işlem içi barındırma modelinin kullanıldığı bir gereksinimdir.

İşlem içi barındırma modeli ve ASP.NET Core modülünü yapılandırma hakkında daha fazla bilgi için bkz <xref:host-and-deploy/aspnet-core-module> ..

## <a name="configuration-of-iis-with-webconfig"></a>web.config ile IIS yapılandırması

IIS yapılandırması, `<system.webServer>` ASP.NET Core modüllü ASP.NET Core uygulamalar için işlevsel olan IIS senaryoları için *web.config* bölümünden etkilenir. Örneğin, IIS yapılandırması dinamik sıkıştırma için işlevseldir. IIS sunucu düzeyinde dinamik sıkıştırma kullanmak üzere yapılandırıldıysa, `<urlCompression>` uygulamanın *web.config* dosyasındaki öğesi, ASP.NET Core bir uygulama için devre dışı bırakabilir.

Daha fazla bilgi edinmek için aşağıdaki kaynaklara bakın:

* [İçin yapılandırma başvurusu \<system.webServer>](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

Yalıtılmış uygulama havuzlarında çalışan ayrı uygulamalara yönelik ortam değişkenlerini ayarlamak için (IIS 10,0 veya üzeri için desteklenir), IIS başvuru belgelerindeki [ortam değişkenleri \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) konusunun *AppCmd.exe komut* bölümüne bakın.

## <a name="configuration-sections-of-webconfig"></a>web.config yapılandırma bölümleri

*web.config* içindeki ASP.NET 4. x uygulamalarının yapılandırma bölümleri yapılandırma için ASP.NET Core uygulamalar tarafından kullanılmaz:

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

ASP.NET Core uygulamalar diğer yapılandırma sağlayıcıları kullanılarak yapılandırılır. Daha fazla bilgi için bkz. [yapılandırma](xref:fundamentals/configuration/index).

## <a name="application-pools"></a>Uygulama havuzları

Uygulama havuzu yalıtımı, barındırma modeliyle belirlenir:

* İşlem içi barındırma: uygulamaların ayrı uygulama havuzlarında çalışması gerekir.
* İşlem dışı barındırma: her uygulamayı kendi uygulama havuzunda çalıştırarak uygulamaları birbirinden yalıtmayı öneririz.

IIS **Web sitesi ekleme** iletişim kutusu varsayılan olarak uygulama başına tek bir uygulama havuzu olur. Bir **site adı** sağlandığında, metin otomatik olarak **uygulama havuzu** metin kutusuna aktarılır. Site eklendiğinde site adı kullanılarak yeni bir uygulama havuzu oluşturulur.

## <a name="application-pool-no-locidentity"></a>Uygulama havuzu Identity

Bir uygulama havuzu kimliği hesabı, bir uygulamanın etki alanı veya yerel hesap oluşturmak ve yönetmek zorunda kalmadan benzersiz bir hesap altında çalışmasına izin verir. IIS 8,0 veya sonraki sürümlerde, IIS Yönetici çalışan Işlemi (WAS), yeni uygulama havuzunun adıyla bir sanal hesap oluşturur ve varsayılan olarak bu hesap altında uygulama havuzunun çalışan işlemlerini çalıştırır. Uygulama havuzu için **Gelişmiş ayarlar** altındaki IIS Yönetim Konsolu 'nda, **Identity** öğesinin **Identity applicationPool** kullanmak için ayarlandığından emin olun:

![Uygulama havuzu Gelişmiş ayarları iletişim kutusu](index/_static/apppool-identity.png)

IIS Yönetim işlemi, Windows güvenlik sisteminde uygulama havuzunun adıyla güvenli bir tanımlayıcı oluşturur. Bu kimlik kullanılarak kaynakların güvenliği sağlanmış olabilir. Ancak, bu kimlik gerçek bir kullanıcı hesabı değildir ve Windows Kullanıcı Yönetimi konsolunda gösterilmez.

IIS çalışan işlemi uygulamaya yükseltilmiş erişim gerektiriyorsa, uygulamayı içeren dizinin Access Control listesini (ACL) değiştirin:

1. Windows Gezgini 'ni açın ve dizine gidin.

1. Dizine sağ tıklayıp **Özellikler**' i seçin.

1. **Güvenlik** sekmesinde, **Düzenle** düğmesini ve ardından **Ekle** düğmesini seçin.

1. **Konumlar** düğmesini seçin ve sistemin seçili olduğundan emin olun.

1. `IIS AppPool\{APP POOL NAME}`Yer tutucusunun `{APP POOL NAME}` uygulama havuzu adı olduğu, burada **Seçilecek nesne adlarını girin** bölümünde yazın. **Adları denetle** düğmesini seçin. *DefaultAppPool* için, kullanarak adları kontrol edin `IIS AppPool\DefaultAppPool` . **Adları denetle** düğmesi seçildiğinde, `DefaultAppPool` nesne adları alanında bir değeri gösterilir. Uygulama havuzu adının doğrudan nesne adları alanına girilmesi mümkün değildir. `IIS AppPool\{APP POOL NAME}` `{APP POOL NAME}` Nesne adı denetlenirken yer tutucunun uygulama havuzu adı olduğu biçimi kullanın.

   ![Uygulama klasörü için Kullanıcı veya Grup Seç iletişim kutusu: "ad denetle" seçmeden önce "DefaultAppPool" uygulama havuzu adı, nesne adları alanında "IIS AppPool" öğesine eklenir \" .](index/_static/select-users-or-groups-1.png)

1. **Tamam**’ı seçin.

   ![Uygulama klasörü için Kullanıcı veya Grup Seç iletişim kutusu: "adları denetle" seçeneğini belirledikten sonra, nesne adları alanında "DefaultAppPool" nesne adı gösterilir.](index/_static/select-users-or-groups-2.png)

1. Okuma &amp; yürütme izinleri varsayılan olarak verilmelidir. Gerektiğinde ek izinler sağlayın.

Erişim, **ıacl 'ler** Aracı kullanılarak bir komut isteminde de verilebilir. `DefaultAppPool`Örnek olarak kullanarak, aşağıdaki komut kullanılır:

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

Daha fazla bilgi için [ıcacl 'ler](/windows-server/administration/windows-commands/icacls) konusuna bakın.

## <a name="http2-support"></a>HTTP/2 desteği

[Http/2](https://httpwg.org/specs/rfc7540.html) aşağıdaki IIS dağıtım senaryolarında ASP.NET Core desteklenir:

* İşlem içi
  * Windows Server 2016/Windows 10 veya üzeri; IIS 10 veya üzeri
  * TLS 1,2 veya üzeri bağlantı
* İşlem dışı
  * Windows Server 2016/Windows 10 veya üzeri; IIS 10 veya üzeri
  * Herkese açık uç sunucu bağlantıları HTTP/2 kullanır, ancak [Kestrel sunucusuyla](xref:fundamentals/servers/kestrel) ters proxy bağlantısı http/1.1 kullanır.
  * TLS 1,2 veya üzeri bağlantı

Bir HTTP/2 bağlantısı oluşturulduğunda, işlem içi dağıtım için [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) raporları `HTTP/2` . Bir HTTP/2 bağlantısı oluşturulduğunda, işlem dışı bir dağıtım için [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) raporları `HTTP/1.1` .

İşlem içi ve işlem dışı barındırma modelleri hakkında daha fazla bilgi için bkz <xref:host-and-deploy/aspnet-core-module> ..

HTTP/2 varsayılan olarak etkindir. HTTP/2 bağlantısı kurulmadıysa bağlantılar HTTP/1.1 'ye geri döner. IIS dağıtımları ile HTTP/2 yapılandırması hakkında daha fazla bilgi için bkz. [IIS 'de http/2](/iis/get-started/whats-new-in-iis-10/http2-on-iis).

## <a name="cors-preflight-requests"></a>CORS ön denetim istekleri

*Bu bölüm, yalnızca .NET Framework hedefleyen ASP.NET Core uygulamalar için geçerlidir.*

.NET Framework hedefleyen ASP.NET Core bir uygulama için, Seçenekler istekleri uygulamaya varsayılan olarak IIS 'de aktarılmaz. ' De uygulama IIS işleyicilerini seçenek isteklerini geçecek şekilde yapılandırma hakkında bilgi edinmek için `web.config` bkz. [ASP.NET Web API 2 ' de çapraz kaynak isteklerini ETKINLEŞTIRME: CORS 'Nin nasıl çalıştığı](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).

## <a name="application-initialization-module-and-idle-timeout"></a>Uygulama başlatma modülü ve boşta kalma zaman aşımı

ASP.NET Core modülü sürüm 2 tarafından IIS 'de barındırıldığında:

* [Uygulama başlatma modülü](#application-initialization-module): uygulamanın çalışan işlem yeniden başlatması veya sunucu yeniden başlatması üzerinde otomatik olarak başlayacak şekilde, [uygulamanın barındırılan veya](#in-process-hosting-model) [işlem dışı](#out-of-process-hosting-model) bir şekilde yapılandırılmış olması için yapılandırılabilir.
* [Boşta kalma zaman aşımı](#idle-timeout): uygulamanın şirket [içinde barındırılan işlemi, işlem](#in-process-hosting-model) yapılmayan dönemler sırasında zaman aşımına uğramaması için yapılandırılabilir.

### <a name="application-initialization-module"></a>Uygulama başlatma modülü

*İşlem içi ve işlem dışı barındırılan uygulamalar için geçerlidir.*

[IIS uygulaması başlatma](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) , uygulama havuzu başlatıldığında veya geri DÖNÜŞTÜRÜLDÜĞÜNDE uygulamaya http isteği gönderen bir IIS özelliğidir. İstek, uygulamayı başlatmak üzere tetikler. Varsayılan olarak IIS, uygulamayı başlatmak için uygulamanın kök URL 'SI () için bir istek yayınlar `/` (yapılandırma hakkında daha fazla bilgi için [ek kaynaklara](#application-initialization-module-and-idle-timeout-additional-resources) bakın).

IIS uygulama başlatma rolü özelliğinin etkin olduğunu doğrulayın:

IIS 'yi yerel olarak kullanırken Windows 7 veya üzeri masaüstü sistemlerinde:

1. **Denetim Masası** > **programları** > **Programlar ve Özellikler** > **Windows özelliklerini aç veya kapat** (ekranın sol tarafında).
1. **Internet Information Services** > **World Wide Web Services** > **uygulama geliştirme özelliklerini** açın.
1. **Uygulama başlatma** onay kutusunu seçin.

Windows Server 2008 R2 veya sonraki sürümlerde:

1. **Rol ve Özellik Ekleme Sihirbazı 'nı** açın.
1. **Rol hizmetlerini Seç** panelinde, **uygulama geliştirme** düğümünü açın.
1. **Uygulama başlatma** onay kutusunu seçin.

Site için uygulama başlatma modülünü etkinleştirmek üzere aşağıdaki yaklaşımlardan birini kullanın:

* IIS Yöneticisi 'Ni kullanma:

  1. **Bağlantılar** panelinde **uygulama havuzları** ' nı seçin.
  1. Listedeki uygulamanın uygulama havuzuna sağ tıklayın ve **Gelişmiş ayarlar**' ı seçin.
  1. Varsayılan **Başlangıç modu** **OnDemand**' dir. **Başlangıç modunu** **AlwaysRunning** olarak ayarlayın. **Tamam**’ı seçin.
  1. **Bağlantılar** panelinde **siteler** düğümünü açın.
  1. Uygulamaya sağ tıklayın ve **Web sitesi** > **Gelişmiş ayarlarını** Yönet ' i seçin.
  1. Varsayılan **önyükleme etkin** ayarı **false** şeklindedir. **Önyükleme etkin** ' i **true** olarak ayarlayın. **Tamam**’ı seçin.

* Kullanarak `web.config` , `<applicationInitialization>` öğesini `doAppInitAfterRestart` `true` `<system.webServer>` uygulamasının web.config' dosyasındaki öğelerine ayarlı öğesine ekleyin:

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

*Yalnızca işlem sırasında barındırılan uygulamalar için geçerlidir.*

Uygulamanın çalışmasını engellemek için, IIS Yöneticisi 'Ni kullanarak uygulama havuzunun boşta kalma zaman aşımını ayarlayın:

1. **Bağlantılar** panelinde **uygulama havuzları** ' nı seçin.
1. Listedeki uygulamanın uygulama havuzuna sağ tıklayın ve **Gelişmiş ayarlar**' ı seçin.
1. Varsayılan **boşta kalma süresi (dakika)** **20** dakikadır. **Boşta kalma zaman aşımını (dakika)** **0** (sıfır) olarak ayarlayın. **Tamam**’ı seçin.
1. Çalışan işlemini geri dönüştür.

[İşlem dışı](#out-of-process-hosting-model) barındırılan uygulamaların zaman aşımına uğramasını engellemek için aşağıdaki yaklaşımlardan birini kullanın:

* Uygulamayı çalışır durumda tutmak için bir dış hizmetten ping yapın.
* Uygulama yalnızca arka plan hizmetleri barındırıyorsa, IIS barındırmaktan kaçının ve [ASP.NET Core uygulamasını barındırmak için bir Windows hizmeti](xref:host-and-deploy/windows-service)kullanın.

### <a name="application-initialization-module-and-idle-timeout-additional-resources"></a>Uygulama başlatma modülü ve boşta kalma zaman aşımı ek kaynakları

* [IIS 8,0 uygulama başlatma](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)
* [Uygulama başlatma \<applicationInitialization> ](/iis/configuration/system.webserver/applicationinitialization/).
* [Uygulama havuzu \<processModel> Için Işlem modeli ayarları ](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).

## <a name="deployment-resources-for-iis-administrators"></a>IIS yöneticileri için dağıtım kaynakları

* [IIS belgeleri](/iis)
* [IIS 'de IIS Yöneticisi 'Ni kullanmaya başlama](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [.NET Core uygulama dağıtımı](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:test/troubleshoot>
* <xref:index>
* [Resmi Microsoft IIS sitesi](https://www.iis.net/)
* [Windows Server teknik içerik kitaplığı](/windows-server/windows-server)
* [IIS üzerinde HTTP/2](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
* <xref:host-and-deploy/iis/transform-webconfig>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

ASP.NET Core uygulamasını bir IIS sunucusuna yayımlamaya yönelik bir öğretici deneyimi için, bkz <xref:tutorials/publish-to-iis> ..

[.NET Core barındırma paketi 'ni yükler](#install-the-net-core-hosting-bundle)

## <a name="supported-operating-systems"></a>Desteklenen işletim sistemleri

Aşağıdaki işletim sistemleri desteklenmektedir:

* Windows 7 veya üzeri
* Windows Server 2008 R2 veya sonraki sürümü

[HTTP.sys sunucusu](xref:fundamentals/servers/httpsys) (eskiden webListener olarak adlandırılır), IIS ile ters proxy yapılandırmasında çalışmaz. [Kestrel sunucusunu](xref:fundamentals/servers/kestrel)kullanın.

Azure 'da barındırma hakkında daha fazla bilgi için bkz <xref:host-and-deploy/azure-apps/index> ..

Sorun giderme kılavuzu için bkz <xref:test/troubleshoot> ..

## <a name="supported-platforms"></a>Desteklenen platformlar

32-bit (x86) veya 64 bit (x64) dağıtımı için yayımlanan uygulamalar desteklenir. Uygulama dışında 32 bitlik bir uygulamayı 32 bit (x86) .NET Core SDK dağıtma:

* 64 bitlik bir uygulama için kullanılabilir daha büyük sanal bellek adres alanını gerektirir.
* Daha büyük IIS yığın boyutunu gerektirir.
* 64 bitlik yerel bağımlılıklara sahiptir.

64 bit uygulama yayımlamak için 64 bit (x64) .NET Core SDK kullanın. Ana bilgisayar sisteminde 64 bit çalışma zamanı bulunmalıdır.

## <a name="hosting-models"></a>Barındırma modelleri

### <a name="in-process-hosting-model"></a>İşlem içi barındırma modeli

ASP.NET Core bir uygulama, işlem içi barındırma kullanarak IIS çalışan işlemiyle aynı işlemde çalışır. İşlem içi barındırma, işlem dışı barındırma üzerinde daha iyi performans sağlar çünkü:

* İsteklerin geri döngü bağdaştırıcısı üzerinde proxy yok. Geri döngü bağdaştırıcısı, giden ağ trafiğinin aynı makineye geri döndürdüğü bir ağ arabirimidir.

IIS, [Windows Işlem etkinleştirme hizmeti (was)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was)ile işlem yönetimini işler.

[ASP.NET Core modülü](xref:host-and-deploy/aspnet-core-module):

* Uygulama başlatmayı gerçekleştirir.
  * [CoreCLR](/dotnet/standard/glossary#coreclr)'yi yükler.
  * Çağırır `Program.Main` .
* IIS yerel isteğinin ömrünü işler.

İşlem içi barındırma modeli, .NET Framework hedef ASP.NET Core uygulamalar için desteklenmez.

Aşağıdaki diyagramda IIS, ASP.NET Core modülü ve süreçte barındırılan bir uygulama arasındaki ilişki gösterilmektedir:

![İşlem içi barındırma senaryosunda modül ASP.NET Core](index/_static/ancm-inprocess.png)

Web 'den çekirdek modu HTTP.sys sürücüsüne bir istek ulaşır. Sürücü, yerel isteği Web sitesinin yapılandırılmış bağlantı noktasında IIS 'ye yönlendirir, genellikle 80 (HTTP) veya 443 (HTTPS). ASP.NET Core modülü yerel isteği alır ve IIS HTTP sunucusuna ( `IISHttpServer` ) geçirir. IIS HTTP sunucusu, isteği yerelden yönetilene dönüştüren bir IIS için işlem içi sunucu uygulamasıdır.

IIS HTTP sunucusu isteği işlediğinde, istek ASP.NET Core ara yazılım ardışık düzenine gönderilir. Ara yazılım ardışık düzeni isteği işler ve `HttpContext` uygulamanın mantığına bir örnek olarak geçirir. Uygulamanın yanıtı IIS HTTP sunucusu aracılığıyla IIS 'e geri geçirilir. IIS yanıtı, isteği başlatan istemciye gönderir.

İşlem içi barındırma, mevcut uygulamalar için kabul ediyor, ancak tüm IIS ve IIS Express senaryoları için varsayılan [DotNet yeni](/dotnet/core/tools/dotnet-new) şablonlar, işlem içi barındırma modeli için varsayılan olarak kullanılır.

`CreateDefaultBuilder`<xref:Microsoft.AspNetCore.Hosting.Server.IServer> <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS%2A> [CoreCLR](/dotnet/standard/glossary#coreclr) 'YI önyüklemek ve uygulamayı IIS çalışan işleminin içinde barındırmak için yöntemini çağırarak bir örnek ekler (*w3wp.exe* veya *iisexpress.exe*). Performans testleri, bir .NET Core uygulamasını işlem içinde barındıran uygulamanın, uygulamayı işlem dışı ve [Kestrel](xref:fundamentals/servers/kestrel) sunucusuna proxy alma isteklerinin barındırılmasına kıyasla önemli ölçüde daha yüksek istek aktarım hızı sağladığını gösterir.

### <a name="out-of-process-hosting-model"></a>İşlem dışı barındırma modeli

ASP.NET Core uygulamalar IIS çalışan işleminden ayrı bir işlemde çalıştığından, ASP.NET Core modülü işlem yönetimini işler. Modül, ilk istek ulaştığında ASP.NET Core uygulama için işlemi başlatır ve kapanırsa veya kilitlenirse uygulamayı yeniden başlatır. Bu aslında, [Windows Işlem etkinleştirme hizmeti (was)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was)tarafından yönetilen işlem içi uygulamalarla birlikte görülen davranışdır.

Aşağıdaki diyagramda IIS, ASP.NET Core modülü ve işlem dışı barındırılan bir uygulama arasındaki ilişki gösterilmektedir:

![İşlem dışı barındırma senaryosunda modül ASP.NET Core](index/_static/ancm-outofprocess.png)

İstekler Web 'den çekirdek modu HTTP.sys sürücüsüne ulaşır. Sürücü, istekleri Web sitesinin yapılandırılmış bağlantı noktasında IIS 'ye yönlendirir, genellikle 80 (HTTP) veya 443 (HTTPS). Modül, 80 veya 443 numaralı bağlantı noktası olmayan uygulama için rastgele bir bağlantı noktasında istekleri Kestrel 'e iletir.

Modül, başlangıç sırasında bir ortam değişkeni aracılığıyla bağlantı noktasını belirtir ve <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> uzantı sunucuyu dinleyecek şekilde yapılandırır `http://localhost:{PORT}` . Ek denetimler gerçekleştirilir ve modülünden kaynaklanmayan istekler reddedilir. Modül HTTPS iletmeyi desteklemez, bu nedenle istekler HTTPS üzerinden IIS tarafından alınsa bile HTTP üzerinden iletilir.

Kestrel, isteği modülden başlattıktan sonra, istek ASP.NET Core ara yazılım ardışık düzenine gönderilir. Ara yazılım ardışık düzeni isteği işler ve `HttpContext` uygulamanın mantığına bir örnek olarak geçirir. IIS tümleştirmesi tarafından eklenen ara yazılım, isteği Kestrel iletmek için düzen, uzak IP ve pathbase 'i hesaba göre güncelleştirir. Uygulamanın yanıtı IIS 'e geri geçirilir ve bu, isteği başlatan HTTP istemcisine geri gönderilir.

ASP.NET Core modülü yapılandırma kılavuzu için bkz <xref:host-and-deploy/aspnet-core-module> ..

Barındırma hakkında daha fazla bilgi için bkz. [ASP.NET Core ana bilgisayar](xref:fundamentals/index#host).

## <a name="application-configuration"></a>Uygulama yapılandırması

### <a name="enable-the-iisintegration-components"></a>Iısıntegration bileşenlerini etkinleştirin

`CreateWebHostBuilder`(*Program.cs*) içinde BIR konak oluştururken <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> IIS tümleştirmesini etkinleştirmek için çağırın:

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        ...
```

Hakkında daha fazla bilgi için `CreateDefaultBuilder` bkz <xref:fundamentals/host/web-host#set-up-a-host> ..

### <a name="iis-options"></a>IIS seçenekleri

**İşlem içi barındırma modeli**

IIS sunucu seçeneklerini yapılandırmak için, içinde için bir hizmet yapılandırması <xref:Microsoft.AspNetCore.Builder.IISServerOptions> ekleyin <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A> . Aşağıdaki örnek, Automatıcauthentication 'ı devre dışı bırakır:

```csharp
services.Configure<IISServerOptions>(options => 
{
    options.AutomaticAuthentication = false;
});
```

| Seçenek                         | Varsayılan | Ayar |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | `true`IIS sunucusu, `HttpContext.User` [Windows kimlik doğrulaması](xref:security/authentication/windowsauth)tarafından kimliği doğrulanmış olarak ayarlanır. İse `false` , sunucu yalnızca bir kimlik sağlar `HttpContext.User` ve tarafından açıkça istendiğinde sorunları yanıtlar `AuthenticationScheme` . ' Nin çalışması için IIS 'de Windows kimlik doğrulamasının etkinleştirilmesi gerekir `AutomaticAuthentication` . Daha fazla bilgi için bkz. [Windows kimlik doğrulaması](xref:security/authentication/windowsauth). |
| `AuthenticationDisplayName`    | `null`  | Oturum açma sayfalarındaki kullanıcılara gösterilen görünen adı ayarlar. |

**İşlem dışı barındırma modeli**

IIS seçeneklerini yapılandırmak için, içinde için bir hizmet yapılandırması <xref:Microsoft.AspNetCore.Builder.IISOptions> ekleyin <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*> . Aşağıdaki örnek, uygulamanın doldurulmasını önler `HttpContext.Connection.ClientCertificate` :

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| Seçenek                         | Varsayılan | Ayar |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | `true` [IIS tümleştirme ara yazılımı](#enable-the-iisintegration-components) , `HttpContext.User` kimliği doğrulanmış [Windows kimlik doğrulaması](xref:security/authentication/windowsauth)tarafından ayarlanır. İse `false` , ara yazılım için yalnızca bir kimlik sağlar `HttpContext.User` ve tarafından açıkça istendiğinde sorunları yanıtlar `AuthenticationScheme` . ' Nin çalışması için IIS 'de Windows kimlik doğrulamasının etkinleştirilmesi gerekir `AutomaticAuthentication` . Daha fazla bilgi için [Windows kimlik doğrulaması](xref:security/authentication/windowsauth) konusuna bakın. |
| `AuthenticationDisplayName`    | `null`  | Oturum açma sayfalarındaki kullanıcılara gösterilen görünen adı ayarlar. |
| `ForwardClientCertificate`     | `true`  | `true`Ve `MS-ASPNETCORE-CLIENTCERT` istek üst bilgisi varsa, `HttpContext.Connection.ClientCertificate` doldurulur. |

### <a name="proxy-server-and-load-balancer-scenarios"></a>Proxy sunucusu ve yük dengeleyici senaryoları

Iletilen üstbilgiler ara yazılımını yapılandıran [IIS tümleştirme ara yazılımı](#enable-the-iisintegration-components)ve ASP.NET Core modülü, DÜZENI (http/https) ve isteğin KAYNAKLANDıĞı uzak IP adresini iletecek şekilde yapılandırılmıştır. Ek proxy sunucularının ve yük dengeleyiciler arkasında barındırılan uygulamalar için ek yapılandırma gerekebilir. Daha fazla bilgi için bkz. [proxy sunucularıyla ve yük dengeleyicilerle çalışacak ASP.NET Core yapılandırma](xref:host-and-deploy/proxy-load-balancer).

### <a name="webconfig-file"></a>web.config dosyası

*web.config* dosyası [ASP.NET Core modülünü](xref:host-and-deploy/aspnet-core-module)yapılandırır. *web.config* dosyası oluşturma, dönüştürme ve yayımlama, proje yayımlandığında MSBuild hedefi () tarafından işlenir `_TransformWebConfig` . Bu hedef, Web SDK hedeflerinde () bulunur `Microsoft.NET.Sdk.Web` . SDK proje dosyasının en üstünde ayarlanır:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

Projede bir *web.config* dosyası yoksa, ASP.NET Core modülünü yapılandırmak için doğru işlem *yolu* ve *bağımsız değişkenlerle* dosya oluşturulur ve [yayımlanan çıktıya](xref:host-and-deploy/directory-structure)taşınır.

Projede bir *web.config* dosyası varsa, dosya doğru *processPath* ve *bağımsız değişkenlerle* birlikte dönüştürülür ve ASP.NET Core modülünü yapılandırır ve yayımlanan çıktıya taşınır. Dönüştürme, dosyadaki IIS yapılandırma ayarlarını değiştirmez.

*web.config* dosyası, etkin IIS modüllerini DENETLEYEN ek IIS yapılandırma ayarları sağlayabilir. ASP.NET Core uygulamalarla istekleri işleyebilen IIS modülleri hakkında daha fazla bilgi için bkz. [IIS modules](xref:host-and-deploy/iis/modules) konusu.

Web SDK 'sının *web.config* dosyasını dönüştürmasını engellemek için, **\<IsTransformWebConfigDisabled>** Proje dosyasındaki özelliğini kullanın:

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

Web SDK 'sının dosyayı dönüştürmesiyle devre dışı bırakıldığında, *processPath* ve *bağımsız değişkenler* geliştirici tarafından el ile ayarlanmalıdır. Daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module>.

### <a name="webconfig-file-location"></a>web.config dosya konumu

[ASP.NET Core modülünü](xref:host-and-deploy/aspnet-core-module) doğru bir şekilde ayarlamak için, dağıtılan uygulamanın [içerik kök](xref:fundamentals/index#content-root) yolunda (genellikle uygulama temel yolu) *web.config* dosyasının mevcut olması gerekir. Bu, IIS 'ye sunulan Web sitesi fiziksel yoluyla aynı konumdadır. *web.config* dosyası, Web dağıtımı kullanarak birden çok uygulamanın yayımlanmasını sağlamak için uygulamanın kökünde gereklidir.

Önemli dosyalar uygulamanın fiziksel yolunda bulunur (örneğin, *\<assembly>.runtimeconfig.json*, *\<assembly> . xml* (XML belge açıklamaları) ve *\<assembly>.deps.js*. *web.config* dosyası mevcut olduğunda ve site normal olarak başlatıldığında, istenirse IIS bu hassas dosyaları sunmaz. *web.config* dosya eksikse, yanlış şekilde adlandırılmış veya site normal başlatma için YAPıLANDıRıMıŞSA IIS hassas dosyalara genel olarak sunabilir.

***web.config* dosyasının her zaman dağıtımda mevcut olması gerekir, doğru şekilde adlandırılmış ve siteyi normal başlangıç için yapılandırabiliyor. *web.config* dosyasını bir üretim dağıtımından hiçbir şekilde kaldırmayın.**

### <a name="transform-webconfig"></a>Web.config’i dönüştürme

Yayımla *web.config* dönüştürmeniz gerekiyorsa (örneğin, yapılandırma, profil veya ortama göre ortam değişkenlerini ayarlayın), bkz <xref:host-and-deploy/iis/transform-webconfig> ..

## <a name="iis-configuration"></a>IIS yapılandırması

**Windows Server işletim sistemleri**

**Web sunucusu (IIS)** sunucu rolünü etkinleştirin ve rol hizmetleri oluşturun.

1. **Yönet** menüsündeki **rol ve özellik ekleme** sihirbazı ' nı veya **Sunucu Yöneticisi** bağlantısındaki bağlantıyı kullanın. **Sunucu rolleri** adımında, **Web sunucusu (IIS)** kutusunu işaretleyin.

   ![Sunucu rollerini seçin adımında Web sunucusu IIS rolü seçilidir.](index/_static/server-roles-ws2016.png)

1. **Özellikler** adımından sonra, Web sunucusu (IIS) için **rol hizmetleri** adımı yüklenir. İstenen IIS rol hizmetlerini seçin veya varsayılan rol hizmetlerini kabul edin.

   ![Rol hizmetlerini seçin adımında varsayılan rol hizmetleri seçilidir.](index/_static/role-services-ws2016.png)

   **Windows kimlik doğrulaması (Isteğe bağlı)**  
   Windows kimlik doğrulamasını etkinleştirmek için şu düğümleri genişletin: **Web sunucusu**  >  **güvenliği**. **Windows kimlik doğrulama** özelliğini seçin. Daha fazla bilgi için bkz. [Windows \<windowsAuthentication> kimlik doğrulaması](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) ve [Windows kimlik doğrulamasını yapılandırma](xref:security/authentication/windowsauth).

   **WebSockets (Isteğe bağlı)**  
   WebSockets ASP.NET Core 1,1 veya üzeri bir sürümde desteklenir. WebSockets etkinleştirmek için şu düğümleri genişletin: **Web sunucusu**  >  **uygulama geliştirme**. **WebSocket protokolü** özelliğini seçin. Daha fazla bilgi için bkz. [WebSockets](xref:fundamentals/websockets).

1. Web sunucusu rolü ve hizmetlerini yüklemek için **onay** adımına ilerleyin. **Web sunucusu (IIS)** rolü yüklendikten sonra sunucu/IIS yeniden başlatması gerekli değildir.

**Windows masaüstü işletim sistemleri**

**IIS Yönetim Konsolu** ve **World Wide Web hizmetlerini** etkinleştirin.

1. **Denetim Masası** > **programları** > **Programlar ve Özellikler** > **Windows özelliklerini aç veya kapat** (ekranın sol tarafında).

1. **Internet Information Services** düğümünü açın. **Web yönetimi araçları** düğümünü açın.

1. **IIS Yönetim Konsolu** kutusunu işaretleyin.

1. **World Wide Web Hizmetleri** kutusunu işaretleyin.

1. **World Wide Web Hizmetleri** için varsayılan özellikleri kabul edın veya IIS özelliklerini özelleştirin.

   **Windows kimlik doğrulaması (Isteğe bağlı)**  
   Windows kimlik doğrulamasını etkinleştirmek için şu düğümleri genişletin: **World Wide Web Hizmetleri**  >  **güvenliği**. **Windows kimlik doğrulama** özelliğini seçin. Daha fazla bilgi için bkz. [Windows \<windowsAuthentication> kimlik doğrulaması](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) ve [Windows kimlik doğrulamasını yapılandırma](xref:security/authentication/windowsauth).

   **WebSockets (Isteğe bağlı)**  
   WebSockets ASP.NET Core 1,1 veya üzeri bir sürümde desteklenir. WebSockets etkinleştirmek için şu düğümleri genişletin: **World Wide Web Services**  >  **uygulaması geliştirme özellikleri**. **WebSocket protokolü** özelliğini seçin. Daha fazla bilgi için bkz. [WebSockets](xref:fundamentals/websockets).

1. IIS yüklemesi için yeniden başlatma gerekiyorsa, sistemi yeniden başlatın.

![IIS Yönetim Konsolu ve World Wide Web Hizmetleri Windows Özellikleri ' nde seçilidir.](index/_static/windows-features-win10.png)

## <a name="install-the-net-core-hosting-bundle"></a>.NET Core barındırma paketi 'ni yükler

*.NET Core barındırma paketi* 'ni barındırma sistemine yükler. Paket, .NET Core çalışma zamanı, .NET Core kitaplığı ve [ASP.NET Core modülünü](xref:host-and-deploy/aspnet-core-module)de yüklüyor. Modül ASP.NET Core uygulamaların IIS 'nin arkasında çalışmasına izin verir.

> [!IMPORTANT]
> Barındırma paketi IIS 'den önce yüklendiyse, paket yüklemesi onarılması gerekir. IIS yükledikten sonra barındırma paketi yükleyicisini yeniden çalıştırın.
>
> .NET Core 'un 64 bit (x64) sürümünü yükledikten sonra barındırma paketi yüklenirse, SDK 'lar eksik gibi görünebilir ([hiçbir .NET Core SDK 'sı algılanmadı](xref:test/troubleshoot#no-net-core-sdks-were-detected)). Sorunu çözmek için bkz <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle> ..

### <a name="download"></a>İndir

1. [.NET Core 'U indir](https://dotnet.microsoft.com/download/dotnet-core) sayfasına gidin.
1. İstediğiniz .NET Core sürümünü seçin.
1. **Uygulamaları Çalıştır-çalışma zamanı** sütununda, Istenen .NET Core çalışma zamanı sürümünün satırını bulun.
1. **Barındırma paketi** bağlantısını kullanarak yükleyiciyi indirin.

> [!WARNING]
> Bazı yükleyicilerle yaşam süresi (EOL) gelmiş olan ve artık Microsoft tarafından desteklenmeyen yayın sürümlerini içerir. Daha fazla bilgi için bkz. [destek ilkesi](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).

### <a name="install-the-hosting-bundle"></a>Barındırma paketini yükler

1. Yükleyiciyi sunucuda çalıştırın. Aşağıdaki parametreler, yükleyiciyi yönetici komut kabuğu 'ndan çalıştırırken kullanılabilir:

   * `OPT_NO_ANCM=1`: ASP.NET Core modülünü yüklemeyi atlayın.
   * `OPT_NO_RUNTIME=1`: .NET Core çalışma zamanını yüklemeyi atlayın. Sunucu yalnızca [kendi kendine içerilen dağıtımları (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd)barındırdığınızda kullanılır.
   * `OPT_NO_SHAREDFX=1`: ASP.NET paylaşılan çerçevesini (ASP.NET çalışma zamanı) yüklemeyi atlayın. Sunucu yalnızca [kendi kendine içerilen dağıtımları (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd)barındırdığınızda kullanılır.
   * `OPT_NO_X86=1`: X86 çalışma zamanlarını yüklemeyi atlayın. 32 bitlik uygulamalar barındırmayabildiğinizi bildiğiniz durumlarda bu parametreyi kullanın. Gelecekte 32-bit ve 64 bit uygulamaları barındırabilmeniz gereken herhangi bir şansınız varsa, bu parametreyi kullanmayın ve her iki çalışma zamanını da yüklemeyin.
   * `OPT_NO_SHARED_CONFIG_CHECK=1`: Paylaşılan yapılandırma (*applicationHost.config*), IIS yüklemesiyle aynı makinada olduğunda, IIS paylaşılan yapılandırması kullanma denetimini devre dışı bırakın. *Yalnızca ASP.NET Core 2,2 veya sonraki bir sürümü Paketcisi yükleyicilerini barındırmak için kullanılabilir.* Daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.
1. Sistemi yeniden başlatın veya komut kabuğu 'nda aşağıdaki komutları yürütün:

   ```console
   net stop was /y
   net start w3svc
   ```
   IIS 'nin yeniden başlatılması, yükleyici tarafından oluşturulan bir ortam değişkeni olan sistem yolunda bir değişiklik seçer.

Barındırma paketini yüklerken IIS 'deki bireysel siteleri el ile durdurmanız gerekli değildir. Barındırılan uygulamalar (IIS siteleri) IIS yeniden başlatıldığında yeniden başlatılır. Uygulamalar, [uygulama başlatma modülünden](#application-initialization-module-and-idle-timeout)da dahil olmak üzere ilk isteklerini alırken yeniden başlatılır.

ASP.NET Core, paylaşılan çerçeve paketlerinin yama sürümleri için ileri sarma davranışını benimseme. IIS ile IIS tarafından barındırılan uygulamalar IIS ile yeniden başlatıldığında, ilk isteklerini alırken başvurulan paketlerinin en son düzeltme eki sürümleriyle yüklenir. IIS yeniden başlatılırsa, uygulamalar yeniden başlatılır ve çalışan işlemlerine geri dönüştürüldüğünde geri alma davranışını gösterir ve ilk isteklerini alırlar.

> [!NOTE]
> IIS paylaşılan Yapılandırması hakkında bilgi için bkz. [IIS paylaşılan yapılandırması ile ASP.NET Core modülü](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a>Visual Studio ile yayımlarken Web Dağıtımı yüklemesi

Uygulamaları [Web dağıtımı](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later)olan sunuculara dağıttığınızda, en son Web dağıtımı sürümünü sunucuya yüklersiniz. Web Dağıtımı yüklemek için [Web Platformu Yükleyicisi 'ni (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) kullanın veya doğrudan [Microsoft İndirme Merkezi](https://www.microsoft.com/download/details.aspx?id=43717)'nden bir yükleyici edinin. Tercih edilen yöntem, WebPI kullanmaktır. WebPI, barındırma sağlayıcıları için tek başına kurulum ve yapılandırma sağlar.

## <a name="create-the-iis-site"></a>IIS sitesini oluşturma

1. Barındırma sisteminde, uygulamanın yayımlanan klasörlerini ve dosyalarını içeren bir klasör oluşturun. Aşağıdaki adımda, klasörün yolu, uygulamanın fiziksel yolu olarak IIS 'ye sağlanır. Uygulamanın dağıtım klasörü ve dosya düzeni hakkında daha fazla bilgi için bkz <xref:host-and-deploy/directory-structure> ..

1. IIS Yöneticisi 'nde, **Bağlantılar** panelinde sunucunun düğümünü açın. **Siteler** klasörüne sağ tıklayın. Bağlamsal menüden **Web sitesi Ekle** ' yi seçin.

1. Bir **site adı** belirtin ve **fiziksel yolu** uygulamanın dağıtım klasörüne ayarlayın. **Bağlama** yapılandırmasını sağlayın ve **Tamam**' ı seçerek Web sitesini oluşturun:

   ![Web sitesi Ekle adımında site adı, fiziksel yol ve ana bilgisayar adını sağlayın.](index/_static/add-website-ws2016.png)

   > [!WARNING]
   > Üst düzey joker karakter bağlamaları ( `http://*:80/` ve `http://+:80` ) kullanılmamalıdır  . Üst düzey joker karakter bağlamaları, uygulamanızı güvenlik açıklarına açabilir. Bu hem güçlü hem de zayıf Joker karakterlere yöneliktir. Joker karakterler yerine açık ana bilgisayar adları kullanın. Alt etki alanı joker bağlantısı (örneğin, `*.mysub.com` ), tüm üst etki alanını (Bu güvenlik açığı olan aksine) kontrol ediyorsanız bu güvenlik riskine sahip değildir `*.com` . Daha fazla bilgi için bkz. [rfc7230 Section-5,4](https://tools.ietf.org/html/rfc7230#section-5.4) .

1. Sunucu düğümünün altında **uygulama havuzları**' nı seçin.

1. Sitenin uygulama havuzuna sağ tıklayın ve bağlam menüsünden **temel ayarlar** ' ı seçin.

1. **Uygulama havuzunu Düzenle** penceresinde, **.NET CLR sürümünü** **yönetilen kod olmadan** ayarlayın:

   ![.NET CLR sürümü için yönetilen kod ayarlama.](index/_static/edit-apppool-ws2016.png)

    ASP.NET Core ayrı bir işlemde çalışır ve çalışma zamanını yönetir. ASP.NET Core, masaüstü CLR 'nin (.NET CLR) yüklenmemesine bağlı değildir &mdash; . .NET Core Için çekirdek ortak dil çalışma zamanı (CoreCLR), uygulamayı çalışan işlemde barındırmak için önyüklenir. **.NET CLR sürümünün** **yönetilen kod olmadan** ayarlanması isteğe bağlıdır, ancak önerilir.

1. *ASP.NET Core 2,2 veya üzeri*: [işlem içi barındırma modelini](#in-process-hosting-model)kullanan 64 bit (x64) [tabanlı bir dağıtım](/dotnet/core/deploying/#self-contained-deployments-scd) için, 32-bit (x86) işlemleri için uygulama havuzunu devre dışı bırakın.

   IIS Yöneticisi > **uygulama havuzlarının** **Eylemler** kenar çubuğunda, **uygulama havuzu varsayılanlarını** veya **Gelişmiş ayarları** ayarla ' yı seçin. **32 bitlik uygulamaları etkinleştir** ' i bulun ve değerini olarak ayarlayın `False` . Bu ayar [, işlem dışı barındırma](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model)için dağıtılan uygulamaları etkilemez.

1. İşlem modeli kimliğinin uygun izinlere sahip olduğunu doğrulayın.

   Uygulama havuzunun varsayılan kimliği (**işlem modeli**  >  **Identity** ) **applicationPool Identity** 'den başka bir kimliğe değiştirilirse, yeni kimliğin uygulamanın klasörüne, veritabanına ve diğer gerekli kaynaklara erişmek için gerekli izinlere sahip olduğunu doğrulayın. Örneğin, uygulama havuzu, uygulamanın dosyaları okuduğu ve yazdığı klasörlere okuma ve yazma erişimi gerektirir.

**Windows kimlik doğrulama yapılandırması (Isteğe bağlı)**  
Daha fazla bilgi için bkz. [Windows kimlik doğrulamasını yapılandırma](xref:security/authentication/windowsauth).

## <a name="deploy-the-app"></a>Uygulamayı dağıtma

Uygulamayı [IIS sitesi oluşturma](#create-the-iis-site) bölümünde oluşturulan IIS **fiziksel yol** klasörüne dağıtın. [Web dağıtımı](/iis/publish/using-web-deploy/introduction-to-web-deploy) dağıtım için önerilen mekanizmadır, ancak uygulamayı projenin *Publish* klasöründen barındırma sisteminin dağıtım klasörüne taşımak için çeşitli seçenekler mevcuttur.

### <a name="web-deploy-with-visual-studio"></a>Visual Studio ile Web Dağıtımı

Web Dağıtımı kullanılacak bir yayımlama profili oluşturma hakkında bilgi edinmek için bkz. [ASP.NET Core App Deployment Için Visual Studio yayımlama profilleri](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) konusuna bakın. Barındırma sağlayıcısı, bir yayımlama profili veya oluşturma desteği sağlıyorsa, profilini indirin ve Visual Studio **Yayımlama** iletişim kutusunu kullanarak içeri aktarın:

![Yayımla iletişim sayfası](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a>Visual Studio dışında Web Dağıtımı

[Web dağıtımı](/iis/publish/using-web-deploy/introduction-to-web-deploy) , komut satırından Visual Studio dışında da kullanılabilir. Daha fazla bilgi için bkz. [Web Dağıtım aracı](/iis/publish/using-web-deploy/use-the-web-deployment-tool).

### <a name="alternatives-to-web-deploy"></a>Web Dağıtımı alternatifleri

Uygulamayı barındırma sistemine taşımak için el ile kopyalama, [xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy)veya [PowerShell](/powershell/)gibi çeşitli yöntemlerden birini kullanın.

IIS 'ye dağıtım ASP.NET Core hakkında daha fazla bilgi için, [IIS yöneticileri Için dağıtım kaynakları](#deployment-resources-for-iis-administrators) bölümüne bakın.

## <a name="browse-the-website"></a>Web sitesine gidin

Uygulama barındırma sistemine dağıtıldıktan sonra, uygulamanın genel uç noktalarından birine bir istek oluşturun.

Aşağıdaki örnekte, site bağlantı noktasındaki IIS **ana bilgisayar adına** bağlıdır `www.mysite.com`  `80` . Bir istek şu şekilde yapılır `http://www.mysite.com` :

![Microsoft Edge tarayıcısı, IIS başlangıç sayfasını yükledi.](index/_static/browsewebsite.png)

## <a name="locked-deployment-files"></a>Kilitli dağıtım dosyaları

Dağıtım klasöründeki dosyalar, uygulama çalışırken kilitlenir. Dağıtım sırasında kilitli dosyaların üzerine yazılamaz. Kilitli dosyaları bir dağıtımda serbest bırakmak için aşağıdaki yaklaşımlardan **birini** kullanarak uygulama havuzunu durdurun:

* Proje dosyasında Web Dağıtımı ve başvurusunu kullanın `Microsoft.NET.Sdk.Web` . Bir *app_offline.htm* dosyası Web uygulaması dizininin köküne yerleştirilir. Dosya mevcut olduğunda, ASP.NET Core modülü uygulamayı düzgün bir şekilde kapatır ve dağıtım sırasında *app_offline.htm* dosyasına hizmet verir. Daha fazla bilgi için [ASP.NET Core modülü yapılandırma başvurusuna](xref:host-and-deploy/aspnet-core-module#app_offlinehtm)bakın.
* Sunucu üzerindeki IIS Yöneticisi 'nde uygulama havuzunu el ile durdurun.
* *app_offline.htm* bırakmak için PowerShell kullanın (PowerShell 5 veya üzeri gerektirir):

  ```powershell
  $pathToApp = 'PATH_TO_APP'

  # Stop the AppPool
  New-Item -Path $pathToApp app_offline.htm

  # Provide script commands here to deploy the app

  # Restart the AppPool
  Remove-Item -Path $pathToApp app_offline.htm

  ```

## <a name="data-protection"></a>Veri koruma

[ASP.NET Core veri koruma yığını](xref:security/data-protection/introduction) , kimlik doğrulamasında kullanılan ara yazılımlar dahil olmak üzere birkaç ASP.NET Core [middlewares](xref:fundamentals/middleware/index)tarafından kullanılır. Veri koruma API 'Leri Kullanıcı kodu tarafından çağrılmasa bile, kalıcı bir şifreleme [anahtarı deposu](xref:security/data-protection/implementation/key-management)oluşturmak için veri koruma bir dağıtım betiği veya Kullanıcı kodu ile yapılandırılmalıdır. Veri koruması yapılandırılmamışsa, anahtarlar bellekte tutulur ve uygulama yeniden başlatıldığında atılır.

Uygulama yeniden başlatıldığında anahtar halkası bellekte depolanıyorsa:

* Tüm cookie tabanlı kimlik doğrulama belirteçleri geçersiz kılındı. 
* Kullanıcıların bir sonraki isteğinde yeniden oturum açması gerekir. 
* Anahtar halkası ile korunan tüm veriler artık çözülemez. Bu, [CSRF belirteçlerini](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) ve [ASP.NET Core MVC TempData cookie s](xref:fundamentals/app-state#tempdata)öğesini içerebilir.

Anahtar halkasını sürdürmek için IIS altındaki veri korumasını yapılandırmak için aşağıdaki yaklaşımlardan **birini** kullanın:

* **Veri koruma kayıt defteri anahtarları oluşturma**

  ASP.NET Core uygulamalar tarafından kullanılan veri koruma anahtarları, uygulamaların dış kayıt defterinde saklanır. Belirli bir uygulamanın anahtarlarını kalıcı hale getirmek için, uygulama havuzu için kayıt defteri anahtarları oluşturun.

  Tek başına, Web grubu olmayan IIS yüklemeleri için, [veri koruma Provision-AutoGenKeys.ps1 PowerShell betiği](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) , bir ASP.NET Core uygulamasıyla kullanılan her uygulama havuzu için kullanılabilir. Bu betik, yalnızca uygulamanın uygulama havuzunun çalışan işlem hesabına erişilebilen HKLM Kayıt defterinde bir kayıt defteri anahtarı oluşturur. Anahtarlar, makine genelindeki bir anahtarla DPAPI kullanılarak şifrelenir.

  Web grubu senaryolarında bir uygulama, veri koruma anahtar halkasını depolamak için bir UNC yolu kullanacak şekilde yapılandırılabilir. Varsayılan olarak, veri koruma anahtarları şifrelenmez. Ağ paylaşımının dosya izinlerinin, uygulamanın çalıştırıldığı Windows hesabıyla sınırlı olduğundan emin olun. Bir x509 sertifikası, bekleyen anahtarları korumak için kullanılabilir. Kullanıcıların sertifikaları karşıya yüklemesine izin vermek için bir mekanizma düşünün: sertifikaları kullanıcının güvenilen sertifika deposuna yerleştir ve kullanıcının uygulamasının çalıştığı tüm makinelerde kullanılabilir olduklarından emin olun. Ayrıntılar için bkz. [ASP.NET Core veri korumasını yapılandırma](xref:security/data-protection/configuration/overview) .

* **Kullanıcı profilini yüklemek için IIS uygulama havuzunu yapılandırma**

  Bu ayar, uygulama havuzunun **Gelişmiş ayarları** altındaki **işlem modeli** bölümünde bulunur. **Kullanıcı profilini yükle** ' ye ayarlayın `True` . Olarak ayarlandığında `True` anahtarlar Kullanıcı profili dizininde depolanır ve Kullanıcı hesabına özgü bir ANAHTARLA DPAPI kullanılarak korunur. Anahtarlar *% LocalAppData%/ASP.exe. net/DataProtection-Keys* klasörüne kalıcıdır.

  Uygulama havuzunun [Setprofileenvironment özniteliğinin](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) de etkinleştirilmesi gerekir. Varsayılan değeri `setProfileEnvironment` `true` . Bazı senaryolarda (örneğin, Windows işletim sistemi), `setProfileEnvironment` olarak ayarlanır `false` . Anahtarlar beklenen şekilde Kullanıcı profili dizininde depolanmıyorsa:

  1. *% Windir%/system32/inetsrv/config* klasörüne gidin.
  1. *applicationHost.config* dosyasını açın.
  1. Öğesini bulun `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` .
  1. `setProfileEnvironment`Özniteliğinin mevcut olmadığını, değeri varsayılan olarak değerini, `true` veya özniteliğin değerini olarak olarak ayarlandığını doğrulayın `true` .

* **Dosya sistemini anahtar halka deposu olarak kullanma**

  [Dosya sistemini anahtar halka deposu olarak kullanmak](xref:security/data-protection/configuration/overview)için uygulama kodunu ayarlayın. Anahtar halkasını korumak ve sertifikanın güvenilen bir sertifika olduğundan emin olmak için bir x509 sertifikası kullanın. Sertifika kendinden imzalı ise, sertifikayı güvenilen kök depoya yerleştirin.

  IIS 'yi bir Web grubunda kullanırken:

  * Tüm makinelerin erişebileceği bir dosya paylaşma kullanın.
  * Her makineye bir x509 sertifikası dağıtın. [Kodda veri korumasını](xref:security/data-protection/configuration/overview)yapılandırın.

* **Veri koruma için makineye özel bir ilke ayarlama**

  Veri koruma sistemi, veri koruma API 'Lerini kullanan tüm uygulamalar için varsayılan [makine genelinde bir ilke](xref:security/data-protection/configuration/machine-wide-policy) ayarlamak için sınırlı destek içerir. Daha fazla bilgi için bkz. <xref:security/data-protection/introduction>.

## <a name="virtual-directories"></a>Sanal Dizinler

[IIS sanal dizinleri](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) ASP.NET Core uygulamalarla desteklenmez. Bir uygulama, bir [alt uygulama](#sub-applications)olarak barındırılabilir.

## <a name="sub-applications"></a>Alt uygulamalar

ASP.NET Core bir uygulama, bir [IIS alt uygulaması (alt uygulama)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications)olarak barındırılabilir. Alt uygulamanın yolu, kök uygulamanın URL 'sinin bir parçası haline gelir.

Alt uygulama içindeki statik varlık bağlantıları, tilde işareti ( `~/` ) gösterimini kullanmalıdır. Tilde işareti gösterimi, alt uygulamanın pathbase 'i işlenmiş göreli bağlantıya eklemek için bir [etiket yardımcısını](xref:mvc/views/tag-helpers/intro) tetikler. Üzerindeki bir alt uygulama için `/subapp_path` ile bağlantılı bir görüntü `src="~/image.png"` olarak işlenir `src="/subapp_path/image.png"` . Kök uygulamanın statik dosya ara yazılımı statik dosya isteğini işlemez. İstek, alt uygulamanın statik dosya ara yazılımı tarafından işlenir.

Statik bir varlığın `src` özniteliği mutlak bir yola ayarlandıysa (örneğin, `src="/image.png"` ), bağlantı alt uygulamanın pathbase olmadan işlenir. Kök uygulamanın statik dosya ara yazılımı, statik varlık kök uygulamadan kullanılabilir olmadığı takdirde *404-bulunamayan* bir Yanıt ile sonuçlanır. Bu, kök uygulamanın [Web kökünden](xref:fundamentals/index#web-root)varlık sunmaya çalışır.

Bir ASP.NET Core uygulamasını başka bir ASP.NET Core uygulaması altında alt uygulama olarak barındırmak için:

1. Alt uygulama için bir uygulama havuzu oluşturun. .NET Core için çekirdek ortak dil çalışma zamanı (CoreCLR), uygulamayı masaüstü CLR (.NET CLR) değil, çalışan işlemde barındırmak için önyüklenirken **.NET CLR sürümünü** **yönetilen kod olmadan** ayarlayın.

1. Kök siteyi, kök sitenin altındaki bir klasörde bulunan alt uygulamayla IIS Yöneticisi 'ne ekleyin.

1. IIS Yöneticisi 'ndeki alt uygulama klasörüne sağ tıklayın ve **uygulamaya Dönüştür**' ü seçin.

1. **Uygulama Ekle** iletişim kutusunda, alt uygulama için oluşturduğunuz uygulama havuzunu atamak üzere **uygulama havuzunun** **Seç** düğmesini kullanın. **Tamam**’ı seçin.

Ayrı bir uygulama havuzunun alt uygulamaya atanması, işlem içi barındırma modelinin kullanıldığı bir gereksinimdir.

İşlem içi barındırma modeli ve ASP.NET Core modülünü yapılandırma hakkında daha fazla bilgi için bkz <xref:host-and-deploy/aspnet-core-module> ..

## <a name="configuration-of-iis-with-webconfig"></a>web.config ile IIS yapılandırması

IIS yapılandırması, `<system.webServer>` ASP.NET Core modüllü ASP.NET Core uygulamalar için işlevsel olan IIS senaryoları için *web.config* bölümünden etkilenir. Örneğin, IIS yapılandırması dinamik sıkıştırma için işlevseldir. IIS sunucu düzeyinde dinamik sıkıştırma kullanmak üzere yapılandırıldıysa, `<urlCompression>` uygulamanın *web.config* dosyasındaki öğesi, ASP.NET Core bir uygulama için devre dışı bırakabilir.

Daha fazla bilgi edinmek için aşağıdaki kaynaklara bakın:

* [İçin yapılandırma başvurusu \<system.webServer>](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

Yalıtılmış uygulama havuzlarında çalışan ayrı uygulamalara yönelik ortam değişkenlerini ayarlamak için (IIS 10,0 veya üzeri için desteklenir), IIS başvuru belgelerindeki [ortam değişkenleri \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) konusunun *AppCmd.exe komut* bölümüne bakın.

## <a name="configuration-sections-of-webconfig"></a>web.config yapılandırma bölümleri

*web.config* içindeki ASP.NET 4. x uygulamalarının yapılandırma bölümleri yapılandırma için ASP.NET Core uygulamalar tarafından kullanılmaz:

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

ASP.NET Core uygulamalar diğer yapılandırma sağlayıcıları kullanılarak yapılandırılır. Daha fazla bilgi için bkz. [yapılandırma](xref:fundamentals/configuration/index).

## <a name="application-pools"></a>Uygulama havuzları

Uygulama havuzu yalıtımı, barındırma modeliyle belirlenir:

* İşlem içi barındırma: uygulamaların ayrı uygulama havuzlarında çalışması gerekir.
* İşlem dışı barındırma: her uygulamayı kendi uygulama havuzunda çalıştırarak uygulamaları birbirinden yalıtmayı öneririz.

IIS **Web sitesi ekleme** iletişim kutusu varsayılan olarak uygulama başına tek bir uygulama havuzu olur. Bir **site adı** sağlandığında, metin otomatik olarak **uygulama havuzu** metin kutusuna aktarılır. Site eklendiğinde site adı kullanılarak yeni bir uygulama havuzu oluşturulur.

## <a name="application-pool-no-locidentity"></a>Uygulama havuzu Identity

Bir uygulama havuzu kimliği hesabı, bir uygulamanın etki alanı veya yerel hesap oluşturmak ve yönetmek zorunda kalmadan benzersiz bir hesap altında çalışmasına izin verir. IIS 8,0 veya sonraki sürümlerde, IIS Yönetici çalışan Işlemi (WAS), yeni uygulama havuzunun adıyla bir sanal hesap oluşturur ve varsayılan olarak bu hesap altında uygulama havuzunun çalışan işlemlerini çalıştırır. Uygulama havuzu için **Gelişmiş ayarlar** altındaki IIS Yönetim Konsolu 'nda, **Identity** öğesinin **Identity applicationPool** kullanmak için ayarlandığından emin olun:

![Uygulama havuzu Gelişmiş ayarları iletişim kutusu](index/_static/apppool-identity.png)

IIS Yönetim işlemi, Windows güvenlik sisteminde uygulama havuzunun adıyla güvenli bir tanımlayıcı oluşturur. Bu kimlik kullanılarak kaynakların güvenliği sağlanmış olabilir. Ancak, bu kimlik gerçek bir kullanıcı hesabı değildir ve Windows Kullanıcı Yönetimi konsolunda gösterilmez.

IIS çalışan işlemi uygulamaya yükseltilmiş erişim gerektiriyorsa, uygulamayı içeren dizinin Access Control listesini (ACL) değiştirin:

1. Windows Gezgini 'ni açın ve dizine gidin.

1. Dizine sağ tıklayıp **Özellikler**' i seçin.

1. **Güvenlik** sekmesinde, **Düzenle** düğmesini ve ardından **Ekle** düğmesini seçin.

1. **Konumlar** düğmesini seçin ve sistemin seçili olduğundan emin olun.

1. **Seçilecek nesne adlarını girin** alanına **\\ app_pool_name>IIS AppPool<** girin. **Adları denetle** düğmesini seçin. *DefaultAppPool* için **IIS APPPOOL\DefaultAppPool** kullanarak adları denetleyin. **Adları denetle** düğmesi seçildiğinde, nesne adları alanında bir **DefaultAppPool** değeri gösterilir. Uygulama havuzu adının doğrudan nesne adları alanına girilmesi mümkün değildir. Nesne adı denetlenirken **IIS AppPool \\<APP_POOL_NAME>** biçimini kullanın.

   ![Uygulama klasörü için Kullanıcı veya Grup Seç iletişim kutusu: "ad denetle" seçmeden önce "DefaultAppPool" uygulama havuzu adı, nesne adları alanında "IIS AppPool" öğesine eklenir \" .](index/_static/select-users-or-groups-1.png)

1. **Tamam**’ı seçin.

   ![Uygulama klasörü için Kullanıcı veya Grup Seç iletişim kutusu: "adları denetle" seçeneğini belirledikten sonra, nesne adları alanında "DefaultAppPool" nesne adı gösterilir.](index/_static/select-users-or-groups-2.png)

1. Okuma &amp; yürütme izinleri varsayılan olarak verilmelidir. Gerektiğinde ek izinler sağlayın.

Erişim, **ıacl 'ler** Aracı kullanılarak bir komut isteminde de verilebilir. Örnek olarak *DefaultAppPool* kullanarak, aşağıdaki komut kullanılır:

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

Daha fazla bilgi için [ıcacl 'ler](/windows-server/administration/windows-commands/icacls) konusuna bakın.

## <a name="http2-support"></a>HTTP/2 desteği

[Http/2](https://httpwg.org/specs/rfc7540.html) aşağıdaki IIS dağıtım senaryolarında ASP.NET Core desteklenir:

* İşlem içi
  * Windows Server 2016/Windows 10 veya üzeri; IIS 10 veya üzeri
  * TLS 1,2 veya üzeri bağlantı
* İşlem dışı
  * Windows Server 2016/Windows 10 veya üzeri; IIS 10 veya üzeri
  * Herkese açık uç sunucu bağlantıları HTTP/2 kullanır, ancak [Kestrel sunucusuyla](xref:fundamentals/servers/kestrel) ters proxy bağlantısı http/1.1 kullanır.
  * TLS 1,2 veya üzeri bağlantı

Bir HTTP/2 bağlantısı oluşturulduğunda, bir işlem içi dağıtım için, [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) Reports `HTTP/2` . Bir HTTP/2 bağlantısı oluşturulduğunda bir işlem dışı dağıtımı için, [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) raporları `HTTP/1.1` .

İşlem içi ve işlem dışı barındırma modelleri hakkında daha fazla bilgi için bkz <xref:host-and-deploy/aspnet-core-module> ..

HTTP/2 varsayılan olarak etkindir. HTTP/2 bağlantısı kurulmadıysa bağlantılar HTTP/1.1 'ye geri döner. IIS dağıtımları ile HTTP/2 yapılandırması hakkında daha fazla bilgi için bkz. [IIS 'de http/2](/iis/get-started/whats-new-in-iis-10/http2-on-iis).

## <a name="cors-preflight-requests"></a>CORS ön denetim istekleri

*Bu bölüm, yalnızca .NET Framework hedefleyen ASP.NET Core uygulamalar için geçerlidir.*

.NET Framework hedefleyen ASP.NET Core bir uygulama için, Seçenekler istekleri uygulamaya varsayılan olarak IIS 'de aktarılmaz. *web.config* ' de uygulamanın IIS işleyicilerini seçenek isteklerini geçirmek üzere nasıl yapılandıracağınızı öğrenmek için bkz. [ASP.NET Web API 2 ' de çapraz kaynak ISTEKLERINI etkinleştirme: CORS 'nin nasıl çalıştığı](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).

## <a name="application-initialization-module-and-idle-timeout"></a>Uygulama başlatma modülü ve boşta kalma zaman aşımı

ASP.NET Core modülü sürüm 2 tarafından IIS 'de barındırıldığında:

* [Uygulama başlatma modülü](#application-initialization-module): uygulamanın çalışan işlem yeniden başlatması veya sunucu yeniden başlatması üzerinde otomatik olarak başlayacak şekilde, [uygulamanın barındırılan veya](#in-process-hosting-model) [işlem dışı](#out-of-process-hosting-model) bir şekilde yapılandırılmış olması için yapılandırılabilir.
* [Boşta kalma zaman aşımı](#idle-timeout): uygulamanın şirket [içinde barındırılan işlemi, işlem](#in-process-hosting-model) yapılmayan dönemler sırasında zaman aşımına uğramaması için yapılandırılabilir.

### <a name="application-initialization-module"></a>Uygulama başlatma modülü

*İşlem içi ve işlem dışı barındırılan uygulamalar için geçerlidir.*

[IIS uygulaması başlatma](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) , uygulama havuzu başlatıldığında veya geri DÖNÜŞTÜRÜLDÜĞÜNDE uygulamaya http isteği gönderen bir IIS özelliğidir. İstek, uygulamayı başlatmak üzere tetikler. Varsayılan olarak IIS, uygulamayı başlatmak için uygulamanın kök URL 'SI () için bir istek yayınlar `/` (yapılandırma hakkında daha fazla bilgi için [ek kaynaklara](#application-initialization-module-and-idle-timeout-additional-resources) bakın).

IIS uygulama başlatma rolü özelliğinin etkin olduğunu doğrulayın:

IIS 'yi yerel olarak kullanırken Windows 7 veya üzeri masaüstü sistemlerinde:

1. **Denetim Masası** > **programları** > **Programlar ve Özellikler** > **Windows özelliklerini aç veya kapat** (ekranın sol tarafında).
1. **Internet Information Services** > **World Wide Web Services** > **uygulama geliştirme özelliklerini** açın.
1. **Uygulama başlatma** onay kutusunu seçin.

Windows Server 2008 R2 veya sonraki sürümlerde:

1. **Rol ve Özellik Ekleme Sihirbazı 'nı** açın.
1. **Rol hizmetlerini Seç** panelinde, **uygulama geliştirme** düğümünü açın.
1. **Uygulama başlatma** onay kutusunu seçin.

Site için uygulama başlatma modülünü etkinleştirmek üzere aşağıdaki yaklaşımlardan birini kullanın:

* IIS Yöneticisi 'Ni kullanma:

  1. **Bağlantılar** panelinde **uygulama havuzları** ' nı seçin.
  1. Listedeki uygulamanın uygulama havuzuna sağ tıklayın ve **Gelişmiş ayarlar**' ı seçin.
  1. Varsayılan **Başlangıç modu** **OnDemand**' dir. **Başlangıç modunu** **AlwaysRunning** olarak ayarlayın. **Tamam**’ı seçin.
  1. **Bağlantılar** panelinde **siteler** düğümünü açın.
  1. Uygulamaya sağ tıklayın ve **Web sitesi** > **Gelişmiş ayarlarını** Yönet ' i seçin.
  1. Varsayılan **önyükleme etkin** ayarı **false** şeklindedir. **Önyükleme etkin** ' i **true** olarak ayarlayın. **Tamam**’ı seçin.

* *web.config* kullanarak, `<applicationInitialization>` öğesini `doAppInitAfterRestart` `true` `<system.webServer>` uygulamasının *web.config* dosyasındaki öğelerine ayarlı olarak ekleyin:

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

*Yalnızca işlem sırasında barındırılan uygulamalar için geçerlidir.*

Uygulamanın çalışmasını engellemek için, IIS Yöneticisi 'Ni kullanarak uygulama havuzunun boşta kalma zaman aşımını ayarlayın:

1. **Bağlantılar** panelinde **uygulama havuzları** ' nı seçin.
1. Listedeki uygulamanın uygulama havuzuna sağ tıklayın ve **Gelişmiş ayarlar**' ı seçin.
1. Varsayılan **boşta kalma süresi (dakika)** **20** dakikadır. **Boşta kalma zaman aşımını (dakika)** **0** (sıfır) olarak ayarlayın. **Tamam**’ı seçin.
1. Çalışan işlemini geri dönüştür.

[İşlem dışı](#out-of-process-hosting-model) barındırılan uygulamaların zaman aşımına uğramasını engellemek için aşağıdaki yaklaşımlardan birini kullanın:

* Uygulamayı çalışır durumda tutmak için bir dış hizmetten ping yapın.
* Uygulama yalnızca arka plan hizmetleri barındırıyorsa, IIS barındırmaktan kaçının ve [ASP.NET Core uygulamasını barındırmak için bir Windows hizmeti](xref:host-and-deploy/windows-service)kullanın.

### <a name="application-initialization-module-and-idle-timeout-additional-resources"></a>Uygulama başlatma modülü ve boşta kalma zaman aşımı ek kaynakları

* [IIS 8,0 uygulama başlatma](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)
* [Uygulama başlatma \<applicationInitialization> ](/iis/configuration/system.webserver/applicationinitialization/).
* [Uygulama havuzu \<processModel> Için Işlem modeli ayarları ](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).

## <a name="deployment-resources-for-iis-administrators"></a>IIS yöneticileri için dağıtım kaynakları

* [IIS belgeleri](/iis)
* [IIS 'de IIS Yöneticisi 'Ni kullanmaya başlama](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [.NET Core uygulama dağıtımı](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:test/troubleshoot>
* <xref:index>
* [Resmi Microsoft IIS sitesi](https://www.iis.net/)
* [Windows Server teknik içerik kitaplığı](/windows-server/windows-server)
* [IIS üzerinde HTTP/2](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
* <xref:host-and-deploy/iis/transform-webconfig>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

ASP.NET Core uygulamasını bir IIS sunucusuna yayımlamaya yönelik bir öğretici deneyimi için, bkz <xref:tutorials/publish-to-iis> ..

[.NET Core barındırma paketi 'ni yükler](#install-the-net-core-hosting-bundle)

## <a name="supported-operating-systems"></a>Desteklenen işletim sistemleri

Aşağıdaki işletim sistemleri desteklenmektedir:

* Windows 7 veya üzeri
* Windows Server 2008 R2 veya sonraki sürümü

[HTTP.sys sunucusu](xref:fundamentals/servers/httpsys) (eskiden webListener olarak adlandırılır), IIS ile ters proxy yapılandırmasında çalışmaz. [Kestrel sunucusunu](xref:fundamentals/servers/kestrel)kullanın.

Azure 'da barındırma hakkında daha fazla bilgi için bkz <xref:host-and-deploy/azure-apps/index> ..

Sorun giderme kılavuzu için bkz <xref:test/troubleshoot> ..

## <a name="supported-platforms"></a>Desteklenen platformlar

32-bit (x86) veya 64 bit (x64) dağıtımı için yayımlanan uygulamalar desteklenir. Uygulama dışında 32 bitlik bir uygulamayı 32 bit (x86) .NET Core SDK dağıtma:

* 64 bitlik bir uygulama için kullanılabilir daha büyük sanal bellek adres alanını gerektirir.
* Daha büyük IIS yığın boyutunu gerektirir.
* 64 bitlik yerel bağımlılıklara sahiptir.

64 bit uygulama yayımlamak için 64 bit (x64) .NET Core SDK kullanın. Ana bilgisayar sisteminde 64 bit çalışma zamanı bulunmalıdır.

ASP.NET Core, varsayılan, platformlar arası bir HTTP sunucusu olan [Kestrel Server](xref:fundamentals/servers/kestrel)ile birlikte gönderilir.

[IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) veya [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)kullanırken, uygulama IIS çalışan işleminden (*işlem dışı*) [Kestrel sunucusu](xref:fundamentals/servers/index#kestrel)ile ayrı bir işlemde çalışır.

ASP.NET Core uygulamalar IIS çalışan işleminden ayrı bir işlemde çalıştığından, modül işlem yönetimini işler. Modül, ilk istek ulaştığında ASP.NET Core uygulama için işlemi başlatır ve kapanırsa veya kilitlenirse uygulamayı yeniden başlatır. Bu aslında, [Windows Işlem etkinleştirme hizmeti (was)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was)tarafından yönetilen işlem içi uygulamalarla birlikte görülen davranışdır.

Aşağıdaki diyagramda IIS, ASP.NET Core modülü ve işlem dışı barındırılan bir uygulama arasındaki ilişki gösterilmektedir:

![ASP.NET Core Modülü](index/_static/ancm-outofprocess.png)

İstekler Web 'den çekirdek modu HTTP.sys sürücüsüne ulaşır. Sürücü, istekleri Web sitesinin yapılandırılmış bağlantı noktasında IIS 'ye yönlendirir, genellikle 80 (HTTP) veya 443 (HTTPS). Modül, 80 veya 443 numaralı bağlantı noktası olmayan uygulama için rastgele bir bağlantı noktasında istekleri Kestrel 'e iletir.

Modül, başlangıç sırasında bir ortam değişkeni aracılığıyla bağlantı noktasını belirtir ve [IIS tümleştirme ara yazılımı](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) sunucuyu dinleyecek şekilde yapılandırır `http://localhost:{port}` . Ek denetimler gerçekleştirilir ve modülünden kaynaklanmayan istekler reddedilir. Modül HTTPS iletmeyi desteklemez, bu nedenle istekler HTTPS üzerinden IIS tarafından alınsa bile HTTP üzerinden iletilir.

Kestrel, isteği modülden başlattıktan sonra, istek ASP.NET Core ara yazılım ardışık düzenine gönderilir. Ara yazılım ardışık düzeni isteği işler ve `HttpContext` uygulamanın mantığına bir örnek olarak geçirir. IIS tümleştirmesi tarafından eklenen ara yazılım, isteği Kestrel iletmek için düzen, uzak IP ve pathbase 'i hesaba göre güncelleştirir. Uygulamanın yanıtı IIS 'e geri geçirilir ve bu, isteği başlatan HTTP istemcisine geri gönderilir.

`CreateDefaultBuilder` Web sunucusu olarak [Kestrel](xref:fundamentals/servers/kestrel) sunucusunu yapılandırır ve [ASP.NET Core modülü](xref:host-and-deploy/aspnet-core-module)için temel yolu ve bağlantı noktasını yapılandırarak IIS tümleştirmesini sunar.

ASP.NET Core modülü, arka uç işlemine atanacak dinamik bir bağlantı noktası oluşturur. `CreateDefaultBuilder` yöntemini çağırır <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration%2A> . `UseIISIntegration` Kestrel 'i, localhost IP adresinde () dinamik bağlantı noktasında dinlemek üzere yapılandırır `127.0.0.1` . Dinamik bağlantı noktası 1234 ise, Kestrel dinler `127.0.0.1:1234` . Bu yapılandırma tarafından belirtilen diğer URL yapılandırmalarının yerini alır:

* `UseUrls`
* [Kestrel 'in dinleme API 'SI](xref:fundamentals/servers/kestrel#endpoint-configuration)
* [Yapılandırma](xref:fundamentals/configuration/index) (veya [komut satırı--URL seçeneği](xref:fundamentals/host/web-host#override-configuration))

`UseUrls`Modül kullanılırken, veya Kestrel API 'sine yapılan çağrılar `Listen` gerekli değildir. `UseUrls`Veya `Listen` çağrılırsa, Kestrel yalnızca uygulamayı IIS olmadan çalıştırırken belirtilen bağlantı noktasını dinler.

ASP.NET Core modülü yapılandırma kılavuzu için bkz <xref:host-and-deploy/aspnet-core-module> ..

Barındırma hakkında daha fazla bilgi için bkz. [ASP.NET Core ana bilgisayar](xref:fundamentals/index#host).

## <a name="application-configuration"></a>Uygulama yapılandırması

### <a name="enable-the-iisintegration-components"></a>Iısıntegration bileşenlerini etkinleştirin

`CreateWebHostBuilder`(*Program.cs*) içinde BIR konak oluştururken <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> IIS tümleştirmesini etkinleştirmek için çağırın:

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        ...
```

Hakkında daha fazla bilgi için `CreateDefaultBuilder` bkz <xref:fundamentals/host/web-host#set-up-a-host> ..

### <a name="iis-options"></a>IIS seçenekleri

| Seçenek                         | Varsayılan | Ayar |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | `true`IIS sunucusu, `HttpContext.User` [Windows kimlik doğrulaması](xref:security/authentication/windowsauth)tarafından kimliği doğrulanmış olarak ayarlanır. İse `false` , sunucu yalnızca bir kimlik sağlar `HttpContext.User` ve tarafından açıkça istendiğinde sorunları yanıtlar `AuthenticationScheme` . ' Nin çalışması için IIS 'de Windows kimlik doğrulamasının etkinleştirilmesi gerekir `AutomaticAuthentication` . Daha fazla bilgi için bkz. [Windows kimlik doğrulaması](xref:security/authentication/windowsauth). |
| `AuthenticationDisplayName`    | `null`  | Oturum açma sayfalarındaki kullanıcılara gösterilen görünen adı ayarlar. |

IIS seçeneklerini yapılandırmak için, içinde için bir hizmet yapılandırması <xref:Microsoft.AspNetCore.Builder.IISOptions> ekleyin <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*> . Aşağıdaki örnek, uygulamanın doldurulmasını önler `HttpContext.Connection.ClientCertificate` :

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| Seçenek                         | Varsayılan | Ayar |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | `true` [IIS tümleştirme ara yazılımı](#enable-the-iisintegration-components) , `HttpContext.User` kimliği doğrulanmış [Windows kimlik doğrulaması](xref:security/authentication/windowsauth)tarafından ayarlanır. İse `false` , ara yazılım için yalnızca bir kimlik sağlar `HttpContext.User` ve tarafından açıkça istendiğinde sorunları yanıtlar `AuthenticationScheme` . ' Nin çalışması için IIS 'de Windows kimlik doğrulamasının etkinleştirilmesi gerekir `AutomaticAuthentication` . Daha fazla bilgi için [Windows kimlik doğrulaması](xref:security/authentication/windowsauth) konusuna bakın. |
| `AuthenticationDisplayName`    | `null`  | Oturum açma sayfalarındaki kullanıcılara gösterilen görünen adı ayarlar. |
| `ForwardClientCertificate`     | `true`  | `true`Ve `MS-ASPNETCORE-CLIENTCERT` istek üst bilgisi varsa, `HttpContext.Connection.ClientCertificate` doldurulur. |

### <a name="proxy-server-and-load-balancer-scenarios"></a>Proxy sunucusu ve yük dengeleyici senaryoları

Iletilen üstbilgiler ara yazılımını yapılandıran [IIS tümleştirme ara yazılımı](#enable-the-iisintegration-components)ve ASP.NET Core modülü, DÜZENI (http/https) ve isteğin KAYNAKLANDıĞı uzak IP adresini iletecek şekilde yapılandırılmıştır. Ek proxy sunucularının ve yük dengeleyiciler arkasında barındırılan uygulamalar için ek yapılandırma gerekebilir. Daha fazla bilgi için bkz. [proxy sunucularıyla ve yük dengeleyicilerle çalışacak ASP.NET Core yapılandırma](xref:host-and-deploy/proxy-load-balancer).

### <a name="webconfig-file"></a>web.config dosyası

*web.config* dosyası [ASP.NET Core modülünü](xref:host-and-deploy/aspnet-core-module)yapılandırır. *web.config* dosyası oluşturma, dönüştürme ve yayımlama, proje yayımlandığında MSBuild hedefi () tarafından işlenir `_TransformWebConfig` . Bu hedef, Web SDK hedeflerinde () bulunur `Microsoft.NET.Sdk.Web` . SDK proje dosyasının en üstünde ayarlanır:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

Projede bir *web.config* dosyası yoksa, ASP.NET Core modülünü yapılandırmak için doğru işlem *yolu* ve *bağımsız değişkenlerle* dosya oluşturulur ve [yayımlanan çıktıya](xref:host-and-deploy/directory-structure)taşınır.

Projede bir *web.config* dosyası varsa, dosya doğru *processPath* ve *bağımsız değişkenlerle* birlikte dönüştürülür ve ASP.NET Core modülünü yapılandırır ve yayımlanan çıktıya taşınır. Dönüştürme, dosyadaki IIS yapılandırma ayarlarını değiştirmez.

*web.config* dosyası, etkin IIS modüllerini DENETLEYEN ek IIS yapılandırma ayarları sağlayabilir. ASP.NET Core uygulamalarla istekleri işleyebilen IIS modülleri hakkında daha fazla bilgi için bkz. [IIS modules](xref:host-and-deploy/iis/modules) konusu.

Web SDK 'sının *web.config* dosyasını dönüştürmasını engellemek için, **\<IsTransformWebConfigDisabled>** Proje dosyasındaki özelliğini kullanın:

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

Web SDK 'sının dosyayı dönüştürmesiyle devre dışı bırakıldığında, *processPath* ve *bağımsız değişkenler* geliştirici tarafından el ile ayarlanmalıdır. Daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module>.

### <a name="webconfig-file-location"></a>web.config dosya konumu

[ASP.NET Core modülünü](xref:host-and-deploy/aspnet-core-module) doğru bir şekilde ayarlamak için, dağıtılan uygulamanın [içerik kök](xref:fundamentals/index#content-root) yolunda (genellikle uygulama temel yolu) *web.config* dosyasının mevcut olması gerekir. Bu, IIS 'ye sunulan Web sitesi fiziksel yoluyla aynı konumdadır. *web.config* dosyası, Web dağıtımı kullanarak birden çok uygulamanın yayımlanmasını sağlamak için uygulamanın kökünde gereklidir.

Önemli dosyalar uygulamanın fiziksel yolunda bulunur (örneğin, *\<assembly>.runtimeconfig.json*, *\<assembly> . xml* (XML belge açıklamaları) ve *\<assembly>.deps.js*. *web.config* dosyası mevcut olduğunda ve site normal olarak başlatıldığında, istenirse IIS bu hassas dosyaları sunmaz. *web.config* dosya eksikse, yanlış şekilde adlandırılmış veya site normal başlatma için YAPıLANDıRıMıŞSA IIS hassas dosyalara genel olarak sunabilir.

***web.config* dosyasının her zaman dağıtımda mevcut olması gerekir, doğru şekilde adlandırılmış ve siteyi normal başlangıç için yapılandırabiliyor. *web.config* dosyasını bir üretim dağıtımından hiçbir şekilde kaldırmayın.**

### <a name="transform-webconfig"></a>Web.config’i dönüştürme

Yayımla *web.config* dönüştürmeniz gerekiyorsa (örneğin, yapılandırma, profil veya ortama göre ortam değişkenlerini ayarlayın), bkz <xref:host-and-deploy/iis/transform-webconfig> ..

## <a name="iis-configuration"></a>IIS yapılandırması

**Windows Server işletim sistemleri**

**Web sunucusu (IIS)** sunucu rolünü etkinleştirin ve rol hizmetleri oluşturun.

1. **Yönet** menüsündeki **rol ve özellik ekleme** sihirbazı ' nı veya **Sunucu Yöneticisi** bağlantısındaki bağlantıyı kullanın. **Sunucu rolleri** adımında, **Web sunucusu (IIS)** kutusunu işaretleyin.

   ![Sunucu rollerini seçin adımında Web sunucusu IIS rolü seçilidir.](index/_static/server-roles-ws2016.png)

1. **Özellikler** adımından sonra, Web sunucusu (IIS) için **rol hizmetleri** adımı yüklenir. İstenen IIS rol hizmetlerini seçin veya varsayılan rol hizmetlerini kabul edin.

   ![Rol hizmetlerini seçin adımında varsayılan rol hizmetleri seçilidir.](index/_static/role-services-ws2016.png)

   **Windows kimlik doğrulaması (Isteğe bağlı)**  
   Windows kimlik doğrulamasını etkinleştirmek için şu düğümleri genişletin: **Web sunucusu**  >  **güvenliği**. **Windows kimlik doğrulama** özelliğini seçin. Daha fazla bilgi için bkz. [Windows \<windowsAuthentication> kimlik doğrulaması](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) ve [Windows kimlik doğrulamasını yapılandırma](xref:security/authentication/windowsauth).

   **WebSockets (Isteğe bağlı)**  
   WebSockets ASP.NET Core 1,1 veya üzeri bir sürümde desteklenir. WebSockets etkinleştirmek için şu düğümleri genişletin: **Web sunucusu**  >  **uygulama geliştirme**. **WebSocket protokolü** özelliğini seçin. Daha fazla bilgi için bkz. [WebSockets](xref:fundamentals/websockets).

1. Web sunucusu rolü ve hizmetlerini yüklemek için **onay** adımına ilerleyin. **Web sunucusu (IIS)** rolü yüklendikten sonra sunucu/IIS yeniden başlatması gerekli değildir.

**Windows masaüstü işletim sistemleri**

**IIS Yönetim Konsolu** ve **World Wide Web hizmetlerini** etkinleştirin.

1. **Denetim Masası** > **programları** > **Programlar ve Özellikler** > **Windows özelliklerini aç veya kapat** (ekranın sol tarafında).

1. **Internet Information Services** düğümünü açın. **Web yönetimi araçları** düğümünü açın.

1. **IIS Yönetim Konsolu** kutusunu işaretleyin.

1. **World Wide Web Hizmetleri** kutusunu işaretleyin.

1. **World Wide Web Hizmetleri** için varsayılan özellikleri kabul edın veya IIS özelliklerini özelleştirin.

   **Windows kimlik doğrulaması (Isteğe bağlı)**  
   Windows kimlik doğrulamasını etkinleştirmek için şu düğümleri genişletin: **World Wide Web Hizmetleri**  >  **güvenliği**. **Windows kimlik doğrulama** özelliğini seçin. Daha fazla bilgi için bkz. [Windows \<windowsAuthentication> kimlik doğrulaması](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) ve [Windows kimlik doğrulamasını yapılandırma](xref:security/authentication/windowsauth).

   **WebSockets (Isteğe bağlı)**  
   WebSockets ASP.NET Core 1,1 veya üzeri bir sürümde desteklenir. WebSockets etkinleştirmek için şu düğümleri genişletin: **World Wide Web Services**  >  **uygulaması geliştirme özellikleri**. **WebSocket protokolü** özelliğini seçin. Daha fazla bilgi için bkz. [WebSockets](xref:fundamentals/websockets).

1. IIS yüklemesi için yeniden başlatma gerekiyorsa, sistemi yeniden başlatın.

![IIS Yönetim Konsolu ve World Wide Web Hizmetleri Windows Özellikleri ' nde seçilidir.](index/_static/windows-features-win10.png)

## <a name="install-the-net-core-hosting-bundle"></a>.NET Core barındırma paketi 'ni yükler

*.NET Core barındırma paketi* 'ni barındırma sistemine yükler. Paket, .NET Core çalışma zamanı, .NET Core kitaplığı ve [ASP.NET Core modülünü](xref:host-and-deploy/aspnet-core-module)de yüklüyor. Modül ASP.NET Core uygulamaların IIS 'nin arkasında çalışmasına izin verir.

> [!IMPORTANT]
> Barındırma paketi IIS 'den önce yüklendiyse, paket yüklemesi onarılması gerekir. IIS yükledikten sonra barındırma paketi yükleyicisini yeniden çalıştırın.
>
> .NET Core 'un 64 bit (x64) sürümünü yükledikten sonra barındırma paketi yüklenirse, SDK 'lar eksik gibi görünebilir ([hiçbir .NET Core SDK 'sı algılanmadı](xref:test/troubleshoot#no-net-core-sdks-were-detected)). Sorunu çözmek için bkz <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle> ..

### <a name="download"></a>İndir

1. [.NET Core 'U indir](https://dotnet.microsoft.com/download/dotnet-core) sayfasına gidin.
1. İstediğiniz .NET Core sürümünü seçin.
1. **Uygulamaları Çalıştır-çalışma zamanı** sütununda, Istenen .NET Core çalışma zamanı sürümünün satırını bulun.
1. **Barındırma paketi** bağlantısını kullanarak yükleyiciyi indirin.

> [!WARNING]
> Bazı yükleyicilerle yaşam süresi (EOL) gelmiş olan ve artık Microsoft tarafından desteklenmeyen yayın sürümlerini içerir. Daha fazla bilgi için bkz. [destek ilkesi](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).

### <a name="install-the-hosting-bundle"></a>Barındırma paketini yükler

1. Yükleyiciyi sunucuda çalıştırın. Aşağıdaki parametreler, yükleyiciyi yönetici komut kabuğu 'ndan çalıştırırken kullanılabilir:

   * `OPT_NO_ANCM=1`: ASP.NET Core modülünü yüklemeyi atlayın.
   * `OPT_NO_RUNTIME=1`: .NET Core çalışma zamanını yüklemeyi atlayın. Sunucu yalnızca [kendi kendine içerilen dağıtımları (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd)barındırdığınızda kullanılır.
   * `OPT_NO_SHAREDFX=1`: ASP.NET paylaşılan çerçevesini (ASP.NET çalışma zamanı) yüklemeyi atlayın. Sunucu yalnızca [kendi kendine içerilen dağıtımları (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd)barındırdığınızda kullanılır.
   * `OPT_NO_X86=1`: X86 çalışma zamanlarını yüklemeyi atlayın. 32 bitlik uygulamalar barındırmayabildiğinizi bildiğiniz durumlarda bu parametreyi kullanın. Gelecekte 32-bit ve 64 bit uygulamaları barındırabilmeniz gereken herhangi bir şansınız varsa, bu parametreyi kullanmayın ve her iki çalışma zamanını da yüklemeyin.
   * `OPT_NO_SHARED_CONFIG_CHECK=1`: Paylaşılan yapılandırma (*applicationHost.config*), IIS yüklemesiyle aynı makinada olduğunda, IIS paylaşılan yapılandırması kullanma denetimini devre dışı bırakın. *Yalnızca ASP.NET Core 2,2 veya sonraki bir sürümü Paketcisi yükleyicilerini barındırmak için kullanılabilir.* Daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.
1. Sistemi yeniden başlatın veya komut kabuğu 'nda aşağıdaki komutları yürütün:

   ```console
   net stop was /y
   net start w3svc
   ```
   IIS 'nin yeniden başlatılması, yükleyici tarafından oluşturulan bir ortam değişkeni olan sistem yolunda bir değişiklik seçer.

Barındırma paketini yüklerken IIS 'deki bireysel siteleri el ile durdurmanız gerekli değildir. Barındırılan uygulamalar (IIS siteleri) IIS yeniden başlatıldığında yeniden başlatılır. Uygulamalar, [uygulama başlatma modülünden](#application-initialization-module-and-idle-timeout)da dahil olmak üzere ilk isteklerini alırken yeniden başlatılır.

ASP.NET Core, paylaşılan çerçeve paketlerinin yama sürümleri için ileri sarma davranışını benimseme. IIS ile IIS tarafından barındırılan uygulamalar IIS ile yeniden başlatıldığında, ilk isteklerini alırken başvurulan paketlerinin en son düzeltme eki sürümleriyle yüklenir. IIS yeniden başlatılırsa, uygulamalar yeniden başlatılır ve çalışan işlemlerine geri dönüştürüldüğünde geri alma davranışını gösterir ve ilk isteklerini alırlar.

> [!NOTE]
> IIS paylaşılan Yapılandırması hakkında bilgi için bkz. [IIS paylaşılan yapılandırması ile ASP.NET Core modülü](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a>Visual Studio ile yayımlarken Web Dağıtımı yüklemesi

Uygulamaları [Web dağıtımı](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later)olan sunuculara dağıttığınızda, en son Web dağıtımı sürümünü sunucuya yüklersiniz. Web Dağıtımı yüklemek için [Web Platformu Yükleyicisi 'ni (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) kullanın veya doğrudan [Microsoft İndirme Merkezi](https://www.microsoft.com/download/details.aspx?id=43717)'nden bir yükleyici edinin. Tercih edilen yöntem, WebPI kullanmaktır. WebPI, barındırma sağlayıcıları için tek başına kurulum ve yapılandırma sağlar.

## <a name="create-the-iis-site"></a>IIS sitesini oluşturma

1. Barındırma sisteminde, uygulamanın yayımlanan klasörlerini ve dosyalarını içeren bir klasör oluşturun. Aşağıdaki adımda, klasörün yolu, uygulamanın fiziksel yolu olarak IIS 'ye sağlanır. Uygulamanın dağıtım klasörü ve dosya düzeni hakkında daha fazla bilgi için bkz <xref:host-and-deploy/directory-structure> ..

1. IIS Yöneticisi 'nde, **Bağlantılar** panelinde sunucunun düğümünü açın. **Siteler** klasörüne sağ tıklayın. Bağlamsal menüden **Web sitesi Ekle** ' yi seçin.

1. Bir **site adı** belirtin ve **fiziksel yolu** uygulamanın dağıtım klasörüne ayarlayın. **Bağlama** yapılandırmasını sağlayın ve **Tamam**' ı seçerek Web sitesini oluşturun:

   ![Web sitesi Ekle adımında site adı, fiziksel yol ve ana bilgisayar adını sağlayın.](index/_static/add-website-ws2016.png)

   > [!WARNING]
   > Üst düzey joker karakter bağlamaları ( `http://*:80/` ve `http://+:80` ) kullanılmamalıdır  . Üst düzey joker karakter bağlamaları, uygulamanızı güvenlik açıklarına açabilir. Bu hem güçlü hem de zayıf Joker karakterlere yöneliktir. Joker karakterler yerine açık ana bilgisayar adları kullanın. Alt etki alanı joker bağlantısı (örneğin, `*.mysub.com` ), tüm üst etki alanını (Bu güvenlik açığı olan aksine) kontrol ediyorsanız bu güvenlik riskine sahip değildir `*.com` . Daha fazla bilgi için bkz. [rfc7230 Section-5,4](https://tools.ietf.org/html/rfc7230#section-5.4) .

1. Sunucu düğümünün altında **uygulama havuzları**' nı seçin.

1. Sitenin uygulama havuzuna sağ tıklayın ve bağlam menüsünden **temel ayarlar** ' ı seçin.

1. **Uygulama havuzunu Düzenle** penceresinde, **.NET CLR sürümünü** **yönetilen kod olmadan** ayarlayın:

   ![.NET CLR sürümü için yönetilen kod ayarlama.](index/_static/edit-apppool-ws2016.png)

    ASP.NET Core ayrı bir işlemde çalışır ve çalışma zamanını yönetir. ASP.NET Core, masaüstü CLR 'nin (.NET CLR) yüklenmemesine bağlı değildir &mdash; . .NET Core Için çekirdek ortak dil çalışma zamanı (CoreCLR), uygulamayı çalışan işlemde barındırmak için önyüklenir. **.NET CLR sürümünün** **yönetilen kod olmadan** ayarlanması isteğe bağlıdır, ancak önerilir.

1. *ASP.NET Core 2,2 veya üzeri*: [işlem içi barındırma modelini](#in-process-hosting-model)kullanan 64 bit (x64) [tabanlı bir dağıtım](/dotnet/core/deploying/#self-contained-deployments-scd) için, 32-bit (x86) işlemleri için uygulama havuzunu devre dışı bırakın.

   IIS Yöneticisi > **uygulama havuzlarının** **Eylemler** kenar çubuğunda, **uygulama havuzu varsayılanlarını** veya **Gelişmiş ayarları** ayarla ' yı seçin. **32 bitlik uygulamaları etkinleştir** ' i bulun ve değerini olarak ayarlayın `False` . Bu ayar [, işlem dışı barındırma](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model)için dağıtılan uygulamaları etkilemez.

1. İşlem modeli kimliğinin uygun izinlere sahip olduğunu doğrulayın.

   Uygulama havuzunun varsayılan kimliği (**işlem modeli**  >  **Identity** ) **applicationPool Identity** 'den başka bir kimliğe değiştirilirse, yeni kimliğin uygulamanın klasörüne, veritabanına ve diğer gerekli kaynaklara erişmek için gerekli izinlere sahip olduğunu doğrulayın. Örneğin, uygulama havuzu, uygulamanın dosyaları okuduğu ve yazdığı klasörlere okuma ve yazma erişimi gerektirir.

**Windows kimlik doğrulama yapılandırması (Isteğe bağlı)**  
Daha fazla bilgi için bkz. [Windows kimlik doğrulamasını yapılandırma](xref:security/authentication/windowsauth).

## <a name="deploy-the-app"></a>Uygulamayı dağıtma

Uygulamayı [IIS sitesi oluşturma](#create-the-iis-site) bölümünde oluşturulan IIS **fiziksel yol** klasörüne dağıtın. [Web dağıtımı](/iis/publish/using-web-deploy/introduction-to-web-deploy) dağıtım için önerilen mekanizmadır, ancak uygulamayı projenin *Publish* klasöründen barındırma sisteminin dağıtım klasörüne taşımak için çeşitli seçenekler mevcuttur.

### <a name="web-deploy-with-visual-studio"></a>Visual Studio ile Web Dağıtımı

Web Dağıtımı kullanılacak bir yayımlama profili oluşturma hakkında bilgi edinmek için bkz. [ASP.NET Core App Deployment Için Visual Studio yayımlama profilleri](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) konusuna bakın. Barındırma sağlayıcısı, bir yayımlama profili veya oluşturma desteği sağlıyorsa, profilini indirin ve Visual Studio **Yayımlama** iletişim kutusunu kullanarak içeri aktarın:

![Yayımla iletişim sayfası](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a>Visual Studio dışında Web Dağıtımı

[Web dağıtımı](/iis/publish/using-web-deploy/introduction-to-web-deploy) , komut satırından Visual Studio dışında da kullanılabilir. Daha fazla bilgi için bkz. [Web Dağıtım aracı](/iis/publish/using-web-deploy/use-the-web-deployment-tool).

### <a name="alternatives-to-web-deploy"></a>Web Dağıtımı alternatifleri

Uygulamayı barındırma sistemine taşımak için el ile kopyalama, [xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy)veya [PowerShell](/powershell/)gibi çeşitli yöntemlerden birini kullanın.

IIS 'ye dağıtım ASP.NET Core hakkında daha fazla bilgi için, [IIS yöneticileri Için dağıtım kaynakları](#deployment-resources-for-iis-administrators) bölümüne bakın.

## <a name="browse-the-website"></a>Web sitesine gidin

Uygulama barındırma sistemine dağıtıldıktan sonra, uygulamanın genel uç noktalarından birine bir istek oluşturun.

Aşağıdaki örnekte, site bağlantı noktasındaki IIS **ana bilgisayar adına** bağlıdır `www.mysite.com`  `80` . Bir istek şu şekilde yapılır `http://www.mysite.com` :

![Microsoft Edge tarayıcısı, IIS başlangıç sayfasını yükledi.](index/_static/browsewebsite.png)

## <a name="locked-deployment-files"></a>Kilitli dağıtım dosyaları

Dağıtım klasöründeki dosyalar, uygulama çalışırken kilitlenir. Dağıtım sırasında kilitli dosyaların üzerine yazılamaz. Kilitli dosyaları bir dağıtımda serbest bırakmak için aşağıdaki yaklaşımlardan **birini** kullanarak uygulama havuzunu durdurun:

* Proje dosyasında Web Dağıtımı ve başvurusunu kullanın `Microsoft.NET.Sdk.Web` . Bir *app_offline.htm* dosyası Web uygulaması dizininin köküne yerleştirilir. Dosya mevcut olduğunda, ASP.NET Core modülü uygulamayı düzgün bir şekilde kapatır ve dağıtım sırasında *app_offline.htm* dosyasına hizmet verir. Daha fazla bilgi için [ASP.NET Core modülü yapılandırma başvurusuna](xref:host-and-deploy/aspnet-core-module#app_offlinehtm)bakın.
* Sunucu üzerindeki IIS Yöneticisi 'nde uygulama havuzunu el ile durdurun.
* *app_offline.htm* bırakmak için PowerShell kullanın (PowerShell 5 veya üzeri gerektirir):

  ```powershell
  $pathToApp = 'PATH_TO_APP'

  # Stop the AppPool
  New-Item -Path $pathToApp app_offline.htm

  # Provide script commands here to deploy the app

  # Restart the AppPool
  Remove-Item -Path $pathToApp app_offline.htm

  ```

## <a name="data-protection"></a>Veri koruma

[ASP.NET Core veri koruma yığını](xref:security/data-protection/introduction) , kimlik doğrulamasında kullanılan ara yazılımlar dahil olmak üzere birkaç ASP.NET Core [middlewares](xref:fundamentals/middleware/index)tarafından kullanılır. Veri koruma API 'Leri Kullanıcı kodu tarafından çağrılmasa bile, kalıcı bir şifreleme [anahtarı deposu](xref:security/data-protection/implementation/key-management)oluşturmak için veri koruma bir dağıtım betiği veya Kullanıcı kodu ile yapılandırılmalıdır. Veri koruması yapılandırılmamışsa, anahtarlar bellekte tutulur ve uygulama yeniden başlatıldığında atılır.

Uygulama yeniden başlatıldığında anahtar halkası bellekte depolanıyorsa:

* Tüm cookie tabanlı kimlik doğrulama belirteçleri geçersiz kılındı. 
* Kullanıcıların bir sonraki isteğinde yeniden oturum açması gerekir. 
* Anahtar halkası ile korunan tüm veriler artık çözülemez. Bu, [CSRF belirteçlerini](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) ve [ASP.NET Core MVC TempData cookie s](xref:fundamentals/app-state#tempdata)öğesini içerebilir.

Anahtar halkasını sürdürmek için IIS altındaki veri korumasını yapılandırmak için aşağıdaki yaklaşımlardan **birini** kullanın:

* **Veri koruma kayıt defteri anahtarları oluşturma**

  ASP.NET Core uygulamalar tarafından kullanılan veri koruma anahtarları, uygulamaların dış kayıt defterinde saklanır. Belirli bir uygulamanın anahtarlarını kalıcı hale getirmek için, uygulama havuzu için kayıt defteri anahtarları oluşturun.

  Tek başına, Web grubu olmayan IIS yüklemeleri için, [veri koruma Provision-AutoGenKeys.ps1 PowerShell betiği](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) , bir ASP.NET Core uygulamasıyla kullanılan her uygulama havuzu için kullanılabilir. Bu betik, yalnızca uygulamanın uygulama havuzunun çalışan işlem hesabına erişilebilen HKLM Kayıt defterinde bir kayıt defteri anahtarı oluşturur. Anahtarlar, makine genelindeki bir anahtarla DPAPI kullanılarak şifrelenir.

  Web grubu senaryolarında bir uygulama, veri koruma anahtar halkasını depolamak için bir UNC yolu kullanacak şekilde yapılandırılabilir. Varsayılan olarak, veri koruma anahtarları şifrelenmez. Ağ paylaşımının dosya izinlerinin, uygulamanın çalıştırıldığı Windows hesabıyla sınırlı olduğundan emin olun. Bir x509 sertifikası, bekleyen anahtarları korumak için kullanılabilir. Kullanıcıların sertifikaları karşıya yüklemesine izin vermek için bir mekanizma düşünün: sertifikaları kullanıcının güvenilen sertifika deposuna yerleştir ve kullanıcının uygulamasının çalıştığı tüm makinelerde kullanılabilir olduklarından emin olun. Ayrıntılar için bkz. [ASP.NET Core veri korumasını yapılandırma](xref:security/data-protection/configuration/overview) .

* **Kullanıcı profilini yüklemek için IIS uygulama havuzunu yapılandırma**

  Bu ayar, uygulama havuzunun **Gelişmiş ayarları** altındaki **işlem modeli** bölümünde bulunur. **Kullanıcı profilini yükle** ' ye ayarlayın `True` . Olarak ayarlandığında `True` anahtarlar Kullanıcı profili dizininde depolanır ve Kullanıcı hesabına özgü bir ANAHTARLA DPAPI kullanılarak korunur. Anahtarlar *% LocalAppData%/ASP.exe. net/DataProtection-Keys* klasörüne kalıcıdır.

  Uygulama havuzunun [Setprofileenvironment özniteliğinin](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) de etkinleştirilmesi gerekir. Varsayılan değeri `setProfileEnvironment` `true` . Bazı senaryolarda (örneğin, Windows işletim sistemi), `setProfileEnvironment` olarak ayarlanır `false` . Anahtarlar beklenen şekilde Kullanıcı profili dizininde depolanmıyorsa:

  1. *% Windir%/system32/inetsrv/config* klasörüne gidin.
  1. *applicationHost.config* dosyasını açın.
  1. Öğesini bulun `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` .
  1. `setProfileEnvironment`Özniteliğinin mevcut olmadığını, değeri varsayılan olarak değerini, `true` veya özniteliğin değerini olarak olarak ayarlandığını doğrulayın `true` .

* **Dosya sistemini anahtar halka deposu olarak kullanma**

  [Dosya sistemini anahtar halka deposu olarak kullanmak](xref:security/data-protection/configuration/overview)için uygulama kodunu ayarlayın. Anahtar halkasını korumak ve sertifikanın güvenilen bir sertifika olduğundan emin olmak için bir x509 sertifikası kullanın. Sertifika kendinden imzalı ise, sertifikayı güvenilen kök depoya yerleştirin.

  IIS 'yi bir Web grubunda kullanırken:

  * Tüm makinelerin erişebileceği bir dosya paylaşma kullanın.
  * Her makineye bir x509 sertifikası dağıtın. [Kodda veri korumasını](xref:security/data-protection/configuration/overview)yapılandırın.

* **Veri koruma için makineye özel bir ilke ayarlama**

  Veri koruma sistemi, veri koruma API 'Lerini kullanan tüm uygulamalar için varsayılan [makine genelinde bir ilke](xref:security/data-protection/configuration/machine-wide-policy) ayarlamak için sınırlı destek içerir. Daha fazla bilgi için bkz. <xref:security/data-protection/introduction>.

## <a name="virtual-directories"></a>Sanal Dizinler

[IIS sanal dizinleri](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) ASP.NET Core uygulamalarla desteklenmez. Bir uygulama, bir [alt uygulama](#sub-applications)olarak barındırılabilir.

## <a name="sub-applications"></a>Alt uygulamalar

ASP.NET Core bir uygulama, bir [IIS alt uygulaması (alt uygulama)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications)olarak barındırılabilir. Alt uygulamanın yolu, kök uygulamanın URL 'sinin bir parçası haline gelir.

Bir alt uygulama işleyici olarak ASP.NET Core modülünü içermemelidir. Modül bir alt uygulamanın *web.config* dosyasına bir işleyici olarak eklenirse, alt uygulamaya gözatmaya çalışılırken hatalı yapılandırma dosyasına başvuran *500,19 iç sunucu hatası* alınır.

Aşağıdaki örnek bir ASP.NET Core alt uygulama için yayımlanmış bir *web.config* dosyası gösterir:

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

Bir ASP.NET Core uygulamasının altında bir non-ASP.NET Core alt uygulaması barındırırken, alt uygulamanın *web.config* dosyasında devralınan işleyiciyi açıkça kaldırın:

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

Alt uygulama içindeki statik varlık bağlantıları, tilde işareti ( `~/` ) gösterimini kullanmalıdır. Tilde işareti gösterimi, alt uygulamanın pathbase 'i işlenmiş göreli bağlantıya eklemek için bir [etiket yardımcısını](xref:mvc/views/tag-helpers/intro) tetikler. Üzerindeki bir alt uygulama için `/subapp_path` ile bağlantılı bir görüntü `src="~/image.png"` olarak işlenir `src="/subapp_path/image.png"` . Kök uygulamanın statik dosya ara yazılımı statik dosya isteğini işlemez. İstek, alt uygulamanın statik dosya ara yazılımı tarafından işlenir.

Statik bir varlığın `src` özniteliği mutlak bir yola ayarlandıysa (örneğin, `src="/image.png"` ), bağlantı alt uygulamanın pathbase olmadan işlenir. Kök uygulamanın statik dosya ara yazılımı, statik varlık kök uygulamadan kullanılabilir olmadığı takdirde *404-bulunamayan* bir Yanıt ile sonuçlanır. Bu, kök uygulamanın [Web kökünden](xref:fundamentals/index#web-root)varlık sunmaya çalışır.

Bir ASP.NET Core uygulamasını başka bir ASP.NET Core uygulaması altında alt uygulama olarak barındırmak için:

1. Alt uygulama için bir uygulama havuzu oluşturun. .NET Core için çekirdek ortak dil çalışma zamanı (CoreCLR), uygulamayı masaüstü CLR (.NET CLR) değil, çalışan işlemde barındırmak için önyüklenirken **.NET CLR sürümünü** **yönetilen kod olmadan** ayarlayın.

1. Kök siteyi, kök sitenin altındaki bir klasörde bulunan alt uygulamayla IIS Yöneticisi 'ne ekleyin.

1. IIS Yöneticisi 'ndeki alt uygulama klasörüne sağ tıklayın ve **uygulamaya Dönüştür**' ü seçin.

1. **Uygulama Ekle** iletişim kutusunda, alt uygulama için oluşturduğunuz uygulama havuzunu atamak üzere **uygulama havuzunun** **Seç** düğmesini kullanın. **Tamam**’ı seçin.

Ayrı bir uygulama havuzunun alt uygulamaya atanması, işlem içi barındırma modelinin kullanıldığı bir gereksinimdir.

İşlem içi barındırma modeli ve ASP.NET Core modülünü yapılandırma hakkında daha fazla bilgi için bkz <xref:host-and-deploy/aspnet-core-module> ..

## <a name="configuration-of-iis-with-webconfig"></a>web.config ile IIS yapılandırması

IIS yapılandırması, `<system.webServer>` ASP.NET Core modüllü ASP.NET Core uygulamalar için işlevsel olan IIS senaryoları için *web.config* bölümünden etkilenir. Örneğin, IIS yapılandırması dinamik sıkıştırma için işlevseldir. IIS sunucu düzeyinde dinamik sıkıştırma kullanmak üzere yapılandırıldıysa, `<urlCompression>` uygulamanın *web.config* dosyasındaki öğesi, ASP.NET Core bir uygulama için devre dışı bırakabilir.

Daha fazla bilgi edinmek için aşağıdaki kaynaklara bakın:

* [İçin yapılandırma başvurusu \<system.webServer>](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

Yalıtılmış uygulama havuzlarında çalışan ayrı uygulamalara yönelik ortam değişkenlerini ayarlamak için (IIS 10,0 veya üzeri için desteklenir), IIS başvuru belgelerindeki [ortam değişkenleri \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) konusunun *AppCmd.exe komut* bölümüne bakın.

## <a name="configuration-sections-of-webconfig"></a>web.config yapılandırma bölümleri

*web.config* içindeki ASP.NET 4. x uygulamalarının yapılandırma bölümleri yapılandırma için ASP.NET Core uygulamalar tarafından kullanılmaz:

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

ASP.NET Core uygulamalar diğer yapılandırma sağlayıcıları kullanılarak yapılandırılır. Daha fazla bilgi için bkz. [yapılandırma](xref:fundamentals/configuration/index).

## <a name="application-pools"></a>Uygulama havuzları

Bir sunucuda birden çok Web sitesi barındırırken, her uygulamayı kendi uygulama havuzunda çalıştırarak uygulamaları birbirinden yalıtmayı öneririz. IIS **Web sitesi ekleme** iletişim kutusu varsayılan olarak bu yapılandırmaya sahiptir. Bir **site adı** sağlandığında, metin otomatik olarak **uygulama havuzu** metin kutusuna aktarılır. Site eklendiğinde site adı kullanılarak yeni bir uygulama havuzu oluşturulur.

## <a name="application-pool-no-locidentity"></a>Uygulama havuzu Identity

Bir uygulama havuzu kimliği hesabı, bir uygulamanın etki alanı veya yerel hesap oluşturmak ve yönetmek zorunda kalmadan benzersiz bir hesap altında çalışmasına izin verir. IIS 8,0 veya sonraki sürümlerde, IIS Yönetici çalışan Işlemi (WAS), yeni uygulama havuzunun adıyla bir sanal hesap oluşturur ve varsayılan olarak bu hesap altında uygulama havuzunun çalışan işlemlerini çalıştırır. Uygulama havuzu için **Gelişmiş ayarlar** altındaki IIS Yönetim Konsolu 'nda, **Identity** öğesinin **Identity applicationPool** kullanmak için ayarlandığından emin olun:

![Uygulama havuzu Gelişmiş ayarları iletişim kutusu](index/_static/apppool-identity.png)

IIS Yönetim işlemi, Windows güvenlik sisteminde uygulama havuzunun adıyla güvenli bir tanımlayıcı oluşturur. Bu kimlik kullanılarak kaynakların güvenliği sağlanmış olabilir. Ancak, bu kimlik gerçek bir kullanıcı hesabı değildir ve Windows Kullanıcı Yönetimi konsolunda gösterilmez.

IIS çalışan işlemi uygulamaya yükseltilmiş erişim gerektiriyorsa, uygulamayı içeren dizinin Access Control listesini (ACL) değiştirin:

1. Windows Gezgini 'ni açın ve dizine gidin.

1. Dizine sağ tıklayıp **Özellikler**' i seçin.

1. **Güvenlik** sekmesinde, **Düzenle** düğmesini ve ardından **Ekle** düğmesini seçin.

1. **Konumlar** düğmesini seçin ve sistemin seçili olduğundan emin olun.

1. **Seçilecek nesne adlarını girin** alanına **\\ app_pool_name>IIS AppPool<** girin. **Adları denetle** düğmesini seçin. *DefaultAppPool* için **IIS APPPOOL\DefaultAppPool** kullanarak adları denetleyin. **Adları denetle** düğmesi seçildiğinde, nesne adları alanında bir **DefaultAppPool** değeri gösterilir. Uygulama havuzu adının doğrudan nesne adları alanına girilmesi mümkün değildir. Nesne adı denetlenirken **IIS AppPool \\<APP_POOL_NAME>** biçimini kullanın.

   ![Uygulama klasörü için Kullanıcı veya Grup Seç iletişim kutusu: "ad denetle" seçmeden önce "DefaultAppPool" uygulama havuzu adı, nesne adları alanında "IIS AppPool" öğesine eklenir \" .](index/_static/select-users-or-groups-1.png)

1. **Tamam**’ı seçin.

   ![Uygulama klasörü için Kullanıcı veya Grup Seç iletişim kutusu: "adları denetle" seçeneğini belirledikten sonra, nesne adları alanında "DefaultAppPool" nesne adı gösterilir.](index/_static/select-users-or-groups-2.png)

1. Okuma &amp; yürütme izinleri varsayılan olarak verilmelidir. Gerektiğinde ek izinler sağlayın.

Erişim, **ıacl 'ler** Aracı kullanılarak bir komut isteminde de verilebilir. Örnek olarak *DefaultAppPool* kullanarak, aşağıdaki komut kullanılır:

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

Daha fazla bilgi için [ıcacl 'ler](/windows-server/administration/windows-commands/icacls) konusuna bakın.

## <a name="http2-support"></a>HTTP/2 desteği

[Http/2](https://httpwg.org/specs/rfc7540.html) , aşağıdaki temel gereksinimleri karşılayan işlem dışı dağıtımlar için desteklenir:

* Windows Server 2016/Windows 10 veya üzeri; IIS 10 veya üzeri
* Herkese açık uç sunucu bağlantıları HTTP/2 kullanır, ancak [Kestrel sunucusuyla](xref:fundamentals/servers/kestrel) ters proxy bağlantısı http/1.1 kullanır.
* Hedef Framework: HTTP/2 bağlantısı tamamen IIS tarafından işlendiğinden, işlem dışı dağıtımlar için geçerli değildir.
* TLS 1,2 veya üzeri bağlantı

Bir HTTP/2 bağlantısı kurulduysa, [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) Reports `HTTP/1.1` .

HTTP/2 varsayılan olarak etkindir. HTTP/2 bağlantısı kurulmadıysa bağlantılar HTTP/1.1 'ye geri döner. IIS dağıtımları ile HTTP/2 yapılandırması hakkında daha fazla bilgi için bkz. [IIS 'de http/2](/iis/get-started/whats-new-in-iis-10/http2-on-iis).

## <a name="cors-preflight-requests"></a>CORS ön denetim istekleri

*Bu bölüm, yalnızca .NET Framework hedefleyen ASP.NET Core uygulamalar için geçerlidir.*

.NET Framework hedefleyen ASP.NET Core bir uygulama için, Seçenekler istekleri uygulamaya varsayılan olarak IIS 'de aktarılmaz. *web.config* ' de uygulamanın IIS işleyicilerini seçenek isteklerini geçirmek üzere nasıl yapılandıracağınızı öğrenmek için bkz. [ASP.NET Web API 2 ' de çapraz kaynak ISTEKLERINI etkinleştirme: CORS 'nin nasıl çalıştığı](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).

## <a name="deployment-resources-for-iis-administrators"></a>IIS yöneticileri için dağıtım kaynakları

* [IIS belgeleri](/iis)
* [IIS 'de IIS Yöneticisi 'Ni kullanmaya başlama](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [.NET Core uygulama dağıtımı](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:test/troubleshoot>
* <xref:index>
* [Resmi Microsoft IIS sitesi](https://www.iis.net/)
* [Windows Server teknik içerik kitaplığı](/windows-server/windows-server)
* [IIS üzerinde HTTP/2](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
* <xref:host-and-deploy/iis/transform-webconfig>

::: moniker-end
