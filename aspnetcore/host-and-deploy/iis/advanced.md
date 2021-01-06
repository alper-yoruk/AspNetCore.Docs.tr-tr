---
title: Gelişmiş yapılandırma
author: rick-anderson
description: ASP.NET Core modülü ve Internet Information Services (IIS) ile gelişmiş yapılandırma.
monikerRange: '>= aspnetcore-5.0'
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
uid: host-and-deploy/iis/advanced
ms.openlocfilehash: 9f14929a7d298d6f4d66abcc88665db34fc072bf
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93058625"
---
# <a name="advanced-configuration-of-the-aspnet-core-module-and-iis"></a>ASP.NET Core modülünün ve IIS 'nin gelişmiş yapılandırması

Bu makalede ASP.NET Core modülü ve IIS için gelişmiş yapılandırma seçenekleri ve senaryoları ele alınmaktadır.

## <a name="modify-the-stack-size"></a>Yığın boyutunu değiştirme

*Yalnızca işlem içi barındırma modeli kullanılırken geçerlidir.*

Yönetilen yığın boyutunu `stackSize` dosyadaki bayt cinsinden ayarını kullanarak yapılandırın `web.config` . Varsayılan boyut 1.048.576 bayttır (1 MB). Aşağıdaki örnek, yığın boyutunu 2 MB (2.097.152 bayt) olarak değiştirir:

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

ASP.NET Core modülü yükleyicisi hesap ayrıcalıklarıyla çalışır `TrustedInstaller` . Yerel sistem hesabı, IIS paylaşılan Yapılandırması tarafından kullanılan paylaşım yolu için değiştirme iznine sahip olmadığından, yükleyici paylaşımdaki dosyadaki modül ayarlarını yapılandırmaya çalışırken bir erişim reddedildi hatası atar `applicationHost.config` .

IIS yüklemesiyle aynı makinede bir IIS paylaşılan yapılandırması kullanırken, şu şekilde ayarlanan parametre ile birlikte ASP.NET Core barındırma paketi yükleyicisini çalıştırın `OPT_NO_SHARED_CONFIG_CHECK` `1` :

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

Paylaşılan yapılandırmanın yolu IIS yüklemesiyle aynı makinede olmadığında, şu adımları izleyin:

1. IIS paylaşılan yapılandırmasını devre dışı bırakın.
1. Yükleyiciyi çalıştırın.
1. Güncelleştirilmiş `applicationHost.config` dosyayı dosya paylaşımında dışarı aktarın.
1. IIS paylaşılan yapılandırmasını yeniden etkinleştirin.

## <a name="data-protection"></a>Veri koruma

[ASP.NET Core veri koruma yığını](xref:security/data-protection/introduction) , kimlik doğrulamasında kullanılan ara yazılımlar dahil olmak üzere birkaç ASP.NET Core [middlewares](xref:fundamentals/middleware/index)tarafından kullanılır. Veri koruma API 'Leri Kullanıcı kodu tarafından çağrılmasa bile, kalıcı bir şifreleme [anahtarı deposu](xref:security/data-protection/implementation/key-management)oluşturmak için veri koruma bir dağıtım betiği veya Kullanıcı kodu ile yapılandırılmalıdır. Veri koruması yapılandırılmamışsa, anahtarlar bellekte tutulur ve uygulama yeniden başlatıldığında atılır.

Veri koruma anahtarı halkası, uygulama yeniden başlatıldığında bellekte depolanıyorsa:

