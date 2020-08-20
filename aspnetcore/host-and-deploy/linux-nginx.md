---
title: NGINX ile Linux üzerinde ana bilgisayar ASP.NET Core
author: rick-anderson
description: HTTP trafiğini Kestrel üzerinde çalışan bir ASP.NET Core Web uygulamasına iletmek için Ubuntu 16,04 üzerinde ters proxy olarak NGINX 'i ayarlamayı öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/09/2020
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
uid: host-and-deploy/linux-nginx
ms.openlocfilehash: 63227f068926c4158ac8162fdc1ac11399fd65cb
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88633792"
---
# <a name="host-aspnet-core-on-linux-with-nginx"></a>NGINX ile Linux üzerinde ana bilgisayar ASP.NET Core

, [Sourabh Shirhatti](https://twitter.com/sshirhatti)

Bu kılavuzda Ubuntu 16,04 sunucusunda üretime hazır ASP.NET Core ortamı ayarlama açıklanmaktadır. Bu yönergeler büyük olasılıkla Ubuntu 'ın daha yeni sürümleriyle çalışır, ancak yönergeler daha yeni sürümlerle sınanmamıştır.

ASP.NET Core tarafından desteklenen diğer Linux dağıtımları hakkında daha fazla bilgi için bkz. [Linux üzerinde .NET Core önkoşulları](/dotnet/core/linux-prerequisites).

> [!NOTE]
> Ubuntu 14,04 için, Kestrel işlemini izlemeye yönelik bir çözüm olarak *supervisof* önerilir. *systemd* , Ubuntu 14,04 ' de kullanılamaz. Ubuntu 14,04 yönergeleri için [Bu konunun önceki sürümüne](https://github.com/dotnet/AspNetCore.Docs/blob/e9c1419175c4dd7e152df3746ba1df5935aaafd5/aspnetcore/publishing/linuxproduction.md)bakın.

Bu kılavuz:

* Mevcut bir ASP.NET Core uygulamasını bir ters proxy sunucusunun arkasına koyar.
* İstekleri Kestrel Web sunucusuna iletmek için ters proxy sunucusunu ayarlar.
* Web uygulamasının, bir arka plan programı olarak başlangıcında çalışmasını sağlar.
* Web uygulamasını yeniden başlatmanıza yardımcı olması için bir işlem yönetim aracı yapılandırır.

## <a name="prerequisites"></a>Ön koşullar

1. Sudo ayrıcalığına sahip standart bir kullanıcı hesabı ile Ubuntu 16,04 sunucusuna erişim.
1. .NET Core çalışma zamanını sunucuya yükler.
   1. [.Net çekirdeğini indir sayfasını](https://dotnet.microsoft.com/download/dotnet-core)ziyaret edin.
   1. En son Önizleme olmayan .NET Core sürümünü seçin.
   1. **Uygulama çalıştırma-çalışma zamanı**altındaki tabloda en son önizleme dışı çalışma zamanını indirin.
   1. Linux **Paket Yöneticisi yönergeleri** bağlantısını seçin ve Ubuntu sürümünüz Için Ubuntu yönergelerini izleyin.
1. Mevcut bir ASP.NET Core uygulaması.

Paylaşılan Framework 'ü yükselttikten sonra gelecekte herhangi bir noktada, sunucu tarafından barındırılan ASP.NET Core uygulamaları yeniden başlatın.

## <a name="publish-and-copy-over-the-app"></a>Uygulama üzerinde Yayımla ve Kopyala

Uygulamayı [çerçeveye bağımlı bir dağıtım](/dotnet/core/deploying/#framework-dependent-deployments-fdd)için yapılandırın.

Uygulama yerel olarak çalıştırılır ve güvenli bağlantı (HTTPS) yapmak üzere yapılandırılmamışsa aşağıdaki yaklaşımlardan birini benimseyin:

* Uygulamayı güvenli yerel bağlantıları işleyecek şekilde yapılandırın. Daha fazla bilgi için [https yapılandırma](#https-configuration) bölümüne bakın.
* `https://localhost:5001` `applicationUrl` Dosyadaki *Properties/launchSettings.js* özelliğinden (varsa) kaldırın.

Bir uygulamayı sunucuda çalışabilecek bir dizine (örneğin, *bin/Release/ &lt; target_framework_moniker &gt; /Publish*) paketlemek için geliştirme ortamından [DotNet Publish](/dotnet/core/tools/dotnet-publish) çalıştırın:

```dotnetcli
dotnet publish --configuration Release
```

Uygulama, sunucuda .NET Core çalışma zamanının bakımını yapmayı tercih ediyorsanız, [kendi kendine içerilen bir dağıtım](/dotnet/core/deploying/#self-contained-deployments-scd) olarak da yayımlanabilir.

ASP.NET Core uygulamasını, kuruluşun iş akışını (örneğin, SCP, SFTP) tümleştiren bir aracı kullanarak sunucuya kopyalayın. *Var* dizini altında Web uygulamalarının (örneğin, *var/www/HelloApp*) yerini bulmak yaygındır.

> [!NOTE]
> Bir üretim dağıtım senaryosunda, sürekli tümleştirme iş akışı, uygulamayı yayımlama ve varlıkları sunucuya kopyalama işini yapar.

Uygulamayı test etme:

1. Komut satırından uygulamayı çalıştırın: `dotnet <app_assembly>.dll` .
1. Bir tarayıcıda, `http://<serveraddress>:<port>` uygulamanın Linux üzerinde yerel olarak çalıştığını doğrulamak için bölümüne gidin.

## <a name="configure-a-reverse-proxy-server"></a>Ters proxy sunucu yapılandırma

Ters proxy, dinamik Web uygulamaları sunmak için ortak bir kurulumtir. Ters proxy, HTTP isteğini sonlandırır ve ASP.NET Core uygulamasına iletir.

### <a name="use-a-reverse-proxy-server"></a>Ters proxy sunucusu kullanma

Kestrel, ASP.NET Core ' den dinamik içerik sunmak için harika. Ancak, Web 'e sunma özellikleri IIS, Apache veya NGINX gibi sunucu olarak zengin özellik değildir. Ters bir ara sunucu, statik içerik sunma, istekleri önbelleğe alma, istekleri sıkıştırma ve HTTP sunucusundan HTTPS sonlandırması gibi işleri devreedebilir. Ters ara sunucu, ayrılmış bir makinede bulunabilir veya bir HTTP sunucusu ile birlikte dağıtılabilir.

Bu kılavuzun amaçları doğrultusunda, tek bir NGINX örneği kullanılmıştır. Bu, HTTP sunucusu ile birlikte aynı sunucuda çalışır. Gereksinimlere bağlı olarak, farklı bir kurulum seçilebilir.

İstekler ters proxy tarafından iletileceği için, [Microsoft. AspNetCore. HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) paketindeki [Iletilen üstbilgiler ara yazılımını](xref:host-and-deploy/proxy-load-balancer) kullanın. Ara yazılım, `Request.Scheme` üstbilgiyi kullanarak, `X-Forwarded-Proto` yeniden yönlendirme URI 'leri ve diğer güvenlik ilkelerini doğru çalışacak şekilde güncelleştirir.


[!INCLUDE[](~/includes/ForwardedHeaders.md)]

<xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> `Startup.Configure` Diğer ara yazılım çağrılmadan önce yönteminin en üstünde yöntemi çağırın. Ara yazılımı, `X-Forwarded-For` ve üst bilgilerini iletecek şekilde yapılandırın `X-Forwarded-Proto` :

```csharp
// using Microsoft.AspNetCore.HttpOverrides;

app.UseForwardedHeaders(new ForwardedHeadersOptions
{
    ForwardedHeaders = ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto
});

app.UseAuthentication();
```

Hayır <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> , ara yazılım için belirtilmemişse, iletmek için varsayılan üstbilgiler şunlardır `None` .

Standart localhost adresi (127.0.0.1) dahil olmak üzere geri döngü adreslerinde çalışan proxy 'ler (127.0.0.0/8, [:: 1]), varsayılan olarak güvenilirdir. Kuruluş içindeki diğer güvenilir proxy 'ler veya ağlar, Internet ve Web sunucusu arasında istekleri ele alıyorsa, bunları <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies*> veya ile listesine ekleyin <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks*> <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> . Aşağıdaki örnek, içindeki Iletilen üstbilgiler ara sunucusuna alana 10.0.0.100 IP adresinde bir güvenilen ara sunucu ekler `KnownProxies` `Startup.ConfigureServices` :

```csharp
// using System.Net;

services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

Daha fazla bilgi için bkz. <xref:host-and-deploy/proxy-load-balancer>.

### <a name="install-nginx"></a>NGINX 'i yükler

`apt-get`NGINX yüklemek için kullanın. Yükleyici, sistem başlangıcında arka plan programı olarak NGINX çalıştıran bir *systemd* init betiği oluşturur. [NGINX: resmi deni/Ubuntu paketlerinde](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/#official-debian-ubuntu-packages)Ubuntu yükleme yönergelerini izleyin.

> [!NOTE]
> İsteğe bağlı NGINX modülleri gerekliyse, kaynaktan NGINX oluşturulması gerekebilir.

NGINX ilk kez yüklendiğinden bu yana şunu çalıştırarak açık olarak başlatın:

```bash
sudo service nginx start
```

Bir tarayıcının NGINX için varsayılan giriş sayfasını görüntülediğini doğrulayın. Giriş sayfasına adresinden ulaşılabilir `http://<server_IP_address>/index.nginx-debian.html` .

### <a name="configure-nginx"></a>Nginx hizmetini yapılandırma

İstekleri ASP.NET Core uygulamanıza iletmek için NGINX 'i ters proxy olarak yapılandırmak için */etc/nginx/sites-available/default*değiştirin. Bu dosyayı bir metin düzenleyicisinde açın ve içeriği şu şekilde değiştirin:

```nginx
server {
    listen        80;
    server_name   example.com *.example.com;
    location / {
        proxy_pass         http://localhost:5000;
        proxy_http_version 1.1;
        proxy_set_header   Upgrade $http_upgrade;
        proxy_set_header   Connection keep-alive;
        proxy_set_header   Host $host;
        proxy_cache_bypass $http_upgrade;
        proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header   X-Forwarded-Proto $scheme;
    }
}
```

Uygulama, Blazor Server WebSockets kullanan bir uygulama ise SignalR , <xref:blazor/host-and-deploy/server#linux-with-nginx> üstbilgiyi ayarlama hakkında daha fazla bilgi için bkz `Connection` ..

Hiçbir `server_name` eşleşme olmadığında NGINX varsayılan sunucuyu kullanır. Varsayılan sunucu tanımlanmazsa, yapılandırma dosyasındaki ilk sunucu varsayılan sunucusudur. En iyi uygulama olarak, yapılandırma dosyanızda 444 durum kodunu döndüren belirli bir varsayılan sunucu ekleyin. Varsayılan bir sunucu yapılandırma örneği:

```nginx
server {
    listen   80 default_server;
    # listen [::]:80 default_server deferred;
    return   444;
}
```

Önceki yapılandırma dosyası ve varsayılan sunucu ile NGINX, bağlantı noktası 80 üzerinde ana bilgisayar üst bilgisi veya olan genel trafiği kabul eder `example.com` `*.example.com` . Bu konaklarla eşleşmeyen istekler Kestrel 'e iletilemiyor. NGINX eşleşen istekleri şurada Kestrel 'e iletir `http://localhost:5000` . Daha fazla bilgi için [NGINX 'in Isteği nasıl işliyorsa öğrenin](https://nginx.org/docs/http/request_processing.html) . Kestrel 'in IP/bağlantı noktasını değiştirmek için bkz. [Kestrel: Endpoint Configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).

> [!WARNING]
> Uygun bir [SERVER_NAME yönergesi](https://nginx.org/docs/http/server_names.html) belirtmemesi, uygulamanızı güvenlik açıklarına karşı kullanıma sunar. Alt etki alanı joker karakteri bağlama (örneğin, `*.example.com` ), tüm üst etki alanını (Bu güvenlik açığı olan aksine) kontrol ediyorsanız bu güvenlik riskini ortadan yapmaz `*.com` . Daha fazla bilgi için bkz. [rfc7230 Section-5,4](https://tools.ietf.org/html/rfc7230#section-5.4) .

NGINX yapılandırması kurulduktan sonra, `sudo nginx -t` yapılandırma dosyalarının söz dizimini doğrulamak için ' i çalıştırın. Yapılandırma dosyası testi başarılıysa, NGINX ' i çalıştırarak değişiklikleri çekmeye zorlayın `sudo nginx -s reload` .

Uygulamayı sunucuda doğrudan çalıştırmak için:

1. Uygulamanın dizinine gidin.
1. Uygulamayı çalıştırın: `dotnet <app_assembly.dll>` , burada `app_assembly.dll` uygulamanın derleme dosyası adıdır.

Uygulama sunucuda çalışır, ancak Internet üzerinden yanıt vermezse, sunucunun güvenlik duvarını denetleyin ve 80 bağlantı noktasının açık olduğunu doğrulayın. Azure Ubuntu VM kullanıyorsanız, gelen bağlantı noktası 80 trafiğine izin veren bir ağ güvenlik grubu (NSG) kuralı ekleyin. Giden trafik, gelen kuralı etkinleştirildiğinde otomatik olarak verildiği için, giden bağlantı noktası 80 kuralını etkinleştirmeniz gerekmez.

Uygulamayı test etmeyi tamamladıktan sonra komut isteminde uygulamayı ile kapatın `Ctrl+C` .

## <a name="monitor-the-app"></a>Uygulamayı izleme

Sunucu, üzerinde yapılan istekleri `http://<serveraddress>:80` üzerinde Kestrel üzerinde çalışan ASP.NET Core uygulamasına iletmek üzere ayarlanır `http://127.0.0.1:5000` . Ancak, NGINX Kestrel işlemini yönetmek için ayarlanmadı. *systemd* , temel Web uygulamasını başlatmak ve izlemek üzere bir hizmet dosyası oluşturmak için kullanılabilir. *systemd* , işlem başlatmak, durdurmak ve yönetmek için birçok güçlü özellik sağlayan bir init sistemidir. 

### <a name="create-the-service-file"></a>Hizmet dosyasını oluşturma

Hizmet tanımı dosyasını oluşturun:

```bash
sudo nano /etc/systemd/system/kestrel-helloapp.service
```

Aşağıda, uygulama için örnek bir hizmet dosyası verilmiştir:

```ini
[Unit]
Description=Example .NET Web API App running on Ubuntu

[Service]
WorkingDirectory=/var/www/helloapp
ExecStart=/usr/bin/dotnet /var/www/helloapp/helloapp.dll
Restart=always
# Restart service after 10 seconds if the dotnet service crashes:
RestartSec=10
KillSignal=SIGINT
SyslogIdentifier=dotnet-example
User=www-data
Environment=ASPNETCORE_ENVIRONMENT=Production
Environment=DOTNET_PRINT_TELEMETRY_MESSAGE=false

[Install]
WantedBy=multi-user.target
```

Yukarıdaki örnekte, hizmeti yöneten Kullanıcı `User` seçeneğiyle belirtilir. Kullanıcı ( `www-data` ) var olmalıdır ve uygulamanın dosyalarının doğru sahipliğini içermelidir.

`TimeoutStopSec`Uygulamanın ilk kesme sinyali aldıktan sonra kapanması için bekleyeceği süreyi yapılandırmak için kullanın. Uygulama bu dönemde kapanmazsa, uygulamayı sonlandırmak için SIGKıLL çıkarılır. Değeri unitless saniyeler (örneğin, `150` ), bir zaman aralığı değeri (örneğin, `2min 30s` ) veya `infinity` zaman aşımını devre dışı bırakmak için girin. `TimeoutStopSec` Varsayılan olarak, `DefaultTimeoutStopSec` yönetici yapılandırma dosyasındaki değerini alır (*systemd-System. conf*, *System. conf. d*, *systemd-User. conf*, *User. conf. d*). Çoğu dağıtım için varsayılan zaman aşımı 90 saniyedir.

```
# The default value is 90 seconds for most distributions.
TimeoutStopSec=90
```

Linux, büyük/küçük harfe duyarlı bir dosya sistemine sahiptir. ASPNETCORE_ENVIRONMENT "üretim" olarak ayarlamak, üzerinde *appsettings.production.js*değil, *appsettings.Production.js*yapılandırma dosyasını aramaya neden olur.

Yapılandırma sağlayıcılarının ortam değişkenlerini okuyabilmesi için bazı değerler (örneğin, SQL bağlantı dizeleri) kaçışmalıdır. Yapılandırma dosyasında kullanılmak üzere uygun bir kaçış değeri oluşturmak için aşağıdaki komutu kullanın:

```console
systemd-escape "<value-to-escape>"
```

::: moniker range=">= aspnetcore-3.0"

İki nokta ( `:` ) ayırıcılar ortam değişkeni adlarında desteklenmez. İki nokta üst üste yerine çift alt çizgi ( `__` ) kullanın. Ortam değişkenleri [yapılandırma sağlayıcısı](xref:fundamentals/configuration/index#environment-variables) , ortam değişkenleri yapılandırmaya okurken çift alt çizgileri iki nokta üst üste dönüştürür. Aşağıdaki örnekte, bağlantı dizesi anahtarı `ConnectionStrings:DefaultConnection` hizmet tanımı dosyasına şu şekilde ayarlanır `ConnectionStrings__DefaultConnection` :

::: moniker-end
::: moniker range="< aspnetcore-3.0"

İki nokta ( `:` ) ayırıcılar ortam değişkeni adlarında desteklenmez. İki nokta üst üste yerine çift alt çizgi ( `__` ) kullanın. Ortam değişkenleri [yapılandırma sağlayıcısı](xref:fundamentals/configuration/index#environment-variables-configuration-provider) , ortam değişkenleri yapılandırmaya okurken çift alt çizgileri iki nokta üst üste dönüştürür. Aşağıdaki örnekte, bağlantı dizesi anahtarı `ConnectionStrings:DefaultConnection` hizmet tanımı dosyasına şu şekilde ayarlanır `ConnectionStrings__DefaultConnection` :

::: moniker-end

```
Environment=ConnectionStrings__DefaultConnection={Connection String}
```

Dosyayı kaydedin ve hizmeti etkinleştirin.

```bash
sudo systemctl enable kestrel-helloapp.service
```

Hizmeti başlatın ve çalıştığını doğrulayın.

```
sudo systemctl start kestrel-helloapp.service
sudo systemctl status kestrel-helloapp.service

◝ kestrel-helloapp.service - Example .NET Web API App running on Ubuntu
    Loaded: loaded (/etc/systemd/system/kestrel-helloapp.service; enabled)
    Active: active (running) since Thu 2016-10-18 04:09:35 NZDT; 35s ago
Main PID: 9021 (dotnet)
    CGroup: /system.slice/kestrel-helloapp.service
            └─9021 /usr/local/bin/dotnet /var/www/helloapp/helloapp.dll
```

Ters proxy yapılandırılmış ve systemd üzerinden yönetilen Kestrel, Web uygulaması tam olarak yapılandırılır ve adresinden yerel makinedeki bir tarayıcıdan erişilebilir `http://localhost` . Ayrıca, uzak bir makineden de erişilebilir, engelleyici olabilecek tüm güvenlik duvarını açabilir. Yanıt üst bilgilerini inceleyerek `Server` üst bilgi, Kestrel tarafından sunulan ASP.NET Core uygulamasını gösterir.

```text
HTTP/1.1 200 OK
Date: Tue, 11 Oct 2016 16:22:23 GMT
Server: Kestrel
Keep-Alive: timeout=5, max=98
Connection: Keep-Alive
Transfer-Encoding: chunked
```

### <a name="view-logs"></a>Günlükleri görüntüleme

Kestrel kullanan Web uygulaması kullanılarak yönetildiğinden `systemd` , tüm olaylar ve süreçler merkezi bir günlüğe kaydedilir. Ancak bu günlük, tarafından yönetilen tüm hizmetler ve süreçler için tüm girişleri içerir `systemd` . `kestrel-helloapp.service`Belirli öğeleri görüntülemek için aşağıdaki komutu kullanın:

```bash
sudo journalctl -fu kestrel-helloapp.service
```

Daha fazla filtreleme için, gibi zaman seçenekleri `--since today` `--until 1 hour ago` veya bunların bir birleşimi döndürülen girdi miktarını azaltabilir.

```bash
sudo journalctl -fu kestrel-helloapp.service --since "2016-10-18" --until "2016-10-18 04:00"
```

## <a name="data-protection"></a>Veri koruma

[ASP.NET Core veri koruma yığını](xref:security/data-protection/introduction) , kimlik doğrulama ara yazılımı ( [middlewares](xref:fundamentals/middleware/index)Örneğin, cookie Ara yazılım) ve siteler arası istek sahteciliğini önleme (CSRF) korumaları dahil olmak üzere birkaç ASP.NET Core middlewares tarafından kullanılır. Veri koruma API 'Leri Kullanıcı kodu tarafından çağrılmasa bile, veri korumasının kalıcı bir şifreleme [anahtarı deposu](xref:security/data-protection/implementation/key-management)oluşturacak şekilde yapılandırılması gerekir. Veri koruması yapılandırılmamışsa, anahtarlar bellekte tutulur ve uygulama yeniden başlatıldığında atılır.

Uygulama yeniden başlatıldığında anahtar halkası bellekte depolanıyorsa:

* Tüm cookie tabanlı kimlik doğrulama belirteçleri geçersiz kılındı.
* Kullanıcıların bir sonraki isteğinde yeniden oturum açması gerekir.
* Anahtar halkası ile korunan tüm veriler artık çözülemez. Bu, [CSRF belirteçlerini](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) ve [ASP.NET Core MVC TempData cookie s](xref:fundamentals/app-state#tempdata)öğesini içerebilir.

Veri korumayı, anahtar halkasını sürdürmek ve şifrelemek üzere yapılandırmak için, bkz.:

* <xref:security/data-protection/implementation/key-storage-providers>
* <xref:security/data-protection/implementation/key-encryption-at-rest>

## <a name="long-request-header-fields"></a>Uzun istek üst bilgisi alanları

Proxy sunucusu varsayılan ayarları, platforma bağlı olarak genellikle istek üst bilgisi alanlarını 4 K veya 8 K ile sınırlandırır. Bir uygulama, varsayılan değerden daha uzun bir süre gerektirebilir (örneğin, [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)kullanan uygulamalar). Daha uzun alanlar gerekliyse, proxy sunucusunun varsayılan ayarları ayarlama gerektirir. Uygulanacak değerler senaryoya göre değişir. Daha fazla bilgi için sunucunuzun belgelerine bakın.

* [proxy_buffer_size](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_buffer_size)
* [proxy_buffers](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_buffers)
* [proxy_busy_buffers_size](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_busy_buffers_size)
* [large_client_header_buffers](https://nginx.org/docs/http/ngx_http_core_module.html#large_client_header_buffers)

> [!WARNING]
> Gerekli olmadığı takdirde proxy arabelleklerinin varsayılan değerlerini artırmaz. Bu değerlerin artırılması, kötü amaçlı kullanıcılar tarafından arabellek taşması (taşma) ve hizmet reddi (DoS) saldırıları riskini artırır.

## <a name="secure-the-app"></a>Uygulamanın güvenliğini sağlama

### <a name="enable-apparmor"></a>AppArmor etkinleştir

Linux güvenlik modülleri (LSM), Linux 2,6 ' den beri Linux çekirdeğinin parçası olan bir çerçevedir. LSM, güvenlik modüllerinin farklı uygulamalarını destekler. [AppArmor](https://wiki.ubuntu.com/AppArmor) , programı sınırlı bir kaynak kümesiyle sınırlandırarak zorunlu bir Access Control sistemi uygulayan bir LSM 'dir. AppArmor etkinleştirildiğinden ve düzgün şekilde yapılandırıldığından emin olun.

### <a name="configure-the-firewall"></a>Güvenlik duvarını yapılandırma

Kullanımda olmayan tüm dış bağlantı noktalarını kapatın. Karmaşık olmayan güvenlik duvarı (UW) `iptables` , güvenlik duvarını yapılandırmak için BIR CLI sağlayarak bir ön uç sağlar.

> [!WARNING]
> Bir güvenlik duvarı, doğru yapılandırılmamışsa tüm sisteme erişimi engeller. Kendisine bağlanmak için SSH kullanıyorsanız doğru SSH bağlantı noktasını belirtmemesi Sistem oturumunuzu etkin bir şekilde kilitleyecek. Varsayılan bağlantı noktası 22 ' dir. Daha fazla bilgi için bkz. [UFW 'ye giriş](https://help.ubuntu.com/community/UFW) ve [el ile](https://manpages.ubuntu.com/manpages/bionic/man8/ufw.8.html).

`ufw`Gerekli olan herhangi bir bağlantı noktasında trafiğe izin vermek için bunu yükleyip yapılandırın.

```bash
sudo apt-get install ufw

sudo ufw allow 22/tcp
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp

sudo ufw enable
```

### <a name="secure-nginx"></a>Güvenli NGINX

#### <a name="change-the-nginx-response-name"></a>NGINX yanıt adını değiştirme

*Src/http/ngx_http_header_filter_module. c*'yi düzenleyin:

```
static char ngx_http_server_string[] = "Server: Web Server" CRLF;
static char ngx_http_server_full_string[] = "Server: Web Server" CRLF;
```

#### <a name="configure-options"></a>Seçenekleri yapılandırma

Sunucuyu gerekli olan ek modüllerle yapılandırın. Uygulamayı sağlamlaştırmak için [ModSecurity](https://www.modsecurity.org/)gibi bir Web uygulaması güvenlik duvarı kullanmayı düşünün.

#### <a name="https-configuration"></a>HTTPS yapılandırması

**Uygulamayı güvenli (HTTPS) yerel bağlantılar için yapılandırma**

[DotNet Run](/dotnet/core/tools/dotnet-run) komutu, uygulamayı özelliği tarafından belirtilen URL 'lerde dinlemek üzere yapılandıran, uygulamanın *Özellikler/launchSettings.js* kullanır `applicationUrl` (örneğin, `https://localhost:5001;http://localhost:5000` ).

`dotnet run`Aşağıdaki yaklaşımlardan birini kullanarak, uygulamayı komut veya geliştirme ortamı için geliştirme sırasında (F5 veya CTRL + f5 Visual Studio Code) bir sertifikayı kullanacak şekilde yapılandırın:

* [Varsayılan sertifikayı yapılandırmadan Değiştir](xref:fundamentals/servers/kestrel#configuration) (*önerilir*)
* [KestrelServerOptions.ConfigureHttpsDefaults](xref:fundamentals/servers/kestrel#configurehttpsdefaultsactionhttpsconnectionadapteroptions)

**Güvenli (HTTPS) istemci bağlantıları için ters proxy 'yi yapılandırma**

* `443`Güvenilen bir sertifika yetkilisi (CA) tarafından verilen geçerli bir sertifika belirterek, sunucu bağlantı NOKTASıNDA HTTPS trafiğini dinleyecek şekilde yapılandırın.

* Aşağıdaki */etc/nginx/nginx.conf* dosyasında gösterilen bazı uygulamalardan yararlanarak güvenliği en iyi şekilde yapın. Daha güçlü bir şifre seçme ve HTTP üzerinden tüm trafiği HTTPS 'ye yeniden yönlendirme örnekleri içerir.

* `HTTP Strict-Transport-Security`(HSTS) üstbilgisi eklemek, istemci tarafından yapılan tüm sonraki ISTEKLERIN https üzerinden yapılmasını sağlar.

* Daha sonra HTTPS devre dışı bırakılabiliyorsanız, aşağıdaki yaklaşımlardan birini kullanın:

  * HSTS üst bilgisini eklemeyin.
  * Kısa bir `max-age` değer seçin.

*/Etc/nginx/proxy.conf* yapılandırma dosyasını ekleyin:

[!code-nginx[](linux-nginx/proxy.conf)]

*/Etc/nginx/nginx.conf* yapılandırma dosyasını düzenleyin. Örnek, `http` `server` tek bir yapılandırma dosyasında hem hem de bölümlerini içerir.

[!code-nginx[](linux-nginx/nginx.conf?highlight=2)]

> [!NOTE]
> Blazor WebAssembly uygulamalar `burst` , bir uygulama tarafından yapılan isteklerin daha fazla sayısına uyum sağlamak için daha büyük bir parametre değeri gerektirir. Daha fazla bilgi için bkz. <xref:blazor/host-and-deploy/webassembly#nginx>.

#### <a name="secure-nginx-from-clickjacking"></a>Tıklama mercekten NGINX 'i güvenli hale getirme

*UI redki saldırısı*olarak da bilinen [tıklama](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger), bir Web sitesi ziyaretçisinin bir bağlantı veya düğmeye Şu anda ziyaret ettiğinden farklı bir sayfada tıklanması zor olan kötü amaçlı bir saldırıya neden olur. `X-FRAME-OPTIONS`Sitesini güvenli hale getirmek için kullanın.

Tıklama saldırılarını azaltmak için:

1. *NGINX. conf* dosyasını düzenleyin:

   ```bash
   sudo nano /etc/nginx/nginx.conf
   ```

   `add_header X-Frame-Options "SAMEORIGIN";` satırını ekleyin.
1. Dosyayı kaydedin.
1. NGINX 'i yeniden başlatın.

#### <a name="mime-type-sniffing"></a>MIME türü algılaması

Bu üst bilgi, tarayıcının, yanıt içerik türünü geçersiz kılmamasını bildiren, büyük bir olasılıkla, MIME tarafından yapılan bir yanıtın bir yanıt olarak bildirimde bulunmasını engeller. `nosniff`Seçeneğiyle, sunucu içeriği "metin/html" olarak söyliyorsa, tarayıcı bunu "metin/html" olarak işler.

*NGINX. conf* dosyasını düzenleyin:

```bash
sudo nano /etc/nginx/nginx.conf
```

Satırı ekleyin `add_header X-Content-Type-Options "nosniff";` ve dosyayı kaydedin, sonra NGINX 'i yeniden başlatın.

## <a name="additional-nginx-suggestions"></a>Ek NGINX önerileri

Sunucuda paylaşılan Framework 'ü yükselttikten sonra, sunucu tarafından barındırılan ASP.NET Core uygulamaları yeniden başlatın.

## <a name="additional-resources"></a>Ek kaynaklar

* [Linux üzerinde .NET Core önkoşulları](/dotnet/core/linux-prerequisites)
* [NGINX: Ikili yayınlar: resmi olmayan/Ubuntu Paketleri](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/#official-debian-ubuntu-packages)
* <xref:test/troubleshoot>
* <xref:host-and-deploy/proxy-load-balancer>
* [NGıNX: Iletilen üstbilgiyi kullanma](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/)
