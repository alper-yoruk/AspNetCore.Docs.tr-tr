---
title: Core Blazor WebAssemblyASP.NET ana bilgisayar ve dağıtma
author: guardrex
description: ASP.NET Core, Content Blazor Delivery Networks (CDN), dosya sunucuları ve GitHub Sayfalarını kullanarak bir uygulamayı nasıl barındıracağınızı ve dağıtacağınızı öğrenin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/06/2020
no-loc:
- Blazor
- SignalR
uid: host-and-deploy/blazor/webassembly
ms.openlocfilehash: f364d94085d175fde5596c222ef21852c0106ec1
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80751125"
---
# <a name="host-and-deploy-aspnet-core-opno-locblazor-webassembly"></a>Core Blazor WebAssemblyASP.NET ana bilgisayar ve dağıtma

Luke [Latham](https://github.com/guardrex)tarafından , [Rainer Stropek](https://www.timecockpit.com), ve [Daniel Roth](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[ Blazor WebAssembly barındırma modeli](xref:blazor/hosting-models#blazor-webassembly)ile:

* Uygulama, Blazor bağımlılıkları ve .NET çalışma süresi tarayıcıya indirilir.
* Uygulama doğrudan tarayıcı UI iş parçacığı üzerinde yürütülür.

Aşağıdaki dağıtım stratejileri desteklenir:

* Uygulama, Blazor ASP.NET Core uygulaması tarafından sunulmaktadır. Bu strateji, [ASP.NET Core bölümüyle Barındırılan dağıtımda](#hosted-deployment-with-aspnet-core) ele alınmıştır.
* Uygulama, Blazor .NET'in uygulamaya hizmet vermek için kullanılmadığı statik bir Blazor barındırma web sunucusuna veya hizmetine yerleştirilir. Bu strateji, Bir [Standalone deployment](#standalone-deployment) Blazor WebAssembly uygulamasını IIS alt uygulaması olarak barındırma hakkında bilgi içeren Bağımsız dağıtım bölümünde ele alınmıştır.

## <a name="rewrite-urls-for-correct-routing"></a>Doğru yönlendirme için URL'leri yeniden yazma

Bir Blazor WebAssembly uygulamasındaki sayfa bileşenleri isteklerini yönlendirme, barındırılan bir Blazor sunucudaki yönlendirme istekleri kadar basit değildir. İki Blazor bileşenden bir WebAssembly uygulaması düşünün:

* *Main.razor* &ndash; Yükler uygulamanın kökünde ve `About` bileşene bir`href="About"`bağlantı içerir ( ).
* *Jilet* &ndash; `About` bileşeni hakkında.

Uygulamanın varsayılan belgesi tarayıcının adres çubuğu kullanılarak istendiğinde (örneğin,): `https://www.contoso.com/`

1. Tarayıcı bir istekte bulundu.
1. Varsayılan sayfa döndürülür, genellikle *index.html*.
1. *index.html* uygulamayı bootstraps.
1. Blazor''nin yönlendirici yükleri `Main` ve Razor bileşeni işlenir.

`About` Ana sayfada, bileşenin bağlantısını seçmek istemcide çalışır, Blazor çünkü yönlendirici tarayıcının Internet'te istekte `www.contoso.com` `About` bulunmasını durdurur `About` ve işlenen bileşenin kendisi için hizmet eder. *WebAssembly uygulamasındaki Blazor * dahili uç noktalara yönelik tüm istekler aynı şekilde çalışır: İstekler, Internet'te sunucu tarafından barındırılan kaynaklara tarayıcı tabanlı istekleri tetiklemez. Yönlendirici istekleri dahili olarak işler.

Tarayıcının adres çubuğu kullanılarak bir istek `www.contoso.com/About`yapılırsa, istek başarısız olur. Uygulamanın Internet ana bilgisayarında böyle bir kaynak bulunmadığından, *404 - Bulunamadı* yanıtı döndürülür.

Tarayıcılar istemci tarafı sayfaları için Internet tabanlı ana bilgisayarlara istekte bulunduğundan, web sunucuları ve barındırma hizmetleri sunucudaki kaynaklara yönelik tüm istekleri ni fiziksel olarak *index.html* sayfasına yeniden yazmalıdır. *index.html* döndürüldüğünde, uygulamanın Blazor yönlendiricisi devreye girer ve doğru kaynakla yanıt verir.

Bir IIS sunucusuna dağıtırken, uygulamanın yayınlanan *web.config* dosyasıyla URL Yeniden Yazma Modül'üni kullanabilirsiniz. Daha fazla bilgi için [IIS](#iis) bölümüne bakın.

## <a name="hosted-deployment-with-aspnet-core"></a>ASP.NET Core ile barındırılan dağıtım

*Barındırılan dağıtım,* WebAssembly uygulamasına Blazor bir web sunucusunda çalışan bir ASP.NET Core [uygulamasından](xref:index) tarayıcılara hizmet eder.

İstemci Blazor WebAssembly uygulaması sunucu uygulamasının */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* klasöründe, sunucu uygulamasının diğer statik web varlıklarıyla birlikte yayınlanır. İki uygulama birlikte dağıtılır. ASP.NET Core uygulaması barındırma yeteneğine sahip bir web sunucusu gereklidir. Barındırılan`blazorwasm` bir dağıtım için Visual Studio,`-ho|--hosted` `dotnet new` ** Blazor WebAssembly App** proje şablonunu [(dotnet yeni](/dotnet/core/tools/dotnet-new) komutunu kullanırken şablon) içerir ve **Barındırılan** seçeneği (komutu kullanırken) seçilir.

ASP.NET Core uygulaması barındırma ve dağıtımı <xref:host-and-deploy/index>hakkında daha fazla bilgi için bkz.

Azure Uygulama Hizmetine dağıtım hakkında <xref:tutorials/publish-to-azure-webapp-using-vs>daha fazla bilgi için bkz.

## <a name="standalone-deployment"></a>Bağımsız dağıtım

*Bağımsız bir dağıtım,* WebAssembly uygulamasına Blazor doğrudan istemciler tarafından istenen statik dosyalar kümesi olarak hizmet verir. Herhangi bir statik dosya sunucusu Blazor uygulamaya hizmet verebiliyor.

Bağımsız dağıtım varlıkları */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* klasöründe yayımlanır.

### <a name="iis"></a>IIS

IIS uygulamalar için Blazor yetenekli bir statik dosya sunucusudur. IIS'yi barındıracak Blazorşekilde yapılandırmak için [bkz.](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis)

Yayınlanan varlıklar */bin/Release/{TARGET FRAMEWORK}/publish* klasöründe oluşturulur. *Yayımlama* klasörünün içeriğini web sunucusunda veya barındırma hizmetinde barındırın.

#### <a name="webconfig"></a>Config

Bir Blazor proje yayımlandığında, aşağıdaki IIS yapılandırmasıyla bir *web.config* dosyası oluşturulur:

* MIME türleri aşağıdaki dosya uzantıları için ayarlanır:
  * *.dll* &ndash;`application/octet-stream`
  * *.json* &ndash;`application/json`
  * *.wasm* &ndash;`application/wasm`
  * *.woff* &ndash;`application/font-woff`
  * *.woff2* &ndash;`application/font-woff`
* Aşağıdaki MIME türleri için HTTP sıkıştırma etkinleştirilir:
  * `application/octet-stream`
  * `application/wasm`
* URL Yeniden Yazma Modülü kuralları belirlenir:
  * Uygulamanın statik varlıklarının bulunduğu alt dizine hizmet *(wwwroot/{PATH REQUESTed}*).
  * Dosya dışı varlıklara yönelik isteklerin, statik varlıklar klasöründe *(wwwroot/index.html)* uygulamanın varsayılan belgesine yönlendirilmesi için SPA geri dönüş yönlendirmesini oluşturun.
  
#### <a name="use-a-custom-webconfig"></a>Özel bir web.config kullanın

Özel bir *web.config* dosyası kullanmak için:

1. Özel *web.config* dosyasını proje klasörünün köküne yerleştirin.
1. Proje dosyasına aşağıdaki hedefi ekleyin (*.csproj):*

   ```xml
   <Target Name="CopyWebConfigOnPublish" AfterTargets="Publish">
     <Copy SourceFiles="web.config" DestinationFolder="$(PublishDir)" />
   </Target>
   ```
   
> [!NOTE]
> IIS'e `<IsWebConfigTransformDisabled>` `true` dağıtılan ASP.NET Core uygulamaları için Blazor olduğu [gibi,](xref:host-and-deploy/iis/index#webconfig-file)WebAssembly uygulamalarında da desteklenmez. Daha fazla bilgi için bkz: [Özel Blazor WASM web.config (dotnet/aspnetcore #20569) sağlamak için gereken kopya hedef.](https://github.com/dotnet/aspnetcore/issues/20569)

#### <a name="install-the-url-rewrite-module"></a>URL Yeniden Yazma Modül'e yükleme

[URL Yeniden Yazma Modülü](https://www.iis.net/downloads/microsoft/url-rewrite) URL'leri yeniden yazmak için gereklidir. Modül varsayılan olarak yüklü değildir ve Web Sunucusu (IIS) rol hizmeti özelliği olarak yüklemek için kullanılamaz. Modül IIS web sitesinden indirilmelidir. Modülü yüklemek için Web Platform Yükleyicisini kullanın:

1. Yerel olarak, [URL Yeniden Yazma Modülü indirme sayfasına](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads)gidin. İngilizce sürüm için **WebPI** yükleyicisini indirmek için WebPI'yi seçin. Diğer diller için, yükleyiciyi indirmek için sunucu (x86/x64) için uygun mimariyi seçin.
1. Yükleyiciyi sunucuya kopyalayın. Yükleyiciyi çalıştırın. **Yükle** düğmesini seçin ve lisans koşullarını kabul edin. Yükleme tamamlandıktan sonra sunucu yeniden başlatması gerekmez.

#### <a name="configure-the-website"></a>Web sitesini yapılandırma

Web sitesinin **Fiziksel yolunu** uygulamanın klasörüne ayarlayın. Klasör şunları içerir:

* IIS'nin web sitesini yapılandırmak için kullandığı *web.config* dosyası, gerekli yönlendirme kuralları ve dosya içeriği türleri de dahil olmak üzere.
* Uygulamanın statik varlık klasörü.

#### <a name="host-as-an-iis-sub-app"></a>IIS alt uygulaması olarak ana bilgisayar

Bağımsız bir uygulama IIS alt uygulaması olarak barındırılırsa, aşağıdakilerden birini gerçekleştirin:

* Devralınan ASP.NET Çekirdek Modülü işleyicisini devre dışı bırak.

  Dosyaya bir `<handlers>` bölüm Blazor ekleyerek uygulamanın yayınlanan *web.config* dosyasındaki işleyiciyi kaldırın:

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* `inheritInChildApplications` Set `false`ile bir `<system.webServer>` `<location>` öğe kullanarak kök (üst) uygulama bölümünün devralma devre dışı bırak:

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <configuration>
    <location path="." inheritInChildApplications="false">
      <system.webServer>
        <handlers>
          <add name="aspNetCore" ... />
        </handlers>
        <aspNetCore ... />
      </system.webServer>
    </location>
  </configuration>
  ```

Uygulamanın [temel yolunu yapılandırmaya](xref:host-and-deploy/blazor/index#app-base-path)ek olarak işleyicinin kaldırılması veya devralmanın devre dışı bırakılması gerçekleştirilir. Uygulamanın *index.html* dosyasındaki uygulama temel yolunu, Alt Uygulamayı IIS'de yapılandırırken kullanılan IIS diğer adıyla ayarlayın.

#### <a name="troubleshooting"></a>Sorun giderme

*500 - Internal Server Hatası* alınırsa ve IIS Yöneticisi web sitesinin yapılandırmasına erişmeye çalışırken hata lar atarsa, URL Yeniden Yazma Modülü'nün yüklü olduğunu onaylayın. Modül yüklenmediği zaman, *web.config* dosyası IIS tarafından ayrıştırılamaz. Bu, IIS Yöneticisi'nin web sitesinin yapılandırmasını ve Blazorweb sitesinin statik dosyalarını yüklemesini engeller.

IIS'deki sorun giderme dağıtımları <xref:test/troubleshoot-azure-iis>hakkında daha fazla bilgi için bkz.

### <a name="azure-storage"></a>Azure Storage

[Azure Depolama](/azure/storage/) statik dosya Blazor barındırma sunucusuz uygulama barındırma sağlar. Özel alan adları, Azure İçerik Dağıtım Ağı (CDN) ve HTTPS desteklenir.

Bir depolama hesabında statik web sitesi barındırma için blob hizmeti etkinleştirildiğinde:

* **Dizin belge** adını `index.html`' a ayarlayın
* Hata **belge yolunu** `index.html`' n için ayarlama Jilet bileşenleri ve diğer dosya dışı uç noktalar, blob hizmeti tarafından depolanan statik içerikteki fiziksel yollarda bulunmaz. Bu kaynaklardan biri için yönlendiricinin Blazor işlemesi gereken bir istek alındığı zaman, blob hizmeti tarafından oluşturulan *404 - Bulunamadı* hatası isteği Hata belge **yoluna**yönlendirir. *Index.html* blob döndürülür ve Blazor yönlendirici yükler ve yol işler.

Daha fazla bilgi için [Azure Depolama'da barındırılmayan Statik web sitesine](/azure/storage/blobs/storage-blob-static-website)bakın.

### <a name="nginx"></a>Nginx

Aşağıdaki *nginx.conf* dosyası, diskte karşılık gelen bir dosya bulamadığında *index.html* dosyasını göndermek için Nginx'in nasıl yapılandırılacağı gösterilebilir.

```
events { }
http {
    server {
        listen 80;

        location / {
            root /usr/share/nginx/html;
            try_files $uri $uri/ /index.html =404;
        }
    }
}
```

Nginx web sunucusu yapılandırması hakkında daha fazla bilgi için [NGINX Plus ve NGINX Configuration Files oluşturma](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/)bilgisine bakın.

### <a name="nginx-in-docker"></a>Docker içinde Nginx

Nginx kullanarak Docker'da barındırmak Blazor için, Alp tabanlı Nginx görüntüsünü kullanmak üzere Dockerfile'ı kurun. *Nginx.config* dosyasını kapsayıcıya kopyalamak için Dockerdosyasını güncelleştirin.

Aşağıdaki örnekte gösterildiği gibi Dockerfile'a bir satır ekleyin:

```dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="apache"></a>Apache

Bir Blazor WebAssembly uygulamasını CentOS 7 veya sonraki lere dağıtmak için:

1. Apache yapılandırma dosyasını oluşturun. Aşağıdaki örnek basitleştirilmiş bir yapılandırma dosyasıdır (*blazorapp.config):*

   ```
   <VirtualHost *:80>
       ServerName www.example.com
       ServerAlias *.example.com

       DocumentRoot "/var/www/blazorapp"
       ErrorDocument 404 /index.html

       AddType application/wasm .wasm
       AddType application/octet-stream .dll
   
       <Directory "/var/www/blazorapp">
           Options -Indexes
           AllowOverride None
       </Directory>

       <IfModule mod_deflate.c>
           AddOutputFilterByType DEFLATE text/css
           AddOutputFilterByType DEFLATE application/javascript
           AddOutputFilterByType DEFLATE text/html
           AddOutputFilterByType DEFLATE application/octet-stream
           AddOutputFilterByType DEFLATE application/wasm
           <IfModule mod_setenvif.c>
           BrowserMatch ^Mozilla/4 gzip-only-text/html
           BrowserMatch ^Mozilla/4.0[678] no-gzip
           BrowserMatch bMSIE !no-gzip !gzip-only-text/html
       </IfModule>
       </IfModule>

       ErrorLog /var/log/httpd/blazorapp-error.log
       CustomLog /var/log/httpd/blazorapp-access.log common
   </VirtualHost>
   ```

1. Apache yapılandırma dosyasını `/etc/httpd/conf.d/` CentOS 7'deki varsayılan Apache yapılandırma dizini olan dizine yerleştirin.

1. Uygulamanın dosyalarını `/var/www/blazorapp` dizine (yapılandırma `DocumentRoot` dosyasında belirtilen konum) yerleştirin.

1. Apache hizmetini yeniden başlatın.

Daha fazla bilgi için [mod_mime](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) ve [mod_deflate](https://httpd.apache.org/docs/current/mod/mod_deflate.html)bakın.

### <a name="github-pages"></a>GitHub Sayfaları

URL yeniden yazma işlemlerini işlemek için, isteği *index.html* sayfasına yönlendirmeyi işleyen bir komut dosyası içeren *bir 404.html* dosyası ekleyin. Topluluk tarafından sağlanan örnek bir uygulama [için, GitHub Sayfaları için Tek Sayfa Uygulamaları](https://spa-github-pages.rafrex.com/) [(GitHub'daki rafrex/spa-github sayfaları)](https://github.com/rafrex/spa-github-pages#readme)sayfasına bakın. Topluluk yaklaşımını kullanan bir örnek [github'daki blazor-demo/blazor-demo.github.io](https://github.com/blazor-demo/blazor-demo.github.io) adresinde görülebilir ([canlı site).](https://blazor-demo.github.io/)

Kuruluş sitesi yerine proje sitesi kullanırken *index.html*etiketini `<base>` ekleyin veya güncelleştirin. `href` Öznitelik değerini GitHub deposu adına sondalı bir eğik çizgiyle `my-repository/`ayarlama (örneğin, .

## <a name="host-configuration-values"></a>Ana bilgisayar yapılandırma değerleri

WebAssembly uygulamaları, geliştirme ortamında çalışma zamanında aşağıdaki ana bilgisayar yapılandırma değerlerini komut satırı bağımsız değişkenleri olarak kabul edebilir. [ Blazor ](xref:blazor/hosting-models#blazor-webassembly)

### <a name="content-root"></a>İçerik kökü

Bağımsız `--contentroot` değişken, uygulamanın içerik dosyalarını[(içerik kökü)](xref:fundamentals/index#content-root)içeren dizin için mutlak yolu ayarlar. Aşağıdaki örneklerde, `/content-root-path` uygulamanın içerik kökü yolu verilmiştir.

* Uygulamayı bir komut istemiyle yerel olarak çalıştırırken bağımsız değişkeni geçirin. Uygulamanın dizininden çalıştırın:

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* **Uygulamanın iIS Express** profilindeki *launchSettings.json* dosyasına bir giriş ekleyin. Bu ayar, uygulama Visual Studio Debugger ile çalıştırıldığında ve `dotnet run`bir komut isteminden kullanılır.

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* Visual Studio'da, **Özellikler** > **Hata Ayıklama** > Uygulaması bağımsız**değişkenindeki**bağımsız değişkeni belirtin. Visual Studio özellik sayfasındaki bağımsız değişkeni ayarlamak, bağımsız değişkeni *launchSettings.json* dosyasına ekler.

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a>Yol tabanı

Bağımsız `--pathbase` değişken, kök olmayan göreli URL yolu ile yerel olarak çalışan `<base>` `href` bir uygulama için uygulama `/` temel yolunu ayarlar (etiket, hazırlama ve üretim dışında bir yola ayarlanır). Aşağıdaki örneklerde, `/relative-URL-path` uygulamanın yol tabanı dır. Daha fazla bilgi için [Uygulama tabanı yoluna](xref:host-and-deploy/blazor/index#app-base-path)bakın.

> [!IMPORTANT]
> Etikete sağlanan yolun aksine, bağımsız değişken değerini geçerken`/`bir çizgi () eklemeyin. `--pathbase` `href` `<base>` Uygulama temel yolu `<base>` etikette (sondaki `<base href="/CoolApp/">` eğik çizgi içerir) sağlanırsa, `--pathbase=/CoolApp` komut satırı bağımsız değişken değerini (izleme çizgisini yok) olarak geçirin.

* Uygulamayı bir komut istemiyle yerel olarak çalıştırırken bağımsız değişkeni geçirin. Uygulamanın dizininden çalıştırın:

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* **Uygulamanın iIS Express** profilindeki *launchSettings.json* dosyasına bir giriş ekleyin. Bu ayar, Visual Studio Debugger ile uygulamayı çalıştırırken ve `dotnet run`bir komut isteminden kullanılır.

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* Visual Studio'da, **Özellikler** > **Hata Ayıklama** > Uygulaması bağımsız**değişkenindeki**bağımsız değişkeni belirtin. Visual Studio özellik sayfasındaki bağımsız değişkeni ayarlamak, bağımsız değişkeni *launchSettings.json* dosyasına ekler.

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a>URL’ler

Bağımsız `--urls` değişken, istekleri dinleyecek bağlantı noktaları ve protokolleri olan IP adreslerini veya ana bilgisayar adreslerini ayarlar.

* Uygulamayı bir komut istemiyle yerel olarak çalıştırırken bağımsız değişkeni geçirin. Uygulamanın dizininden çalıştırın:

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* **Uygulamanın iIS Express** profilindeki *launchSettings.json* dosyasına bir giriş ekleyin. Bu ayar, Visual Studio Debugger ile uygulamayı çalıştırırken ve `dotnet run`bir komut isteminden kullanılır.

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* Visual Studio'da, **Özellikler** > **Hata Ayıklama** > Uygulaması bağımsız**değişkenindeki**bağımsız değişkeni belirtin. Visual Studio özellik sayfasındaki bağımsız değişkeni ayarlamak, bağımsız değişkeni *launchSettings.json* dosyasına ekler.

  ```console
  --urls=http://127.0.0.1:0
  ```

## <a name="configure-the-linker"></a>Bağlayıcıyı yapılandırma

BlazorÇıktı derlemelerinden gereksiz IL'yi kaldırmak için her Sürüm yapısına ara dil (IL) bağlama gerçekleştirir. Daha fazla bilgi için bkz. <xref:host-and-deploy/blazor/configure-linker>.
