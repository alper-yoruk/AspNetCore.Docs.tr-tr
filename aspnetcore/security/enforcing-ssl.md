---
title: ASP.NET Core 'de HTTPS 'yi zorla
author: rick-anderson
description: ASP.NET Core Web uygulamasında HTTPS/TLS isteme hakkında bilgi edinin.
ms.author: riande
ms.custom: mvc
ms.date: 12/06/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/enforcing-ssl
ms.openlocfilehash: 5dcdf50ff9f750e4966ed3bdf24a71b9f433240a
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88019007"
---
# <a name="enforce-https-in-aspnet-core"></a>ASP.NET Core 'de HTTPS 'yi zorla

Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)

Bu belgede nasıl yapılacağı gösterilmektedir:

* Tüm istekler için HTTPS gerektir.
* Tüm HTTP isteklerini HTTPS 'ye yeniden yönlendirin.

Hiçbir API, istemcinin ilk istekte hassas veri göndermesini engelleyebilir.

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> ## <a name="api-projects"></a>API projeleri
>
> Hassas bilgileri alan Web API 'Lerinde [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) **kullanmayın.** `RequireHttpsAttribute`tarayıcıları HTTP 'den HTTPS 'ye yönlendirmek için HTTP durum kodlarını kullanır. API istemcileri HTTP 'den HTTPS 'ye yeniden yönlendirmeyi anlamayabilir veya buna uymayabilir. Bu tür istemciler, HTTP üzerinden bilgi gönderebilir. Web API 'Leri şunlardan biri olmalıdır:
>
> * HTTP üzerinde dinleme yok.
> * Durum kodu 400 olan bağlantıyı kapatın (Hatalı Istek) ve isteğe bağlı değildir.
>
> ## <a name="hsts-and-api-projects"></a>HSTS ve API projeleri
>
> Varsayılan API projeleri, genellikle yalnızca bir tarayıcı yönergesi olduğu için [HSTS](#hsts) içermez. Telefon veya masaüstü uygulamaları gibi diğer çağıranlar, yönergeye **uymayın.** Tarayıcılar içinde bile, HTTP üzerinden bir API 'ye yapılan tek bir kimlik doğrulamalı çağrı, güvenli olmayan ağlarda risk içerir. Güvenli yaklaşım, API projelerini yalnızca HTTPS dinlemesi ve HTTPS üzerinden yanıt verecek şekilde yapılandırmaktır.

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

> [!WARNING]
> ## <a name="api-projects"></a>API projeleri
>
> Hassas bilgileri alan Web API 'Lerinde [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) **kullanmayın.** `RequireHttpsAttribute`tarayıcıları HTTP 'den HTTPS 'ye yönlendirmek için HTTP durum kodlarını kullanır. API istemcileri HTTP 'den HTTPS 'ye yeniden yönlendirmeyi anlamayabilir veya buna uymayabilir. Bu tür istemciler, HTTP üzerinden bilgi gönderebilir. Web API 'Leri şunlardan biri olmalıdır:
>
> * HTTP üzerinde dinleme yok.
> * Durum kodu 400 olan bağlantıyı kapatın (Hatalı Istek) ve isteğe bağlı değildir.

::: moniker-end

## <a name="require-https"></a>HTTPS gerektir

Üretim ASP.NET Core Web uygulamalarının kullanmasını öneririz:

* <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>Http ISTEKLERINI https 'ye yeniden yönlendirmek IÇIN https yeniden yönlendirme ara yazılımı ().
* İstemcilere HTTP katı aktarım güvenliği Protokolü (HSTS) üst bilgileri göndermek için HSTS ara yazılımı ([Usehsts](#http-strict-transport-security-protocol-hsts)).

> [!NOTE]
> Ters Proxy yapılandırmasında dağıtılan uygulamalar, proxy 'nin bağlantı güvenliğini (HTTPS) işlemesini sağlar. Ara sunucu HTTPS yeniden yönlendirmeyi de işleiyorsa, HTTPS yeniden yönlendirme ara yazılımı kullanmanız gerekmez. Proxy sunucusu Ayrıca, HSTS üst bilgilerini (örneğin, [ııs 10,0 (1709) veya sonraki sürümlerde yerel HSTS desteği](/iis/get-started/whats-new-in-iis-10-version-1709/iis-10-version-1709-hsts#iis-100-version-1709-native-hsts-support)) yazmayı de işişişişsa, uygulama Için HSTS ara yazılımı gerekli değildir. Daha fazla bilgi için bkz. [Proje oluşturma SıRASıNDA https/HSTS 'nin geri çevirme](#opt-out-of-httpshsts-on-project-creation).

### <a name="usehttpsredirection"></a>UseHttpsRedirection

Aşağıdaki kod, `UseHttpsRedirection` `Startup` sınıfında çağırır:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](enforcing-ssl/sample-snapshot/3.x/Startup.cs?name=snippet1&highlight=14)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](enforcing-ssl/sample-snapshot/2.x/Startup.cs?name=snippet1&highlight=13)]

