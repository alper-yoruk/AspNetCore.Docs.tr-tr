---
title: ASP.NET Core webassembly 'ı barındırma ve dağıtma Blazor
author: guardrex
description: BlazorASP.NET Core, Içerik teslim ağları (CDN), dosya sunucuları ve GitHub sayfalarını kullanarak bir uygulamayı nasıl barındırılacağını ve dağıtacağınızı öğrenin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/28/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/blazor/webassembly
ms.openlocfilehash: 09f74edaa3d1cb0d51e0ce8d0209383885b81f5f
ms.sourcegitcommit: cd73744bd75fdefb31d25ab906df237f07ee7a0a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/05/2020
ms.locfileid: "84239387"
---
# <a name="host-and-deploy-aspnet-core-blazor-webassembly"></a>ASP.NET Core webassembly 'ı barındırma ve dağıtma Blazor

[Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), [ben Adams](https://twitter.com/ben_a_adams)ve [Safia Abdalla](https://safia.rocks) tarafından

[ Blazor Webassembly barındırma modeliyle](xref:blazor/hosting-models#blazor-webassembly):

* BlazorUygulama, bağımlılıkları ve .NET çalışma zamanı, tarayıcıya paralel olarak indirilir.
* Uygulama doğrudan tarayıcı kullanıcı arabirimi iş parçacığında yürütülür.

Aşağıdaki dağıtım stratejileri desteklenir:

* BlazorUygulama, bir ASP.NET Core uygulaması tarafından sunulur. Bu strateji [ASP.NET Core bölümünde barındırılan dağıtımda](#hosted-deployment-with-aspnet-core) ele alınmıştır.
* BlazorUygulama, bir statik barındırma Web sunucusuna veya hizmetine yerleştirilir, burada .NET uygulamayı sunmak için kullanılmaz Blazor . Bu strateji [tek başına dağıtım](#standalone-deployment) bölümünde ele alınmıştır. Bu, bir Blazor WEBASSEMBLY uygulamasını bir IIS alt uygulaması olarak barındırma hakkında bilgiler içerir.

## <a name="precompression"></a>Ön sıkıştırma

BlazorWebassembly uygulaması yayımlandığında, çıkış, uygulamanın boyutunu azaltmak ve çalışma zamanı sıkıştırması gereksinimini ortadan kaldırmak için önceden sıkıştırılır. Aşağıdaki sıkıştırma algoritmaları kullanılır:

* [Brotli](https://tools.ietf.org/html/rfc7932) (en yüksek düzey)
* [Gzip](https://tools.ietf.org/html/rfc1952)

Sıkıştırmayı devre dışı bırakmak için `BlazorEnableCompression` uygulamanın proje dosyasına MSBuild özelliğini ekleyin ve değeri şu şekilde ayarlayın `false` :

```xml
<PropertyGroup>
  <BlazorEnableCompression>false</BlazorEnableCompression>
</PropertyGroup>
```

IIS *Web. config* sıkıştırma yapılandırması için [IIS: Brotli ve gzip sıkıştırma](#brotli-and-gzip-compression) bölümüne bakın.

## <a name="rewrite-urls-for-correct-routing"></a>Doğru yönlendirme için URL 'Leri yeniden yazın

Webassembly uygulamasındaki sayfa bileşenlerine yönelik yönlendirme istekleri Blazor Blazor , sunucuda, barındırılan bir uygulamada yönlendirme istekleri kadar basittir. Blazorİki bileşeni olan bir webassembly uygulaması düşünün:

* *Main. Razor*: uygulamanın köküne yükler ve `About` bileşene () bir bağlantı içerir `href="About"` .
* *. Razor*: `About` bileşeni hakkında.

Uygulamanın varsayılan belgesi, tarayıcının adres çubuğu kullanılarak istendiğinde (örneğin, `https://www.contoso.com/` ):

1. Tarayıcı bir istek yapar.
1. Varsayılan sayfa döndürülür, bu genellikle *index. html*'dir.
1. uygulamanın *index. html* önyükleme.
1. Blazoruygulamasının yönlendirici yüklenir ve Razor `Main` bileşen işlenir.

Ana sayfada, `About` Blazor yönlendirici tarayıcıyı Internet 'te için bir istek yapmayı durdurduğundan `www.contoso.com` `About` ve Işlenmiş `About` bileşenin kendisini sunan ana sayfada, istemci üzerinde çalışır. * Blazor Webassembly uygulamasındaki* iç uç noktalara yönelik tüm istekler aynı şekilde çalışır: istekler tarayıcı tabanlı istekleri Internet 'teki sunucu tarafından barındırılan kaynaklara tetiklemez. Yönlendirici istekleri dahili olarak işler.

Tarayıcının adres çubuğu kullanılarak bir istek yapılırsa `www.contoso.com/About` , istek başarısız olur. Uygulamanın Internet ana bilgisayarında böyle bir kaynak yok, bu nedenle *404-bulunamayan* bir yanıt döndürülür.

Tarayıcılar, istemci tarafı sayfaları için Internet tabanlı konaklara istek yaptığından, Web sunucuları ve barındırma hizmetleri, fiziksel olarak sunucu üzerinde olmayan kaynakların tüm isteklerini *Dizin. html* sayfasına yeniden yazmalıdır. *İndex. html* döndürüldüğünde, uygulamanın Blazor yönlendiricisi doğru kaynakla sürer ve yanıt verir.

Bir IIS sunucusuna dağıtım yaparken, URL yeniden yazma modülünü uygulamanın yayınlanan *Web. config* dosyasıyla birlikte kullanabilirsiniz. Daha fazla bilgi için [IIS](#iis) bölümüne bakın.

## <a name="hosted-deployment-with-aspnet-core"></a>ASP.NET Core ile barındırılan dağıtım

*Barındırılan dağıtım* , Blazor webassembly uygulamasını Web sunucusunda çalışan bir [ASP.NET Core](xref:index) uygulamasından tarayıcılara sunar.

İstemci Blazor weelsembly uygulaması, sunucu uygulamasının */bin/Release/{Target Framework}/Publish/Wwwroot* klasöründe, sunucu uygulamasının diğer statik Web varlıklarıyla birlikte yayımlanır. İki uygulama birlikte dağıtılır. ASP.NET Core uygulamasını barındırabilen bir Web sunucusu gereklidir. Barındırılan bir dağıtım için Visual Studio, ** Blazor ** `blazorwasm` **barındırılan** seçenek seçili olarak ( [DotNet yeni](/dotnet/core/tools/dotnet-new) komut kullanılırken şablon), webassembly uygulama projesi şablonunu (komut kullanılırken) içerir `-ho|--hosted` `dotnet new` .

Uygulama barındırma ve dağıtım ASP.NET Core hakkında daha fazla bilgi için bkz <xref:host-and-deploy/index> ..

Azure App Service dağıtma hakkında daha fazla bilgi için bkz <xref:tutorials/publish-to-azure-webapp-using-vs> ..

## <a name="standalone-deployment"></a>Tek başına dağıtım

*Tek başına dağıtım* , Blazor webassembly uygulamasına doğrudan istemciler tarafından istenen statik dosyalar kümesi olarak hizmet verir. Herhangi bir statik dosya sunucusu, Blazor uygulamayı sunabilir.

Tek başına dağıtım varlıkları */BIN/Release/{Target Framework}/Publish/Wwwroot* klasöründe yayımlanır.

### <a name="azure-app-service"></a>Azure App Service

BlazorWebAssembly uygulamaları, uygulamayı [IIS](#iis)üzerinde barındıran Windows üzerinde Azure Uygulama Hizmetleri 'ne dağıtılabilir.

BlazorLinux için Azure App Service tek başına webassembly uygulaması dağıtmak Şu anda desteklenmemektedir. Uygulamayı barındıracak bir Linux sunucu görüntüsü şu anda kullanılamıyor. Bu senaryoyu etkinleştirmek için iş devam ediyor.

### <a name="iis"></a>IIS

IIS, uygulamalar için özellikli bir statik dosya sunucusudur Blazor . IIS 'yi barındıracak şekilde yapılandırmak için Blazor bkz. [IIS 'de statik Web sitesi oluşturma](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).

Yayımlanan varlıklar */BIN/Release/{Target Framework}/Publish* klasöründe oluşturulur. Web sunucusunda veya barındırma hizmetinde *Yayımlama* klasörünün içeriğini barındırın.

#### <a name="webconfig"></a>Web. config

Bir Blazor Proje yayımlandığında, AŞAĞıDAKI IIS yapılandırmasıyla bir *Web. config* dosyası oluşturulur:

* MIME türleri aşağıdaki dosya uzantıları için ayarlanır:
  * *. dll*:`application/octet-stream`
  * *. JSON*:`application/json`
  * *...*:`application/wasm`
  * *. WOFF*:`application/font-woff`
  * *. woff2*:`application/font-woff`
* Aşağıdaki MIME türleri için HTTP sıkıştırması etkindir:
  * `application/octet-stream`
  * `application/wasm`
* URL yeniden yazma modülü kuralları oluşturuldu:
  * Uygulamanın statik varlıklarının bulunduğu alt dizini (*Wwwroot/{Path istenen}*) sunar.
  * Dosya olmayan varlıklar için isteklerin statik varlıklar klasöründe (*Wwwroot/index.html*) uygulamanın varsayılan belgesine yeniden YÖNLENDIRILMESI için Spa geri dönüş yönlendirmesi oluşturun.
  
#### <a name="use-a-custom-webconfig"></a>Özel Web. config kullanma

Özel *Web. config* dosyasını kullanmak için, özel *Web. config* dosyasını proje klasörünün köküne yerleştirin ve projeyi yayımlayın.

#### <a name="install-the-url-rewrite-module"></a>URL yeniden yazma modülünü yükler

[URL yeniden yazma modülü](https://www.iis.net/downloads/microsoft/url-rewrite) , URL 'leri yeniden yazmak için gereklidir. Modül varsayılan olarak yüklenmez ve bir Web sunucusu (IIS) rol hizmeti özelliği olarak yükleme için kullanılamaz. Modül IIS Web sitesinden indirilmelidir. Modülünü yüklemek için Web Platformu Yükleyicisi 'ni kullanın:

1. Yerel olarak, [URL yeniden yazma modülü İndirmeleri sayfasına](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads)gidin. Ingilizce sürümünde, WebPI yükleyicisini indirmek için **WebPI** ' ı seçin. Diğer diller için, yükleyiciyi indirmek üzere sunucu için uygun mimariyi (x86/x64) seçin.
1. Yükleyiciyi sunucuya kopyalayın. Yükleyiciyi çalıştırın. , **Install** düğmesini seçin ve lisans koşullarını kabul edin. Yüklemesi tamamlandıktan sonra sunucu yeniden başlatması gerekli değildir.

#### <a name="configure-the-website"></a>Web sitesini yapılandırma

Web sitesinin **fiziksel yolunu** uygulamanın klasörüne ayarlayın. Klasör şunları içerir:

* Gerekli yeniden yönlendirme kuralları ve dosya içerik türleri dahil olmak üzere IIS 'nin Web sitesini yapılandırmak için kullandığı *Web. config* dosyası.
* Uygulamanın statik varlık klasörü.

#### <a name="host-as-an-iis-sub-app"></a>IIS alt uygulaması olarak barındırma

Tek başına bir uygulama bir IIS alt uygulaması olarak barındırılıyorsa, aşağıdakilerden birini yapın:

* Devralınan ASP.NET Core modülü işleyicisini devre dışı bırakın.

  BlazorDosyaya bir bölüm ekleyerek uygulamanın yayınlanan *Web. config* dosyasındaki işleyiciyi kaldırın `<handlers>` :

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* Şu `<system.webServer>` şekilde ayarlanmış bir öğe kullanarak kök (üst) uygulamanın devralınmasını devre dışı bırakın `<location>` `inheritInChildApplications` `false` :

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

İşleyicinin kaldırılması veya devralma devre dışı bırakılması, [uygulamanın temel yolunun yapılandırılmasına](xref:host-and-deploy/blazor/index#app-base-path)ek olarak gerçekleştirilir. Uygulamanın *index. html* dosyasındaki uygulama temel yolunu, IIS 'de alt uygulamayı YAPıLANDıRıRKEN kullanılan IIS diğer adına ayarlayın.

#### <a name="brotli-and-gzip-compression"></a>Brotli ve gzip sıkıştırması

IIS, *Web. config* aracılığıyla Brotli veya gzip sıkıştırılan varlıkları sunacak şekilde yapılandırılabilir Blazor . Örnek bir yapılandırma için bkz. [Web. config](webassembly/_samples/web.config?raw=true).

#### <a name="troubleshooting"></a>Sorun giderme

*500-Iç sunucu hatası* ALıNMıŞSA ve IIS Yöneticisi Web sitesinin yapılandırmasına erişmeye çalışırken hatalar OLUŞTURURSA, URL yeniden yazma modülünün yüklü olduğunu doğrulayın. Modül yüklü olmadığında, *Web. config* dosyası IIS tarafından ayrıştırılamaz. Bu, IIS yöneticisinin Web sitesinin yapılandırmasını ve Web sitesini, statik dosyaları hizmet olarak yüklemesini engeller Blazor .

IIS ile dağıtım sorunlarını giderme hakkında daha fazla bilgi için bkz <xref:test/troubleshoot-azure-iis> ..

### <a name="azure-storage"></a>Azure Storage

[Azure depolama](/azure/storage/) statik dosya barındırma, sunucusuz Blazor uygulama barındırmayı sağlar. Özel etki alanı adları, Azure Content Delivery Network (CDN) ve HTTPS desteklenir.

Blob hizmeti bir depolama hesabında barındırılan statik Web sitesi için etkinleştirildiğinde:

* **Dizin belgesi adını** olarak ayarlayın `index.html` .
* **Hata belge yolunu** olarak ayarlayın `index.html` . Razorbileşenler ve diğer dosya olmayan uç noktaları, blob hizmeti tarafından depolanan statik içerikte fiziksel yollarda yer vermez. Yönlendiricinin işlemesi gereken bu kaynaklardan birine yönelik bir istek alındığında Blazor , blob hizmeti tarafından oluşturulan *404-bulunamayan* hata, isteği **hata belge yoluna**yönlendirir. *İndex. html* blobu döndürülür ve Blazor yönlendirici yolu yükler ve işler.

Dosyalar ' üst bilgilerinde uygunsuz MIME türleri nedeniyle çalışma zamanında dosya yüklenmemişse `Content-Type` , aşağıdaki eylemlerden birini gerçekleştirin:

* Araçlar, dosyalar dağıtıldığında doğru MIME türlerini (üstbilgiler) ayarlamak üzere yapılandırın `Content-Type` .
* `Content-Type`Uygulama dağıtıldıktan sonra dosyalar IÇIN MIME türlerini (üstbilgiler) değiştirin.

  Her dosya için Depolama Gezgini (Azure portal):
  
  1. Dosyaya sağ tıklayın ve **Özellikler**' i seçin.
  1. **ContentType** ' ı ayarlayın ve **Kaydet** düğmesini seçin.

Daha fazla bilgi için bkz. [Azure Storage 'Da statik Web sitesi barındırma](/azure/storage/blobs/storage-blob-static-website).

### <a name="nginx"></a>Nginx

Aşağıdaki *NGINX. conf* dosyası, NGINX 'in, disk üzerinde karşılık gelen bir dosyayı bulamadığı her seferinde *index. html* dosyasını göndermek üzere nasıl yapılandırılacağını gösterecek şekilde basitleştirilmiştir.

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

Üretim NGINX web sunucusu yapılandırması hakkında daha fazla bilgi için bkz. [NGINX Plus ve NGINX yapılandırma dosyaları oluşturma](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).

### <a name="nginx-in-docker"></a>Docker 'da NGINX

BlazorNGINX kullanarak Docker 'da barındırmak Için Dockerfile 'ı alp tabanlı NGINX görüntüsünü kullanacak şekilde ayarlayın. Dockerfile dosyasını, *NGINX. config* dosyasını kapsayıcıya kopyalamak için güncelleştirin.

Aşağıdaki örnekte gösterildiği gibi Dockerfile dosyasına bir satır ekleyin:

```dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="apache"></a>Apache

BlazorCenelsembly uygulamasını CentOS 7 veya sonraki bir sürüme dağıtmak için:

1. Apache yapılandırma dosyasını oluşturun. Aşağıdaki örnek basitleştirilmiş bir yapılandırma dosyasıdır (*blazorapp. config*):

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

1. Apache yapılandırma dosyasını `/etc/httpd/conf.d/` , CentOS 7 ' de varsayılan Apache yapılandırma dizini olan dizine yerleştirin.

1. Uygulamanın dosyalarını `/var/www/blazorapp` dizine yerleştirin ( `DocumentRoot` yapılandırma dosyasında belirtilen konum).

1. Apache hizmetini yeniden başlatın.

Daha fazla bilgi için bkz. [mod_mime](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) ve [mod_deflate](https://httpd.apache.org/docs/current/mod/mod_deflate.html).

### <a name="github-pages"></a>GitHub sayfaları

URL yeniden işlemesini işlemek için, isteği *index. html* sayfasına yönlendirmeyi işleyen bir betiği olan bir *404. html* dosyası ekleyin. Topluluk tarafından sunulan örnek bir uygulama için bkz. [GitHub sayfaları Için tek sayfalı uygulamalar](https://spa-github-pages.rafrex.com/) ([GitHub üzerinde rafrex/Spa-GitHub-Pages](https://github.com/rafrex/spa-github-pages#readme)). Topluluk yaklaşımını kullanan bir örnek, GitHub ([canlı site](https://blazor-demo.github.io/)) [üzerinde blazor-demo/blazor-demo. GitHub. IO](https://github.com/blazor-demo/blazor-demo.github.io) adresinde görülebilir.

Bir kuruluş sitesi yerine bir proje sitesi kullanırken `<base>` etiketi *index. html*dosyasına ekleyin veya güncelleştirin. `href`Öznitelik değerini GitHub deposu adına sondaki eğik çizgiyle (örneğin,) ayarlayın `my-repository/` .

## <a name="host-configuration-values"></a>Ana bilgisayar yapılandırma değerleri

[ Blazor Webassembly uygulamaları](xref:blazor/hosting-models#blazor-webassembly) , geliştirme ortamındaki çalışma zamanında aşağıdaki ana bilgisayar yapılandırma değerlerini komut satırı bağımsız değişkenleri olarak kabul edebilir.

### <a name="content-root"></a>İçerik kökü

`--contentroot`Bağımsız değişkeni, uygulamanın içerik dosyalarını ([içerik kökü](xref:fundamentals/index#content-root)) içeren dizinin mutlak yolunu ayarlar. Aşağıdaki örneklerde, `/content-root-path` uygulamanın içerik kök yolu bulunur.

* Uygulamayı bir komut isteminde yerel olarak çalıştırırken bağımsız değişkenini geçirin. Uygulamanın dizininden şunu yürütün:

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* **IIS Express** profilindeki uygulamanın *launchsettings. JSON* dosyasına bir giriş ekleyin. Bu ayar, uygulama Visual Studio hata ayıklayıcısı ve ile bir komut isteminden çalıştırıldığında kullanılır `dotnet run` .

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* Visual Studio 'da, **Özellikler**  >  **hata ayıklama**  >  **uygulama bağımsız değişkenlerinde**bağımsız değişkenini belirtin. Visual Studio özellik sayfasında bağımsız değişkeni ayarlama, bağımsız değişkenini *Launchsettings. JSON* dosyasına ekler.

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a>Yol tabanı

`--pathbase`Bağımsız değişkeni, kök olmayan göreli BIR URL yoluyla yerel olarak çalışan bir uygulamanın uygulama temeli yolunu ayarlar ( `<base>` etiket, `href` `/` hazırlama ve üretim için dışında bir yola ayarlanır). Aşağıdaki örneklerde, `/relative-URL-path` uygulamanın yol tabanı bulunur. Daha fazla bilgi için bkz. [uygulama temel yolu](xref:host-and-deploy/blazor/index#app-base-path).

> [!IMPORTANT]
> Etiketinde belirtilen yolun aksine `href` `<base>` , `/` bağımsız değişken değeri geçirilirken sondaki eğik çizgi () eklemeyin `--pathbase` . Etikette uygulama temel yolu `<base>` `<base href="/CoolApp/">` (sondaki eğik çizgi içeriyorsa) olarak sağlanmışsa, komut satırı bağımsız değişken değerini `--pathbase=/CoolApp` (sondaki eğik çizgi yok) olarak geçirin.

* Uygulamayı bir komut isteminde yerel olarak çalıştırırken bağımsız değişkenini geçirin. Uygulamanın dizininden şunu yürütün:

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* **IIS Express** profilindeki uygulamanın *launchsettings. JSON* dosyasına bir giriş ekleyin. Bu ayar, uygulamayı Visual Studio hata ayıklayıcıyla ve ile bir komut isteminden çalıştırırken kullanılır `dotnet run` .

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* Visual Studio 'da, **Özellikler**  >  **hata ayıklama**  >  **uygulama bağımsız değişkenlerinde**bağımsız değişkenini belirtin. Visual Studio özellik sayfasında bağımsız değişkeni ayarlama, bağımsız değişkenini *Launchsettings. JSON* dosyasına ekler.

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a>URL’ler

`--urls`Bağımsız değişkeni, istekler için dinlemek üzere bağlantı noktaları ve protokollerle IP adreslerini veya konak adreslerini ayarlar.

* Uygulamayı bir komut isteminde yerel olarak çalıştırırken bağımsız değişkenini geçirin. Uygulamanın dizininden şunu yürütün:

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* **IIS Express** profilindeki uygulamanın *launchsettings. JSON* dosyasına bir giriş ekleyin. Bu ayar, uygulamayı Visual Studio hata ayıklayıcıyla ve ile bir komut isteminden çalıştırırken kullanılır `dotnet run` .

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* Visual Studio 'da, **Özellikler**  >  **hata ayıklama**  >  **uygulama bağımsız değişkenlerinde**bağımsız değişkenini belirtin. Visual Studio özellik sayfasında bağımsız değişkeni ayarlama, bağımsız değişkenini *Launchsettings. JSON* dosyasına ekler.

  ```console
  --urls=http://127.0.0.1:0
  ```

## <a name="configure-the-linker"></a>Bağlayıcıyı yapılandırma

Blazorçıkış derlemelerinden gereksiz Il 'yi kaldırmak için her sürüm derlemesinde ara dil (IL) bağlamayı gerçekleştirir. Daha fazla bilgi için bkz. <xref:host-and-deploy/blazor/configure-linker>.

## <a name="custom-boot-resource-loading"></a>Özel önyükleme kaynağı yükleme

Blazor `loadBootResource` Yerleşik önyükleme kaynağı yükleme mekanizmasını geçersiz kılmak için bir webassembly uygulaması işleviyle başlatılabilir. `loadBootResource`Aşağıdaki senaryolar için kullanın:

* Kullanıcıların bir CDN 'den saat dilimi verileri veya *DotNet. IStream* gibi statik kaynakları yüklemesine izin verin.
* Bir HTTP isteği kullanarak sıkıştırılmış derlemeler yükleyin ve sunucudan sıkıştırılmış içerik getirmeyi desteklemeyen konaklar için istemcide sıkıştırmasını açın.
* Her isteği yeni bir ada yönlendirerek farklı bir ada diğer ad kaynakları `fetch` .

`loadBootResource`Parametreler aşağıdaki tabloda görüntülenir.

| Parametre    | Açıklama |
| ------------ | ----------- |
| `type`       | Kaynağın türü. İzinleri olan türler: `assembly` , `pdb` , `dotnetjs` , `dotnetwasm` ,`timezonedata` |
| `name`       | Kaynağın adı. |
| `defaultUri` | Kaynağın göreli veya mutlak URI 'SI. |
| `integrity`  | Yanıtta beklenen içeriği temsil eden bütünlük dizesi. |

`loadBootResource`yükleme işlemini geçersiz kılmak için aşağıdakilerden herhangi birini döndürür:

* URI dizesi. Aşağıdaki örnekte (*Wwwroot/index.html*), aşağıdaki dosyalar ŞURADA bir CDN 'den sunulur `https://my-awesome-cdn.com/` :

  * *DotNet. \* . JS*
  * *DotNet.*
  * Saat dilimi verileri

  ```html
  ...

  <script src="_framework/blazor.webassembly.js" autostart="false"></script>
  <script>
    Blazor.start({
      loadBootResource: function (type, name, defaultUri, integrity) {
        console.log(`Loading: '${type}', '${name}', '${defaultUri}', '${integrity}'`);
        switch (type) {
          case 'dotnetjs':
          case 'dotnetwasm':
          case 'timezonedata':
            return `https://my-awesome-cdn.com/blazorwebassembly/3.2.0/${name}`;
        }
      }
    });
  </script>
  ```

* `Promise<Response>`. `integrity`Varsayılan bütünlük denetimi davranışını korumak için parametreyi bir üstbilgiye geçirin.

  Aşağıdaki örnek (*Wwwroot/index.html*) giden isteklere özel bir http üst bilgisi ekler ve `integrity` parametresini `fetch` çağrıya geçirir:
  
  ```html
  <script src="_framework/blazor.webassembly.js" autostart="false"></script>
  <script>
    Blazor.start({
      loadBootResource: function (type, name, defaultUri, integrity) {
        return fetch(defaultUri, { 
          cache: 'no-cache',
          integrity: integrity,
          headers: { 'MyCustomHeader': 'My custom value' }
        });
      }
    });
  </script>
  ```

* `null`/`undefined`Bu, varsayılan yükleme davranışına neden olur.

Dış kaynaklar, tarayıcılar arası kaynak yüklemeye izin vermek için gereken CORS üst bilgilerini döndürmelidir. CDNs, genellikle gerekli üst bilgileri varsayılan olarak sağlar.

Yalnızca özel davranışlar için türleri belirtmeniz yeterlidir. İçin belirtilmemiş türler `loadBootResource` , varsayılan yükleme davranışları başına Framework tarafından yüklenir.

## <a name="change-the-filename-extension-of-dll-files"></a>DLL dosyalarının dosya adı uzantısını değiştirme

Uygulamanın yayımlanmış *. dll* dosyalarının dosya adı uzantılarını değiştirmeniz gerekiyorsa, bu bölümdeki yönergeleri izleyin.

Uygulamayı yayımladıktan sonra, *. dll* dosyalarını farklı bir dosya uzantısı kullanacak şekilde yeniden adlandırmak için bir kabuk betiği veya DevOps derleme işlem hattı kullanın. Uygulamanın yayımlanmış çıktısının *Wwwroot* dizinindeki *. dll* dosyalarını hedefleyin (ÖRNEĞIN, *{Content root}/bin/Release/netstandard2.1/Publish/Wwwroot*).

Aşağıdaki örneklerde,. *DLL* dosyaları *. bin* dosya uzantısını kullanacak şekilde yeniden adlandırılır.

Windows'da:

```powershell
dir .\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content .\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content .\_framework\blazor.boot.json
```

Hizmet çalışanı varlıkları da kullanılıyorsa, aşağıdaki komutu ekleyin:

```powershell
((Get-Content .\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content .\service-worker-assets.js
```

Linux veya macOS 'ta:

```console
for f in _framework/_bin/*; do mv "$f" "`echo $f | sed -e 's/\.dll\b/.bin/g'`"; done
sed -i 's/\.dll"/.bin"/g' _framework/blazor.boot.json
```

Hizmet çalışanı varlıkları da kullanılıyorsa, aşağıdaki komutu ekleyin:

```console
sed -i 's/\.dll"/.bin"/g' service-worker-assets.js
```
   
*. Bin*' den farklı bir dosya uzantısı kullanmak için, önceki komutlarda *. bin yerine.*

Sıkıştırılmış *blazor. Boot. JSON. gz* ve *blazor.Boot.JSON.br* dosyalarını ele almak için aşağıdaki yaklaşımlardan birini benimseyin:

* Sıkıştırılmış *blazor. Boot. JSON. gz* ve *blazor.Boot.JSON.br* dosyalarını kaldırın. Sıkıştırma bu yaklaşım ile devre dışı bırakıldı.
* Güncelleştirilmiş *blazor. Boot. JSON* dosyasını yeniden sıkıştırın.

Yukarıdaki kılavuz, hizmet çalışanı varlıkları kullanımda olduğunda da geçerlidir. *Wwwroot/Service-Worker-varlıklar. js. br* ve *Wwwroot/Service-Worker-assets. js. gz*'yi kaldırın veya yeniden sıkıştırın. Aksi halde, tarayıcıda dosya bütünlüğü denetimleri başarısız olur.

Aşağıdaki Windows örneği, projenin köküne yerleştirilmiş bir PowerShell betiği kullanır.

*ChangeDLLExtensions. ps1:*:

```powershell
param([string]$filepath,[string]$tfm)
dir $filepath\bin\Release\$tfm\wwwroot\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json
Remove-Item $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json.gz
```

Hizmet çalışanı varlıkları da kullanılıyorsa, aşağıdaki komutu ekleyin:

```powershell
((Get-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js
```

Proje dosyasında, komut dosyası uygulama yayımlandıktan sonra çalıştırılır:

```xml
<Target Name="ChangeDLLFileExtensions" AfterTargets="Publish" Condition="'$(Configuration)'=='Release'">
  <Exec Command="powershell.exe -command &quot;&amp; { .\ChangeDLLExtensions.ps1 '$(SolutionDir)' '$(TargetFramework)'}&quot;" />
</Target>
```

Geri bildirim sağlamak için, [#5477 aspnetcore/sorunlar](https://github.com/dotnet/aspnetcore/issues/5477)' ı ziyaret edin.
 
