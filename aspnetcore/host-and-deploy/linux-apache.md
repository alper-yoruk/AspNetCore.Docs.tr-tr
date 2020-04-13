---
title: Apache ile Linux'ta Ana ASP.NET Core'u barındırın
author: rick-anderson
description: Http trafiğini Kestrel'de çalışan ASP.NET Core web uygulamasına yönlendirmek için Apache'yi CentOS'ta ters proxy sunucusu olarak nasıl ayarlayabilirsiniz öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: shboyer
ms.custom: mvc
ms.date: 04/10/2020
uid: host-and-deploy/linux-apache
ms.openlocfilehash: 1256f6d21f94ef6c4baad7aae4bd0e751af5c675
ms.sourcegitcommit: 6f1b516e0c899a49afe9a29044a2383ce2ada3c7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81224043"
---
# <a name="host-aspnet-core-on-linux-with-apache"></a>Apache ile Linux'ta Ana ASP.NET Core'u barındırın

Yazar: [Shayne Boyer](https://github.com/spboyer)

Bu kılavuzu kullanarak, Http trafiğini [Kestrel](xref:fundamentals/servers/kestrel) sunucusunda çalışan ASP.NET Core web uygulamasına yönlendirmek için [Apache'yi](https://httpd.apache.org/) [CentOS 7'de](https://www.centos.org/) ters proxy sunucusu olarak nasıl ayarlayabilirsiniz öğrenin. [mod_proxy uzantısı](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html) ve ilgili modüller sunucunun ters proxy'sini oluşturur.

## <a name="prerequisites"></a>Ön koşullar

* Sudo ayrıcalığına sahip standart bir kullanıcı hesabı ile CentOS 7 çalıştıran sunucu.
* Sunucuya .NET Core çalışma saatini yükleyin.
   1. İndir [.NET Çekirdek sayfasını](https://dotnet.microsoft.com/download/dotnet-core)ziyaret edin.
   1. En son önizleme olmayan .NET Core sürümünü seçin.
   1. Uygulamaları Çalıştır - Runtime altında tabloda en son önizleme olmayan çalışma **süresini indirin.**
   1. Linux **Paketi yöneticisi yönergeleri** bağlantısını seçin ve CentOS yönergelerini izleyin.
* Mevcut bir ASP.NET Core uygulaması.

Paylaşılan çerçeveyi yükselttikten sonra gelecekte herhangi bir noktada, sunucu tarafından barındırılan ASP.NET Core uygulamalarını yeniden başlatın.

## <a name="publish-and-copy-over-the-app"></a>Uygulama üzerinde yayımlama ve kopyalama

Uygulamayı [çerçeveye bağımlı bir dağıtım](/dotnet/core/deploying/#framework-dependent-deployments-fdd)için yapılandırın.

Uygulama yerel olarak çalıştırılırsa ve güvenli bağlantılar (HTTPS) yapacak şekilde yapılandırılmamışsa, aşağıdaki yaklaşımlardan birini benimseyin:

* Uygulamayı güvenli yerel bağlantıları işleyeceğini zedele. Daha fazla bilgi için [HTTPS yapılandırma](#https-configuration) bölümüne bakın.
* `https://localhost:5001` *Properties/launchSettings.json* dosyasındaki `applicationUrl` özellikten (varsa) kaldırın.

Bir uygulamayı sunucuda çalıştırabilen bir dizine (örneğin, *&lt;bin/Release/ target_framework_moniker&gt;/publish)* paketlemek için geliştirme ortamından [dotnet yayımlamayı](/dotnet/core/tools/dotnet-publish) çalıştırın:

```dotnetcli
dotnet publish --configuration Release
```

Sunucuda .NET Core çalışma süresini korumamak isterseniz, uygulama bağımsız bir [dağıtım](/dotnet/core/deploying/#self-contained-deployments-scd) olarak da yayınlanabilir.

kuruluşun iş akışına entegre olan bir aracı kullanarak ASP.NET Core uygulamasını sunucuya kopyalayın (örneğin, SCP, SFTP). Var *dizininin* altında web uygulamalarını bulmak yaygındır (örneğin, *var/www/helloapp).*

> [!NOTE]
> Üretim dağıtım senaryosunda, sürekli tümleştirme iş akışı, uygulamayı yayımlama ve varlıkları sunucuya kopyalama işini yapar.

## <a name="configure-a-proxy-server"></a>Proxy sunucusu yapılandırma

Ters proxy, dinamik web uygulamaları için ortak bir kurulumdur. Ters proxy HTTP isteğini sona erdirir ve ASP.NET uygulamasına iletilir.

Proxy sunucusu, istekleriyerine yerine getirmek yerine istemci isteklerini başka bir sunucuya iletir. Ters proxy, genellikle rasgele istemciler adına sabit bir hedefe iletir. Bu kılavuzda, Apache Kestrel'in ASP.NET Core uygulamasına hizmet verdiği sunucuda çalışan ters proxy olarak yapılandırılır.

İstekler ters proxy ile iletilir olduğundan, [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) paketinden [Iletili Üstbilgi Middleware](xref:host-and-deploy/proxy-load-balancer) kullanın. Orta `Request.Scheme`yazılım, üstbilgi `X-Forwarded-Proto` kullanarak, URI'leri ve diğer güvenlik ilkelerini yeniden yönlendirmenin doğru çalışmasını güncelleştirir.

Kimlik doğrulama, bağlantı oluşturma, yeniden yönlendirmeler ve coğrafi konum gibi şemaya bağlı olan tüm bileşen, İlliyeli Üstbilgi Middleware'i çağırdıktan sonra yerleştirilmelidir. Genel bir kural olarak, Iletili Üstbilgi Middleware tanılama ve hata işleme middleware dışında diğer ara önce çalışması gerekir. Bu sıralama, iletilen üstbilgiler bilgilerine güvenen ara yazılımın işlem için üstbilgi değerlerini tüketmesini sağlar.

<xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> Diğer ara yazılımları `Startup.Configure` çağırmadan önce yöntemi en üstteki yönteme çağırın. Orta yazılımı ve `X-Forwarded-For` üstbilgiileri `X-Forwarded-Proto` iletecek şekilde yapılandırın:

```csharp
// using Microsoft.AspNetCore.HttpOverrides;

app.UseForwardedHeaders(new ForwardedHeadersOptions
{
    ForwardedHeaders = ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto
});

app.UseAuthentication();
```

Ara <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> yazılımda hayır belirtilmemişse, ileriye dönük `None`varsayılan üstbilgi .

Standart localhost adresi (127.0.0.1) dahil olmak üzere geri dönüş adreslerinde çalışan ekseksenler varsayılan olarak güvenilir. Kuruluş içindeki diğer güvenilir vekiller veya ağlar, Internet ve web sunucusu arasındaki istekleri <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies*> işlerse, bunları listeye <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks*> <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>ekleyin. Aşağıdaki örnek, IP adresi 10.0.0.100'de güvenilir bir proxy sunucusunu `KnownProxies` `Startup.ConfigureServices`Ilileli Üstbilgi Orta Ware'e ekler:

```csharp
// using System.Net;

services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

Daha fazla bilgi için bkz. <xref:host-and-deploy/proxy-load-balancer>.

### <a name="install-apache"></a>Apache'yi Yükleyin

CentOS paketlerini en son kararlı sürümlerine güncelleyin:

```bash
sudo yum update -y
```

Apache web sunucusunu CentOS'a `yum` tek bir komutla yükleyin:

```bash
sudo yum -y install httpd mod_ssl
```

Komutu çalıştırdıktan sonra örnek çıktı:

```bash
Downloading packages:
httpd-2.4.6-40.el7.centos.4.x86_64.rpm               | 2.7 MB  00:00:01
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
Installing : httpd-2.4.6-40.el7.centos.4.x86_64      1/1 
Verifying  : httpd-2.4.6-40.el7.centos.4.x86_64      1/1 

Installed:
httpd.x86_64 0:2.4.6-40.el7.centos.4

Complete!
```

> [!NOTE]
> Bu örnekte, CentOS 7 sürümü 64 bit olduğundan çıktı httpd.86_64 yansıtır. Apache'nin nerede yüklü olduğunu `whereis httpd` doğrulamak için bir komut isteminden çalıştırın.

### <a name="configure-apache"></a>Apache'yi Yapılandır

Apache için yapılandırma dosyaları dizinde `/etc/httpd/conf.d/` yer alır. *.conf* uzantılı tüm dosyalar, modülyüklemek için gerekli yapılandırma dosyalarını içeren modül yapılandırma dosyalarına `/etc/httpd/conf.modules.d/`ek olarak alfabetik sıraya göre işlenir.

Uygulama için *helloapp.conf*adlı bir yapılandırma dosyası oluşturun:

```
<VirtualHost *:*>
    RequestHeader set "X-Forwarded-Proto" expr=%{REQUEST_SCHEME}
</VirtualHost>

<VirtualHost *:80>
    ProxyPreserveHost On
    ProxyPass / http://127.0.0.1:5000/
    ProxyPassReverse / http://127.0.0.1:5000/
    ServerName www.example.com
    ServerAlias *.example.com
    ErrorLog ${APACHE_LOG_DIR}helloapp-error.log
    CustomLog ${APACHE_LOG_DIR}helloapp-access.log common
</VirtualHost>
```

Blok, `VirtualHost` bir sunucudaki bir veya daha fazla dosyada birden çok kez görünebilir. Önceki yapılandırma dosyasında, Apache bağlantı noktası 80'deki genel trafiği kabul eder. Etki `www.example.com` alanı sunuluyor ve `*.example.com` diğer ad aynı web sitesinde çözülür. Daha fazla bilgi için [Ad tabanlı sanal ana bilgisayar desteğine](https://httpd.apache.org/docs/current/vhosts/name-based.html) bakın. İstekler 127.0.0.1 adresindesunucunun 5000 bağlantı noktasına kökten yakınlanır. Çift yönlü iletişim `ProxyPass` için `ProxyPassReverse` gereklidir. Kerkenez'in IP/bağlantı noktasını değiştirmek için [Kerkenez: Bitiş Noktası yapılandırmasına](xref:fundamentals/servers/kestrel#endpoint-configuration)bakın.

> [!WARNING]
> **VirtualHost** bloğunda uygun bir [ServerName yönergesi](https://httpd.apache.org/docs/current/mod/core.html#servername) belirtilmemesi, uygulamanızın güvenlik açıklarına maruz kalır. Alt etki alanı joker karakter `*.example.com`bağlama (örneğin, ) tüm üst etki alanını denetlerseniz (bunun `*.com`aksine, savunmasız dır) bu güvenlik riski oluşturmaz. Daha fazla bilgi için [rfc7230 bölüm-5.4'e](https://tools.ietf.org/html/rfc7230#section-5.4) bakın.

Günlük kullanımı `ErrorLog` ve `CustomLog` `VirtualHost` yönergeleri başına yapılandırılabilir. `ErrorLog`sunucunun hataları günlüğe kaydettiği `CustomLog` ve günlük dosyasının dosya adını ve biçimini ayarladığı yerdir. Bu durumda, istek bilgilerinin günlüğe kaydedildiği yer burasıdır. Her istek için bir satır vardır.

Dosyayı kaydedin ve yapılandırmayı test edin. Her şey geçerse, yanıt `Syntax [OK]`.

```bash
sudo service httpd configtest
```

Apache'yi yeniden başlatın:

```bash
sudo systemctl restart httpd
sudo systemctl enable httpd
```

## <a name="monitor-the-app"></a>Uygulamayı izleme

Apache şimdi kerkenez `http://localhost:80` `http://127.0.0.1:5000`üzerinde çalışan ASP.NET Core uygulamasına yapılan istekleri iletmek için kurulum. Ancak, Apache Kerkenez işlemini yönetmek için kurulmaz. *Sistemli* kullanın ve temel web uygulamasını başlatmak ve izlemek için bir hizmet dosyası oluşturun. *sistemli,* süreçleri başlatmak, durdurmak ve yönetmek için birçok güçlü özellik sağlayan bir init sistemidir.

### <a name="create-the-service-file"></a>Hizmet dosyasını oluşturma

Hizmet tanımı dosyasını oluşturun:

```bash
sudo nano /etc/systemd/system/kestrel-helloapp.service
```

Uygulama için örnek bir hizmet dosyası:

```
[Unit]
Description=Example .NET Web API App running on CentOS 7

[Service]
WorkingDirectory=/var/www/helloapp
ExecStart=/usr/local/bin/dotnet /var/www/helloapp/helloapp.dll
Restart=always
# Restart service after 10 seconds if the dotnet service crashes:
RestartSec=10
KillSignal=SIGINT
SyslogIdentifier=dotnet-example
User=apache
Environment=ASPNETCORE_ENVIRONMENT=Production 

[Install]
WantedBy=multi-user.target
```

Önceki örnekte, hizmeti yöneten kullanıcı `User` seçeneği tarafından belirtilir. Kullanıcı (`apache`) var olmalı ve uygulamanın dosyalarının düzgün sahipliğine sahip olmalıdır.

Uygulamanın ilk kesme sinyalini aldıktan sonra kapanmasını beklemek için süreyi yapılandırmak için kullanın. `TimeoutStopSec` Bu süre içinde uygulama kapanmazsa, uygulamayı sonlandırmak için SIGKILL verilir. Değeri birimsiz saniye (örneğin, `150`), zaman aralığı değeri (örneğin, `infinity` `2min 30s`) olarak veya zaman aralığını devre dışı düşürün. `TimeoutStopSec``DefaultTimeoutStopSec` yönetici yapılandırma dosyasındaki değere varsayılanlar (*systemd-system.conf*, *system.conf.d*, *systemd-user.conf*, *user.conf.d*). Çoğu dağıtım için varsayılan zaman ası 90 saniyedir.

```
# The default value is 90 seconds for most distributions.
TimeoutStopSec=90
```

Yapılandırma sağlayıcılarının ortam değişkenlerini okuyabilmesi için bazı değerlerin (örneğin, SQL bağlantı dizeleri) kaçılması gerekir. Yapılandırma dosyasında kullanılmak üzere düzgün bir şekilde kaçan bir değer oluşturmak için aşağıdaki komutu kullanın:

```console
systemd-escape "<value-to-escape>"
```

Kolon`:`( ) ayırıcıları çevre değişken adlarında desteklenmez. Bir kolon yerine`__`bir çift alt çizgi ( ) kullanın. [Çevre Değişkenleri yapılandırma sağlayıcısı,](xref:fundamentals/configuration/index#environment-variables-configuration-provider) ortam değişkenleri yapılandırmaya okunduğunda çift alt puanları iki nokta üst üste dönüştürür. Aşağıdaki örnekte, bağlantı dizesi anahtarı `ConnectionStrings:DefaultConnection` hizmet tanım `ConnectionStrings__DefaultConnection`dosyasına şu şekilde ayarlanır:

```
Environment=ConnectionStrings__DefaultConnection={Connection String}
```

Dosyayı kaydedin ve hizmeti etkinleştirin:

```bash
sudo systemctl enable kestrel-helloapp.service
```

Hizmeti başlatın ve çalıştığını doğrulayın:

```bash
sudo systemctl start kestrel-helloapp.service
sudo systemctl status kestrel-helloapp.service

● kestrel-helloapp.service - Example .NET Web API App running on CentOS 7
    Loaded: loaded (/etc/systemd/system/kestrel-helloapp.service; enabled)
    Active: active (running) since Thu 2016-10-18 04:09:35 NZDT; 35s ago
Main PID: 9021 (dotnet)
    CGroup: /system.slice/kestrel-helloapp.service
            └─9021 /usr/local/bin/dotnet /var/www/helloapp/helloapp.dll
```

Ters proxy yapılandırılan ve Kerkenez *sistemi*ile yönetilen ile, web uygulaması tamamen yapılandırılmış ve yerel makinede bir tarayıcıdan erişilebilir `http://localhost`. Yanıt üstbilgilerini inceleyen **Sunucu** üstbilgi, ASP.NET Core uygulamasının Kestrel tarafından sunulduğunu gösterir:

```
HTTP/1.1 200 OK
Date: Tue, 11 Oct 2016 16:22:23 GMT
Server: Kestrel
Keep-Alive: timeout=5, max=98
Connection: Keep-Alive
Transfer-Encoding: chunked
```

### <a name="view-logs"></a>Günlükleri görüntüleme

Kestrel kullanarak web uygulaması *systemd*kullanılarak yönetildiği için, olaylar ve işlemler merkezi bir günlükte günlüğe kaydedilir. Ancak, bu *günlük, systemd*tarafından yönetilen tüm hizmet ve işlemleriçin girişleri içerir. `kestrel-helloapp.service`-belirli öğeleri görüntülemek için aşağıdaki komutu kullanın:

```bash
sudo journalctl -fu kestrel-helloapp.service
```

Zaman filtreleme için, komutla zaman seçeneklerini belirtin. Örneğin, geçerli `--since today` gün için filtre `--until 1 hour ago` uygulama veya önceki saatin girişlerini görmek için kullanın. Daha fazla bilgi [için journalctl için adam sayfasına](https://www.unix.com/man-page/centos/1/journalctl/)bakın.

```bash
sudo journalctl -fu kestrel-helloapp.service --since "2016-10-18" --until "2016-10-18 04:00"
```

## <a name="data-protection"></a>Veri koruma

[ASP.NET Çekirdek Veri Koruma yığını,](xref:security/data-protection/introduction) kimlik doğrulama ara yazılımları (örneğin, çerez ara yazılımları) ve site ler arası istek sahteciliği (CSRF) korumaları da dahil olmak üzere birkaç ASP.NET Core ara [yazılımtarafından](xref:fundamentals/middleware/index)kullanılır. Veri Koruma API'leri kullanıcı koduyla çağrılmasa bile, veri koruması kalıcı bir şifreleme [anahtar deposu](xref:security/data-protection/implementation/key-management)oluşturacak şekilde yapılandırılmalıdır. Veri koruması yapılandırılmamışsa, anahtarlar bellekte tutulur ve uygulama yeniden başlatıldığında atılır.

Uygulama yeniden başlatıldığında anahtarlık bellekte depolanırsa:

* Tüm çerez tabanlı kimlik doğrulama belirteçleri geçersiz kılındı.
* Kullanıcıların bir sonraki istekleri üzerine yeniden oturum açmaları gerekmektedir.
* Anahtarlıkla korunan tüm verilerin şifresi artık çözülemez. Buna [CSRF belirteçleri](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) ve [ASP.NET Core MVC TempData tanımlama bilgileri](xref:fundamentals/app-state#tempdata)de dahil olabilir.

Anahtarlık devam etmek ve şifrelemek için veri korumayapılandırmak için bkz:

* <xref:security/data-protection/implementation/key-storage-providers>
* <xref:security/data-protection/implementation/key-encryption-at-rest>

## <a name="secure-the-app"></a>Uygulamayı güvenli hale

### <a name="configure-firewall"></a>Güvenlik duvarını yapılandırma

*Firewalld* ağ bölgeleri desteği ile güvenlik duvarı yönetmek için dinamik bir daemon olduğunu. Bağlantı noktaları ve paket filtreleme iptables tarafından yönetilebilir. *Firewalld* varsayılan olarak yüklenmelidir. `yum`paketi yüklemek veya yüklü olduğunu doğrulamak için kullanılabilir.

```bash
sudo yum install firewalld -y
```

Yalnızca `firewalld` uygulama için gereken bağlantı noktalarını açmak için kullanın. Bu durumda, port 80 ve 443 kullanılır. Aşağıdaki komutlar kalıcı olarak 80 ve 443 bağlantı noktalarını açmak üzere ayarlar:

```bash
sudo firewall-cmd --add-port=80/tcp --permanent
sudo firewall-cmd --add-port=443/tcp --permanent
```

Güvenlik duvarı ayarlarını yeniden yükleyin. Varsayılan bölgede kullanılabilir hizmetleri ve bağlantı noktalarını denetleyin. Seçenekler inceleyerek `firewall-cmd -h`kullanılabilir.

```bash
sudo firewall-cmd --reload
sudo firewall-cmd --list-all
```

```bash
public (default, active)
interfaces: eth0
sources: 
services: dhcpv6-client
ports: 443/tcp 80/tcp
masquerade: no
forward-ports: 
icmp-blocks: 
rich rules: 
```

### <a name="https-configuration"></a>HTTPS yapılandırması

**Uygulamayı güvenli (HTTPS) yerel bağlantılar için yapılandırın**

[Dotnet çalıştır](/dotnet/core/tools/dotnet-run) komutu, uygulamanın özellik tarafından sağlanan URL'leri dinleyecek şekilde yapılandıran *Özellikler/launchSettings.json* dosyasını `applicationUrl` kullanır (örneğin, `https://localhost:5001;http://localhost:5000`).

Aşağıdaki yaklaşımlardan birini kullanarak uygulamayı `dotnet run` komut veya geliştirme ortamı (Visual Studio Code'ta F5 veya Ctrl+F5) için geliştirme aşamasında bir sertifika kullanacak şekilde yapılandırın:

* [Yapılandırmadan varsayılan sertifikayı değiştirme](xref:fundamentals/servers/kestrel#configuration) (*Önerilen*)
* [KestrelServerOptions.ConfigureHttpsDefaults](xref:fundamentals/servers/kestrel#configurehttpsdefaultsactionhttpsconnectionadapteroptions)

**Güvenli (HTTPS) istemci bağlantıları için ters proxy'yi yapılandırın**

Https için Apache yapılandırmak için *mod_ssl* modülü kullanılır. *Httpd* modülü *kurulduğunda, mod_ssl* modülü de kuruldu. Yüklenmiyorsa, yapılandırmaya `yum` eklemek için kullanın.

```bash
sudo yum install mod_ssl
```

HTTPS'yi zorlamak `mod_rewrite` için, URL yeniden yazmayı etkinleştirmek için modülü yükleyin:

```bash
sudo yum install mod_rewrite
```

URL yeniden yazma ve port 443 üzerinde güvenli iletişim etkinleştirmek için *helloapp.conf* dosyasını değiştirin:

```
<VirtualHost *:*>
    RequestHeader set "X-Forwarded-Proto" expr=%{REQUEST_SCHEME}
</VirtualHost>

<VirtualHost *:80>
    RewriteEngine On
    RewriteCond %{HTTPS} !=on
    RewriteRule ^/?(.*) https://%{SERVER_NAME}/$1 [R,L]
</VirtualHost>

<VirtualHost *:443>
    ProxyPreserveHost On
    ProxyPass / http://127.0.0.1:5000/
    ProxyPassReverse / http://127.0.0.1:5000/
    ErrorLog /var/log/httpd/helloapp-error.log
    CustomLog /var/log/httpd/helloapp-access.log common
    SSLEngine on
    SSLProtocol all -SSLv2
    SSLCipherSuite ALL:!ADH:!EXPORT:!SSLv2:!RC4+RSA:+HIGH:+MEDIUM:!LOW:!RC4
    SSLCertificateFile /etc/pki/tls/certs/localhost.crt
    SSLCertificateKeyFile /etc/pki/tls/private/localhost.key
</VirtualHost>
```

> [!NOTE]
> Bu örnek, yerel olarak oluşturulan bir sertifika kullanıyor. **SSLCertificateFile** etki alanı adı için birincil sertifika dosyası olmalıdır. **SSLCertificateKeyFile** CSR oluşturulduğunda oluşturulan anahtar dosyası olmalıdır. **SSLCertificateChainFile,** sertifika yetkilisi tarafından sağlanan ara sertifika dosyası (varsa) olmalıdır.

Dosyayı kaydedin ve yapılandırmayı test edin:

```bash
sudo service httpd configtest
```

Apache'yi yeniden başlatın:

```bash
sudo systemctl restart httpd
```

## <a name="additional-apache-suggestions"></a>Ek Apaçi önerileri

### <a name="restart-apps-with-shared-framework-updates"></a>Paylaşılan çerçeve güncellemeleriyle uygulamaları yeniden başlatın

Sunucuda paylaşılan çerçeveyi yükselttikten sonra, sunucu tarafından barındırılan ASP.NET Core uygulamalarını yeniden başlatın.

### <a name="additional-headers"></a>Ek üstbilgi

Kötü amaçlı saldırılara karşı güvenli hale getirmek için, değiştirilmesi veya eklenmesi gereken birkaç üstbilgi vardır. Modülün `mod_headers` yüklü olduğundan emin olun:

```bash
sudo yum install mod_headers
```

#### <a name="secure-apache-from-clickjacking-attacks"></a>Apaçi'yi clickjacking saldırılarından koruma

*UI telafi saldırısı*olarak da bilinen [Clickjacking,](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger)bir web sitesi ziyaretçisi şu anda ziyaret ettiklerinden farklı bir sayfadaki bir bağlantıyı veya düğmeyi tıklatmak için kandırıldığı kötü amaçlı bir saldırıdır. Siteyi `X-FRAME-OPTIONS` güvenli hale getirmek için kullanın.

Tıklama kaçırma saldırılarını azaltmak için:

1. *httpd.conf* dosyasını edin:

   ```bash
   sudo nano /etc/httpd/conf/httpd.conf
   ```

   `Header append X-FRAME-OPTIONS "SAMEORIGIN"` satırını ekleyin.
1. Dosyayı kaydedin.
1. Apaçi'yi yeniden başlatın.

#### <a name="mime-type-sniffing"></a>MIME tipi koklama

Üstbilgi, Internet Explorer'ın `X-Content-Type-Options` *MIME koklamasını* (dosyanın `Content-Type` içeriğinden bir dosyanın kini belirleme) engeller. Sunucu üstbilgiyi `Content-Type` `text/html` `nosniff` seçenek kümesiyle ayarlarsa, Internet Explorer `text/html` içeriği dosyanın içeriğinden bağımsız olarak işler.

*httpd.conf* dosyasını edin:

```bash
sudo nano /etc/httpd/conf/httpd.conf
```

`Header set X-Content-Type-Options "nosniff"` satırını ekleyin. Dosyayı kaydedin. Apaçi'yi yeniden başlatın.

### <a name="load-balancing"></a>Yük Dengeleme

Bu örnek, CentOS 7 ve Kestrel'de Apache'nin aynı örnek makinede nasıl ayarlandığı ve yapılandırılabildiği gösterilmektedir. Tek bir başarısızlık noktası nın olmaması için; *mod_proxy_balancer* kullanarak ve **VirtualHost** değiştirerek Apache proxy sunucusu arkasında web uygulamaları birden fazla örneklerini yönetmek için izin verecek.

```bash
sudo yum install mod_proxy_balancer
```

Aşağıda gösterilen yapılandırma dosyasında, bağlantı `helloapp` noktası 5001'de çalışacak şekilde ek bir örnek ayarlanır. *Proxy* bölümü, bakiye *yan isteklerini*yüklemek için iki üyeli bir denge yapılandırması ile ayarlanır.

```
<VirtualHost *:*>
    RequestHeader set "X-Forwarded-Proto" expr=%{REQUEST_SCHEME}
</VirtualHost>

<VirtualHost *:80>
    RewriteEngine On
    RewriteCond %{HTTPS} !=on
    RewriteRule ^/?(.*) https://%{SERVER_NAME}/$1 [R,L]
</VirtualHost>

<VirtualHost *:443>
    ProxyPass / balancer://mycluster/ 

    ProxyPassReverse / http://127.0.0.1:5000/
    ProxyPassReverse / http://127.0.0.1:5001/

    <Proxy balancer://mycluster>
        BalancerMember http://127.0.0.1:5000
        BalancerMember http://127.0.0.1:5001 
        ProxySet lbmethod=byrequests
    </Proxy>

    <Location />
        SetHandler balancer
    </Location>
    ErrorLog /var/log/httpd/helloapp-error.log
    CustomLog /var/log/httpd/helloapp-access.log common
    SSLEngine on
    SSLProtocol all -SSLv2
    SSLCipherSuite ALL:!ADH:!EXPORT:!SSLv2:!RC4+RSA:+HIGH:+MEDIUM:!LOW:!RC4
    SSLCertificateFile /etc/pki/tls/certs/localhost.crt
    SSLCertificateKeyFile /etc/pki/tls/private/localhost.key
</VirtualHost>
```

### <a name="rate-limits"></a>Fiyat Limitleri

*httpd* modülünde yer alan *mod_ratelimit*kullanarak istemcilerin bant genişliği sınırlanabilir:

```bash
sudo nano /etc/httpd/conf.d/ratelimit.conf
```

Örnek dosya, kök konumu altında bant genişliğini 600 KB/sn olarak sınırlar:

```
<IfModule mod_ratelimit.c>
    <Location />
        SetOutputFilter RATE_LIMIT
        SetEnv rate-limit 600
    </Location>
</IfModule>
```

### <a name="long-request-header-fields"></a>Uzun istek üstbilgi alanları

Proxy sunucusu varsayılan ayarları genellikle istek üstbilgi alanları 8.190 bayt ile sınırlandırın. Bir uygulama varsayılan dan daha uzun alanlar gerektirebilir (örneğin, [Azure Etkin Dizinkullanan](https://azure.microsoft.com/services/active-directory/)uygulamalar). Daha uzun alanlar gerekiyorsa, proxy sunucusunun [LimitRequestFieldSize](https://httpd.apache.org/docs/2.4/mod/core.html#LimitRequestFieldSize) yönergesi ayarlama gerektirir. Uygulanacak değer senaryoya bağlıdır. Daha fazla bilgi için sunucunuzun belgelerine bakın.

> [!WARNING]
> Gerekli `LimitRequestFieldSize` olmadıkça varsayılan değerini artırmayın. Değeri artırmak, arabellek taşması (taşması) ve kötü amaçlı kullanıcıların Hizmet Reddi (DoS) saldırılarına riskini artırır.

## <a name="additional-resources"></a>Ek kaynaklar

* [Linux'ta .NET Core için ön koşullar](/dotnet/core/linux-prerequisites)
* <xref:test/troubleshoot>
* <xref:host-and-deploy/proxy-load-balancer>