::: moniker-end

Vurgulanan önceki kod:

* Varsayılan [Httpsredirectionoptions. RedirectStatusCode](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.redirectstatuscode) ([Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect)) kullanır.
* , [HttpsRedirectionOptions.HttpsPort](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.httpsport) `ASPNETCORE_HTTPS_PORT` Ortam değişkeni veya [ıveraddressesözelliği](/dotnet/api/microsoft.aspnetcore.hosting.server.features.iserveraddressesfeature)tarafından geçersiz kılınmadıkça varsayılan Httpsredirectionoptions. HttpsPort (null) kullanır.

Kalıcı yeniden yönlendirmeler yerine geçici yeniden yönlendirmeler kullanmanızı öneririz. Bağlantıyı önbelleğe alma, geliştirme ortamlarında kararsız davranışa neden olabilir. Uygulama geliştirme dışı bir ortamda olduğunda kalıcı yeniden yönlendirme durum kodu göndermek isterseniz, [üretim içindeki kalıcı yeniden yönlendirmeleri yapılandırma](#configure-permanent-redirects-in-production) bölümüne bakın. İstemcilere yalnızca güvenli kaynak isteği gönderilmesi gerektiğini bildirmek için [HSTS](#http-strict-transport-security-protocol-hsts) kullanılması önerilir (yalnızca üretimde).

### <a name="port-configuration"></a>Bağlantı noktası yapılandırması

Ara yazılımın güvenli olmayan bir isteği HTTPS 'ye yönlendirmesi için bir bağlantı noktası kullanılabilir olmalıdır. Kullanılabilir bağlantı noktası yoksa:

* HTTPS olarak yeniden yönlendirme gerçekleşmez.
* Ara yazılım "yeniden yönlendirme için HTTPS bağlantı noktası saptanamadı" uyarısını günlüğe kaydeder.

Aşağıdaki yaklaşımlardan herhangi birini kullanarak HTTPS bağlantı noktasını belirtin:

* [Httpsredirectionoptions. HttpsPort](#options)öğesini ayarlayın.

::: moniker range=">= aspnetcore-3.0"

* `https_port` [Konak ayarını](/aspnet/core/fundamentals/host/generic-host?view=aspnetcore-3.0#https_port)ayarlayın:

  * Konak yapılandırmasında.
  * `ASPNETCORE_HTTPS_PORT`Ortam değişkenini ayarlayarak.
  * *Üzerindeappsettings.js*en üst düzey bir giriş ekleyerek:

    [!code-json[](enforcing-ssl/sample-snapshot/3.x/appsettings.json?highlight=2)]

* [ASPNETCORE_URLS ortam değişkenini](/aspnet/core/fundamentals/host/generic-host?view=aspnetcore-3.0#urls)kullanarak güvenli düzene sahip bir bağlantı noktası belirtin. Ortam değişkeni sunucusunu yapılandırır. Ara yazılım, üzerinden HTTPS bağlantı noktasını dolaylı olarak bulur <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature> . Bu yaklaşım, ters proxy dağıtımlarında çalışmaz.

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

* `https_port` [Konak ayarını](xref:fundamentals/host/web-host#https-port)ayarlayın:

  * Konak yapılandırmasında.
  * `ASPNETCORE_HTTPS_PORT`Ortam değişkenini ayarlayarak.
  * *Üzerindeappsettings.js*en üst düzey bir giriş ekleyerek:

    [!code-json[](enforcing-ssl/sample-snapshot/2.x/appsettings.json?highlight=2)]

* [ASPNETCORE_URLS ortam değişkenini](xref:fundamentals/host/web-host#server-urls)kullanarak güvenli düzene sahip bir bağlantı noktası belirtin. Ortam değişkeni sunucusunu yapılandırır. Ara yazılım, üzerinden HTTPS bağlantı noktasını dolaylı olarak bulur <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature> . Bu yaklaşım, ters proxy dağıtımlarında çalışmaz.

::: moniker-end

* Geliştirme aşamasında, *üzerindelaunchsettings.js*bir https URL 'si ayarlayın. IIS Express kullanıldığında HTTPS 'yi etkinleştirin.

* [Kestrel](xref:fundamentals/servers/kestrel) server veya [HTTP.sys](xref:fundamentals/servers/httpsys) Server 'ın herkese açık BIR uç dağıtımı için https URL uç noktası yapılandırın. Uygulama tarafından yalnızca **BIR HTTPS bağlantı noktası** kullanılır. Ara yazılım, ile bağlantı noktasını bulur <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature> .

> [!NOTE]
> Bir uygulama ters proxy yapılandırmasında çalıştırıldığında, <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature> kullanılabilir değildir. Bu bölümde açıklanan diğer yaklaşımlardan birini kullanarak bağlantı noktasını ayarlayın.

### <a name="edge-deployments"></a>Edge dağıtımları 

Kestrel veya HTTP.sys, herkese açık bir uç sunucu olarak kullanıldığında, Kestrel veya HTTP.sys her ikisini de dinlemek için yapılandırılmış olmalıdır:

* İstemcinin yeniden yönlendirildiği güvenli bağlantı noktası (genellikle üretim ortamında 443 ve geliştirme sırasında 5001).
* Güvenli olmayan bağlantı noktası (genellikle üretim ortamında 80 ve geliştirme sırasında 5000).

Güvenli olmayan bir istek alabilmesi ve istemciyi güvenli bağlantı noktasına yeniden yönlendirebilmesi için güvensiz bağlantı noktasına istemci tarafından erişilebilir olması gerekir.

Daha fazla bilgi için bkz. [Kestrel Endpoint Configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) veya <xref:fundamentals/servers/httpsys> .

### <a name="deployment-scenarios"></a>Dağıtım senaryoları

İstemci ve sunucu arasındaki herhangi bir güvenlik duvarının trafik için iletişim bağlantı noktaları açık olması gerekir.

İstekler ters bir ara sunucu yapılandırmasında iletilirse, HTTPS yeniden yönlendirme ara yazılımı çağrılmadan önce [Iletilen üstbilgiler ara yazılımını](xref:host-and-deploy/proxy-load-balancer) kullanın. İletilen üstbilgiler ara yazılımı, `Request.Scheme` üst bilgisini kullanarak öğesini güncelleştirir `X-Forwarded-Proto` . Ara yazılım, yeniden yönlendirme URI 'Leri ve diğer güvenlik ilkelerinin doğru çalışmasına izin verir. Iletilen üstbilgiler ara yazılımı kullanılmazsa, arka uç uygulaması doğru düzeni alamayabilir ve yeniden yönlendirme döngüsünde sona ermeyebilir. Yaygın bir son kullanıcı hata iletisi, çok fazla yeniden yönlendirme meydana geldi.

Azure App Service dağıtım sırasında, [öğreticideki yönergeleri izleyin: mevcut bir özel SSL sertifikasını Azure 'A bağlama Web Apps](/azure/app-service/app-service-web-tutorial-custom-ssl).

### <a name="options"></a>Seçenekler

Aşağıdaki vurgulanan kod, ara yazılım seçeneklerini yapılandırmak için [Addhttpsredirection](/dotnet/api/microsoft.aspnetcore.builder.httpsredirectionservicesextensions.addhttpsredirection) öğesini çağırır:


::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](enforcing-ssl/sample-snapshot/3.x/Startup.cs?name=snippet2&highlight=14-18)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](enforcing-ssl/sample-snapshot/2.x/Startup.cs?name=snippet2&highlight=14-18)]

::: moniker-end


Çağırma `AddHttpsRedirection` yalnızca veya değerlerini değiştirmek için gereklidir `HttpsPort` `RedirectStatusCode` .

Vurgulanan önceki kod:

* [Httpsredirectionoptions. RedirectStatusCode](xref:Microsoft.AspNetCore.HttpsPolicy.HttpsRedirectionOptions.RedirectStatusCode*) <xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect> değerini, varsayılan değer olan olarak ayarlar. Atamaları için sınıfının alanlarını kullanın <xref:Microsoft.AspNetCore.Http.StatusCodes> `RedirectStatusCode` .
* HTTPS bağlantı noktasını 5001 olarak ayarlar.

#### <a name="configure-permanent-redirects-in-production"></a>Üretimde kalıcı yeniden yönlendirmeleri yapılandırma

Ara yazılım varsayılan olarak tüm yeniden yönlendirmelere bir [Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect) gönderir. Uygulama geliştirme dışı bir ortamda olduğunda kalıcı yeniden yönlendirme durum kodu göndermek isterseniz, geliştirme dışı bir ortam için bir koşullu denetim içindeki ara yazılım seçenekleri yapılandırmasını sarın.

::: moniker range=">= aspnetcore-3.0"

*Startup.cs*'de Hizmetleri yapılandırırken:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // IWebHostEnvironment (stored in _env) is injected into the Startup class.
    if (!_env.IsDevelopment())
    {
        services.AddHttpsRedirection(options =>
        {
            options.RedirectStatusCode = StatusCodes.Status308PermanentRedirect;
            options.HttpsPort = 443;
        });
    }
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

*Startup.cs*'de Hizmetleri yapılandırırken:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // IHostingEnvironment (stored in _env) is injected into the Startup class.
    if (!_env.IsDevelopment())
    {
        services.AddHttpsRedirection(options =>
        {
            options.RedirectStatusCode = StatusCodes.Status308PermanentRedirect;
            options.HttpsPort = 443;
        });
    }
}
```

::: moniker-end


## <a name="https-redirection-middleware-alternative-approach"></a>HTTPS yeniden yönlendirme ara yazılımı alternatif yaklaşımı

HTTPS yeniden yönlendirme ara yazılımı () kullanmanın bir alternatifi `UseHttpsRedirection` , URL yeniden yazma ara yazılımı ( `AddRedirectToHttps` ) kullanmaktır. `AddRedirectToHttps`, yeniden yönlendirme yürütüldüğünde durum kodunu ve bağlantı noktasını da ayarlayabilir. Daha fazla bilgi için bkz. [URL yeniden yazma ara yazılımı](xref:fundamentals/url-rewriting).

Ek yeniden yönlendirme kuralları gereksinimi olmadan HTTPS 'ye yönlendirilirken, bu konuda açıklanan HTTPS yeniden yönlendirme ara yazılımı () kullanmanızı öneririz `UseHttpsRedirection` .

<a name="hsts"></a>

## <a name="http-strict-transport-security-protocol-hsts"></a>HTTP katı aktarım güvenliği Protokolü (HSTS)

Her [OWASP](https://www.owasp.org/index.php/About_The_Open_Web_Application_Security_Project)için, [http katı aktarım güvenliği (HSTS)](https://cheatsheetseries.owasp.org/cheatsheets/HTTP_Strict_Transport_Security_Cheat_Sheet.html) , yanıt üst bilgisi kullanılarak bir Web uygulaması tarafından belirtilen bir katılım güvenlik geliştirmedir. [HSTS 'yi destekleyen bir tarayıcı](https://cheatsheetseries.owasp.org/cheatsheets/Transport_Layer_Protection_Cheat_Sheet.html#browser-support) bu üstbilgiyi alırsa:

* Tarayıcı, HTTP üzerinden iletişimin gönderilmesini önleyen etki alanı için yapılandırmayı depolar. Tarayıcı, HTTPS üzerinden tüm iletişimi zorlar.
* Tarayıcı, kullanıcının güvenilmeyen veya geçersiz sertifikalar kullanmasını engeller. Tarayıcı, kullanıcının bu sertifikaya geçici olarak güvenmesine izin veren istemleri devre dışı bırakır.

HSTS istemci tarafından zorlandığından bazı sınırlamalar vardır:

* İstemcinin HSTS 'yi desteklemesi gerekir.
* HSTS, HSTS ilkesini oluşturmak için en az bir başarılı HTTPS isteği gerektirir.
* Uygulamanın her HTTP isteğini denetlemesi ve HTTP isteğini yeniden yönlendirmesi veya reddetmesi gerekir.

ASP.NET Core 2,1 ve üzeri, genişletme yöntemiyle HSTS uygular `UseHsts` . Aşağıdaki kod, `UseHsts` uygulama [geliştirme modunda](xref:fundamentals/environments)olmadığında çağrı yapılır:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](enforcing-ssl/sample-snapshot/3.x/Startup.cs?name=snippet1&highlight=11)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](enforcing-ssl/sample-snapshot/2.x/Startup.cs?name=snippet1&highlight=10)]

::: moniker-end

`UseHsts`geliştirme aşamasında önerilmez çünkü HSTS ayarları tarayıcılar tarafından yüksek oranda önbelleklenebilir. Varsayılan olarak, `UseHsts` yerel geri döngü adresini dışlar.

İlk kez HTTPS 'yi uygulayan üretim ortamları için, yöntemlerden birini kullanarak ilk [HstsOptions. maxAge](xref:Microsoft.AspNetCore.HttpsPolicy.HstsOptions.MaxAge*) değerini küçük bir değere ayarlayın <xref:System.TimeSpan> . HTTPS altyapısını HTTP 'ye döndürmeniz gerekirse, değeri saat olarak bir tek güne kadar ayarlayın. HTTPS yapılandırmasının sürdürülebilirliği konusunda emin olduktan sonra, HSTS `max-age` değerini artırın; yaygın olarak kullanılan bir değer bir yıldır.

Aşağıdaki kod:


::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](enforcing-ssl/sample-snapshot/3.x/Startup.cs?name=snippet2&highlight=5-12)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](enforcing-ssl/sample-snapshot/2.x/Startup.cs?name=snippet2&highlight=5-12)]

::: moniker-end


* Üstbilginin preload parametresini ayarlar `Strict-Transport-Security` . Önyükleme, [RFC HSTS belirtiminin](https://tools.ietf.org/html/rfc6797)bir parçası değildir, ancak Web tarayıcıları tarafından Yeni yüklemede HSTS sitelerini önceden yüklemek için desteklenir. Daha fazla bilgi için bkz. [https://hstspreload.org/](https://hstspreload.org/).
* SSTS ilkesini konak alt etki alanlarını barındıracak şekilde uygulayan [ıncludealt etki alanını](https://tools.ietf.org/html/rfc6797#section-6.1.2)sağlar.
* `max-age`Üstbilginin parametresini 60 güne açık olarak ayarlar `Strict-Transport-Security` . Ayarlanmazsa, varsayılan olarak 30 gün olur. Daha fazla bilgi için bkz. [Maksimum yaş yönergesi](https://tools.ietf.org/html/rfc6797#section-6.1.1).
* `example.com`Dışlanacak konaklar listesine ekler.

`UseHsts`Aşağıdaki geri döngü Konakları dışlar:

* `localhost`: IPv4 geri döngü adresi.
* `127.0.0.1`: IPv4 geri döngü adresi.
* `[::1]`: IPv6 geri döngü adresi.

## <a name="opt-out-of-httpshsts-on-project-creation"></a>Proje oluşturulurken HTTPS/HSTS 'nin katılımı

Bağlantı güvenliğinin ağın herkese açık kenarından işlendiği bazı arka uç hizmet senaryolarında, her düğümde bağlantı güvenliğini yapılandırmak gerekli değildir. Visual Studio 'da veya [DotNet yeni](/dotnet/core/tools/dotnet-new) komutundan oluşturulan Web Apps, [https yeniden yönlendirmeyi](#require-https) ve [HSTS](#http-strict-transport-security-protocol-hsts)'yi etkinleştirir. Bu senaryoları gerektirmeyen dağıtımlar için, uygulama şablondan oluşturulduğunda HTTPS/HSTS 'nin devre dışı bırakabilirsiniz.

HTTPS/HSTS 'yi devre dışı bırakmak için:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio) 

**Https Için Yapılandır** onay kutusunun işaretini kaldırın.

::: moniker range=">= aspnetcore-3.0"

![HTTPS için Yapılandır onay kutusunun seçili olduğu yeni ASP.NET Core Web uygulaması iletişim kutusu.](enforcing-ssl/_static/out-vs2019.png)

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

![HTTPS için Yapılandır onay kutusunun seçili olduğu yeni ASP.NET Core Web uygulaması iletişim kutusu.](enforcing-ssl/_static/out.png)

::: moniker-end


# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli) 

Seçeneğini kullanın `--no-https` . Örneğin:

```dotnetcli
dotnet new webapp --no-https
```

---

<a name="trust"></a>

## <a name="trust-the-aspnet-core-https-development-certificate-on-windows-and-macos"></a>Windows ve macOS 'ta ASP.NET Core HTTPS geliştirme sertifikasına güvenin

.NET Core SDK bir HTTPS geliştirme sertifikası içerir. Sertifika, ilk çalıştırma deneyiminin bir parçası olarak yüklenir. Örneğin, `dotnet --info` aşağıdaki çıkışın bir varyasyonunu üretir:

```
ASP.NET Core
------------
Successfully installed the ASP.NET Core HTTPS Development Certificate.
To trust the certificate run 'dotnet dev-certs https --trust' (Windows and macOS only).
For establishing trust on other platforms refer to the platform specific documentation.
For more information on configuring HTTPS see https://go.microsoft.com/fwlink/?linkid=848054.
```

.NET Core SDK yükleme ASP.NET Core HTTPS geliştirme sertifikasını Yerel Kullanıcı sertifika deposuna yüklenir. Sertifika yüklendi, ancak güvenilir değil. Sertifikaya güvenmek için, DotNet aracını çalıştırmak için tek seferlik bir adım gerçekleştirin `dev-certs` :

```dotnetcli
dotnet dev-certs https --trust
```

Aşağıdaki komut araç üzerinde yardım sağlar `dev-certs` :

```dotnetcli
dotnet dev-certs https --help
```

## <a name="how-to-set-up-a-developer-certificate-for-docker"></a>Docker için Geliştirici Sertifikası ayarlama

[Bu GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/6199)bakın.

<a name="wsl"></a>

## <a name="trust-https-certificate-from-windows-subsystem-for-linux"></a>Linux için Windows alt sistemi 'nden HTTPS sertifikasına güven

Linux için Windows alt sistemi (WSL), HTTPS otomatik olarak imzalanan bir sertifika oluşturur. Windows sertifika deposunu, WSL sertifikasına güvenmek üzere yapılandırmak için:

* WSL tarafından oluşturulan sertifikayı dışarı aktarmak için aşağıdaki komutu çalıştırın:

  ```
  dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p <cryptic-password>
  ```
* Bir WSL penceresinde, aşağıdaki komutu çalıştırın:

  ```
    ASPNETCORE_Kestrel__Certificates__Default__Password="<cryptic-password>" 
    ASPNETCORE_Kestrel__Certificates__Default__Path=/mnt/c/Users/user-name/.aspnet/https/aspnetapp.pfx
    dotnet watch run
  ```

  Yukarıdaki komut, ortam değişkenlerini, Linux 'un Windows güvenilen sertifikasını kullanmasını sağlayacak şekilde ayarlar.

## <a name="troubleshoot-certificate-problems"></a>Sertifika sorunlarını giderme

Bu bölümde, ASP.NET Core HTTPS geliştirme sertifikası [yüklendiğinde ve güveniliyorsa](#trust), ancak hala sertifikaya güvenilmediğini belirten tarayıcı uyarıları varsa yardım sağlanmaktadır. ASP.NET Core HTTPS geliştirme sertifikası, [Kestrel](xref:fundamentals/servers/kestrel)tarafından kullanılır.

### <a name="all-platforms---certificate-not-trusted"></a>Tüm platformlar-Sertifikaya güvenilmiyor

Aşağıdaki komutları çalıştırın:

```dotnetcli
dotnet dev-certs https --clean
dotnet dev-certs https --trust
```

Açık olan tüm tarayıcı örneklerini kapatın. Uygulamaya yeni bir tarayıcı penceresi açın. Sertifika güveni tarayıcılar tarafından önbelleğe alınır.

Yukarıdaki komutlar çoğu tarayıcı güveni sorununu çözüyor. Tarayıcı yine de sertifikaya güvenmiyor ise, aşağıdaki platforma özgü önerileri izleyin.

### <a name="docker---certificate-not-trusted"></a>Docker-Sertifikaya güvenilmiyor

* *C:\Users \{ Kullanıcı} \AppData\Roaming\ASP.NET\Https* klasörünü silin.
* Çözümü temizleyin. *Bin* ve *obj* klasörlerini silin.
* Geliştirme aracını yeniden başlatın. Örneğin, Visual Studio, Visual Studio Code veya Mac için Visual Studio.

### <a name="windows---certificate-not-trusted"></a>Windows-Sertifikaya güvenilmiyor

* Sertifika deposundaki sertifikaları kontrol edin. Hem hem de içinde `localhost` `ASP.NET Core HTTPS development certificate` kolay ada sahip bir sertifika olmalıdır `Current User > Personal > Certificates``Current User > Trusted root certification authorities > Certificates`
* Yalnızca kişisel ve güvenilen kök sertifika yetkililerinden bulunan tüm sertifikaları kaldırın. IIS Express localhost **sertifikasını kaldırmayın.**
* Aşağıdaki komutları çalıştırın:

```dotnetcli
dotnet dev-certs https --clean
dotnet dev-certs https --trust
```

Açık olan tüm tarayıcı örneklerini kapatın. Uygulamaya yeni bir tarayıcı penceresi açın.

### <a name="os-x---certificate-not-trusted"></a>OS X-Sertifikaya güvenilmiyor

* Anahtarlık erişimini açın.
* Sistem anahtarlığı ' nı seçin.
* Localhost sertifikası olup olmadığını denetleyin.
* `+`Tüm kullanıcılar için güvenilir olduğunu göstermek için simgenin üzerinde bir simge içerip içermediğinden emin olun.
* Sertifikayı sistem anahtarlığınızdan kaldırın.
* Aşağıdaki komutları çalıştırın:

```dotnetcli
dotnet dev-certs https --clean
dotnet dev-certs https --trust
```

Açık olan tüm tarayıcı örneklerini kapatın. Uygulamaya yeni bir tarayıcı penceresi açın.

Visual Studio 'da sertifika sorunlarını gidermek için [IIS Express (DotNet/AspNetCore #16892) kullanarak https hatası](https://github.com/dotnet/AspNetCore/issues/16892) makalesine bakın.

### <a name="iis-express-ssl-certificate-used-with-visual-studio"></a>Visual Studio ile kullanılan SSL sertifikası IIS Express

IIS Express sertifikayla ilgili sorunları gidermek için Visual Studio yükleyicisinden **Onar** ' ı seçin. Daha fazla bilgi için [Bu GitHub sorununa](https://github.com/dotnet/aspnetcore/issues/16892)bakın.

## <a name="additional-information"></a>Ek bilgiler

* <xref:host-and-deploy/proxy-load-balancer>
* [Linux üzerinde Apache: HTTPS yapılandırmasıyla konak ASP.NET Core](xref:host-and-deploy/linux-apache#https-configuration)
* [NGINX ile Linux üzerinde ana bilgisayar ASP.NET Core: HTTPS yapılandırması](xref:host-and-deploy/linux-nginx#https-configuration)
* [IIS 'de SSL ayarlama](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis)
* [OWASP HSTS tarayıcı desteği](https://www.owasp.org/index.php/HTTP_Strict_Transport_Security_Cheat_Sheet#Browser_Support)
