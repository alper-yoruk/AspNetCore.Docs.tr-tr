---
title: ASP.NET Core Web sunucusu uygulamaları
author: rick-anderson
description: Kestrel için Web sunucularını bulun ve ASP.NET Core HTTP.sys. Sunucu seçme ve ters proxy sunucusu ne zaman kullanılacağı hakkında bilgi edinin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/07/2019
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
uid: fundamentals/servers/index
ms.openlocfilehash: 91d1373d764644820d1fac6064ee503e1ef4455c
ms.sourcegitcommit: 83524f739dd25fbfa95ee34e95342afb383b49fe
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99057349"
---
# <a name="web-server-implementations-in-aspnet-core"></a>ASP.NET Core Web sunucusu uygulamaları

[Tom Dykstra](https://github.com/tdykstra), [Steve Smith](https://ardalis.com/), [Stephen halter](https://twitter.com/halter73)ve [Chris](https://github.com/Tratcher) 'e göre

ASP.NET Core bir uygulama, işlem içi HTTP sunucu uygulamasıyla çalışır. Sunucu uygulaması, HTTP isteklerini dinler ve bunları uygulamaya oluşturulan [istek özellikleri](xref:fundamentals/request-features) kümesi olarak uygulamaya sunar <xref:Microsoft.AspNetCore.Http.HttpContext> .

::: moniker range=">= aspnetcore-2.2"

# <a name="windows"></a>[Windows](#tab/windows)

ASP.NET Core aşağıdakiler ile birlikte gelir:

* [Kestrel sunucusu](xref:fundamentals/servers/kestrel) varsayılan, platformlar arası http sunucu uygulamasıdır. Kestrel, en iyi performans ve bellek kullanımını sağlar, ancak bağlantı noktası Paylaşımı gibi bazı gelişmiş özelliklere sahip değildir `Http.Sys` .
* IIS HTTP sunucusu, IIS için bir [işlem içi sunucusudur](#hosting-models) .
* [HTTP.sys sunucusu](xref:fundamentals/servers/httpsys) , [HTTP.sys çekırdek sürücüsünü ve http sunucusu API](/windows/desktop/Http/http-api-start-page)'sini temel alan bir yalnızca Windows HTTP sunucusudur.

[IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) veya [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)kullanırken, uygulama şu şekilde çalışır:

* IIS çalışan işlemiyle aynı işlemde ( [işlem içi barındırma modeli](#hosting-models)) IIS HTTP sunucusu ile. *Işlem içi* önerilen yapılandırmadır.
* Bir işlemde, IIS çalışan işleminden ( [işlem dışı barındırma modeli](#hosting-models)) [Kestrel sunucusuyla](#kestrel)ayırın.

[ASP.NET Core modülü](xref:host-and-deploy/aspnet-core-module) , IIS ile Işlem ıçı IIS HTTP sunucusu ya da Kestrel arasında yerel IIS isteklerini işleyen yerel bir IIS modülüdür. Daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module>.

## <a name="hosting-models"></a>Barındırma modelleri

ASP.NET Core bir uygulama, işlem içi barındırma kullanarak IIS çalışan işlemiyle aynı işlemde çalışır. İşlem içi barındırma, istek dışı barındırmak için gelişmiş performans sağlar çünkü istekler, giden ağ trafiği ile aynı makineye geri döndürülen bir ağ arabirimidir. IIS, [Windows Işlem etkinleştirme hizmeti (was)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was)ile işlem yönetimini işler.

İşlem dışı barındırma kullanma, ASP.NET Core uygulamalar IIS çalışan işleminden ayrı bir işlemde çalışır ve modül işlem yönetimini işler. Modül, ilk istek ulaştığında ASP.NET Core uygulama için işlemi başlatır ve kapanırsa veya kilitlenirse uygulamayı yeniden başlatır. Bu aslında, [Windows Işlem etkinleştirme hizmeti (was)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was)tarafından yönetilen işlem içi uygulamalarla birlikte görülen davranışdır.

Daha fazla bilgi ve yapılandırma kılavuzu için aşağıdaki konulara bakın:

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>

# <a name="macos"></a>[macOS](#tab/macos)

ASP.NET Core, varsayılan, platformlar arası HTTP sunucusu olan [Kestrel Server](xref:fundamentals/servers/kestrel)ile birlikte gönderilir.

# <a name="linux"></a>[Linux](#tab/linux)

ASP.NET Core, varsayılan, platformlar arası HTTP sunucusu olan [Kestrel Server](xref:fundamentals/servers/kestrel)ile birlikte gönderilir.

---

::: moniker-end

## <a name="kestrel"></a>Kestrel

 [Kestrel sunucusu](xref:fundamentals/servers/kestrel) varsayılan, platformlar arası http sunucu uygulamasıdır. Kestrel, en iyi performans ve bellek kullanımını sağlar, ancak bağlantı noktası Paylaşımı gibi bazı gelişmiş özelliklere sahip değildir `Http.Sys` .
 
Kestrel kullanın:

* Kendi başına bir uç sunucu olarak, Internet dahil olmak üzere istekleri doğrudan bir ağdan işleme.

  ![Kestrel, ters proxy sunucusu olmadan doğrudan Internet ile iletişim kurar](kestrel/_static/kestrel-to-internet2.png)

* [Internet Information Services (IIS)](https://www.iis.net/), [NGINX](https://nginx.org)veya [Apache](https://httpd.apache.org/)gibi bir *ters ara sunucu* ile. Ters proxy sunucusu, Internet 'ten gelen HTTP isteklerini alır ve Kestrel 'e iletir.

  ![Kestrel, IIS, NGINX veya Apache gibi bir ters ara sunucu üzerinden Internet ile dolaylı olarak iletişim kurar](kestrel/_static/kestrel-to-internet.png)

&mdash;Ters ara sunucu sunucusuyla ya da olmadan barındırma yapılandırması &mdash; desteklenir.

Kestrel yapılandırma kılavuzu ve bir ters proxy yapılandırmasında Kestrel 'in ne zaman kullanılacağı hakkında bilgi için, bkz <xref:fundamentals/servers/kestrel> ..

::: moniker range="< aspnetcore-2.2"

# <a name="windows"></a>[Windows](#tab/windows)

ASP.NET Core aşağıdakiler ile birlikte gelir:

* [Kestrel sunucusu](xref:fundamentals/servers/kestrel) , platformlar arası varsayılan HTTP sunucusudur.
* [HTTP.sys sunucusu](xref:fundamentals/servers/httpsys) , [HTTP.sys çekırdek sürücüsünü ve http sunucusu API](/windows/desktop/Http/http-api-start-page)'sini temel alan bir yalnızca Windows HTTP sunucusudur.

[IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) veya [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)kullanırken, uygulama IIS çalışan işleminden (*işlem dışı*) [Kestrel sunucusu](#kestrel)ile ayrı bir işlemde çalışır.

ASP.NET Core uygulamalar IIS çalışan işleminden ayrı bir işlemde çalıştığından, modül işlem yönetimini işler. Modül, ilk istek ulaştığında ASP.NET Core uygulama için işlemi başlatır ve kapanırsa veya kilitlenirse uygulamayı yeniden başlatır. Bu aslında, [Windows Işlem etkinleştirme hizmeti (was)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was)tarafından yönetilen işlem içi uygulamalarla birlikte görülen davranışdır.

Aşağıdaki diyagramda IIS, ASP.NET Core modülü ve işlem dışı barındırılan bir uygulama arasındaki ilişki gösterilmektedir:

![ASP.NET Core Modülü](_static/ancm-outofprocess.png)

İstekler Web 'den çekirdek modu HTTP.sys sürücüsüne ulaşır. Sürücü, istekleri Web sitesinin yapılandırılmış bağlantı noktasında IIS 'ye yönlendirir, genellikle 80 (HTTP) veya 443 (HTTPS). Modül, 80 veya 443 numaralı bağlantı noktası olmayan uygulama için rastgele bir bağlantı noktasında istekleri Kestrel 'e iletir.

Modül, başlangıç sırasında bir ortam değişkeni aracılığıyla bağlantı noktasını belirtir ve [IIS tümleştirme ara yazılımı](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) sunucuyu dinleyecek şekilde yapılandırır `http://localhost:{port}` . Ek denetimler gerçekleştirilir ve modülünden kaynaklanmayan istekler reddedilir. Modül HTTPS iletmeyi desteklemez, bu nedenle istekler HTTPS üzerinden IIS tarafından alınsa bile HTTP üzerinden iletilir.

Kestrel, isteği modülden başlattıktan sonra, istek ASP.NET Core ara yazılım ardışık düzenine gönderilir. Ara yazılım ardışık düzeni isteği işler ve `HttpContext` uygulamanın mantığına bir örnek olarak geçirir. IIS tümleştirmesi tarafından eklenen ara yazılım, isteği Kestrel iletmek için düzen, uzak IP ve pathbase 'i hesaba göre güncelleştirir. Uygulamanın yanıtı IIS 'e geri geçirilir ve bu, isteği başlatan HTTP istemcisine geri gönderilir.

IIS ve ASP.NET Core modülü yapılandırma kılavuzu için aşağıdaki konulara bakın:

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>

# <a name="macos"></a>[macOS](#tab/macos)

ASP.NET Core, varsayılan, platformlar arası HTTP sunucusu olan [Kestrel Server](xref:fundamentals/servers/kestrel)ile birlikte gönderilir.

# <a name="linux"></a>[Linux](#tab/linux)

ASP.NET Core, varsayılan, platformlar arası HTTP sunucusu olan [Kestrel Server](xref:fundamentals/servers/kestrel)ile birlikte gönderilir.

---

::: moniker-end

### <a name="nginx-with-kestrel"></a>Kestrel ile NGINX

Linux üzerinde NGINX 'i Kestrel için ters proxy sunucusu olarak kullanma hakkında daha fazla bilgi için bkz <xref:host-and-deploy/linux-nginx> ..

### <a name="apache-with-kestrel"></a>Kestrel ile Apache

Linux üzerinde Apache 'yi Kestrel için ters proxy sunucusu olarak kullanma hakkında daha fazla bilgi için bkz <xref:host-and-deploy/linux-apache> ..

## <a name="httpsys"></a>HTTP.sys

ASP.NET Core uygulamalar Windows üzerinde çalışıyorsa HTTP.sys, Kestrel için bir alternatiftir. Uygulama, Kestrel içinde kullanılamayan özellikler gerektirmediği takdirde, Kestrel üzerinde HTTP.sys önerilir. Daha fazla bilgi için bkz. <xref:fundamentals/servers/httpsys>.

![HTTP.sys doğrudan Internet ile iletişim kurar](httpsys/_static/httpsys-to-internet.png)

HTTP.sys, yalnızca bir iç ağa açık olan uygulamalar için de kullanılabilir.

![HTTP.sys doğrudan iç ağla iletişim kurar](httpsys/_static/httpsys-to-internal.png)

HTTP.sys yapılandırma kılavuzu için bkz <xref:fundamentals/servers/httpsys> ..

## <a name="aspnet-core-server-infrastructure"></a>ASP.NET Core Server altyapısı

<xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> `Startup.Configure` Yönteminde bulunan <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ServerFeatures> özelliği türünün özelliğini kullanıma sunar <xref:Microsoft.AspNetCore.Http.Features.IFeatureCollection> . Kestrel ve HTTP.sys her biri yalnızca tek bir özellik sunar, <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature> ancak farklı sunucu uygulamaları ek işlevsellik sergilede gösterebilir.

`IServerAddressesFeature` sunucu uygulamasının çalışma zamanında hangi bağlantı noktasını bağladığına ilişkin bilgi edinmek için kullanılabilir.

## <a name="custom-servers"></a>Özel sunucular

Yerleşik sunucular uygulamanın gereksinimlerini karşılamıyorsa, özel bir sunucu uygulaması oluşturulabilir. [.Net Için açık Web arabirimi (OWıN) Kılavuzu](xref:fundamentals/owin) , [nowin](https://github.com/Bobris/Nowin)tabanlı bir uygulamanın nasıl yazılacağını gösterir <xref:Microsoft.AspNetCore.Hosting.Server.IServer> . Yalnızca uygulamanın kullandığı Özellik arabirimleri, en azından <xref:Microsoft.AspNetCore.Http.Features.IHttpRequestFeature> ve <xref:Microsoft.AspNetCore.Http.Features.IHttpResponseFeature> desteklenmesi gereken uygulama gerektirir.

## <a name="server-startup"></a>Sunucu başlatma

Tümleşik geliştirme ortamı (IDE) veya düzenleyici uygulamayı başlattığında sunucu başlatılır:

* [Visual Studio](https://visualstudio.microsoft.com): başlatma profilleri, uygulamayı ve sunucuyu [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) / [ASP.NET Core modülü](xref:host-and-deploy/aspnet-core-module) ya da konsolundan başlatmak için kullanılabilir.
* [Visual Studio Code](https://code.visualstudio.com/): uygulama ve sunucu [Omnisharp](https://github.com/OmniSharp/omnisharp-vscode)tarafından başlatılır ve bu, CoreCLR hata ayıklayıcısını etkinleştirir.
* [Mac için Visual Studio](https://visualstudio.microsoft.com/vs/mac/): uygulama ve sunucu [mono Soft-Mode hata ayıklayıcısı](https://www.mono-project.com/docs/advanced/runtime/docs/soft-debugger/)tarafından başlatılır.

Uygulamanın, projenin klasöründeki bir komut isteminden başlatılması sırasında [DotNet Run](/dotnet/core/tools/dotnet-run) uygulamayı ve sunucuyu başlatır (yalnızca Kestrel ve HTTP.sys). Yapılandırma `-c|--configuration` seçeneğiyle belirtilir, `Debug` (varsayılan) veya olarak ayarlanır `Release` .

Dosyadaki bir *launchSettings.js* `dotnet run` , Visual Studio gibi araç ile yerleşik bir hata ayıklayıcı ile veya bir uygulama başlatırken yapılandırma sağlar. Bir *launchSettings.jsdosya üzerinde* başlatma profilleri varsa, `--launch-profile {PROFILE NAME}` komutuyla seçeneğini kullanın `dotnet run` veya Visual Studio 'da profili seçin. Daha fazla bilgi için bkz. [DotNet Run](/dotnet/core/tools/dotnet-run) ve [.NET Core Distribution paketleme](/dotnet/core/build/distribution-packaging).

## <a name="http2-support"></a>HTTP/2 desteği

[Http/2](https://httpwg.org/specs/rfc7540.html) aşağıdaki dağıtım senaryolarında ASP.NET Core desteklenir:

::: moniker range=">= aspnetcore-5.0"

* [Kestrel](xref:fundamentals/servers/kestrel/http2)
  * İşletim sistemi
    * Windows Server 2016/Windows 10 veya üzeri&dagger;
    * OpenSSL 1.0.2 veya üzerini içeren Linux (örneğin, Ubuntu 16,04 veya üzeri)
    * HTTP/2, gelecek sürümlerde macOS 'ta desteklenecektir.
  * Hedef Framework: .NET Core 2,2 veya üzeri
* [HTTP.sys](xref:fundamentals/servers/httpsys#http2-support)
  * Windows Server 2016/Windows 10 veya üzeri
  * Hedef Framework: HTTP.sys dağıtımlar için geçerli değildir.
* [IIS (işlem içi)](xref:host-and-deploy/iis/index#http2-support)
  * Windows Server 2016/Windows 10 veya üzeri; IIS 10 veya üzeri
  * Hedef Framework: .NET Core 2,2 veya üzeri
* [IIS (işlem dışı)](xref:host-and-deploy/iis/index#http2-support)
  * Windows Server 2016/Windows 10 veya üzeri; IIS 10 veya üzeri
  * Herkese açık uç sunucu bağlantıları HTTP/2 kullanır, ancak Kestrel ile ters proxy bağlantısı HTTP/1.1 kullanır.
  * Hedef Framework: IIS işlem dışı dağıtımlar için geçerli değildir.

&dagger;Kestrel, Windows Server 2012 R2 ve Windows 8.1 'de HTTP/2 için sınırlı destek içerir. Bu işletim sistemlerinde kullanılabilir olan desteklenen TLS şifre paketlerinin listesi sınırlı olduğundan destek sınırlıdır. TLS bağlantılarının güvenliğini sağlamak için Eliptik Eğri dijital Imza algoritması (ECDSA) kullanılarak oluşturulan bir sertifika gerekli olabilir.

::: moniker-end

::: moniker range=">= aspnetcore-2.2 < aspnetcore-5.0"

* [Kestrel](xref:fundamentals/servers/kestrel#http2-support)
  * İşletim sistemi
    * Windows Server 2016/Windows 10 veya üzeri&dagger;
    * OpenSSL 1.0.2 veya üzerini içeren Linux (örneğin, Ubuntu 16,04 veya üzeri)
    * HTTP/2, gelecek sürümlerde macOS 'ta desteklenecektir.
  * Hedef Framework: .NET Core 2,2 veya üzeri
* [HTTP.sys](xref:fundamentals/servers/httpsys#http2-support)
  * Windows Server 2016/Windows 10 veya üzeri
  * Hedef Framework: HTTP.sys dağıtımlar için geçerli değildir.
* [IIS (işlem içi)](xref:host-and-deploy/iis/index#http2-support)
  * Windows Server 2016/Windows 10 veya üzeri; IIS 10 veya üzeri
  * Hedef Framework: .NET Core 2,2 veya üzeri
* [IIS (işlem dışı)](xref:host-and-deploy/iis/index#http2-support)
  * Windows Server 2016/Windows 10 veya üzeri; IIS 10 veya üzeri
  * Herkese açık uç sunucu bağlantıları HTTP/2 kullanır, ancak Kestrel ile ters proxy bağlantısı HTTP/1.1 kullanır.
  * Hedef Framework: IIS işlem dışı dağıtımlar için geçerli değildir.

&dagger;Kestrel, Windows Server 2012 R2 ve Windows 8.1 'de HTTP/2 için sınırlı destek içerir. Bu işletim sistemlerinde kullanılabilir olan desteklenen TLS şifre paketlerinin listesi sınırlı olduğundan destek sınırlıdır. TLS bağlantılarının güvenliğini sağlamak için Eliptik Eğri dijital Imza algoritması (ECDSA) kullanılarak oluşturulan bir sertifika gerekli olabilir.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* [HTTP.sys](xref:fundamentals/servers/httpsys#http2-support)
  * Windows Server 2016/Windows 10 veya üzeri
  * Hedef Framework: HTTP.sys dağıtımlar için geçerli değildir.
* [IIS (işlem dışı)](xref:host-and-deploy/iis/index#http2-support)
  * Windows Server 2016/Windows 10 veya üzeri; IIS 10 veya üzeri
  * Herkese açık uç sunucu bağlantıları HTTP/2 kullanır, ancak Kestrel ile ters proxy bağlantısı HTTP/1.1 kullanır.
  * Hedef Framework: IIS işlem dışı dağıtımlar için geçerli değildir.

::: moniker-end

Bir HTTP/2 bağlantısı, [uygulama katmanı protokol anlaşması (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) ve TLS 1,2 veya üstünü kullanmalıdır. Daha fazla bilgi için, sunucu dağıtım senaryolarınıza ait konulara bakın.

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:fundamentals/servers/kestrel>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/linux-nginx>
* <xref:host-and-deploy/linux-apache>
* <xref:fundamentals/servers/httpsys>