* Tüm cookie tabanlı kimlik doğrulama belirteçleri geçersiz kılındı. 
* Kullanıcıların bir sonraki isteğinde yeniden oturum açması gerekir. 
* Anahtar halkası ile korunan tüm veriler artık çözülemez. Bu, [CSRF belirteçlerini](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) ve [ASP.NET Core MVC TempData cookie s](xref:fundamentals/app-state#tempdata)öğesini içerebilir.

Anahtar halkasını sürdürmek için IIS altındaki veri korumasını yapılandırmak için aşağıdaki yaklaşımlardan **birini** kullanın:

* **Veri koruma kayıt defteri anahtarları oluşturma**

  ASP.NET Core uygulamalar tarafından kullanılan veri koruma anahtarları, uygulamaların dış kayıt defterinde saklanır. Belirli bir uygulamanın anahtarlarını kalıcı hale getirmek için, uygulama havuzu için kayıt defteri anahtarları oluşturun.

  Tek başına, Web grubu olmayan IIS yüklemeleri için, [veri koruma Provision-AutoGenKeys.ps1 PowerShell betiği](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) , bir ASP.NET Core uygulamasıyla kullanılan her uygulama havuzu için kullanılabilir. Bu betik, yalnızca uygulamanın uygulama havuzunun çalışan işlem hesabına erişilebilen HKLM Kayıt defterinde bir kayıt defteri anahtarı oluşturur. Anahtarlar, makine genelindeki bir anahtarla DPAPI kullanılarak şifrelenir.

  Web grubu senaryolarında bir uygulama, veri koruma anahtar halkasını depolamak için bir UNC yolu kullanacak şekilde yapılandırılabilir. Varsayılan olarak anahtarlar şifrelenmez. Ağ paylaşımının dosya izinlerinin, uygulamanın çalıştığı Windows hesabıyla sınırlı olduğundan emin olun. Bir x509 sertifikası, bekleyen anahtarları korumak için kullanılabilir. Kullanıcıların sertifikaları karşıya yüklemesine izin vermek için bir mekanizma düşünün. Sertifikaları kullanıcının güvenilen sertifika deposuna yerleştirin ve kullanıcının uygulamasının çalıştığı tüm makinelerde kullanılabilir olduklarından emin olun. Daha fazla bilgi için bkz. <xref:security/data-protection/configuration/overview>.

* **Kullanıcı profilini yüklemek için IIS uygulama havuzunu yapılandırma**

  Bu ayar, uygulama havuzunun **Gelişmiş ayarları** altındaki **işlem modeli** bölümünde bulunur. **Kullanıcı profilini yükle** ' ye ayarlayın `True` . Olarak ayarlandığında `True` anahtarlar Kullanıcı profili dizininde depolanır ve Kullanıcı hesabına özgü bir ANAHTARLA DPAPI kullanılarak korunur. Anahtarlar `%LOCALAPPDATA%/ASP.NET/DataProtection-Keys` klasörde kalıcıdır.

  Uygulama havuzunun [ `setProfileEnvironment` özniteliğinin](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) de etkinleştirilmesi gerekir. Varsayılan değeri `setProfileEnvironment` `true` . Bazı senaryolarda (örneğin, Windows işletim sistemi), `setProfileEnvironment` olarak ayarlanır `false` . Anahtarlar beklenen şekilde Kullanıcı profili dizininde depolanmıyorsa:

  1. `%windir%/system32/inetsrv/config` klasörüne gidin.
  1. `applicationHost.config` dosyasını açın.
  1. Öğesini bulun `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` .
  1. `setProfileEnvironment`Özniteliğinin mevcut olmadığını, değeri varsayılan olarak değerini, `true` veya özniteliğin değerini olarak olarak ayarlandığını doğrulayın `true` .

* **Dosya sistemini anahtar halka deposu olarak kullanma**

  [Dosya sistemini anahtar halka deposu olarak kullanmak](xref:security/data-protection/configuration/overview)için uygulama kodunu ayarlayın. Anahtar halkasını korumak ve sertifikanın güvenilen bir sertifika olduğundan emin olmak için bir x509 sertifikası kullanın. Sertifika kendinden imzalı ise, sertifikayı güvenilen kök depoya yerleştirin.

  IIS 'yi bir Web grubunda kullanırken:

  * Tüm makinelerin erişebileceği bir dosya paylaşma kullanın.
  * Her makineye bir x509 sertifikası dağıtın. [Kodda veri korumasını](xref:security/data-protection/configuration/overview)yapılandırın.

* **Veri koruma için makineye özel bir ilke ayarlama**

  Veri koruma sistemi, veri koruma API 'Lerini kullanan tüm uygulamalar için varsayılan [makine genelinde bir ilke](xref:security/data-protection/configuration/machine-wide-policy) ayarlamak için sınırlı destek içerir. Daha fazla bilgi için bkz. <xref:security/data-protection/introduction>.

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

1. **Denetim Masası**  >  **programları**  >  **Programlar ve Özellikler**  >  **Windows özelliklerini aç veya kapat** (ekranın sol tarafında).

1. **Internet Information Services** düğümünü açın. **Web yönetimi araçları** düğümünü açın.

1. **IIS Yönetim Konsolu** kutusunu işaretleyin.

1. **World Wide Web Hizmetleri** kutusunu işaretleyin.

1. **World Wide Web Hizmetleri** için varsayılan özellikleri kabul edın veya IIS özelliklerini özelleştirin.

   **Windows kimlik doğrulaması (Isteğe bağlı)**  
   Windows kimlik doğrulamasını etkinleştirmek için şu düğümleri genişletin: **World Wide Web Hizmetleri**  >  **güvenliği**. **Windows kimlik doğrulama** özelliğini seçin. Daha fazla bilgi için bkz. [Windows `<windowsAuthentication>` kimlik doğrulaması](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) ve [Windows kimlik doğrulamasını yapılandırma](xref:security/authentication/windowsauth).

   **WebSockets (Isteğe bağlı)**  
   WebSockets ASP.NET Core 1,1 veya üzeri bir sürümde desteklenir. WebSockets etkinleştirmek için şu düğümleri genişletin: **World Wide Web Services**  >  **uygulaması geliştirme özellikleri**. **WebSocket protokolü** özelliğini seçin. Daha fazla bilgi için bkz. [WebSockets](xref:fundamentals/websockets).

1. IIS yüklemesi için yeniden başlatma gerekiyorsa, sistemi yeniden başlatın.

![IIS Yönetim Konsolu ve World Wide Web Hizmetleri Windows Özellikleri ' nde seçilidir.](index/_static/windows-features-win10.png)

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

## <a name="application-pools"></a>Uygulama havuzları

Uygulama havuzu yalıtımı, barındırma modeliyle belirlenir:

* İşlem içi barındırma: uygulamaların ayrı uygulama havuzlarında çalışması gerekir.
* İşlem dışı barındırma: her uygulamayı kendi uygulama havuzunda çalıştırarak uygulamaları birbirinden yalıtmayı öneririz.

IIS **Web sitesi ekleme** iletişim kutusu varsayılan olarak uygulama başına tek bir uygulama havuzu olur. Bir **site adı** sağlandığında, metin otomatik olarak **uygulama havuzu** metin kutusuna aktarılır. Site eklendiğinde site adı kullanılarak yeni bir uygulama havuzu oluşturulur.

## <a name="application-pool-no-locidentity"></a>Uygulama havuzu Identity

Bir uygulama havuzu kimliği hesabı, bir uygulamanın etki alanı veya yerel hesap oluşturmak ve yönetmek zorunda kalmadan benzersiz bir hesap altında çalışmasına izin verir. IIS 8,0 veya sonraki sürümlerde, IIS Yönetici çalışan Işlemi (WAS), yeni uygulama havuzunun adıyla bir sanal hesap oluşturur ve varsayılan olarak bu hesap altında uygulama havuzunun çalışan işlemlerini çalıştırır. Uygulama havuzu için **Gelişmiş ayarlar** altındaki IIS Yönetim Konsolu ' nda, **Identity** öğesinin kullanılmak üzere ayarlandığından emin olun `ApplicationPoolIdentity` :

![Uygulama havuzu Gelişmiş ayarları iletişim kutusu](index/_static/apppool-identity.png)

IIS Yönetim işlemi, Windows güvenlik sisteminde uygulama havuzunun adıyla güvenli bir tanımlayıcı oluşturur. Bu kimlik kullanılarak kaynakların güvenliği sağlanmış olabilir. Ancak, bu kimlik gerçek bir kullanıcı hesabı değildir ve Windows Kullanıcı Yönetimi konsolunda gösterilmez.

IIS çalışan işlemi uygulamaya yükseltilmiş erişim gerektiriyorsa, uygulamayı içeren dizinin Access Control listesini (ACL) değiştirin:

1. Windows Gezgini 'ni açın ve dizine gidin.

1. Dizine sağ tıklayıp **Özellikler**' i seçin.

1. **Güvenlik** sekmesinde, **Düzenle** düğmesini ve ardından **Ekle** düğmesini seçin.

1. **Konumlar** düğmesini seçin ve sistemin seçili olduğundan emin olun.

1. `IIS AppPool\{APP POOL NAME}`Yer tutucunun `{APP POOL NAME}` uygulama havuzu adı olduğu, burada **Seçilecek nesne adlarını girin** alanına biçim girin. **Adları denetle** düğmesini seçin. *DefaultAppPool* için, kullanarak adları kontrol edin `IIS AppPool\DefaultAppPool` . **Adları denetle** düğmesi seçildiğinde, `DefaultAppPool` nesne adları alanında bir değeri gösterilir. Uygulama havuzu adının doğrudan nesne adları alanına girilmesi mümkün değildir. `IIS AppPool\{APP POOL NAME}` `{APP POOL NAME}` Nesne adı denetlenirken yer tutucunun uygulama havuzu adı olduğu biçimi kullanın.

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

Bir HTTP/2 bağlantısı oluşturulduğunda, işlem içi dağıtım için [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) raporları `HTTP/2` . Bir HTTP/2 bağlantısı oluşturulduğunda, işlem dışı bir dağıtım için [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) raporları `HTTP/1.1` .

İşlem içi ve işlem dışı barındırma modelleri hakkında daha fazla bilgi için bkz <xref:host-and-deploy/aspnet-core-module> ..

HTTP/2 varsayılan olarak etkindir. HTTP/2 bağlantısı kurulmadıysa bağlantılar HTTP/1.1 'ye geri döner. IIS dağıtımları ile HTTP/2 yapılandırması hakkında daha fazla bilgi için bkz. [IIS 'de http/2](/iis/get-started/whats-new-in-iis-10/http2-on-iis).

## <a name="cors-preflight-requests"></a>CORS ön denetim istekleri

*Bu bölüm, yalnızca .NET Framework hedefleyen ASP.NET Core uygulamalar için geçerlidir.*

.NET Framework hedefleyen ASP.NET Core bir uygulama için, Seçenekler istekleri uygulamaya varsayılan olarak IIS 'de aktarılmaz. ' De uygulama IIS işleyicilerini seçenek isteklerini geçecek şekilde yapılandırma hakkında bilgi edinmek için `web.config` bkz. [ASP.NET Web API 2 ' de çapraz kaynak isteklerini ETKINLEŞTIRME: CORS 'Nin nasıl çalıştığı](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).

## <a name="application-initialization-module-and-idle-timeout"></a>Uygulama başlatma modülü ve boşta kalma zaman aşımı

ASP.NET Core modülü sürüm 2 tarafından IIS 'de barındırıldığında:

* [Uygulama başlatma modülü](#application-initialization-module): uygulamanın çalışan işlem yeniden başlatması veya sunucu yeniden başlatması üzerinde otomatik olarak başlayacak şekilde, [uygulamanın barındırılan veya](xref:host-and-deploy/iis/in-process-hosting) [işlem dışı](xref:host-and-deploy/iis/out-of-process-hosting) bir şekilde yapılandırılmış olması için yapılandırılabilir.
* [Boşta kalma zaman aşımı](#idle-timeout): uygulamanın barındırılan, [işlem](xref:host-and-deploy/iis/in-process-hosting) yapılmayan dönemler sırasında zaman aşımına uğramaması için yapılandırılabilir.

### <a name="application-initialization-module"></a>Uygulama başlatma modülü

*İşlem içi ve işlem dışı barındırılan uygulamalar için geçerlidir.*

[IIS uygulaması başlatma](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) , uygulama havuzu başlatıldığında veya geri DÖNÜŞTÜRÜLDÜĞÜNDE uygulamaya http isteği gönderen bir IIS özelliğidir. İstek, uygulamayı başlatmak üzere tetikler. Varsayılan olarak IIS, uygulamayı başlatmak için uygulamanın kök URL 'SI () için bir istek yayınlar `/` (yapılandırma hakkında daha fazla bilgi için [ek kaynaklara](#application-initialization-module-and-idle-timeout-additional-resources) bakın).

IIS uygulama başlatma rolü özelliğinin etkin olduğunu doğrulayın:

IIS 'yi yerel olarak kullanırken Windows 7 veya üzeri masaüstü sistemlerinde:

1. **Denetim Masası**  >  **programları**  >  **Programlar ve Özellikler**  >  **Windows özelliklerini aç veya kapat** (ekranın sol tarafında).
1. **Internet Information Services**  >  **World Wide Web Services**  >  **uygulama geliştirme özelliklerini** açın.
1. **Uygulama başlatma** onay kutusunu seçin.

Windows Server 2008 R2 veya sonraki sürümlerde:

1. **Rol ve Özellik Ekleme Sihirbazı 'nı** açın.
1. **Rol hizmetlerini Seç** panelinde, **uygulama geliştirme** düğümünü açın.
1. **Uygulama başlatma** onay kutusunu seçin.

Site için uygulama başlatma modülünü etkinleştirmek üzere aşağıdaki yaklaşımlardan birini kullanın:

* IIS Yöneticisi 'Ni kullanma:

  1. **Bağlantılar** panelinde **uygulama havuzları** ' nı seçin.
  1. Listedeki uygulamanın uygulama havuzuna sağ tıklayın ve **Gelişmiş ayarlar**' ı seçin.
  1. Varsayılan **Başlangıç modu** `OnDemand` . **Başlangıç modunu** olarak ayarlayın `AlwaysRunning` . **Tamam**’ı seçin.
  1. **Bağlantılar** panelinde **siteler** düğümünü açın.
  1. Uygulamaya sağ tıklayın ve **Web sitesi**  >  **Gelişmiş ayarlarını** Yönet ' i seçin.
  1. Varsayılan **önyükleme etkin** ayarı `False` . **Önyüklemeyi etkin** olarak ayarlayın `True` . **Tamam**’ı seçin.

* Kullanarak `web.config` , `<applicationInitialization>` öğesini `doAppInitAfterRestart` olarak ayarlanmış öğesini `true` `<system.webServer>` uygulamanın dosyasındaki öğelerine ekleyin `web.config` :

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
1. Varsayılan **boşta kalma zaman aşımı (dakika)** `20` dakikadır. **Boşta kalma süresi (dakika)** `0` değerini (sıfır) olarak ayarlayın. **Tamam**’ı seçin.
1. Çalışan işlemini geri dönüştür.

[İşlem dışı](xref:host-and-deploy/iis/out-of-process-hosting) barındırılan uygulamaların zaman aşımına uğramasını engellemek için aşağıdaki yaklaşımlardan birini kullanın:

* Uygulamayı çalışır durumda tutmak için bir dış hizmetten ping yapın.
* Uygulama yalnızca arka plan hizmetleri barındırıyorsa, IIS barındırmaktan kaçının ve [ASP.NET Core uygulamasını barındırmak için bir Windows hizmeti](xref:host-and-deploy/windows-service)kullanın.

### <a name="application-initialization-module-and-idle-timeout-additional-resources"></a>Uygulama başlatma modülü ve boşta kalma zaman aşımı ek kaynakları

* [IIS 8,0 uygulama başlatma](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)
* [Uygulama başlatma `<applicationInitialization>` ](/iis/configuration/system.webserver/applicationinitialization/).
* [Uygulama havuzu `<processModel>` Için Işlem modeli ayarları ](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).

## <a name="module-schema-and-configuration-file-locations"></a>Modül, şema ve yapılandırma dosyası konumları

### <a name="module"></a>Modül

**IIS (X86/AMD64)**:

* `%windir%\System32\inetsrv\aspnetcore.dll`

* `%windir%\SysWOW64\inetsrv\aspnetcore.dll`

* `%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll`

* `%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll`

**IIS Express (X86/AMD64)**:

* `%ProgramFiles%\IIS Express\aspnetcore.dll`

* `%ProgramFiles(x86)%\IIS Express\aspnetcore.dll`

* `%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll`

* `%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll`

### <a name="schema"></a>Şema

**IIS**

* `%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml`

* `%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml`

**IIS Express**

* `%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml`

* `%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml`

### <a name="configuration"></a>Yapılandırma

**IIS**

* `%windir%\System32\inetsrv\config\applicationHost.config`

**IIS Express**

* Visual Studio: `{APPLICATION ROOT}\.vs\config\applicationHost.config`

* *iisexpress.exe* CLı `%USERPROFILE%\Documents\IISExpress\config\applicationhost.config`

Dosyalar dosyada arayarak bulunabilir `aspnetcore` `applicationHost.config` .
