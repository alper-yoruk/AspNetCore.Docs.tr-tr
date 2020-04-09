---
title: ASP.NET Core'daki web sunucusu uygulamaları
author: rick-anderson
description: ASP.NET Core için kerkenez ve HTTP.sys web sunucularını keşfedin. Sunucunasıl seçeceğinizi ve ters proxy sunucusunun ne zaman kullanılacağını öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/07/2019
uid: fundamentals/servers/index
ms.openlocfilehash: d46793ef54c99fe609b5983c5a658fb7b20032fa
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78666343"
---
# <a name="web-server-implementations-in-aspnet-core"></a>ASP.NET Core'daki web sunucusu uygulamaları

Tarafından [Tom Dykstra](https://github.com/tdykstra), [Steve Smith](https://ardalis.com/), Stephen [Halter](https://twitter.com/halter73), ve [Chris Ross](https://github.com/Tratcher)

Core ASP.NET uygulaması, işlem içi http sunucu uygulamasıyla çalışır. Sunucu uygulaması HTTP isteklerini dinler ve bunları bir . [request features](xref:fundamentals/request-features) <xref:Microsoft.AspNetCore.Http.HttpContext>

## <a name="kestrel"></a>Kestrel

Kerkenez, ASP.NET Core proje şablonları tarafından belirtilen varsayılan web sunucusudur.

Kerkenez kullanın:

* Tek başına bir kenar sunucu işleme isteklerini doğrudan internet de dahil olmak üzere bir ağdan alır.

  ![Kerkenez ters proxy sunucusu olmadan internet ile doğrudan iletişim kurar](kestrel/_static/kestrel-to-internet2.png)

* [Internet Information Services (IIS) ,](https://www.iis.net/) [Nginx](https://nginx.org)veya [Apache](https://httpd.apache.org/)gibi *ters proxy sunucusu*ile. Ters proxy sunucusu Internet'ten HTTP isteklerini alır ve bunları Kestrel'e ileter.

  ![Kerkenez, IIS, Nginx veya Apache gibi ters proxy sunucusu aracılığıyla Internet ile dolaylı olarak iletişim kurar](kestrel/_static/kestrel-to-internet.png)

Ters proxy&mdash;sunucusu&mdash;olan veya olmayan yapılandırmayı barındırma desteklenir.

Kestrel yapılandırma kılavuzu ve ters proxy yapılandırmasında Kerkenez'in <xref:fundamentals/servers/kestrel>ne zaman kullanılacağı hakkında bilgi için bkz.

::: moniker range=">= aspnetcore-2.2"

# <a name="windows"></a>[Windows](#tab/windows)

ASP.NET Core gemileri aşağıdakilerle birlikte:

* [Kerkenez sunucu](xref:fundamentals/servers/kestrel) varsayılan, çapraz platform HTTP sunucu uygulamasıdır.
* IIS HTTP Server, IIS için işlem aşamasında bir [sunucudur.](#hosting-models)
* [HTTP.sys sunucusu,](xref:fundamentals/servers/httpsys) [HTTP.sys çekirdek sürücüsü ve HTTP Server API'ye](/windows/desktop/Http/http-api-start-page)dayalı bir Windows'a özel HTTP sunucusudur.

[IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) veya [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)kullanırken uygulama aşağıdakileri çalıştırarak çalışır:

* IIS HTTP Server ile IIS alt işlemi [(süreç içi barındırma modeli)](#hosting-models)ile aynı süreçte. *Süreç içi* önerilen yapılandırmadır.
* [Kerkenez sunucusu](#kestrel)ile IIS alt işlemden [(işlem dışı barındırma modeli)](#hosting-models)ayrı bir işlemde.

[ASP.NET Çekirdek Modülü,](xref:host-and-deploy/aspnet-core-module) IIS ile işlem deki IIS HTTP Server veya Kestrel arasındaki yerel IIS isteklerini işleyen yerel bir IIS modülüdür. Daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module>.

## <a name="hosting-models"></a>Barındırma modelleri

ASP.NET Core uygulaması, süreç içi barındırmayı kullanarak IIS alt işlemiyle aynı işlemde çalışır. İstekler giden ağ trafiğini aynı makineye geri döndüren bir ağ arabirimi olan döngü bağdaştırıcısı üzerinden yakınlamadığından, işlem dışı barındırma işlemi barındırma üzerinde gelişmiş performans sağlar. IIS, Windows Process [Etkinleştirme Hizmeti (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was)ile işlem yönetimini işler.

İşlem dışı barındırma yı kullanarak, ASP.NET Core uygulamaları IIS alt işleminden ayrı bir işlemle çalışır ve modül süreç yönetimini işler. Modül, ilk istek geldiğinde ASP.NET Core uygulaması için süreci başlatır ve kapanDığında veya çökerse uygulamayı yeniden başlatır. Bu aslında [Windows Process Etkinleştirme Hizmeti (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was)tarafından yönetilen süreç içinde çalışan uygulamalarda görülen aynı davranıştır.

Daha fazla bilgi ve yapılandırma kılavuzu için aşağıdaki konulara bakın:

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>

# <a name="macos"></a>[macOS](#tab/macos)

ASP.NET Core, varsayılan olarak, çapraz platform HTTP sunucusu olan [Kestrel sunucusuile](xref:fundamentals/servers/kestrel)birlikte gemilere biner.

# <a name="linux"></a>[Linux](#tab/linux)

ASP.NET Core, varsayılan olarak, çapraz platform HTTP sunucusu olan [Kestrel sunucusuile](xref:fundamentals/servers/kestrel)birlikte gemilere biner.

---

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="windows"></a>[Windows](#tab/windows)

ASP.NET Core gemileri aşağıdakilerle birlikte:

* [Kerkenez sunucu](xref:fundamentals/servers/kestrel) varsayılan, çapraz platform HTTP sunucusudur.
* [HTTP.sys sunucusu,](xref:fundamentals/servers/httpsys) [HTTP.sys çekirdek sürücüsü ve HTTP Server API'ye](/windows/desktop/Http/http-api-start-page)dayalı bir Windows'a özel HTTP sunucusudur.

[IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) veya [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)kullanırken, uygulama [Kestrel sunucusu](#kestrel)ile IIS alt işleminden ayrı bir işlemde *(işlem dışı)* çalışır.

ASP.NET Core uygulamaları IIS alt işleminden ayrı bir işlemde çalıştığı için, modül işlem yönetimini işler. Modül, ilk istek geldiğinde ASP.NET Core uygulaması için süreci başlatır ve kapanDığında veya çökerse uygulamayı yeniden başlatır. Bu aslında [Windows Process Etkinleştirme Hizmeti (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was)tarafından yönetilen süreç içinde çalışan uygulamalarda görülen aynı davranıştır.

Aşağıdaki diyagram, IIS, ASP.NET Çekirdek Modülü ve işlem dışı barındırılan bir uygulama arasındaki ilişkiyi göstermektedir:

![ASP.NET Core Modülü](_static/ancm-outofprocess.png)

İstekler web'den çekirdek modu HTTP.sys sürücüsüne gelir. Sürücü, istekleri genellikle 80 (HTTP) veya 443 (HTTPS) olarak web sitesinin yapılandırılmış bağlantı noktasında IIS'ye yönlendirir. Modül, 80 veya 443 bağlantı noktası olmayan uygulama için rastgele bir bağlantı noktası üzerindeki istekleri Kestrel'e iletiyor.

Modül başlangıçta bir ortam değişkeni üzerinden bağlantı noktasını belirtir ve [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) sunucuyu dinleyecek şekilde `http://localhost:{port}`yapılandırır. Ek denetimler gerçekleştirilir ve modülden kaynaklanmıyor istekleri reddedilir. Modül HTTPS iletmesini desteklemez, bu nedenle iIS tarafından HTTPS üzerinden alınsa bile istekler HTTP üzerinden iletilir.

Kestrel modülden isteği aldıktan sonra, istek ASP.NET Core ara yazılım boru hattına itilir. Ara yazılım ardışık alanı isteği işler ve `HttpContext` uygulamanın mantığına örnek olarak aktarın. IIS Integration tarafından eklenen ara yazılım, isteği Kestrel'e iletmek için hesap vermek üzere şemayı, uzak IP'yi ve yol tabanını güncelleştirir. Uygulamanın yanıtı IIS'ye geri aktarılır ve bu da isteği başlatan HTTP istemcisine geri iter.

IIS ve ASP.NET Core Module yapılandırma kılavuzu için aşağıdaki konulara bakın:

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>

# <a name="macos"></a>[macOS](#tab/macos)

ASP.NET Core, varsayılan olarak, çapraz platform HTTP sunucusu olan [Kestrel sunucusuile](xref:fundamentals/servers/kestrel)birlikte gemilere biner.

# <a name="linux"></a>[Linux](#tab/linux)

ASP.NET Core, varsayılan olarak, çapraz platform HTTP sunucusu olan [Kestrel sunucusuile](xref:fundamentals/servers/kestrel)birlikte gemilere biner.

---

::: moniker-end

### <a name="nginx-with-kestrel"></a>Kerkenez ile Nginx

Kerkenez için ters proxy sunucusu olarak Linux'ta Nginx'in nasıl kullanılacağı hakkında bilgi için bkz. <xref:host-and-deploy/linux-nginx>

### <a name="apache-with-kestrel"></a>Kerkenez ile Apaçi

Kestrel için ters proxy sunucusu olarak Linux'ta Apache'nin nasıl kullanılacağı hakkında bilgi için bkz. <xref:host-and-deploy/linux-apache>

## <a name="httpsys"></a>HTTP.sys

ASP.NET Core uygulamaları Windows'da çalıştırılıyorsa, HTTP.sys Kestrel'e alternatiftir. Kerkenez genellikle en iyi performans için tavsiye edilir. HTTP.sys, uygulamanın Internet'e maruz kaldığı ve gerekli özelliklerin KEStrel tarafından desteklendiği senaryolarda kullanılabilir. Daha fazla bilgi için bkz. <xref:fundamentals/servers/httpsys>.

![HTTP.sys internet ile doğrudan iletişim kurar](httpsys/_static/httpsys-to-internet.png)

HTTP.sys, yalnızca dahili bir ağa maruz kalan uygulamalar için de kullanılabilir.

![HTTP.sys dahili ağ ile doğrudan iletişim kurar](httpsys/_static/httpsys-to-internal.png)

HTTP.sys yapılandırma kılavuzu <xref:fundamentals/servers/httpsys>için bkz.

## <a name="aspnet-core-server-infrastructure"></a>ASP.NET Core sunucu altyapısı

Yöntemde <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> kullanılabilir türü <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ServerFeatures> <xref:Microsoft.AspNetCore.Http.Features.IFeatureCollection>özelliğini ortaya çıkarır. `Startup.Configure` Kerkenez ve HTTP.sys yalnızca tek <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>bir özelliği ortaya çıkarır, ancak farklı sunucu uygulamaları ek işlevselliği ortaya çıkarabilir.

`IServerAddressesFeature`sunucu uygulamasının çalışma zamanında hangi bağlantı noktasına bağlı olduğunu bulmak için kullanılabilir.

## <a name="custom-servers"></a>Özel sunucular

Yerleşik sunucular uygulamanın gereksinimlerini karşılamazsa, özel bir sunucu uygulaması oluşturulabilir. [.NET (OWIN) için Açık Web Arabirimi kılavuzu,](xref:fundamentals/owin) [Nowin](https://github.com/Bobris/Nowin)tabanlı <xref:Microsoft.AspNetCore.Hosting.Server.IServer> bir uygulamanın nasıl yazılacağını gösterir. Yalnızca uygulamanın kullandığı özellik arabirimleri, en azından <xref:Microsoft.AspNetCore.Http.Features.IHttpRequestFeature> <xref:Microsoft.AspNetCore.Http.Features.IHttpResponseFeature> uygulanması nı gerektirir ve desteklenmesi gerekir.

## <a name="server-startup"></a>Sunucu başlatma

Sunucu, Tümleşik Geliştirme Ortamı (IDE) veya düzenleyici uygulamayı başlattığında başlatılır:

* [Visual Studio](https://visualstudio.microsoft.com) &ndash; Launch profilleri, uygulamayı ve sunucuyu [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)/[ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) veya konsolile başlatmak için kullanılabilir.
* [Görsel Stüdyo Kodu](https://code.visualstudio.com/) &ndash; Uygulama ve sunucu [Omnisharp](https://github.com/OmniSharp/omnisharp-vscode)tarafından başlatılır , Hangi CoreCLR hata ayıklayıcı etkinleştirir.
* [Mac](https://visualstudio.microsoft.com/vs/mac/) &ndash; için Visual Studio Uygulama ve sunucu [Mono Soft-Mode Debugger](https://www.mono-project.com/docs/advanced/runtime/docs/soft-debugger/)tarafından başlatılır.

Uygulamayı projenin klasöründeki bir komut isteminden başlatırken, [dotnet run](/dotnet/core/tools/dotnet-run) uygulamayı ve sunucuyu başlatır (yalnızca Kestrel ve HTTP.sys). Yapılandırma, (varsayılan) `-c|--configuration` veya `Debug` `Release`. olarak ayarlanan seçenek tarafından belirtilir.

*LaunchSettings.json* dosyası, Visual Studio gibi `dotnet run` araç yapısına yerleşik bir hata ayıklayıcıile veya hata ayıklayıcıyla bir uygulama başlatırken yapılandırma sağlar. Başlatma profilleri *launchSettings.json* dosyasında varsa, `--launch-profile {PROFILE NAME}` `dotnet run` komutla birlikte seçeneği kullanın veya Visual Studio'daki profili seçin. Daha fazla bilgi için [dotnet çalıştır](/dotnet/core/tools/dotnet-run) ve [.NET Core dağıtım ambalajına](/dotnet/core/build/distribution-packaging)bakın.

## <a name="http2-support"></a>HTTP/2 desteği

[HTTP/2](https://httpwg.org/specs/rfc7540.html) aşağıdaki dağıtım senaryolarında ASP.NET Core ile desteklenir:

::: moniker range=">= aspnetcore-2.2"

* [Kestrel](xref:fundamentals/servers/kestrel#http2-support)
  * İşletim sistemi
    * Windows Server 2016/Windows 10 veya sonrası&dagger;
    * OpenSSL 1.0.2 veya sonrası (örneğin, Ubuntu 16.04 veya sonrası) ile Linux
    * HTTP/2 gelecekte yayınlanacak bir sürümde macOS'ta desteklenecektir.
  * Hedef çerçeve: .NET Core 2.2 veya sonrası
* [HTTP.sys](xref:fundamentals/servers/httpsys#http2-support)
  * Windows Server 2016/Windows 10 veya sonrası
  * Hedef çerçeve: HTTP.sys dağıtımları için geçerli değildir.
* [IIS (süreç içinde)](xref:host-and-deploy/iis/index#http2-support)
  * Windows Server 2016/Windows 10 veya sonrası; IIS 10 veya sonrası
  * Hedef çerçeve: .NET Core 2.2 veya sonrası
* [IIS (işlem dışı)](xref:host-and-deploy/iis/index#http2-support)
  * Windows Server 2016/Windows 10 veya sonrası; IIS 10 veya sonrası
  * Genel kullanıma dönük kenar sunucusu bağlantıları HTTP/2'yi kullanır, ancak Kestrel'e ters proxy bağlantısı HTTP/1.1 kullanır.
  * Hedef çerçeve: IIS işlem dışı dağıtımlar için geçerli değildir.

&dagger;Kerkenez, Windows Server 2012 R2 ve Windows 8.1'de HTTP/2 için sınırlı bir desteğe sahiptir. Bu işletim sistemlerinde bulunan desteklenen TLS şifreleme paketlerinin listesi sınırlı olduğundan destek sınırlıdır. TLS bağlantılarını güvence altına almak için Eliptik Eğri Dijital İmza Algoritması (ECDSA) kullanılarak oluşturulan bir sertifika gerekebilir.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* [HTTP.sys](xref:fundamentals/servers/httpsys#http2-support)
  * Windows Server 2016/Windows 10 veya sonrası
  * Hedef çerçeve: HTTP.sys dağıtımları için geçerli değildir.
* [IIS (işlem dışı)](xref:host-and-deploy/iis/index#http2-support)
  * Windows Server 2016/Windows 10 veya sonrası; IIS 10 veya sonrası
  * Genel kullanıma dönük kenar sunucusu bağlantıları HTTP/2'yi kullanır, ancak Kestrel'e ters proxy bağlantısı HTTP/1.1 kullanır.
  * Hedef çerçeve: IIS işlem dışı dağıtımlar için geçerli değildir.

::: moniker-end

BIR HTTP/2 [bağlantısı, Uygulama Katmanı Protokol Anlaşması (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) ve TLS 1.2 veya daha sonra kullanmalıdır. Daha fazla bilgi için sunucu dağıtım senaryolarınız ile ilgili konulara bakın.

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:fundamentals/servers/kestrel>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/linux-nginx>
* <xref:host-and-deploy/linux-apache>
* <xref:fundamentals/servers/httpsys>
