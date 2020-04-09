---
title: Host ASP.NET Core Nginx ile Linux üzerinde
author: rick-anderson
description: Http trafiğini Kestrel'de çalışan bir ASP.NET Core web uygulamasına iletmek için Nginx'i Ubuntu 16.04'te ters proxy olarak nasıl kurarak kurabilirsiniz.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/05/2020
uid: host-and-deploy/linux-nginx
ms.openlocfilehash: 320a5364efe85b06028d8e80000e3455bb8ebd18
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78657915"
---
# <a name="host-aspnet-core-on-linux-with-nginx"></a>Host ASP.NET Core Nginx ile Linux üzerinde

Yazar: [Sourabh Shirhatti](https://twitter.com/sshirhatti)

Bu kılavuz, ubuntu 16.04 sunucusunda üretime hazır ASP.NET Core ortamının ayarlanması açıklanmaktadır. Bu yönergeler büyük olasılıkla Ubuntu'nun yeni sürümleriyle çalışır, ancak yönergeler yeni sürümlerle sınanmamıştır.

ASP.NET Core tarafından desteklenen diğer Linux dağıtımları hakkında bilgi için [Linux'ta .NET Core için Ön koşullara](/dotnet/core/linux-prerequisites)bakın.

> [!NOTE]
> Ubuntu 14.04 için Kerkenez işleminin izlenmesi için bir çözüm olarak *denetlenmesi* önerilir. *sistemli* Ubuntu 14.04'te kullanılamaz. Ubuntu 14.04 yönergeleri için [bu konunun önceki sürümüne](https://github.com/dotnet/AspNetCore.Docs/blob/e9c1419175c4dd7e152df3746ba1df5935aaafd5/aspnetcore/publishing/linuxproduction.md)bakın.

Bu kılavuz:

* Varolan bir ASP.NET Core uygulamasını ters proxy sunucusunun arkasına yerleştirir.
* İstenilenleri Kestrel web sunucusuna iletmek için ters proxy sunucusunu ayarlar.
* Web uygulamasının başlangıç olarak daemon olarak çalıştırMasını sağlar.
* Web uygulamasını yeniden başlatmaya yardımcı olacak bir işlem yönetimi aracını yapılandırır.

## <a name="prerequisites"></a>Ön koşullar

1. Sudo ayrıcalığına sahip standart bir kullanıcı hesabı olan bir Ubuntu 16.04 sunucusuna erişim.
1. Sunucuya .NET Core çalışma saatini yükleyin.
   1. İndir [.NET Çekirdek sayfasını](https://dotnet.microsoft.com/download/dotnet-core)ziyaret edin.
   1. En son önizleme olmayan .NET Core sürümünü seçin.
   1. Uygulamaları Çalıştır - Runtime altında tabloda en son önizleme olmayan çalışma **süresini indirin.**
   1. Linux **Paketi yöneticisi talimatları** bağlantısını seçin ve Ubuntu sürümünüz için Ubuntu yönergelerini izleyin.
1. Mevcut bir ASP.NET Core uygulaması.

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

Uygulamayı test edin:

1. Komut satırından uygulamayı çalıştırın: `dotnet <app_assembly>.dll`.
1. Bir tarayıcıda, `http://<serveraddress>:<port>` uygulamanın Linux'ta yerel olarak çalıştığını doğrulamak için gidin.

## <a name="configure-a-reverse-proxy-server"></a>Ters proxy sunucusu yapılandırma

Ters proxy, dinamik web uygulamaları için ortak bir kurulumdur. Ters proxy, HTTP isteğini sonlandırır ve ASP.NET Core uygulamasına iletir.

### <a name="use-a-reverse-proxy-server"></a>Ters proxy sunucusu kullanma

Kerkenez, ASP.NET Core'dan dinamik içerik sunan harika bir yerdir. Ancak, web hizmet özellikleri IIS, Apache veya Nginx gibi sunucular kadar zengin özellik değildir. Ters proxy sunucusu, http sunucusundan statik içerik sunma, istekleri önbelleğe alma, istekleri sıkıştırma ve HTTPS sonlandırma gibi işleri boşaltabilir. Ters proxy sunucusu özel bir makinede bulunabilir veya bir HTTP sunucusunun yanında dağıtılabilir.

Bu kılavuzun amaçları için, Nginx tek bir örnek kullanılır. HTTP sunucusunun yanında aynı sunucuda çalışır. Gereksinimlere bağlı olarak, farklı bir kurulum seçilebilir.

İstekler ters proxy ile iletilir olduğundan, [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) paketinden [Iletili Üstbilgi Middleware](xref:host-and-deploy/proxy-load-balancer) kullanın. Orta `Request.Scheme`yazılım, üstbilgi `X-Forwarded-Proto` kullanarak, URI'leri ve diğer güvenlik ilkelerini yeniden yönlendirmenin doğru çalışmasını güncelleştirir.

Kimlik doğrulama, bağlantı oluşturma, yeniden yönlendirmeler ve coğrafi konum gibi şemaya bağlı olan tüm bileşen, İlliyeli Üstbilgi Middleware'i çağırdıktan sonra yerleştirilmelidir. Genel bir kural olarak, Iletili Üstbilgi Middleware tanılama ve hata işleme middleware dışında diğer ara önce çalışması gerekir. Bu sıralama, iletilen üstbilgiler bilgilerine güvenen ara yazılımın işlem için üstbilgi değerlerini tüketmesini sağlar.

Arama <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> dan `Startup.Configure` veya benzer kimlik doğrulama düzeni ara ware önce yöntemi çağırın. Orta yazılımı ve `X-Forwarded-For` üstbilgiileri `X-Forwarded-Proto` iletecek şekilde yapılandırın:

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

### <a name="install-nginx"></a>Nginx'i yükleyin

Nginx yüklemek için kullanın. `apt-get` Yükleyici, sistem başlatmada Nginx'i daemon olarak çalıştıran *sistemli* bir init komut dosyası oluşturur. Nginx de Ubuntu için kurulum talimatları [izleyin: Resmi Debian / Ubuntu paketleri](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/#official-debian-ubuntu-packages).

> [!NOTE]
> İsteğe bağlı Nginx modülleri gerekiyorsa, kaynaktan Nginx bina gerekli olabilir.

Nginx ilk kez kurulduğundan, açıkça çalıştırarak başlatın:

```bash
sudo service nginx start
```

Bir tarayıcının Nginx için varsayılan açılış sayfasını görüntülenin. Açılış sayfasına ' `http://<server_IP_address>/index.nginx-debian.html`dan ulaşılabilir.

### <a name="configure-nginx"></a>Nginx hizmetini yapılandırma

Nginx'i ASP.NET Core uygulamanıza istekleri iletmek için ters proxy olarak yapılandırmak için, */etc/nginx/sites-available/default adresini değiştirin.* Bir metin düzenleyicisinde açın ve içeriği aşağıdakilerle değiştirin:

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

Uygulama SignalR WebSockets'e dayanan bir Blazor Server <xref:host-and-deploy/blazor/server#linux-with-nginx> uygulamasıysa, üstbilginin `Connection` nasıl ayarlanabildiğini öğrenin.

Eşleşme `server_name` olmadığında, Nginx varsayılan sunucuyu kullanır. Varsayılan sunucu tanımlanmamışsa, yapılandırma dosyasındaki ilk sunucu varsayılan sunucudur. En iyi yöntem olarak, yapılandırma dosyanızda 444'lük bir durum kodu döndüren belirli bir varsayılan sunucu ekleyin. Varsayılan sunucu yapılandırma örneği:

```nginx
server {
    listen   80 default_server;
    # listen [::]:80 default_server deferred;
    return   444;
}
```

Önceki yapılandırma dosyası ve varsayılan sunucu ile Nginx ana bilgisayar üstbilgi `example.com` veya `*.example.com`. Bu ana bilgisayarlarla eşleşmeyen istekler Kestrel'e iletilmeyecek. Nginx eşleşen istekleri Kerkenez'e `http://localhost:5000`. [Nginx'in daha](https://nginx.org/docs/http/request_processing.html) fazla bilgi isteğini nasıl işlediğini görün. Kerkenez'in IP/bağlantı noktasını değiştirmek için [Kerkenez: Bitiş Noktası yapılandırmasına](xref:fundamentals/servers/kestrel#endpoint-configuration)bakın.

> [!WARNING]
> Uygun bir [server_name yönergesi](https://nginx.org/docs/http/server_names.html) belirtilmemesi, uygulamanızı güvenlik açıklarına maruz bırakır. Alt etki alanı joker karakter `*.example.com`bağlama (örneğin, ) tüm üst etki alanını denetlerseniz (bunun `*.com`aksine, savunmasız dır) bu güvenlik riski oluşturmaz. Daha fazla bilgi için [rfc7230 bölüm-5.4'e](https://tools.ietf.org/html/rfc7230#section-5.4) bakın.

Nginx yapılandırması kurulduktan sonra, yapılandırma dosyalarının sözdizimini doğrulamak için çalıştırın. `sudo nginx -t` Yapılandırma dosyası testi başarılı olursa, Nginx'i çalıştırarak `sudo nginx -s reload`değişiklikleri almaya zorlar.

Uygulamayı doğrudan sunucuda çalıştırmak için:

1. Uygulamanın dizinine gidin.
1. Uygulamayı çalıştırın: `dotnet <app_assembly.dll>` `app_assembly.dll` , uygulamanın montaj dosyası adı nerededir.

Uygulama sunucuda çalışıyorsa ancak Internet üzerinden yanıt veremediyse, sunucunun güvenlik duvarını kontrol edin ve bağlantı noktası 80'in açık olduğunu onaylayın. Bir Azure Ubuntu VM kullanıyorsanız, gelen bağlantı noktası 80 trafiğine olanak tanıyan bir Ağ Güvenlik Grubu (NSG) kuralı ekleyin. Giden kural etkinleştirildiğinden, giden trafik otomatik olarak verildiğinden, giden bağlantı noktası 80 kuralını etkinleştirmeye gerek yoktur.

Uygulamayı test etmek bittiğinde, uygulamayı `Ctrl+C` komut istemiyle kapatın.

## <a name="monitor-the-app"></a>Uygulamayı izleme

Sunucu, kestrel'de `http://127.0.0.1:5000` `http://<serveraddress>:80` çalışan ASP.NET Core uygulamasına yapılan istekleri iletmek için kurulumdur. Ancak, Nginx Kerkenez işlemini yönetmek için kurulmaz. *sistemli* başlatmak ve altta yatan web uygulamasını izlemek için bir hizmet dosyası oluşturmak için kullanılabilir. *sistemli,* süreçleri başlatmak, durdurmak ve yönetmek için birçok güçlü özellik sağlayan bir init sistemidir. 

### <a name="create-the-service-file"></a>Hizmet dosyasını oluşturma

Hizmet tanımı dosyasını oluşturun:

```bash
sudo nano /etc/systemd/system/kestrel-helloapp.service
```

Uygulama için örnek bir hizmet dosyası aşağıda verilmiştir:

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

Önceki örnekte, hizmeti yöneten kullanıcı `User` seçeneği tarafından belirtilir. Kullanıcı (`www-data`) var olmalı ve uygulamanın dosyalarının düzgün sahipliğine sahip olmalıdır.

Uygulamanın ilk kesme sinyalini aldıktan sonra kapanmasını beklemek için süreyi yapılandırmak için kullanın. `TimeoutStopSec` Bu süre içinde uygulama kapanmazsa, uygulamayı sonlandırmak için SIGKILL verilir. Değeri birimsiz saniye (örneğin, `150`), zaman aralığı değeri (örneğin, `infinity` `2min 30s`) olarak veya zaman aralığını devre dışı düşürün. `TimeoutStopSec``DefaultTimeoutStopSec` yönetici yapılandırma dosyasındaki değere varsayılanlar (*systemd-system.conf*, *system.conf.d*, *systemd-user.conf*, *user.conf.d*). Çoğu dağıtım için varsayılan zaman ası 90 saniyedir.

```
# The default value is 90 seconds for most distributions.
TimeoutStopSec=90
```

Linux'un büyük/küçük harf duyarlı bir dosya sistemi vardır. ASPNETCORE_ENVIRONMENT "Üretim" olarak ayarlamak, yapılandırma dosyası ayarlarının aranmasıyla *sonuçlanır. Production.json*, *appsettings.production.json*değil .

Yapılandırma sağlayıcılarının ortam değişkenlerini okuyabilmesi için bazı değerlerin (örneğin, SQL bağlantı dizeleri) kaçılması gerekir. Yapılandırma dosyasında kullanılmak üzere düzgün bir şekilde kaçan bir değer oluşturmak için aşağıdaki komutu kullanın:

```console
systemd-escape "<value-to-escape>"
```

Kolon`:`( ) ayırıcıları çevre değişken adlarında desteklenmez. Bir kolon yerine`__`bir çift alt çizgi ( ) kullanın. [Çevre Değişkenleri yapılandırma sağlayıcısı,](xref:fundamentals/configuration/index#environment-variables-configuration-provider) ortam değişkenleri yapılandırmaya okunduğunda çift alt puanları iki nokta üst üste dönüştürür. Aşağıdaki örnekte, bağlantı dizesi anahtarı `ConnectionStrings:DefaultConnection` hizmet tanım `ConnectionStrings__DefaultConnection`dosyasına şu şekilde ayarlanır:

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

● kestrel-helloapp.service - Example .NET Web API App running on Ubuntu
    Loaded: loaded (/etc/systemd/system/kestrel-helloapp.service; enabled)
    Active: active (running) since Thu 2016-10-18 04:09:35 NZDT; 35s ago
Main PID: 9021 (dotnet)
    CGroup: /system.slice/kestrel-helloapp.service
            └─9021 /usr/local/bin/dotnet /var/www/helloapp/helloapp.dll
```

Ters proxy yapılandırılan ve Kestrel sistemli aracılığıyla yönetilen ile, web uygulaması tamamen yapılandırılmışve yerel makinede bir tarayıcıdan erişilebilir `http://localhost`. Ayrıca, engellenen herhangi bir güvenlik duvarı hariç, uzak bir makineden erişilebilir. Yanıt üstaylarını inceleyen `Server` üstbilgi, Kestrel tarafından sunulan ASP.NET Core uygulamasını gösterir.

```text
HTTP/1.1 200 OK
Date: Tue, 11 Oct 2016 16:22:23 GMT
Server: Kestrel
Keep-Alive: timeout=5, max=98
Connection: Keep-Alive
Transfer-Encoding: chunked
```

### <a name="view-logs"></a>Günlükleri görüntüleme

Kestrel'i kullanan web uygulaması `systemd`kullanılarak yönetildiği için, tüm olaylar ve işlemler merkezi bir günlükte günlüğe kaydedilir. Ancak, bu günlük tarafından `systemd`yönetilen tüm hizmetler ve işlemler için tüm girişleri içerir. `kestrel-helloapp.service`-belirli öğeleri görüntülemek için aşağıdaki komutu kullanın:

```bash
sudo journalctl -fu kestrel-helloapp.service
```

Daha fazla filtreleme için, `--since today` `--until 1 hour ago` bu gibi zaman seçenekleri veya bunların bir kombinasyonu döndürülen giriş miktarını azaltabilir.

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

## <a name="long-request-header-fields"></a>Uzun istek üstbilgi alanları

Proxy sunucusu varsayılan ayarları genellikle platforma bağlı olarak istek üstbilgisi alanlarını 4 K veya 8 K ile sınırlandırırlar. Bir uygulama varsayılan dan daha uzun alanlar gerektirebilir (örneğin, [Azure Etkin Dizinkullanan](https://azure.microsoft.com/services/active-directory/)uygulamalar). Daha uzun alanlar gerekiyorsa, proxy sunucusunun varsayılan ayarları ayarlama gerektirir. Uygulanacak değerler senaryoya bağlıdır. Daha fazla bilgi için sunucunuzun belgelerine bakın.

* [proxy_buffer_size](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_buffer_size)
* [proxy_buffers](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_buffers)
* [proxy_busy_buffers_size](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_busy_buffers_size)
* [large_client_header_buffers](https://nginx.org/docs/http/ngx_http_core_module.html#large_client_header_buffers)

> [!WARNING]
> Gerekli olmadıkça proxy arabelleklerinin varsayılan değerlerini artırmayın. Bu değerlerin artırılması, arabellek taşması (taşması) ve kötü amaçlı kullanıcıların Hizmet Reddi (DoS) saldırılarına riskini artırır.

## <a name="secure-the-app"></a>Uygulamayı güvenli hale

### <a name="enable-apparmor"></a>AppArmor etkinleştirin

Linux Güvenlik Modülleri (LSM), Linux 2.6'dan beri Linux çekirdeğinin bir parçası olan bir çerçevedir. LSM, güvenlik modüllerinin farklı uygulamalarını destekler. [AppArmor,](https://wiki.ubuntu.com/AppArmor) programın sınırlı bir kaynak kümesiyle sınırlanmasına olanak tanıyan bir Zorunlu Erişim Kontrol sistemi uygulayan bir LSM'dir. AppArmor'un etkin olduğundan ve doğru şekilde yapılandırıldığından emin olun.

### <a name="configure-the-firewall"></a>Güvenlik duvarını yapılandırma

Kullanılmayan tüm harici bağlantı noktalarını kapatın. Karmaşık olmayan güvenlik duvarı (ufw), `iptables` güvenlik duvarını yapılandırmak için bir CLI sağlayarak ön uç sağlar.

> [!WARNING]
> Güvenlik duvarı, doğru şekilde yapılandırılmazsa tüm sisteme erişimi engeller. Doğru SSH bağlantı noktasının belirtilmemesi, bağlantı kurmak için SSH kullanıyorsanız sizi etkin bir şekilde sistemin dışına kilitler. Varsayılan bağlantı noktası 22'dir. Daha fazla bilgi [için, ufw](https://help.ubuntu.com/community/UFW) ve [kılavuzuna](https://manpages.ubuntu.com/manpages/bionic/man8/ufw.8.html)giriş bakın.

Gerekli `ufw` tüm bağlantı noktalarında trafiğe izin verecek şekilde yükleyin ve yapılandırın.

```bash
sudo apt-get install ufw

sudo ufw allow 22/tcp
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp

sudo ufw enable
```

### <a name="secure-nginx"></a>Güvenli Nginx

#### <a name="change-the-nginx-response-name"></a>Nginx yanıt adını değiştirme

*Src/http/ngx_http_header_filter_module.c'yi*edit:

```
static char ngx_http_server_string[] = "Server: Web Server" CRLF;
static char ngx_http_server_full_string[] = "Server: Web Server" CRLF;
```

#### <a name="configure-options"></a>Seçenekleri yapılandırma

Sunucuyu gerekli ek modüllerle yapılandırın. Uygulamayı sertleştirmek için [ModSecurity](https://www.modsecurity.org/)gibi bir web uygulaması güvenlik duvarı kullanmayı düşünün.

#### <a name="https-configuration"></a>HTTPS yapılandırması

**Uygulamayı güvenli (HTTPS) yerel bağlantılar için yapılandırın**

[Dotnet çalıştır](/dotnet/core/tools/dotnet-run) komutu, uygulamanın özellik tarafından sağlanan URL'leri dinleyecek şekilde yapılandıran *Özellikler/launchSettings.json* dosyasını `applicationUrl` kullanır (örneğin, `https://localhost:5001;http://localhost:5000`).

Aşağıdaki yaklaşımlardan birini kullanarak uygulamayı `dotnet run` komut veya geliştirme ortamı (Visual Studio Code'ta F5 veya Ctrl+F5) için geliştirme aşamasında bir sertifika kullanacak şekilde yapılandırın:

* [Yapılandırmadan varsayılan sertifikayı değiştirme](xref:fundamentals/servers/kestrel#configuration) (*Önerilen*)
* [KestrelServerOptions.ConfigureHttpsDefaults](xref:fundamentals/servers/kestrel#configurehttpsdefaultsactionhttpsconnectionadapteroptions)

**Güvenli (HTTPS) istemci bağlantıları için ters proxy'yi yapılandırın**

* Güvenilir bir Sertifika Yetkilisi (CA) `443` tarafından verilen geçerli bir sertifika belirterek sunucuyu bağlantı noktasındaki HTTPS trafiğini dinleyecek şekilde yapılandırın.

* Aşağıdaki */etc/nginx/nginx.conf* dosyasında gösterilen bazı uygulamaları uygulayarak güvenliği güçlendirin. Örnekler arasında daha güçlü bir şifre seçmek ve HTTP üzerindeki tüm trafiği HTTPS'ye yönlendirmek sayılabilir.

* `HTTP Strict-Transport-Security` (HSTS) üstbilgi eklemek, istemci tarafından yapılan sonraki tüm isteklerin HTTPS üzerinde olmasını sağlar.

* Gelecekte HTTPS devre dışı bırakılırsa HSTS üstbilgisini eklemeyin veya uygun `max-age` bir üstbilgi seçmeyin.

*/etc/nginx/proxy.conf* yapılandırma dosyasını ekleyin:

[!code-nginx[](linux-nginx/proxy.conf)]

*/etc/nginx/nginx.conf* yapılandırma dosyasını düzenleme. Örnek, tek `http` `server` bir yapılandırma dosyasında hem hem de bölümleri içerir.

[!code-nginx[](linux-nginx/nginx.conf?highlight=2)]

#### <a name="secure-nginx-from-clickjacking"></a>Clickjacking güvenli Nginx

*UI telafi saldırısı*olarak da bilinen [Clickjacking,](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger)bir web sitesi ziyaretçisi şu anda ziyaret ettiklerinden farklı bir sayfadaki bir bağlantıyı veya düğmeyi tıklatmak için kandırıldığı kötü amaçlı bir saldırıdır. Siteyi `X-FRAME-OPTIONS` güvenli hale getirmek için kullanın.

Tıklama kaçırma saldırılarını azaltmak için:

1. *Nginx.conf* dosyasını edin:

   ```bash
   sudo nano /etc/nginx/nginx.conf
   ```

   `add_header X-Frame-Options "SAMEORIGIN";` satırını ekleyin.
1. Dosyayı kaydedin.
1. Nginx'i yeniden başlatın.

#### <a name="mime-type-sniffing"></a>MIME tipi koklama

Üstbilgi, üstbilgi tarayıcıya yanıt içeriği türünü geçersiz kılmamasını söylerken, bu üstbilgi çoğu tarayıcının bildirilen içerik türünden uzakta bir yanıtı mime koklamasını önler. `nosniff` Sunucu, içeriğin "text/html" olduğunu söylüyorsa, tarayıcı içeriği "text/html" olarak işler.

*Nginx.conf* dosyasını edin:

```bash
sudo nano /etc/nginx/nginx.conf
```

Satırı `add_header X-Content-Type-Options "nosniff";` ekleyin ve dosyayı kaydedin, sonra Nginx'i yeniden başlatın.

## <a name="additional-nginx-suggestions"></a>Ek Nginx önerileri

Sunucuda paylaşılan çerçeveyi yükselttikten sonra, sunucu tarafından barındırılan ASP.NET Core uygulamalarını yeniden başlatın.

## <a name="additional-resources"></a>Ek kaynaklar

* [Linux'ta .NET Core için ön koşullar](/dotnet/core/linux-prerequisites)
* [Nginx: İkili Bültenleri: Resmi Debian / Ubuntu paketleri](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/#official-debian-ubuntu-packages)
* <xref:test/troubleshoot>
* <xref:host-and-deploy/proxy-load-balancer>
* [NGINX: İlerleyen üstbilginin kullanılması](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/)
