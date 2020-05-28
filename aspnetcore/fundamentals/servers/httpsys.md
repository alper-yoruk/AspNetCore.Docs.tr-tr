---
title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

---
# <a name="httpsys-web-server-implementation-in-aspnet-core"></a>ASP.NET Core 'de HTTP. sys Web sunucusu uygulama

[Tom Dykstra](https://github.com/tdykstra) ve [Chris](https://github.com/Tratcher) 'e göre

::: moniker range=">= aspnetcore-3.1"

[Http. sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) , yalnızca Windows üzerinde çalışan [ASP.NET Core için bir Web sunucusudur](xref:fundamentals/servers/index) . HTTP. sys, [Kestrel](xref:fundamentals/servers/kestrel) Server için bir alternatiftir ve Kestrel tarafından sağlamayan bazı özellikler sunar.

> [!IMPORTANT]
> HTTP. sys [ASP.NET Core modülüyle](xref:host-and-deploy/aspnet-core-module) uyumlu DEĞILDIR ve ııs veya IIS Express ile kullanılamaz.

HTTP. sys aşağıdaki özellikleri destekler:

* [Windows Kimlik Doğrulaması](xref:security/authentication/windowsauth)
* Bağlantı noktası Paylaşımı
* SNı ile HTTPS
* TLS üzerinden HTTP/2 (Windows 10 veya üzeri)
* Doğrudan dosya iletimi
* Yanıtları Önbelleğe Alma
* WebSockets (Windows 8 veya üzeri)

Desteklenen Windows sürümleri:

* Windows 7 veya üzeri
* Windows Server 2008 R2 veya sonraki sürümü

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="when-to-use-httpsys"></a>HTTP. sys ne zaman kullanılır

HTTP. sys, şu durumlarda olduğu dağıtımlar için yararlıdır:

* Sunucuyu IIS kullanmadan doğrudan Internet 'e sunmaya gerek vardır.

  ![HTTP. sys doğrudan Internet ile iletişim kurar](httpsys/_static/httpsys-to-internet.png)

* İç dağıtım, [Windows kimlik doğrulaması](xref:security/authentication/windowsauth)gibi Kestrel 'de kullanılamayan bir özelliği gerektirir.

  ![HTTP. sys doğrudan iç ağla iletişim kurar](httpsys/_static/httpsys-to-internal.png)

HTTP. sys pek çok tür saldırılara karşı koruyan ve tam özellikli bir Web sunucusu için sağlamlık, güvenlik ve ölçeklenebilirlik sağlayan çok sayıda teknolojiden oluşur. IIS, HTTP. sys ' nin üstünde HTTP dinleyicisi olarak çalışır.

## <a name="http2-support"></a>HTTP/2 desteği

Aşağıdaki temel gereksinimler karşılanıyorsa ASP.NET Core uygulamalar için [http/2](https://httpwg.org/specs/rfc7540.html) etkinleştirilir:

* Windows Server 2016/Windows 10 veya üzeri
* [Uygulama katmanı protokol anlaşması (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) bağlantısı
* TLS 1,2 veya üzeri bağlantı

Bir HTTP/2 bağlantısı kurulduysa, [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) Reports `HTTP/2` .

HTTP/2 varsayılan olarak etkindir. HTTP/2 bağlantısı kurulmadıysa, bağlantı HTTP/1.1 'ye geri döner. Windows 'un gelecek bir sürümünde http/2 yapılandırma bayrakları http/2 ' yi HTTP. sys ile devre dışı bırakma özelliği de dahil olmak üzere kullanılabilir olacaktır.

## <a name="kernel-mode-authentication-with-kerberos"></a>Kerberos ile çekirdek modu kimlik doğrulaması

HTTP. sys, Kerberos kimlik doğrulama protokolü ile çekirdek modu kimlik doğrulamasına temsilciler. Kullanıcı modu kimlik doğrulaması, Kerberos ve HTTP. sys ile desteklenmez. Makine hesabı, Active Directory alındığı ve kullanıcının kimliğini doğrulamak için istemci tarafından sunucuya iletilen Kerberos belirtecinin/biletinin şifresini çözmek için kullanılmalıdır. Uygulamanın kullanıcısına değil, ana bilgisayar için hizmet asıl adını (SPN) kaydedin.

## <a name="how-to-use-httpsys"></a>HTTP. sys kullanma

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a>ASP.NET Core uygulamasını HTTP. sys kullanacak şekilde yapılandırma

<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*>Ana bilgisayarı oluştururken, gerekli herhangi bir yöntemi belirterek uzantı yöntemini çağırın <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions> . Aşağıdaki örnek, seçeneklerini varsayılan değerlerine ayarlar:

[!code-csharp[](httpsys/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=5-13)]

Ek HTTP. sys yapılandırması, [kayıt defteri ayarları](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows)aracılığıyla işlenir.

**HTTP. sys seçenekleri**

| Özellik | Açıklama | Varsayılan |
| ---
title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

-
title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

---- | ---title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

-
title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

-
title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

------ | :-----: | | [Allowsynchronousio](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | Ve için zaman uyumlu giriş/çıkışa izin verilip verilmeyeceğini `HttpContext.Request.Body` denetleyin `HttpContext.Response.Body` . | `false`| | [Authentication. AllowAnonymous](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | Anonim isteklere izin verin. | `true`| | [Kimlik doğrulaması. şemalar](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | İzin verilen kimlik doğrulama düzenlerini belirtin. Dinleyici elden atılırken önce herhangi bir zamanda değiştirilebilir. Değerler [authenticationdüzenlerinin numaralandırması](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes)tarafından sağlanır: `Basic` ,,, `Kerberos` `Negotiate` `None` , ve `NTLM` . | `None`| | [Enableresponsecaching](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | Uygun üst bilgileri içeren yanıtlar için [çekirdek modu](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) önbelleğe almayı deneyin. Yanıt `Set-Cookie` , `Vary` veya `Pragma` üst bilgileri içermeyebilir. `Cache-Control` `public` Bir ya da değeri ya da bir üst bilgi içermelidir `shared-max-age` `max-age` `Expires` . | `true`| | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | En fazla eşzamanlı kabul sayısı. | 5 &times; [ortam. <br> ProcessorCount](xref:System.Environment.ProcessorCount) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | Kabul edilecek eşzamanlı bağlantı sayısı üst sınırı. `-1`Sonsuz için kullanın. `null`Kayıt defterinin makine genelindeki ayarını kullanmak için kullanın. | `null`<br>(makine genelinde<br>ayar) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | <a href="#maxrequestbodysize">MaxRequestBodySize</a> bölümüne bakın. | 30000000 bayt<br>(~ 28,6 MB) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | Sıraya alınabilen en fazla istek sayısı. | 1000 | | `RequestQueueMode` | Bu, sunucunun istek kuyruğunu oluşturma ve yapılandırmadan sorumlu olup olmadığını veya mevcut bir kuyruğa iliştirilmesinin gerekip gerekmediğini belirtir.<br>Mevcut bir kuyruğa eklenirken, mevcut yapılandırma seçeneklerinin çoğu geçerli değildir. | `RequestQueueMode.Create`| | `RequestQueueName` | HTTP. sys istek kuyruğunun adı. | `null`(Anonim kuyruk) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | İstemci bağlantısının kesilmesinden kaynaklanan yanıt gövdesi yazmasının, özel durumlar oluşturması veya normal şekilde tamamlanması gerektiğini belirtin. | `false`<br>(normal olarak) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager>Kayıt defterinde da YAPıLANDıRıLABILEN http. sys yapılandırmasını kullanıma sunun. Varsayılan değerler de dahil olmak üzere her bir ayar hakkında daha fazla bilgi edinmek için API bağlantılarını izleyin:<ul><li>[TimeoutManager. DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): http sunucusu API 'sinin varlık gövdesini etkin tut bağlantısı üzerinde boşaltmasına izin verilen süre.</li><li>[TimeoutManager. EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): istek varlığı gövdesinin gelmesi için izin verilen süre.</li><li>[TimeoutManager. HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): http sunucusu API 'sinin istek üst bilgisini ayrıştırması için izin verilen süre.</li><li>[TimeoutManager. IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): boştaki bir bağlantı için izin verilen süre.</li><li>[TimeoutManager. MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): yanıt için en düşük gönderme hızı.</li><li>[TimeoutManager. RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): isteğin, uygulamanın onu seçmeden önce istek kuyruğunda kalması için izin verilen süre.</li></ul> |  | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection>Http. sys ile kaydolmak için öğesini belirtin. En yararlı olan [UrlPrefixCollection. Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), koleksiyona bir ön ek eklemek için kullanılır. Bunlar, dinleyici elden atılıyor öncesinde herhangi bir zamanda değiştirilebilir. |  |

<a name="maxrequestbodysize"></a>

**MaxRequestBodySize**

Herhangi bir istek gövdesinin bayt olarak izin verilen en büyük boyutu. Olarak ayarlandığında `null` , en büyük istek gövdesi boyutu sınırsızdır. Bu sınırın, her zaman sınırsız olan yükseltilmiş bağlantıları üzerinde hiçbir etkisi yoktur.

Tek bir ASP.NET Core MVC uygulamasında sınırı geçersiz kılmak için önerilen yöntem, `IActionResult` <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> bir eylem yönteminde özniteliğini kullanmaktır:

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

Uygulama isteği okumayı başlattıktan sonra bir istek üzerindeki sınırı yapılandırmayı denerse bir özel durum oluşur. Özelliğin `IsReadOnly` salt okuma durumunda olup olmadığını göstermek için bir özellik kullanılabilir `MaxRequestBodySize` , yani sınırı yapılandırmak için çok geç.

Uygulamanın istek başına geçersiz kılması gerekiyorsa <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> , şunu kullanın <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature> :

[!code-csharp[](httpsys/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

Visual Studio kullanıyorsanız, uygulamanın IIS veya IIS Express çalıştıracak şekilde yapılandırılmadığından emin olun.

Visual Studio 'da varsayılan başlatma profili IIS Express içindir. Projeyi konsol uygulaması olarak çalıştırmak için, aşağıdaki ekran görüntüsünde gösterildiği gibi seçili profili el ile değiştirin:

![Konsol uygulaması profilini seçin](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a>Windows Server 'ı yapılandırma

1. Uygulamanın açmak için bağlantı noktalarını belirleme ve [Windows Güvenlik Duvarı](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) 'Nı veya [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell CMDLET 'ini kullanarak trafiğin http. sys ' ye ulaşmasını sağlamak için güvenlik duvarı bağlantı noktalarını açın. Aşağıdaki komutlarda ve uygulama yapılandırmasında, 443 numaralı bağlantı noktası kullanılır.

1. Bir Azure sanal makinesine dağıtım yaparken, [ağ güvenlik grubundaki](/azure/virtual-machines/windows/nsg-quickstart-portal)bağlantı noktalarını açın. Aşağıdaki komutlarda ve uygulama yapılandırmasında, 443 numaralı bağlantı noktası kullanılır.

1. Gerekirse, X. 509.440 sertifikalarını edinin ve yükler.

   Windows 'da, [New-SelfSignedCertificate PowerShell cmdlet 'ini](/powershell/module/pkiclient/new-selfsignedcertificate)kullanarak otomatik olarak imzalanan sertifikalar oluşturun. Desteklenmeyen bir örnek için bkz [. UpdateIISExpressSSLForChrome. ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).

   Sunucunun **yerel makine** > **Kişisel** deposunda otomatik olarak imzalanan veya CA imzalı sertifikalar yükler.

1. Uygulama [çerçeveye bağımlı bir dağıtım](/dotnet/core/deploying/#framework-dependent-deployments-fdd)ise, .net core, .NET Framework veya her ikisini de (uygulama .NET Framework hedefleyen bir .NET Core uygulaması ise) yükler.

   * **.NET Core**: uygulama .NET Core gerektiriyorsa .NET Core [Indirmelerinde](https://dotnet.microsoft.com/download) **.NET Core çalışma zamanı** yükleyicisini edinip çalıştırın. Tam SDK 'Yı sunucuya yüklemeyin.
   * **.NET Framework**: uygulama .NET Framework gerektiriyorsa [.NET Framework yükleme kılavuzuna](/dotnet/framework/install/)bakın. Gerekli .NET Framework yüklemesi. En son .NET Framework yükleyicisi [.NET Core İndirmeleri](https://dotnet.microsoft.com/download) sayfasından edinilebilir.

   Uygulama, [kendi içinde bir dağıtım](/dotnet/core/deploying/#self-contained-deployments-scd)ise, uygulama çalışma zamanını dağıtımda içerir. Sunucuda çerçeve yüklemesi gerekmez.

1. Uygulamada URL 'Leri ve bağlantı noktalarını yapılandırın.

   Varsayılan olarak, ASP.NET Core öğesine bağlanır `http://localhost:5000` . URL öneklerini ve bağlantı noktalarını yapılandırmak için seçenekler şunları içerir:

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * `urls`komut satırı bağımsız değişkeni
   * `ASPNETCORE_URLS`ortam değişkeni
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   Aşağıdaki kod örneği, <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> `10.0.0.4` 443 numaralı bağlantı noktasında sunucunun yerel IP adresi ile nasıl kullanılacağını göstermektedir:

   [!code-csharp[](httpsys/samples_snapshot/3.x/Program.cs?highlight=7)]

   Avantajı `UrlPrefixes` , hatalı biçimli ön ekler için hemen bir hata iletisi oluşturulmasından oluşur.

   Ayarları `UrlPrefixes` geçersiz kıl `UseUrls` / `urls` / `ASPNETCORE_URLS` . Bu nedenle, `UseUrls` `urls` ve `ASPNETCORE_URLS` ortam değişkeninin AVANTAJı Kestrel ve http. sys arasında geçiş yapmak daha kolay olabilir.

   HTTP. sys, [http sunucusu API UrlPrefix dize biçimlerini](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)kullanır.

   > [!WARNING]
   > Üst düzey joker karakter bağlamaları ( `http://*:80/` ve `http://+:80` ) kullanılmamalıdır **not** . Üst düzey joker karakter bağlamaları uygulama güvenliği güvenlik açıklarını oluşturur. Bu hem güçlü hem de zayıf Joker karakterlere yöneliktir. Joker karakterler yerine açık konak adlarını veya IP adreslerini kullanın. Alt etki alanı joker bağlantısı (örneğin, `*.mysub.com` ), tüm üst etki alanını ( `*.com` Bu güvenlik açığı olan aksine) kontrol ediyorsanız bir güvenlik riski değildir. Daha fazla bilgi için bkz. [RFC 7230: bölüm 5,4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).

1. Ön ek, sunucudaki URL öneklerini ister.

   HTTP. sys ' yi yapılandırmaya yönelik yerleşik araç *netsh. exe*' dir. *netsh. exe* , URL öneklerini ayırmak ve X. 509.440 sertifikaları atamak için kullanılır. Araç, yönetici ayrıcalıkları gerektirir.

   Uygulamanın URL 'Lerini kaydettirmek için *netsh. exe* aracını kullanın:

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * `<URL>`: Tam nitelikli Tekdüzen Kaynak Bulucu (URL). Joker karakter bağlama kullanmayın. Geçerli bir ana bilgisayar adı veya yerel IP adresi kullanın. *URL, sondaki eğik çizgi içermelidir.*
   * `<USER>`: Kullanıcı veya Kullanıcı grubu adını belirtir.

   Aşağıdaki örnekte sunucusunun yerel IP adresi `10.0.0.4` :

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   Bir URL kaydedildiğinde, araç ile yanıt verir `URL reservation successfully added` .

   Kayıtlı bir URL 'yi silmek için şu `delete urlacl` komutu kullanın:

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. X. 509.440 sertifikalarını sunucusuna kaydedin.

   Uygulamanın sertifikalarını kaydetmek için *netsh. exe* aracını kullanın:

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * `<IP>`: Bağlama için yerel IP adresini belirtir. Joker karakter bağlama kullanmayın. Geçerli bir IP adresi kullanın.
   * `<PORT>`: Bağlama için bağlantı noktasını belirtir.
   * `<THUMBPRINT>`: X. 509.440 sertifika parmak izi.
   * `<GUID>`: Uygulamayı bilgilendirme amacıyla temsil eden geliştirici tarafından oluşturulan bir GUID.

   Başvuru amacıyla, GUID 'yi uygulamada bir paket etiketi olarak depolayın:

   * Visual Studio 'da:
     * **Çözüm Gezgini** ' de uygulamaya sağ tıklayıp **Özellikler**' i seçerek uygulamanın proje özelliklerini açın.
     * **Paket** sekmesini seçin.
     * **Etiketler** alanında oluşturduğunuz GUID 'yi girin.
   * Visual Studio kullanmadığınız durumlarda:
     * Uygulamanın proje dosyasını açın.
     * `<PackageTags>`Oluşturduğunuz GUID ile yeni veya var olan bir özelliği ekleyin `<PropertyGroup>` :

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   Aşağıdaki örnekte:

   * Sunucunun yerel IP adresi `10.0.0.4` .
   * Bir çevrimiçi rastgele GUID Oluşturucu değeri sağlar `appid` .

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   Bir sertifika kaydedildiğinde, araç ile yanıt verir `SSL Certificate successfully added` .

   Bir sertifika kaydını silmek için şu komutu kullanın `delete sslcert` :

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   *Netsh. exe*için başvuru belgeleri:

   * [Köprü Metni Aktarım Protokolü (HTTP) için Netsh komutları](https://technet.microsoft.com/library/cc725882.aspx)
   * [UrlPrefix dizeleri](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)

1. Uygulamayı çalıştırın.

   1024 'den büyük bir bağlantı noktası numarası ile HTTP (HTTPS değil) kullanarak localhost 'a bağlanırken uygulamayı çalıştırmak için yönetici ayrıcalıklarına gerek yoktur. Diğer yapılandırmalarda (örneğin, yerel bir IP adresi veya 443 numaralı bağlantı noktasına bağlama), uygulamayı yönetici ayrıcalıklarıyla çalıştırın.

   Uygulama, sunucunun genel IP adresinde yanıt verir. Bu örnekte, sunucusuna, genel IP adresinde Internet 'ten ulaşılırsa `104.214.79.47` .

   Bu örnekte bir geliştirme sertifikası kullanılır. Tarayıcının güvenilmeyen sertifika uyarısı atlandıktan sonra sayfa güvenli bir şekilde yüklenir.

   ![Uygulamanın dizin sayfasını yüklü olarak gösteren tarayıcı penceresi](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a>Proxy sunucusu ve yük dengeleyici senaryoları

Internet veya şirket ağından gelen isteklerle etkileşime geçen HTTP. sys tarafından barındırılan uygulamalar için, proxy sunucularının ve yük dengeleyiciler 'nin arkasında barındırılırken ek yapılandırma gerekebilir. Daha fazla bilgi için bkz. [proxy sunucularıyla ve yük dengeleyicilerle çalışacak ASP.NET Core yapılandırma](xref:host-and-deploy/proxy-load-balancer).

## <a name="additional-resources"></a>Ek kaynaklar

* [HTTP. sys ile Windows kimlik doğrulamasını etkinleştirme](xref:security/authentication/windowsauth#httpsys)
* [HTTP Sunucusu API 'SI](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx)
* [ASPNET/HttpSysServer GitHub deposu (kaynak kodu)](https://github.com/aspnet/HttpSysServer/)
* [Ana bilgisayar](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

[Http. sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) , yalnızca Windows üzerinde çalışan [ASP.NET Core için bir Web sunucusudur](xref:fundamentals/servers/index) . HTTP. sys, [Kestrel](xref:fundamentals/servers/kestrel) Server için bir alternatiftir ve Kestrel tarafından sağlamayan bazı özellikler sunar.

> [!IMPORTANT]
> HTTP. sys [ASP.NET Core modülüyle](xref:host-and-deploy/aspnet-core-module) uyumlu DEĞILDIR ve ııs veya IIS Express ile kullanılamaz.

HTTP. sys aşağıdaki özellikleri destekler:

* [Windows Kimlik Doğrulaması](xref:security/authentication/windowsauth)
* Bağlantı noktası Paylaşımı
* SNı ile HTTPS
* TLS üzerinden HTTP/2 (Windows 10 veya üzeri)
* Doğrudan dosya iletimi
* Yanıtları Önbelleğe Alma
* WebSockets (Windows 8 veya üzeri)

Desteklenen Windows sürümleri:

* Windows 7 veya üzeri
* Windows Server 2008 R2 veya sonraki sürümü

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="when-to-use-httpsys"></a>HTTP. sys ne zaman kullanılır

HTTP. sys, şu durumlarda olduğu dağıtımlar için yararlıdır:

* Sunucuyu IIS kullanmadan doğrudan Internet 'e sunmaya gerek vardır.

  ![HTTP. sys doğrudan Internet ile iletişim kurar](httpsys/_static/httpsys-to-internet.png)

* İç dağıtım, [Windows kimlik doğrulaması](xref:security/authentication/windowsauth)gibi Kestrel 'de kullanılamayan bir özelliği gerektirir.

  ![HTTP. sys doğrudan iç ağla iletişim kurar](httpsys/_static/httpsys-to-internal.png)

HTTP. sys pek çok tür saldırılara karşı koruyan ve tam özellikli bir Web sunucusu için sağlamlık, güvenlik ve ölçeklenebilirlik sağlayan çok sayıda teknolojiden oluşur. IIS, HTTP. sys ' nin üstünde HTTP dinleyicisi olarak çalışır.

## <a name="http2-support"></a>HTTP/2 desteği

Aşağıdaki temel gereksinimler karşılanıyorsa ASP.NET Core uygulamalar için [http/2](https://httpwg.org/specs/rfc7540.html) etkinleştirilir:

* Windows Server 2016/Windows 10 veya üzeri
* [Uygulama katmanı protokol anlaşması (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) bağlantısı
* TLS 1,2 veya üzeri bağlantı

Bir HTTP/2 bağlantısı kurulduysa, [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) Reports `HTTP/2` .

HTTP/2 varsayılan olarak etkindir. HTTP/2 bağlantısı kurulmadıysa, bağlantı HTTP/1.1 'ye geri döner. Windows 'un gelecek bir sürümünde http/2 yapılandırma bayrakları http/2 ' yi HTTP. sys ile devre dışı bırakma özelliği de dahil olmak üzere kullanılabilir olacaktır.

## <a name="kernel-mode-authentication-with-kerberos"></a>Kerberos ile çekirdek modu kimlik doğrulaması

HTTP. sys, Kerberos kimlik doğrulama protokolü ile çekirdek modu kimlik doğrulamasına temsilciler. Kullanıcı modu kimlik doğrulaması, Kerberos ve HTTP. sys ile desteklenmez. Makine hesabı, Active Directory alındığı ve kullanıcının kimliğini doğrulamak için istemci tarafından sunucuya iletilen Kerberos belirtecinin/biletinin şifresini çözmek için kullanılmalıdır. Uygulamanın kullanıcısına değil, ana bilgisayar için hizmet asıl adını (SPN) kaydedin.

## <a name="how-to-use-httpsys"></a>HTTP. sys kullanma

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a>ASP.NET Core uygulamasını HTTP. sys kullanacak şekilde yapılandırma

<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*>Ana bilgisayarı oluştururken, gerekli herhangi bir yöntemi belirterek uzantı yöntemini çağırın <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions> . Aşağıdaki örnek, seçeneklerini varsayılan değerlerine ayarlar:

[!code-csharp[](httpsys/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=5-13)]

Ek HTTP. sys yapılandırması, [kayıt defteri ayarları](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows)aracılığıyla işlenir.

**HTTP. sys seçenekleri**

| Özellik | Açıklama | Varsayılan |
| ---
title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

-
title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

---- | ---title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

-
title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

-
title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

------ | :-----: | | [Allowsynchronousio](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | Ve için zaman uyumlu giriş/çıkışa izin verilip verilmeyeceğini `HttpContext.Request.Body` denetleyin `HttpContext.Response.Body` . | `false`| | [Authentication. AllowAnonymous](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | Anonim isteklere izin verin. | `true`| | [Kimlik doğrulaması. şemalar](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | İzin verilen kimlik doğrulama düzenlerini belirtin. Dinleyici elden atılırken önce herhangi bir zamanda değiştirilebilir. Değerler [authenticationdüzenlerinin numaralandırması](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes)tarafından sağlanır: `Basic` ,,, `Kerberos` `Negotiate` `None` , ve `NTLM` . | `None`| | [Enableresponsecaching](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | Uygun üst bilgileri içeren yanıtlar için [çekirdek modu](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) önbelleğe almayı deneyin. Yanıt `Set-Cookie` , `Vary` veya `Pragma` üst bilgileri içermeyebilir. `Cache-Control` `public` Bir ya da değeri ya da bir üst bilgi içermelidir `shared-max-age` `max-age` `Expires` . | `true`| | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | En fazla eşzamanlı kabul sayısı. | 5 &times; [ortam. <br> ProcessorCount](xref:System.Environment.ProcessorCount) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | Kabul edilecek eşzamanlı bağlantı sayısı üst sınırı. `-1`Sonsuz için kullanın. `null`Kayıt defterinin makine genelindeki ayarını kullanmak için kullanın. | `null`<br>(makine genelinde<br>ayar) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | <a href="#maxrequestbodysize">MaxRequestBodySize</a> bölümüne bakın. | 30000000 bayt<br>(~ 28,6 MB) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | Sıraya alınabilen en fazla istek sayısı. | 1000 | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | İstemci bağlantısının kesilmesinden kaynaklanan yanıt gövdesi yazmasının, özel durumlar oluşturması veya normal şekilde tamamlanması gerektiğini belirtin. | `false`<br>(normal olarak) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager>Kayıt defterinde da YAPıLANDıRıLABILEN http. sys yapılandırmasını kullanıma sunun. Varsayılan değerler de dahil olmak üzere her bir ayar hakkında daha fazla bilgi edinmek için API bağlantılarını izleyin:<ul><li>[TimeoutManager. DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): http sunucusu API 'sinin varlık gövdesini etkin tut bağlantısı üzerinde boşaltmasına izin verilen süre.</li><li>[TimeoutManager. EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): istek varlığı gövdesinin gelmesi için izin verilen süre.</li><li>[TimeoutManager. HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): http sunucusu API 'sinin istek üst bilgisini ayrıştırması için izin verilen süre.</li><li>[TimeoutManager. IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): boştaki bir bağlantı için izin verilen süre.</li><li>[TimeoutManager. MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): yanıt için en düşük gönderme hızı.</li><li>[TimeoutManager. RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): isteğin, uygulamanın onu seçmeden önce istek kuyruğunda kalması için izin verilen süre.</li></ul> |  | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection>Http. sys ile kaydolmak için öğesini belirtin. En yararlı olan [UrlPrefixCollection. Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), koleksiyona bir ön ek eklemek için kullanılır. Bunlar, dinleyici elden atılıyor öncesinde herhangi bir zamanda değiştirilebilir. |  |

<a name="maxrequestbodysize"></a>

**MaxRequestBodySize**

Herhangi bir istek gövdesinin bayt olarak izin verilen en büyük boyutu. Olarak ayarlandığında `null` , en büyük istek gövdesi boyutu sınırsızdır. Bu sınırın, her zaman sınırsız olan yükseltilmiş bağlantıları üzerinde hiçbir etkisi yoktur.

Tek bir ASP.NET Core MVC uygulamasında sınırı geçersiz kılmak için önerilen yöntem, `IActionResult` <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> bir eylem yönteminde özniteliğini kullanmaktır:

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

Uygulama isteği okumayı başlattıktan sonra bir istek üzerindeki sınırı yapılandırmayı denerse bir özel durum oluşur. Özelliğin `IsReadOnly` salt okuma durumunda olup olmadığını göstermek için bir özellik kullanılabilir `MaxRequestBodySize` , yani sınırı yapılandırmak için çok geç.

Uygulamanın istek başına geçersiz kılması gerekiyorsa <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> , şunu kullanın <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature> :

[!code-csharp[](httpsys/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

Visual Studio kullanıyorsanız, uygulamanın IIS veya IIS Express çalıştıracak şekilde yapılandırılmadığından emin olun.

Visual Studio 'da varsayılan başlatma profili IIS Express içindir. Projeyi konsol uygulaması olarak çalıştırmak için, aşağıdaki ekran görüntüsünde gösterildiği gibi seçili profili el ile değiştirin:

![Konsol uygulaması profilini seçin](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a>Windows Server 'ı yapılandırma

1. Uygulamanın açmak için bağlantı noktalarını belirleme ve [Windows Güvenlik Duvarı](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) 'Nı veya [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell CMDLET 'ini kullanarak trafiğin http. sys ' ye ulaşmasını sağlamak için güvenlik duvarı bağlantı noktalarını açın. Aşağıdaki komutlarda ve uygulama yapılandırmasında, 443 numaralı bağlantı noktası kullanılır.

1. Bir Azure sanal makinesine dağıtım yaparken, [ağ güvenlik grubundaki](/azure/virtual-machines/windows/nsg-quickstart-portal)bağlantı noktalarını açın. Aşağıdaki komutlarda ve uygulama yapılandırmasında, 443 numaralı bağlantı noktası kullanılır.

1. Gerekirse, X. 509.440 sertifikalarını edinin ve yükler.

   Windows 'da, [New-SelfSignedCertificate PowerShell cmdlet 'ini](/powershell/module/pkiclient/new-selfsignedcertificate)kullanarak otomatik olarak imzalanan sertifikalar oluşturun. Desteklenmeyen bir örnek için bkz [. UpdateIISExpressSSLForChrome. ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).

   Sunucunun **yerel makine** > **Kişisel** deposunda otomatik olarak imzalanan veya CA imzalı sertifikalar yükler.

1. Uygulama [çerçeveye bağımlı bir dağıtım](/dotnet/core/deploying/#framework-dependent-deployments-fdd)ise, .net core, .NET Framework veya her ikisini de (uygulama .NET Framework hedefleyen bir .NET Core uygulaması ise) yükler.

   * **.NET Core**: uygulama .NET Core gerektiriyorsa .NET Core [Indirmelerinde](https://dotnet.microsoft.com/download) **.NET Core çalışma zamanı** yükleyicisini edinip çalıştırın. Tam SDK 'Yı sunucuya yüklemeyin.
   * **.NET Framework**: uygulama .NET Framework gerektiriyorsa [.NET Framework yükleme kılavuzuna](/dotnet/framework/install/)bakın. Gerekli .NET Framework yüklemesi. En son .NET Framework yükleyicisi [.NET Core İndirmeleri](https://dotnet.microsoft.com/download) sayfasından edinilebilir.

   Uygulama, [kendi içinde bir dağıtım](/dotnet/core/deploying/#self-contained-deployments-scd)ise, uygulama çalışma zamanını dağıtımda içerir. Sunucuda çerçeve yüklemesi gerekmez.

1. Uygulamada URL 'Leri ve bağlantı noktalarını yapılandırın.

   Varsayılan olarak, ASP.NET Core öğesine bağlanır `http://localhost:5000` . URL öneklerini ve bağlantı noktalarını yapılandırmak için seçenekler şunları içerir:

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * `urls`komut satırı bağımsız değişkeni
   * `ASPNETCORE_URLS`ortam değişkeni
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   Aşağıdaki kod örneği, <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> `10.0.0.4` 443 numaralı bağlantı noktasında sunucunun yerel IP adresi ile nasıl kullanılacağını göstermektedir:

   [!code-csharp[](httpsys/samples_snapshot/3.x/Program.cs?highlight=7)]

   Avantajı `UrlPrefixes` , hatalı biçimli ön ekler için hemen bir hata iletisi oluşturulmasından oluşur.

   Ayarları `UrlPrefixes` geçersiz kıl `UseUrls` / `urls` / `ASPNETCORE_URLS` . Bu nedenle, `UseUrls` `urls` ve `ASPNETCORE_URLS` ortam değişkeninin AVANTAJı Kestrel ve http. sys arasında geçiş yapmak daha kolay olabilir.

   HTTP. sys, [http sunucusu API UrlPrefix dize biçimlerini](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)kullanır.

   > [!WARNING]
   > Üst düzey joker karakter bağlamaları ( `http://*:80/` ve `http://+:80` ) kullanılmamalıdır **not** . Üst düzey joker karakter bağlamaları uygulama güvenliği güvenlik açıklarını oluşturur. Bu hem güçlü hem de zayıf Joker karakterlere yöneliktir. Joker karakterler yerine açık konak adlarını veya IP adreslerini kullanın. Alt etki alanı joker bağlantısı (örneğin, `*.mysub.com` ), tüm üst etki alanını ( `*.com` Bu güvenlik açığı olan aksine) kontrol ediyorsanız bir güvenlik riski değildir. Daha fazla bilgi için bkz. [RFC 7230: bölüm 5,4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).

1. Ön ek, sunucudaki URL öneklerini ister.

   HTTP. sys ' yi yapılandırmaya yönelik yerleşik araç *netsh. exe*' dir. *netsh. exe* , URL öneklerini ayırmak ve X. 509.440 sertifikaları atamak için kullanılır. Araç, yönetici ayrıcalıkları gerektirir.

   Uygulamanın URL 'Lerini kaydettirmek için *netsh. exe* aracını kullanın:

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * `<URL>`: Tam nitelikli Tekdüzen Kaynak Bulucu (URL). Joker karakter bağlama kullanmayın. Geçerli bir ana bilgisayar adı veya yerel IP adresi kullanın. *URL, sondaki eğik çizgi içermelidir.*
   * `<USER>`: Kullanıcı veya Kullanıcı grubu adını belirtir.

   Aşağıdaki örnekte sunucusunun yerel IP adresi `10.0.0.4` :

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   Bir URL kaydedildiğinde, araç ile yanıt verir `URL reservation successfully added` .

   Kayıtlı bir URL 'yi silmek için şu `delete urlacl` komutu kullanın:

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. X. 509.440 sertifikalarını sunucusuna kaydedin.

   Uygulamanın sertifikalarını kaydetmek için *netsh. exe* aracını kullanın:

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * `<IP>`: Bağlama için yerel IP adresini belirtir. Joker karakter bağlama kullanmayın. Geçerli bir IP adresi kullanın.
   * `<PORT>`: Bağlama için bağlantı noktasını belirtir.
   * `<THUMBPRINT>`: X. 509.440 sertifika parmak izi.
   * `<GUID>`: Uygulamayı bilgilendirme amacıyla temsil eden geliştirici tarafından oluşturulan bir GUID.

   Başvuru amacıyla, GUID 'yi uygulamada bir paket etiketi olarak depolayın:

   * Visual Studio 'da:
     * **Çözüm Gezgini** ' de uygulamaya sağ tıklayıp **Özellikler**' i seçerek uygulamanın proje özelliklerini açın.
     * **Paket** sekmesini seçin.
     * **Etiketler** alanında oluşturduğunuz GUID 'yi girin.
   * Visual Studio kullanmadığınız durumlarda:
     * Uygulamanın proje dosyasını açın.
     * `<PackageTags>`Oluşturduğunuz GUID ile yeni veya var olan bir özelliği ekleyin `<PropertyGroup>` :

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   Aşağıdaki örnekte:

   * Sunucunun yerel IP adresi `10.0.0.4` .
   * Bir çevrimiçi rastgele GUID Oluşturucu değeri sağlar `appid` .

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   Bir sertifika kaydedildiğinde, araç ile yanıt verir `SSL Certificate successfully added` .

   Bir sertifika kaydını silmek için şu komutu kullanın `delete sslcert` :

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   *Netsh. exe*için başvuru belgeleri:

   * [Köprü Metni Aktarım Protokolü (HTTP) için Netsh komutları](https://technet.microsoft.com/library/cc725882.aspx)
   * [UrlPrefix dizeleri](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)

1. Uygulamayı çalıştırın.

   1024 'den büyük bir bağlantı noktası numarası ile HTTP (HTTPS değil) kullanarak localhost 'a bağlanırken uygulamayı çalıştırmak için yönetici ayrıcalıklarına gerek yoktur. Diğer yapılandırmalarda (örneğin, yerel bir IP adresi veya 443 numaralı bağlantı noktasına bağlama), uygulamayı yönetici ayrıcalıklarıyla çalıştırın.

   Uygulama, sunucunun genel IP adresinde yanıt verir. Bu örnekte, sunucusuna, genel IP adresinde Internet 'ten ulaşılırsa `104.214.79.47` .

   Bu örnekte bir geliştirme sertifikası kullanılır. Tarayıcının güvenilmeyen sertifika uyarısı atlandıktan sonra sayfa güvenli bir şekilde yüklenir.

   ![Uygulamanın dizin sayfasını yüklü olarak gösteren tarayıcı penceresi](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a>Proxy sunucusu ve yük dengeleyici senaryoları

Internet veya şirket ağından gelen isteklerle etkileşime geçen HTTP. sys tarafından barındırılan uygulamalar için, proxy sunucularının ve yük dengeleyiciler 'nin arkasında barındırılırken ek yapılandırma gerekebilir. Daha fazla bilgi için bkz. [proxy sunucularıyla ve yük dengeleyicilerle çalışacak ASP.NET Core yapılandırma](xref:host-and-deploy/proxy-load-balancer).

## <a name="additional-resources"></a>Ek kaynaklar

* [HTTP. sys ile Windows kimlik doğrulamasını etkinleştirme](xref:security/authentication/windowsauth#httpsys)
* [HTTP Sunucusu API 'SI](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx)
* [ASPNET/HttpSysServer GitHub deposu (kaynak kodu)](https://github.com/aspnet/HttpSysServer/)
* [Ana bilgisayar](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[Http. sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) , yalnızca Windows üzerinde çalışan [ASP.NET Core için bir Web sunucusudur](xref:fundamentals/servers/index) . HTTP. sys, [Kestrel](xref:fundamentals/servers/kestrel) Server için bir alternatiftir ve Kestrel tarafından sağlamayan bazı özellikler sunar.

> [!IMPORTANT]
> HTTP. sys [ASP.NET Core modülüyle](xref:host-and-deploy/aspnet-core-module) uyumlu DEĞILDIR ve ııs veya IIS Express ile kullanılamaz.

HTTP. sys aşağıdaki özellikleri destekler:

* [Windows Kimlik Doğrulaması](xref:security/authentication/windowsauth)
* Bağlantı noktası Paylaşımı
* SNı ile HTTPS
* TLS üzerinden HTTP/2 (Windows 10 veya üzeri)
* Doğrudan dosya iletimi
* Yanıtları Önbelleğe Alma
* WebSockets (Windows 8 veya üzeri)

Desteklenen Windows sürümleri:

* Windows 7 veya üzeri
* Windows Server 2008 R2 veya sonraki sürümü

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="when-to-use-httpsys"></a>HTTP. sys ne zaman kullanılır

HTTP. sys, şu durumlarda olduğu dağıtımlar için yararlıdır:

* Sunucuyu IIS kullanmadan doğrudan Internet 'e sunmaya gerek vardır.

  ![HTTP. sys doğrudan Internet ile iletişim kurar](httpsys/_static/httpsys-to-internet.png)

* İç dağıtım, [Windows kimlik doğrulaması](xref:security/authentication/windowsauth)gibi Kestrel 'de kullanılamayan bir özelliği gerektirir.

  ![HTTP. sys doğrudan iç ağla iletişim kurar](httpsys/_static/httpsys-to-internal.png)

HTTP. sys pek çok tür saldırılara karşı koruyan ve tam özellikli bir Web sunucusu için sağlamlık, güvenlik ve ölçeklenebilirlik sağlayan çok sayıda teknolojiden oluşur. IIS, HTTP. sys ' nin üstünde HTTP dinleyicisi olarak çalışır.

## <a name="http2-support"></a>HTTP/2 desteği

Aşağıdaki temel gereksinimler karşılanıyorsa ASP.NET Core uygulamalar için [http/2](https://httpwg.org/specs/rfc7540.html) etkinleştirilir:

* Windows Server 2016/Windows 10 veya üzeri
* [Uygulama katmanı protokol anlaşması (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) bağlantısı
* TLS 1,2 veya üzeri bağlantı

Bir HTTP/2 bağlantısı kurulduysa, [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) Reports `HTTP/2` .

HTTP/2 varsayılan olarak etkindir. HTTP/2 bağlantısı kurulmadıysa, bağlantı HTTP/1.1 'ye geri döner. Windows 'un gelecek bir sürümünde http/2 yapılandırma bayrakları http/2 ' yi HTTP. sys ile devre dışı bırakma özelliği de dahil olmak üzere kullanılabilir olacaktır.

## <a name="kernel-mode-authentication-with-kerberos"></a>Kerberos ile çekirdek modu kimlik doğrulaması

HTTP. sys, Kerberos kimlik doğrulama protokolü ile çekirdek modu kimlik doğrulamasına temsilciler. Kullanıcı modu kimlik doğrulaması, Kerberos ve HTTP. sys ile desteklenmez. Makine hesabı, Active Directory alındığı ve kullanıcının kimliğini doğrulamak için istemci tarafından sunucuya iletilen Kerberos belirtecinin/biletinin şifresini çözmek için kullanılmalıdır. Uygulamanın kullanıcısına değil, ana bilgisayar için hizmet asıl adını (SPN) kaydedin.

## <a name="how-to-use-httpsys"></a>HTTP. sys kullanma

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a>ASP.NET Core uygulamasını HTTP. sys kullanacak şekilde yapılandırma

[Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app) ([NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)) kullanılırken proje dosyasındaki bir paket başvurusu gerekli değildir. `Microsoft.AspNetCore.App`Metapackage 'i kullanmıyorsanız [Microsoft. Aspnetcore. Server. httpsys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/)öğesine bir paket başvurusu ekleyin.

<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*>Ana bilgisayarı oluştururken, gerekli herhangi bir yöntemi belirterek uzantı yöntemini çağırın <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions> . Aşağıdaki örnek, seçeneklerini varsayılan değerlerine ayarlar:

[!code-csharp[](httpsys/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=4-12)]

Ek HTTP. sys yapılandırması, [kayıt defteri ayarları](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows)aracılığıyla işlenir.

**HTTP. sys seçenekleri**

| Özellik | Açıklama | Varsayılan |
| ---
title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

-
title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

---- | ---title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

-
title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

-
title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

------ | :-----: | | [Allowsynchronousio](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | Ve için zaman uyumlu giriş/çıkışa izin verilip verilmeyeceğini `HttpContext.Request.Body` denetleyin `HttpContext.Response.Body` . | `true`| | [Authentication. AllowAnonymous](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | Anonim isteklere izin verin. | `true`| | [Kimlik doğrulaması. şemalar](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | İzin verilen kimlik doğrulama düzenlerini belirtin. Dinleyici elden atılırken önce herhangi bir zamanda değiştirilebilir. Değerler [authenticationdüzenlerinin numaralandırması](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes)tarafından sağlanır: `Basic` ,,, `Kerberos` `Negotiate` `None` , ve `NTLM` . | `None`| | [Enableresponsecaching](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | Uygun üst bilgileri içeren yanıtlar için [çekirdek modu](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) önbelleğe almayı deneyin. Yanıt `Set-Cookie` , `Vary` veya `Pragma` üst bilgileri içermeyebilir. `Cache-Control` `public` Bir ya da değeri ya da bir üst bilgi içermelidir `shared-max-age` `max-age` `Expires` . | `true`| | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | En fazla eşzamanlı kabul sayısı. | 5 &times; [ortam. <br> ProcessorCount](xref:System.Environment.ProcessorCount) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | Kabul edilecek eşzamanlı bağlantı sayısı üst sınırı. `-1`Sonsuz için kullanın. `null`Kayıt defterinin makine genelindeki ayarını kullanmak için kullanın. | `null`<br>(makine genelinde<br>ayar) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | <a href="#maxrequestbodysize">MaxRequestBodySize</a> bölümüne bakın. | 30000000 bayt<br>(~ 28,6 MB) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | Sıraya alınabilen en fazla istek sayısı. | 1000 | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | İstemci bağlantısının kesilmesinden kaynaklanan yanıt gövdesi yazmasının, özel durumlar oluşturması veya normal şekilde tamamlanması gerektiğini belirtin. | `false`<br>(normal olarak) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager>Kayıt defterinde da YAPıLANDıRıLABILEN http. sys yapılandırmasını kullanıma sunun. Varsayılan değerler de dahil olmak üzere her bir ayar hakkında daha fazla bilgi edinmek için API bağlantılarını izleyin:<ul><li>[TimeoutManager. DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): http sunucusu API 'sinin varlık gövdesini etkin tut bağlantısı üzerinde boşaltmasına izin verilen süre.</li><li>[TimeoutManager. EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): istek varlığı gövdesinin gelmesi için izin verilen süre.</li><li>[TimeoutManager. HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): http sunucusu API 'sinin istek üst bilgisini ayrıştırması için izin verilen süre.</li><li>[TimeoutManager. IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): boştaki bir bağlantı için izin verilen süre.</li><li>[TimeoutManager. MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): yanıt için en düşük gönderme hızı.</li><li>[TimeoutManager. RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): isteğin, uygulamanın onu seçmeden önce istek kuyruğunda kalması için izin verilen süre.</li></ul> |  | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection>Http. sys ile kaydolmak için öğesini belirtin. En yararlı olan [UrlPrefixCollection. Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), koleksiyona bir ön ek eklemek için kullanılır. Bunlar, dinleyici elden atılıyor öncesinde herhangi bir zamanda değiştirilebilir. |  |

<a name="maxrequestbodysize"></a>

**MaxRequestBodySize**

Herhangi bir istek gövdesinin bayt olarak izin verilen en büyük boyutu. Olarak ayarlandığında `null` , en büyük istek gövdesi boyutu sınırsızdır. Bu sınırın, her zaman sınırsız olan yükseltilmiş bağlantıları üzerinde hiçbir etkisi yoktur.

Tek bir ASP.NET Core MVC uygulamasında sınırı geçersiz kılmak için önerilen yöntem, `IActionResult` <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> bir eylem yönteminde özniteliğini kullanmaktır:

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

Uygulama isteği okumayı başlattıktan sonra bir istek üzerindeki sınırı yapılandırmayı denerse bir özel durum oluşur. Özelliğin `IsReadOnly` salt okuma durumunda olup olmadığını göstermek için bir özellik kullanılabilir `MaxRequestBodySize` , yani sınırı yapılandırmak için çok geç.

Uygulamanın istek başına geçersiz kılması gerekiyorsa <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> , şunu kullanın <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature> :

[!code-csharp[](httpsys/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

Visual Studio kullanıyorsanız, uygulamanın IIS veya IIS Express çalıştıracak şekilde yapılandırılmadığından emin olun.

Visual Studio 'da varsayılan başlatma profili IIS Express içindir. Projeyi konsol uygulaması olarak çalıştırmak için, aşağıdaki ekran görüntüsünde gösterildiği gibi seçili profili el ile değiştirin:

![Konsol uygulaması profilini seçin](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a>Windows Server 'ı yapılandırma

1. Uygulamanın açmak için bağlantı noktalarını belirleme ve [Windows Güvenlik Duvarı](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) 'Nı veya [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell CMDLET 'ini kullanarak trafiğin http. sys ' ye ulaşmasını sağlamak için güvenlik duvarı bağlantı noktalarını açın. Aşağıdaki komutlarda ve uygulama yapılandırmasında, 443 numaralı bağlantı noktası kullanılır.

1. Bir Azure sanal makinesine dağıtım yaparken, [ağ güvenlik grubundaki](/azure/virtual-machines/windows/nsg-quickstart-portal)bağlantı noktalarını açın. Aşağıdaki komutlarda ve uygulama yapılandırmasında, 443 numaralı bağlantı noktası kullanılır.

1. Gerekirse, X. 509.440 sertifikalarını edinin ve yükler.

   Windows 'da, [New-SelfSignedCertificate PowerShell cmdlet 'ini](/powershell/module/pkiclient/new-selfsignedcertificate)kullanarak otomatik olarak imzalanan sertifikalar oluşturun. Desteklenmeyen bir örnek için bkz [. UpdateIISExpressSSLForChrome. ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).

   Sunucunun **yerel makine** > **Kişisel** deposunda otomatik olarak imzalanan veya CA imzalı sertifikalar yükler.

1. Uygulama [çerçeveye bağımlı bir dağıtım](/dotnet/core/deploying/#framework-dependent-deployments-fdd)ise, .net core, .NET Framework veya her ikisini de (uygulama .NET Framework hedefleyen bir .NET Core uygulaması ise) yükler.

   * **.NET Core**: uygulama .NET Core gerektiriyorsa .NET Core [Indirmelerinde](https://dotnet.microsoft.com/download) **.NET Core çalışma zamanı** yükleyicisini edinip çalıştırın. Tam SDK 'Yı sunucuya yüklemeyin.
   * **.NET Framework**: uygulama .NET Framework gerektiriyorsa [.NET Framework yükleme kılavuzuna](/dotnet/framework/install/)bakın. Gerekli .NET Framework yüklemesi. En son .NET Framework yükleyicisi [.NET Core İndirmeleri](https://dotnet.microsoft.com/download) sayfasından edinilebilir.

   Uygulama, [kendi içinde bir dağıtım](/dotnet/core/deploying/#self-contained-deployments-scd)ise, uygulama çalışma zamanını dağıtımda içerir. Sunucuda çerçeve yüklemesi gerekmez.

1. Uygulamada URL 'Leri ve bağlantı noktalarını yapılandırın.

   Varsayılan olarak, ASP.NET Core öğesine bağlanır `http://localhost:5000` . URL öneklerini ve bağlantı noktalarını yapılandırmak için seçenekler şunları içerir:

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * `urls`komut satırı bağımsız değişkeni
   * `ASPNETCORE_URLS`ortam değişkeni
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   Aşağıdaki kod örneği, <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> `10.0.0.4` 443 numaralı bağlantı noktasında sunucunun yerel IP adresi ile nasıl kullanılacağını göstermektedir:

   [!code-csharp[](httpsys/samples_snapshot/2.x/Program.cs?highlight=6)]

   Avantajı `UrlPrefixes` , hatalı biçimli ön ekler için hemen bir hata iletisi oluşturulmasından oluşur.

   Ayarları `UrlPrefixes` geçersiz kıl `UseUrls` / `urls` / `ASPNETCORE_URLS` . Bu nedenle, `UseUrls` `urls` ve `ASPNETCORE_URLS` ortam değişkeninin AVANTAJı Kestrel ve http. sys arasında geçiş yapmak daha kolay olabilir.

   HTTP. sys, [http sunucusu API UrlPrefix dize biçimlerini](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)kullanır.

   > [!WARNING]
   > Üst düzey joker karakter bağlamaları ( `http://*:80/` ve `http://+:80` ) kullanılmamalıdır **not** . Üst düzey joker karakter bağlamaları uygulama güvenliği güvenlik açıklarını oluşturur. Bu hem güçlü hem de zayıf Joker karakterlere yöneliktir. Joker karakterler yerine açık konak adlarını veya IP adreslerini kullanın. Alt etki alanı joker bağlantısı (örneğin, `*.mysub.com` ), tüm üst etki alanını ( `*.com` Bu güvenlik açığı olan aksine) kontrol ediyorsanız bir güvenlik riski değildir. Daha fazla bilgi için bkz. [RFC 7230: bölüm 5,4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).

1. Ön ek, sunucudaki URL öneklerini ister.

   HTTP. sys ' yi yapılandırmaya yönelik yerleşik araç *netsh. exe*' dir. *netsh. exe* , URL öneklerini ayırmak ve X. 509.440 sertifikaları atamak için kullanılır. Araç, yönetici ayrıcalıkları gerektirir.

   Uygulamanın URL 'Lerini kaydettirmek için *netsh. exe* aracını kullanın:

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * `<URL>`: Tam nitelikli Tekdüzen Kaynak Bulucu (URL). Joker karakter bağlama kullanmayın. Geçerli bir ana bilgisayar adı veya yerel IP adresi kullanın. *URL, sondaki eğik çizgi içermelidir.*
   * `<USER>`: Kullanıcı veya Kullanıcı grubu adını belirtir.

   Aşağıdaki örnekte sunucusunun yerel IP adresi `10.0.0.4` :

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   Bir URL kaydedildiğinde, araç ile yanıt verir `URL reservation successfully added` .

   Kayıtlı bir URL 'yi silmek için şu `delete urlacl` komutu kullanın:

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. X. 509.440 sertifikalarını sunucusuna kaydedin.

   Uygulamanın sertifikalarını kaydetmek için *netsh. exe* aracını kullanın:

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * `<IP>`: Bağlama için yerel IP adresini belirtir. Joker karakter bağlama kullanmayın. Geçerli bir IP adresi kullanın.
   * `<PORT>`: Bağlama için bağlantı noktasını belirtir.
   * `<THUMBPRINT>`: X. 509.440 sertifika parmak izi.
   * `<GUID>`: Uygulamayı bilgilendirme amacıyla temsil eden geliştirici tarafından oluşturulan bir GUID.

   Başvuru amacıyla, GUID 'yi uygulamada bir paket etiketi olarak depolayın:

   * Visual Studio 'da:
     * **Çözüm Gezgini** ' de uygulamaya sağ tıklayıp **Özellikler**' i seçerek uygulamanın proje özelliklerini açın.
     * **Paket** sekmesini seçin.
     * **Etiketler** alanında oluşturduğunuz GUID 'yi girin.
   * Visual Studio kullanmadığınız durumlarda:
     * Uygulamanın proje dosyasını açın.
     * `<PackageTags>`Oluşturduğunuz GUID ile yeni veya var olan bir özelliği ekleyin `<PropertyGroup>` :

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   Aşağıdaki örnekte:

   * Sunucunun yerel IP adresi `10.0.0.4` .
   * Bir çevrimiçi rastgele GUID Oluşturucu değeri sağlar `appid` .

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   Bir sertifika kaydedildiğinde, araç ile yanıt verir `SSL Certificate successfully added` .

   Bir sertifika kaydını silmek için şu komutu kullanın `delete sslcert` :

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   *Netsh. exe*için başvuru belgeleri:

   * [Köprü Metni Aktarım Protokolü (HTTP) için Netsh komutları](https://technet.microsoft.com/library/cc725882.aspx)
   * [UrlPrefix dizeleri](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)

1. Uygulamayı çalıştırın.

   1024 'den büyük bir bağlantı noktası numarası ile HTTP (HTTPS değil) kullanarak localhost 'a bağlanırken uygulamayı çalıştırmak için yönetici ayrıcalıklarına gerek yoktur. Diğer yapılandırmalarda (örneğin, yerel bir IP adresi veya 443 numaralı bağlantı noktasına bağlama), uygulamayı yönetici ayrıcalıklarıyla çalıştırın.

   Uygulama, sunucunun genel IP adresinde yanıt verir. Bu örnekte, sunucusuna, genel IP adresinde Internet 'ten ulaşılırsa `104.214.79.47` .

   Bu örnekte bir geliştirme sertifikası kullanılır. Tarayıcının güvenilmeyen sertifika uyarısı atlandıktan sonra sayfa güvenli bir şekilde yüklenir.

   ![Uygulamanın dizin sayfasını yüklü olarak gösteren tarayıcı penceresi](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a>Proxy sunucusu ve yük dengeleyici senaryoları

Internet veya şirket ağından gelen isteklerle etkileşime geçen HTTP. sys tarafından barındırılan uygulamalar için, proxy sunucularının ve yük dengeleyiciler 'nin arkasında barındırılırken ek yapılandırma gerekebilir. Daha fazla bilgi için bkz. [proxy sunucularıyla ve yük dengeleyicilerle çalışacak ASP.NET Core yapılandırma](xref:host-and-deploy/proxy-load-balancer).

## <a name="additional-resources"></a>Ek kaynaklar

* [HTTP. sys ile Windows kimlik doğrulamasını etkinleştirme](xref:security/authentication/windowsauth#httpsys)
* [HTTP Sunucusu API 'SI](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx)
* [ASPNET/HttpSysServer GitHub deposu (kaynak kodu)](https://github.com/aspnet/HttpSysServer/)
* [Ana bilgisayar](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

[Http. sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) , yalnızca Windows üzerinde çalışan [ASP.NET Core için bir Web sunucusudur](xref:fundamentals/servers/index) . HTTP. sys, [Kestrel](xref:fundamentals/servers/kestrel) Server için bir alternatiftir ve Kestrel tarafından sağlamayan bazı özellikler sunar.

> [!IMPORTANT]
> HTTP. sys [ASP.NET Core modülüyle](xref:host-and-deploy/aspnet-core-module) uyumlu DEĞILDIR ve ııs veya IIS Express ile kullanılamaz.

HTTP. sys aşağıdaki özellikleri destekler:

* [Windows Kimlik Doğrulaması](xref:security/authentication/windowsauth)
* Bağlantı noktası Paylaşımı
* SNı ile HTTPS
* TLS üzerinden HTTP/2 (Windows 10 veya üzeri)
* Doğrudan dosya iletimi
* Yanıtları Önbelleğe Alma
* WebSockets (Windows 8 veya üzeri)

Desteklenen Windows sürümleri:

* Windows 7 veya üzeri
* Windows Server 2008 R2 veya sonraki sürümü

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="when-to-use-httpsys"></a>HTTP. sys ne zaman kullanılır

HTTP. sys, şu durumlarda olduğu dağıtımlar için yararlıdır:

* Sunucuyu IIS kullanmadan doğrudan Internet 'e sunmaya gerek vardır.

  ![HTTP. sys doğrudan Internet ile iletişim kurar](httpsys/_static/httpsys-to-internet.png)

* İç dağıtım, [Windows kimlik doğrulaması](xref:security/authentication/windowsauth)gibi Kestrel 'de kullanılamayan bir özelliği gerektirir.

  ![HTTP. sys doğrudan iç ağla iletişim kurar](httpsys/_static/httpsys-to-internal.png)

HTTP. sys pek çok tür saldırılara karşı koruyan ve tam özellikli bir Web sunucusu için sağlamlık, güvenlik ve ölçeklenebilirlik sağlayan çok sayıda teknolojiden oluşur. IIS, HTTP. sys ' nin üstünde HTTP dinleyicisi olarak çalışır.

## <a name="http2-support"></a>HTTP/2 desteği

Aşağıdaki temel gereksinimler karşılanıyorsa ASP.NET Core uygulamalar için [http/2](https://httpwg.org/specs/rfc7540.html) etkinleştirilir:

* Windows Server 2016/Windows 10 veya üzeri
* [Uygulama katmanı protokol anlaşması (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) bağlantısı
* TLS 1,2 veya üzeri bağlantı

Bir HTTP/2 bağlantısı kurulduysa, [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) Reports `HTTP/1.1` .

HTTP/2 varsayılan olarak etkindir. HTTP/2 bağlantısı kurulmadıysa, bağlantı HTTP/1.1 'ye geri döner. Windows 'un gelecek bir sürümünde http/2 yapılandırma bayrakları http/2 ' yi HTTP. sys ile devre dışı bırakma özelliği de dahil olmak üzere kullanılabilir olacaktır.

## <a name="kernel-mode-authentication-with-kerberos"></a>Kerberos ile çekirdek modu kimlik doğrulaması

HTTP. sys, Kerberos kimlik doğrulama protokolü ile çekirdek modu kimlik doğrulamasına temsilciler. Kullanıcı modu kimlik doğrulaması, Kerberos ve HTTP. sys ile desteklenmez. Makine hesabı, Active Directory alındığı ve kullanıcının kimliğini doğrulamak için istemci tarafından sunucuya iletilen Kerberos belirtecinin/biletinin şifresini çözmek için kullanılmalıdır. Uygulamanın kullanıcısına değil, ana bilgisayar için hizmet asıl adını (SPN) kaydedin.

## <a name="how-to-use-httpsys"></a>HTTP. sys kullanma

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a>ASP.NET Core uygulamasını HTTP. sys kullanacak şekilde yapılandırma

[Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app) ([NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)) kullanılırken proje dosyasındaki bir paket başvurusu gerekli değildir. `Microsoft.AspNetCore.App`Metapackage 'i kullanmıyorsanız [Microsoft. Aspnetcore. Server. httpsys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/)öğesine bir paket başvurusu ekleyin.

<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*>Ana bilgisayarı oluştururken, gerekli herhangi bir yöntemi belirterek uzantı yöntemini çağırın <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions> . Aşağıdaki örnek, seçeneklerini varsayılan değerlerine ayarlar:

[!code-csharp[](httpsys/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=4-12)]

Ek HTTP. sys yapılandırması, [kayıt defteri ayarları](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows)aracılığıyla işlenir.

**HTTP. sys seçenekleri**

| Özellik | Açıklama | Varsayılan |
| ---
title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

-
title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

---- | ---title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

-
title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

-
title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

------ | :-----: | | [Allowsynchronousio](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | Ve için zaman uyumlu giriş/çıkışa izin verilip verilmeyeceğini `HttpContext.Request.Body` denetleyin `HttpContext.Response.Body` . | `true`| | [Authentication. AllowAnonymous](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | Anonim isteklere izin verin. | `true`| | [Kimlik doğrulaması. şemalar](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | İzin verilen kimlik doğrulama düzenlerini belirtin. Dinleyici elden atılırken önce herhangi bir zamanda değiştirilebilir. Değerler [authenticationdüzenlerinin numaralandırması](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes)tarafından sağlanır: `Basic` ,,, `Kerberos` `Negotiate` `None` , ve `NTLM` . | `None`| | [Enableresponsecaching](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | Uygun üst bilgileri içeren yanıtlar için [çekirdek modu](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) önbelleğe almayı deneyin. Yanıt `Set-Cookie` , `Vary` veya `Pragma` üst bilgileri içermeyebilir. `Cache-Control` `public` Bir ya da değeri ya da bir üst bilgi içermelidir `shared-max-age` `max-age` `Expires` . | `true`| | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | En fazla eşzamanlı kabul sayısı. | 5 &times; [ortam. <br> ProcessorCount](xref:System.Environment.ProcessorCount) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | Kabul edilecek eşzamanlı bağlantı sayısı üst sınırı. `-1`Sonsuz için kullanın. `null`Kayıt defterinin makine genelindeki ayarını kullanmak için kullanın. | `null`<br>(makine genelinde<br>ayar) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | <a href="#maxrequestbodysize">MaxRequestBodySize</a> bölümüne bakın. | 30000000 bayt<br>(~ 28,6 MB) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | Sıraya alınabilen en fazla istek sayısı. | 1000 | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | İstemci bağlantısının kesilmesinden kaynaklanan yanıt gövdesi yazmasının, özel durumlar oluşturması veya normal şekilde tamamlanması gerektiğini belirtin. | `false`<br>(normal olarak) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager>Kayıt defterinde da YAPıLANDıRıLABILEN http. sys yapılandırmasını kullanıma sunun. Varsayılan değerler de dahil olmak üzere her bir ayar hakkında daha fazla bilgi edinmek için API bağlantılarını izleyin:<ul><li>[TimeoutManager. DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): http sunucusu API 'sinin varlık gövdesini etkin tut bağlantısı üzerinde boşaltmasına izin verilen süre.</li><li>[TimeoutManager. EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): istek varlığı gövdesinin gelmesi için izin verilen süre.</li><li>[TimeoutManager. HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): http sunucusu API 'sinin istek üst bilgisini ayrıştırması için izin verilen süre.</li><li>[TimeoutManager. IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): boştaki bir bağlantı için izin verilen süre.</li><li>[TimeoutManager. MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): yanıt için en düşük gönderme hızı.</li><li>[TimeoutManager. RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): isteğin, uygulamanın onu seçmeden önce istek kuyruğunda kalması için izin verilen süre.</li></ul> |  | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection>Http. sys ile kaydolmak için öğesini belirtin. En yararlı olan [UrlPrefixCollection. Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), koleksiyona bir ön ek eklemek için kullanılır. Bunlar, dinleyici elden atılıyor öncesinde herhangi bir zamanda değiştirilebilir. |  |

<a name="maxrequestbodysize"></a>

**MaxRequestBodySize**

Herhangi bir istek gövdesinin bayt olarak izin verilen en büyük boyutu. Olarak ayarlandığında `null` , en büyük istek gövdesi boyutu sınırsızdır. Bu sınırın, her zaman sınırsız olan yükseltilmiş bağlantıları üzerinde hiçbir etkisi yoktur.

Tek bir ASP.NET Core MVC uygulamasında sınırı geçersiz kılmak için önerilen yöntem, `IActionResult` <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> bir eylem yönteminde özniteliğini kullanmaktır:

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

Uygulama isteği okumayı başlattıktan sonra bir istek üzerindeki sınırı yapılandırmayı denerse bir özel durum oluşur. Özelliğin `IsReadOnly` salt okuma durumunda olup olmadığını göstermek için bir özellik kullanılabilir `MaxRequestBodySize` , yani sınırı yapılandırmak için çok geç.

Uygulamanın istek başına geçersiz kılması gerekiyorsa <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> , şunu kullanın <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature> :

[!code-csharp[](httpsys/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

Visual Studio kullanıyorsanız, uygulamanın IIS veya IIS Express çalıştıracak şekilde yapılandırılmadığından emin olun.

Visual Studio 'da varsayılan başlatma profili IIS Express içindir. Projeyi konsol uygulaması olarak çalıştırmak için, aşağıdaki ekran görüntüsünde gösterildiği gibi seçili profili el ile değiştirin:

![Konsol uygulaması profilini seçin](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a>Windows Server 'ı yapılandırma

1. Uygulamanın açmak için bağlantı noktalarını belirleme ve [Windows Güvenlik Duvarı](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) 'Nı veya [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell CMDLET 'ini kullanarak trafiğin http. sys ' ye ulaşmasını sağlamak için güvenlik duvarı bağlantı noktalarını açın. Aşağıdaki komutlarda ve uygulama yapılandırmasında, 443 numaralı bağlantı noktası kullanılır.

1. Bir Azure sanal makinesine dağıtım yaparken, [ağ güvenlik grubundaki](/azure/virtual-machines/windows/nsg-quickstart-portal)bağlantı noktalarını açın. Aşağıdaki komutlarda ve uygulama yapılandırmasında, 443 numaralı bağlantı noktası kullanılır.

1. Gerekirse, X. 509.440 sertifikalarını edinin ve yükler.

   Windows 'da, [New-SelfSignedCertificate PowerShell cmdlet 'ini](/powershell/module/pkiclient/new-selfsignedcertificate)kullanarak otomatik olarak imzalanan sertifikalar oluşturun. Desteklenmeyen bir örnek için bkz [. UpdateIISExpressSSLForChrome. ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).

   Sunucunun **yerel makine** > **Kişisel** deposunda otomatik olarak imzalanan veya CA imzalı sertifikalar yükler.

1. Uygulama [çerçeveye bağımlı bir dağıtım](/dotnet/core/deploying/#framework-dependent-deployments-fdd)ise, .net core, .NET Framework veya her ikisini de (uygulama .NET Framework hedefleyen bir .NET Core uygulaması ise) yükler.

   * **.NET Core**: uygulama .NET Core gerektiriyorsa .NET Core [Indirmelerinde](https://dotnet.microsoft.com/download) **.NET Core çalışma zamanı** yükleyicisini edinip çalıştırın. Tam SDK 'Yı sunucuya yüklemeyin.
   * **.NET Framework**: uygulama .NET Framework gerektiriyorsa [.NET Framework yükleme kılavuzuna](/dotnet/framework/install/)bakın. Gerekli .NET Framework yüklemesi. En son .NET Framework yükleyicisi [.NET Core İndirmeleri](https://dotnet.microsoft.com/download) sayfasından edinilebilir.

   Uygulama, [kendi içinde bir dağıtım](/dotnet/core/deploying/#self-contained-deployments-scd)ise, uygulama çalışma zamanını dağıtımda içerir. Sunucuda çerçeve yüklemesi gerekmez.

1. Uygulamada URL 'Leri ve bağlantı noktalarını yapılandırın.

   Varsayılan olarak, ASP.NET Core öğesine bağlanır `http://localhost:5000` . URL öneklerini ve bağlantı noktalarını yapılandırmak için seçenekler şunları içerir:

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * `urls`komut satırı bağımsız değişkeni
   * `ASPNETCORE_URLS`ortam değişkeni
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   Aşağıdaki kod örneği, <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> `10.0.0.4` 443 numaralı bağlantı noktasında sunucunun yerel IP adresi ile nasıl kullanılacağını göstermektedir:

   [!code-csharp[](httpsys/samples_snapshot/2.x/Program.cs?highlight=6)]

   Avantajı `UrlPrefixes` , hatalı biçimli ön ekler için hemen bir hata iletisi oluşturulmasından oluşur.

   Ayarları `UrlPrefixes` geçersiz kıl `UseUrls` / `urls` / `ASPNETCORE_URLS` . Bu nedenle, `UseUrls` `urls` ve `ASPNETCORE_URLS` ortam değişkeninin AVANTAJı Kestrel ve http. sys arasında geçiş yapmak daha kolay olabilir.

   HTTP. sys, [http sunucusu API UrlPrefix dize biçimlerini](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)kullanır.

   > [!WARNING]
   > Üst düzey joker karakter bağlamaları ( `http://*:80/` ve `http://+:80` ) kullanılmamalıdır **not** . Üst düzey joker karakter bağlamaları uygulama güvenliği güvenlik açıklarını oluşturur. Bu hem güçlü hem de zayıf Joker karakterlere yöneliktir. Joker karakterler yerine açık konak adlarını veya IP adreslerini kullanın. Alt etki alanı joker bağlantısı (örneğin, `*.mysub.com` ), tüm üst etki alanını ( `*.com` Bu güvenlik açığı olan aksine) kontrol ediyorsanız bir güvenlik riski değildir. Daha fazla bilgi için bkz. [RFC 7230: bölüm 5,4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).

1. Ön ek, sunucudaki URL öneklerini ister.

   HTTP. sys ' yi yapılandırmaya yönelik yerleşik araç *netsh. exe*' dir. *netsh. exe* , URL öneklerini ayırmak ve X. 509.440 sertifikaları atamak için kullanılır. Araç, yönetici ayrıcalıkları gerektirir.

   Uygulamanın URL 'Lerini kaydettirmek için *netsh. exe* aracını kullanın:

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * `<URL>`: Tam nitelikli Tekdüzen Kaynak Bulucu (URL). Joker karakter bağlama kullanmayın. Geçerli bir ana bilgisayar adı veya yerel IP adresi kullanın. *URL, sondaki eğik çizgi içermelidir.*
   * `<USER>`: Kullanıcı veya Kullanıcı grubu adını belirtir.

   Aşağıdaki örnekte sunucusunun yerel IP adresi `10.0.0.4` :

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   Bir URL kaydedildiğinde, araç ile yanıt verir `URL reservation successfully added` .

   Kayıtlı bir URL 'yi silmek için şu `delete urlacl` komutu kullanın:

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. X. 509.440 sertifikalarını sunucusuna kaydedin.

   Uygulamanın sertifikalarını kaydetmek için *netsh. exe* aracını kullanın:

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * `<IP>`: Bağlama için yerel IP adresini belirtir. Joker karakter bağlama kullanmayın. Geçerli bir IP adresi kullanın.
   * `<PORT>`: Bağlama için bağlantı noktasını belirtir.
   * `<THUMBPRINT>`: X. 509.440 sertifika parmak izi.
   * `<GUID>`: Uygulamayı bilgilendirme amacıyla temsil eden geliştirici tarafından oluşturulan bir GUID.

   Başvuru amacıyla, GUID 'yi uygulamada bir paket etiketi olarak depolayın:

   * Visual Studio 'da:
     * **Çözüm Gezgini** ' de uygulamaya sağ tıklayıp **Özellikler**' i seçerek uygulamanın proje özelliklerini açın.
     * **Paket** sekmesini seçin.
     * **Etiketler** alanında oluşturduğunuz GUID 'yi girin.
   * Visual Studio kullanmadığınız durumlarda:
     * Uygulamanın proje dosyasını açın.
     * `<PackageTags>`Oluşturduğunuz GUID ile yeni veya var olan bir özelliği ekleyin `<PropertyGroup>` :

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   Aşağıdaki örnekte:

   * Sunucunun yerel IP adresi `10.0.0.4` .
   * Bir çevrimiçi rastgele GUID Oluşturucu değeri sağlar `appid` .

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   Bir sertifika kaydedildiğinde, araç ile yanıt verir `SSL Certificate successfully added` .

   Bir sertifika kaydını silmek için şu komutu kullanın `delete sslcert` :

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   *Netsh. exe*için başvuru belgeleri:

   * [Köprü Metni Aktarım Protokolü (HTTP) için Netsh komutları](https://technet.microsoft.com/library/cc725882.aspx)
   * [UrlPrefix dizeleri](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)

1. Uygulamayı çalıştırın.

   1024 'den büyük bir bağlantı noktası numarası ile HTTP (HTTPS değil) kullanarak localhost 'a bağlanırken uygulamayı çalıştırmak için yönetici ayrıcalıklarına gerek yoktur. Diğer yapılandırmalarda (örneğin, yerel bir IP adresi veya 443 numaralı bağlantı noktasına bağlama), uygulamayı yönetici ayrıcalıklarıyla çalıştırın.

   Uygulama, sunucunun genel IP adresinde yanıt verir. Bu örnekte, sunucusuna, genel IP adresinde Internet 'ten ulaşılırsa `104.214.79.47` .

   Bu örnekte bir geliştirme sertifikası kullanılır. Tarayıcının güvenilmeyen sertifika uyarısı atlandıktan sonra sayfa güvenli bir şekilde yüklenir.

   ![Uygulamanın dizin sayfasını yüklü olarak gösteren tarayıcı penceresi](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a>Proxy sunucusu ve yük dengeleyici senaryoları

Internet veya şirket ağından gelen isteklerle etkileşime geçen HTTP. sys tarafından barındırılan uygulamalar için, proxy sunucularının ve yük dengeleyiciler 'nin arkasında barındırılırken ek yapılandırma gerekebilir. Daha fazla bilgi için bkz. [proxy sunucularıyla ve yük dengeleyicilerle çalışacak ASP.NET Core yapılandırma](xref:host-and-deploy/proxy-load-balancer).

## <a name="additional-resources"></a>Ek kaynaklar

* [HTTP. sys ile Windows kimlik doğrulamasını etkinleştirme](xref:security/authentication/windowsauth#httpsys)
* [HTTP Sunucusu API 'SI](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx)
* [ASPNET/HttpSysServer GitHub deposu (kaynak kodu)](https://github.com/aspnet/HttpSysServer/)
* [Ana bilgisayar](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end
