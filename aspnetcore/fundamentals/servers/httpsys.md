---
title: ASP.NET Core HTTP.sys web sunucusu uygulaması
author: rick-anderson
description: Windows'da Core'ASP.NET için bir web sunucusu olan HTTP.sys hakkında bilgi edinin. HTTP.sys çekirdek modu sürücüsü üzerine inşa edilen HTTP.sys, IIS olmadan Internet'e doğrudan bağlantı için kullanılabilecek Kerkenez'e alternatiftir.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: fundamentals/servers/httpsys
ms.openlocfilehash: 3e858a974d6a5c008969c3c51a507880cc25a7ff
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78666924"
---
# <a name="httpsys-web-server-implementation-in-aspnet-core"></a>ASP.NET Core HTTP.sys web sunucusu uygulaması

Yazar: [Tom Dykstra](https://github.com/tdykstra) ve [Chris Ross](https://github.com/Tratcher)

::: moniker range=">= aspnetcore-3.1"

[HTTP.sys,](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) [ASP.NET Core için](xref:fundamentals/servers/index) yalnızca Windows'da çalışan bir web sunucusudur. HTTP.sys Kerkenez sunucusuna alternatiftir ve [Kestrel'in](xref:fundamentals/servers/kestrel) sağlamadığı bazı özellikler sunar.

> [!IMPORTANT]
> HTTP.sys [ASP.NET Çekirdek Modülü](xref:host-and-deploy/aspnet-core-module) ile uyumlu değildir ve IIS veya IIS Express ile kullanılamaz.

HTTP.sys aşağıdaki özellikleri destekler:

* [Windows Kimlik Doğrulama](xref:security/authentication/windowsauth)
* Bağlantı noktası paylaşımı
* SNI ile HTTPS
* TLS üzerinden HTTP/2 (Windows 10 veya sonrası)
* Doğrudan dosya iletimi
* Yanıtları Önbelleğe Alma
* WebSockets (Windows 8 veya sonrası)

Desteklenen Windows sürümleri:

* Windows 7 veya sonraki
* Windows Server 2008 R2 veya sonraki sürümü

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ( nasıl[indirilir](xref:index#how-to-download-a-sample))

## <a name="when-to-use-httpsys"></a>HTTP.sys ne zaman kullanılır

HTTP.sys aşağıdaki durumlarda dağıtımlar için yararlıdır:

* IIS kullanmadan sunucuyu doğrudan Internet'e maruz bırakmak gerekir.

  ![HTTP.sys internet ile doğrudan iletişim kurar](httpsys/_static/httpsys-to-internet.png)

* Dahili dağıtım, Kestrel'de bulunmayan Windows [Kimlik Doğrulama](xref:security/authentication/windowsauth)gibi bir özellik gerektirir.

  ![HTTP.sys dahili ağ ile doğrudan iletişim kurar](httpsys/_static/httpsys-to-internal.png)

HTTP.sys, birçok saldırı türüne karşı koruma sağlayan ve tam özellikli bir web sunucusunun sağlamlığını, güvenliğini ve ölçeklenebilirliğini sağlayan olgun bir teknolojidir. IIS kendisi HTTP.sys üstüne bir HTTP dinleyici olarak çalışır.

## <a name="http2-support"></a>HTTP/2 desteği

Aşağıdaki temel gereksinimler karşılanırsa, ASP.NET Core uygulamaları için [HTTP/2](https://httpwg.org/specs/rfc7540.html) etkinleştirilir:

* Windows Server 2016/Windows 10 veya sonrası
* [Uygulama Katmanı Protokolü Anlaşması (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) bağlantısı
* TLS 1.2 veya daha sonra bağlantı

Bir HTTP/2 bağlantısı kuruluysa, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) bildirir. `HTTP/2`

HTTP/2 varsayılan olarak etkinleştirilir. BIR HTTP/2 bağlantısı kurulmazsa, bağlantı HTTP/1.1'e geri döner. Windows'un gelecekteki sürümünde, HTTP/2 yapılandırma bayrakları, HTTP.sys ile HTTP/2'yi devre dışı bırakma olanağı da dahil olmak üzere kullanılabilir olacaktır.

## <a name="kernel-mode-authentication-with-kerberos"></a>Kerberos ile Çekirdek modu kimlik doğrulaması

KERBEROS kimlik doğrulama protokolü ile çekirdek modu kimlik doğrulaması için HTTP.sys delegeler. Kullanıcı modu kimlik doğrulaması Kerberos ve HTTP.sys ile desteklenmez. Makine hesabı, Active Directory'den alınan ve kullanıcının kimliğini doğrulamak için istemci tarafından sunucuya iletilen Kerberos jetonunun/biletinin şifresini çözmek için kullanılmalıdır. Hizmet Ana Adı'nı (SPN) uygulamanın kullanıcısına değil, ana bilgisayara kaydedin.

## <a name="how-to-use-httpsys"></a>HTTP.sys nasıl kullanılır?

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a>ASP.NET Core uygulamasını HTTP.sys'i kullanacak şekilde yapılandırın

<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> Gerekli <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>olan her türlü . Aşağıdaki örnek seçenekleri varsayılan değerlerine göre ayarlar:

[!code-csharp[](httpsys/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=5-13)]

Ek HTTP.sys [yapılandırmakayıt defteri ayarları](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows)üzerinden işlenir.

**HTTP.sys seçenekleri**

| Özellik | Açıklama | Varsayılan |
| -------- | ----------- | :-----: |
| [AllowSynchronousIO](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | Senkron giriş/çıkış için izin verilip `HttpContext.Request.Body` `HttpContext.Response.Body`verilmediğini kontrol edin ve . | `false` |
| [Kimlik Doğrulama.AllowAnonymous](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | Anonim isteklere izin verin. | `true` |
| [Kimlik Doğrulama.Şemalar](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | İzin verilen kimlik doğrulama düzenlerini belirtin. Dinleyicinin atılmasından önce herhangi bir zamanda değiştirilebilir. Değerler [Kimlik Doğrulama Şemaları tarafından](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes)sağlanmaktadır : `Basic`, `NTLM` `Kerberos`, `Negotiate` `None`, ve . | `None` |
| [EtkinleştirYanıtCaching](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | Uygun üstbilgi içeren yanıtlar için [çekirdek modu](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) önbelleğe alma girişimi. Yanıt, üstbilgi `Set-Cookie` `Vary`veya `Pragma` üstbilgi içermeyebilir. Bir `Cache-Control` `public` üstbilgi ve bir `shared-max-age` veya `max-age` değer veya bir `Expires` üstbilgi içermelidir. | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | En fazla eşzamanlı kabul sayısı. | 5 &times; [Çevre.<br> İşlemci Sayısı](xref:System.Environment.ProcessorCount) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | Kabul etmek için eşzamanlı bağlantıların maksimum sayısı. Sonsuz `-1` için kullanın. Kayıt `null` defterinin makine çapındaki ayarını kullanmak için kullanın. | `null`<br>(makine çapında<br>ayarı) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | <a href="#maxrequestbodysize">MaxRequestBodySize</a> bölümüne bakın. | 30000000 bayt<br>(~28.6 MB) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | Sıraya alınabilecek en fazla istek sayısı. | 1000 |
| `RequestQueueMode` | Bu, sunucunun istek sırasını oluşturmak ve yapılandırmaktan mı yoksa varolan bir kuyruğa eklenmesi ni mi yoksa sunucunun mı sorumlu olduğunu gösterir.<br>Varolan yapılandırma seçeneklerinin çoğu varolan bir kuyruğa iliştirilirken geçerli değildir. | `RequestQueueMode.Create` |
| `RequestQueueName` | HTTP.sys istek sırasının adı. | `null`(Anonim sıra) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | Yanıt gövdesi, istemci bağlantıları nedeniyle başarısız olan yazarsa özel durumlar atmalı veya normal olarak tamamlanmalıdır. | `false`<br>(normal olarak tamamlayın) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | Kayıt defterinde de <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> yapılandırılabilen HTTP.sys yapılandırmasını açığa alın. Varsayılan değerler de dahil olmak üzere her ayar hakkında daha fazla bilgi edinmek için API bağlantılarını izleyin:<ul><li>[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody) &ndash; Time HTTP Server API'nin varlık gövdesini Canlı Tut bağlantısında boşaltmasına izin verdi.</li><li>[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody) &ndash; Time istek varlık gövdesi gelmesi için izin verdi.</li><li>[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait) &ndash; Time HTTP Server API istek üstbilgiayrış ayrıştırmak için izin.</li><li>[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection) &ndash; Time boşta bağlantı için izin verilir.</li><li>[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond) &ndash; Yanıt için minimum gönderme oranı.</li><li>[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue) &ndash; Time, uygulama açmadan önce isteğin istek kuyruğunda kalmasına izin verdi.</li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> HTTP.sys'ye kaydolun. En kullanışlı [urlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), koleksiyona bir önek eklemek için kullanılır. Bunlar, dinleyicinin atılmasından önce herhangi bir zamanda değiştirilebilir. |  |

<a name="maxrequestbodysize"></a>

**MaxRequestBodySize**

Baytlarda herhangi bir istek gövdesinin izin verilen maksimum boyutu. `null`Ayarlandığında, maksimum istek gövde boyutu sınırsızdır. Bu sınırın, her zaman sınırsız olan yükseltilmiş bağlantılar üzerinde hiçbir etkisi yoktur.

Tek bir ASP.NET Core MVC uygulamasındasınırı geçersiz kılmak `IActionResult` için önerilen <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> yöntem, özniteliği bir eylem yönteminde kullanmaktır:

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

Uygulama isteği okumaya başladıktan sonra uygulama istek üzerindeki sınırı yapılandırmaya çalışırsa bir özel durum atılır. Özellik, `IsReadOnly` özelliğin `MaxRequestBodySize` salt okunur durumda olup olmadığını belirtmek için kullanılabilir, bu da sınırı yapılandırmak için çok geç olduğu anlamına gelir.

Uygulama istek başına <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> geçersiz kılınması durumunda, aşağıdakileri <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>kullanın:

[!code-csharp[](httpsys/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

Visual Studio kullanıyorsanız, uygulamanın IIS veya IIS Express'i çalıştıracak şekilde yapılandırılmadığından emin olun.

Visual Studio'da varsayılan başlatma profili IIS Express içindir. Projeyi konsol uygulaması olarak çalıştırmak için, aşağıdaki ekran görüntüsünde gösterildiği gibi seçili profili el ile değiştirin:

![Konsol uygulaması profilini seçin](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a>Windows Server'ı yapılandırma

1. Uygulama için açılacak bağlantı noktalarını belirleyin ve trafiğin HTTP.sys'ye ulaşmasını sağlamak için güvenlik duvarı bağlantı noktalarını açmak için [Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) veya [New-NetWallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet'i kullanın. Aşağıdaki komutlarda ve uygulama yapılandırmasında port 443 kullanılır.

1. Bir Azure VM'ye dağıtılırken, [Ağ Güvenlik Grubu'ndaki](/azure/virtual-machines/windows/nsg-quickstart-portal)bağlantı noktalarını açın. Aşağıdaki komutlarda ve uygulama yapılandırmasında port 443 kullanılır.

1. Gerekirse X.509 sertifikaları edinin ve yükleyin.

   Windows'da, [Yeni İmzalı Sertifika PowerShell cmdlet'i](/powershell/module/pkiclient/new-selfsignedcertificate)kullanarak kendi imzalı sertifikalar oluşturun. Desteklenmeyen bir örnek [için](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1)bkz.

   Sunucunun **Local Machine** > **Personal** deposuna kendi imzalı veya CA imzalı sertifikalar yükleyin.

1. Uygulama [çerçeveye bağımlı](/dotnet/core/deploying/#framework-dependent-deployments-fdd)bir dağıtımsa ,NET Core, .NET Framework veya her ikisini de yükleyin (uygulama .NET Framework'ü hedefleyen bir .NET Core uygulamasıysa).

   * **.NET Core** &ndash; Uygulama .NET Core [gerektiriyorsa, .NET Core İndirmelerden .NET Core](https://dotnet.microsoft.com/download) **Runtime** yükleyicisini edinin ve çalıştırın. Sunucuya tam SDK yüklemeyin.
   * **.NET Framework** &ndash; Uygulama .NET Framework gerektiriyorsa,.NET [Framework kurulum kılavuzuna](/dotnet/framework/install/)bakın. Gerekli .NET Framework'u yükleyin. En son .NET Framework için yükleyici [.NET Çekirdek İndirmeler](https://dotnet.microsoft.com/download) sayfasından edinilebilir.

   Uygulama [bağımsız](/dotnet/core/deploying/#self-contained-deployments-scd)bir dağıtımsa, uygulama dağıtımda çalışma süresini içerir. Sunucuda çerçeve yüklemesi gerekmez.

1. Uygulamadaki URL'leri ve bağlantı noktalarını yapılandırın.

   Varsayılan olarak, ASP.NET Core `http://localhost:5000`bağlanır. URL öneklerini ve bağlantı noktalarını yapılandırmak için seçenekler şunlardır:

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * `urls`komut satırı bağımsız değişkeni
   * `ASPNETCORE_URLS`çevre değişkeni
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   Aşağıdaki kod örneği, sunucunun 443 no'lu bağlantı noktasındaki yerel IP adresiyle <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> `10.0.0.4` nasıl kullanılacağını gösterir:

   [!code-csharp[](httpsys/samples_snapshot/3.x/Program.cs?highlight=7)]

   Bunun bir `UrlPrefixes` avantajı, yanlış biçimlendirilmiş öneekler için hemen bir hata iletisi oluşturulmasıdır.

   Geçersiz kılma `UrlPrefixes` `UseUrls` / `urls` / ayarlarındaki `ASPNETCORE_URLS` ayarlar. Bu nedenle, `UseUrls`bir `urls`avantajı `ASPNETCORE_URLS` , ve çevre değişkeni Kerkenez ve HTTP.sys arasında geçiş yapmak daha kolay olmasıdır.

   HTTP.sys [HTTP Server API UrlPrefix dize biçimlerini](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)kullanır.

   > [!WARNING]
   > Üst düzey joker karakter`http://*:80/` bağlamaları ( ve `http://+:80`) **kullanılmamalıdır.** Üst düzey joker karakter bağlamaları uygulama güvenliği güvenlik açıkları oluşturur. Bu hem güçlü hem de zayıf joker karakterler için geçerlidir. Joker karakterler yerine açık ana bilgisayar adları veya IP adresleri kullanın. Alt etki alanı joker karakter `*.mysub.com`bağlama (örneğin, ) tüm üst etki alanını denetlerseniz (bunun `*.com`aksine, savunmasız dır) bir güvenlik riski değildir. Daha fazla bilgi için [Bkz. RFC 7230: Bölüm 5.4: Ana Bilgisayar.](https://tools.ietf.org/html/rfc7230#section-5.4)

1. Sunucuda ön kayıt URL önekleri.

   HTTP.sys yapılandırmak için yerleşik araç *netsh.exe*olduğunu. *netsh.exe,* URL önekleri ayırmak ve X.509 sertifikalarını atamak için kullanılır. Araç yönetici ayrıcalıkları gerektirir.

   Uygulama için URL'leri kaydetmek için *netsh.exe* aracını kullanın:

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * `<URL>`&ndash; Tam nitelikli Tek düzen kaynak bulucu (URL). Joker karakter bağlama kullanmayın. Geçerli bir ana bilgisayar adı veya yerel IP adresi kullanın. *URL'de sondaki eğik çizgi içermelidir.*
   * `<USER>`&ndash; Kullanıcı veya kullanıcı grubu adını belirtir.

   Aşağıdaki örnekte, sunucunun yerel IP `10.0.0.4`adresi:

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   Bir URL kaydedildiğinde, araç `URL reservation successfully added`.

   Kayıtlı bir URL'yi `delete urlacl` silmek için aşağıdaki komutu kullanın:

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. X.509 sertifikalarını sunucuya kaydedin.

   Uygulama için sertifikaları kaydetmek için *netsh.exe* aracını kullanın:

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * `<IP>`&ndash; Bağlama için yerel IP adresini belirtir. Joker karakter bağlama kullanmayın. Geçerli bir IP adresi kullanın.
   * `<PORT>`&ndash; Bağlama için bağlantı noktasını belirtir.
   * `<THUMBPRINT>`&ndash; X.509 sertifikası parmak izi.
   * `<GUID>`&ndash; Uygulamayı bilgilendirme amacıyla temsil etmek üzere geliştirici tarafından oluşturulan GUID.

   Başvuru amacıyla GUID'i uygulamada paket etiketi olarak saklayın:

   * Görsel Stüdyo'da:
     * **Solution Explorer'daki** uygulamaya sağ tıklayarak ve **Özellikler'i**seçerek uygulamanın proje özelliklerini açın.
     * **Paket** sekmesini seçin.
     * **Etiketler** alanında oluşturduğunuz GUID'i girin.
   * Visual Studio'u kullanmadığı zaman:
     * Uygulamanın proje dosyasını açın.
     * Oluşturduğunuz `<PackageTags>` GUID ile yeni `<PropertyGroup>` veya varolan bir özellik ekleyin:

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   Aşağıdaki örnekte:

   * Sunucunun yerel IP `10.0.0.4`adresi.
   * Bir online rasgele GUID `appid` jeneratör değeri sağlar.

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   Bir sertifika kaydedildiğinde, araç `SSL Certificate successfully added`.

   Sertifika kaydını silmek `delete sslcert` için aşağıdaki komutu kullanın:

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   *netsh.exe*için referans belgeleri :

   * [Hypertext Transfer Protokolü için Netsh Komutları (HTTP)](https://technet.microsoft.com/library/cc725882.aspx)
   * [UrlPrefix Dizeleri](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)

1. Uygulamayı çalıştırın.

   Yönetici ayrıcalıkları, 1024'ten büyük bir bağlantı noktası numarasına sahip HTTP (HTTPS değil) kullanarak yerel barındırmaya bağlanırken uygulamayı çalıştırmak için gerekli değildir. Diğer yapılandırmalar için (örneğin, yerel bir IP adresi kullanarak veya bağlantı noktası 443'e bağlanmak), uygulamayı yönetici ayrıcalıklarıyla çalıştırın.

   Uygulama sunucunun genel IP adresinde yanıt verir. Bu örnekte, sunucuinternetten genel IP adresinden `104.214.79.47`ulaşılır.

   Bu örnekte bir geliştirme sertifikası kullanılır. Sayfa, tarayıcının güvenilmeyen sertifika uyarısını atladıktan sonra güvenli bir şekilde yüklenir.

   ![Uygulamanın Dizin sayfasını gösteren tarayıcı penceresi yüklendi](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a>Proxy sunucusu ve yük dengeleyici senaryoları

Internet'ten veya kurumsal bir ağdan gelen isteklerle etkileşimde bulunan HTTP.sys tarafından barındırılan uygulamalar için proxy sunucularının ve yük dengeleyicilerinin arkasında barındırma yaparken ek yapılandırma gerekebilir. Daha fazla bilgi için proxy [sunucuları ve yük dengeleyicileri ile çalışmak için ASP.NET Core'u yapılandırın'](xref:host-and-deploy/proxy-load-balancer)a bakın.

## <a name="additional-resources"></a>Ek kaynaklar

* [HTTP.sys ile Windows Kimlik Doğrulamasını Etkinleştirme](xref:security/authentication/windowsauth#httpsys)
* [HTTP Sunucu API'si](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx)
* [aspnet/HttpSysServer GitHub deposu (kaynak kodu)](https://github.com/aspnet/HttpSysServer/)
* [Ana bilgisayar](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

[HTTP.sys,](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) [ASP.NET Core için](xref:fundamentals/servers/index) yalnızca Windows'da çalışan bir web sunucusudur. HTTP.sys Kerkenez sunucusuna alternatiftir ve [Kestrel'in](xref:fundamentals/servers/kestrel) sağlamadığı bazı özellikler sunar.

> [!IMPORTANT]
> HTTP.sys [ASP.NET Çekirdek Modülü](xref:host-and-deploy/aspnet-core-module) ile uyumlu değildir ve IIS veya IIS Express ile kullanılamaz.

HTTP.sys aşağıdaki özellikleri destekler:

* [Windows Kimlik Doğrulama](xref:security/authentication/windowsauth)
* Bağlantı noktası paylaşımı
* SNI ile HTTPS
* TLS üzerinden HTTP/2 (Windows 10 veya sonrası)
* Doğrudan dosya iletimi
* Yanıtları Önbelleğe Alma
* WebSockets (Windows 8 veya sonrası)

Desteklenen Windows sürümleri:

* Windows 7 veya sonraki
* Windows Server 2008 R2 veya sonraki sürümü

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ( nasıl[indirilir](xref:index#how-to-download-a-sample))

## <a name="when-to-use-httpsys"></a>HTTP.sys ne zaman kullanılır

HTTP.sys aşağıdaki durumlarda dağıtımlar için yararlıdır:

* IIS kullanmadan sunucuyu doğrudan Internet'e maruz bırakmak gerekir.

  ![HTTP.sys internet ile doğrudan iletişim kurar](httpsys/_static/httpsys-to-internet.png)

* Dahili dağıtım, Kestrel'de bulunmayan Windows [Kimlik Doğrulama](xref:security/authentication/windowsauth)gibi bir özellik gerektirir.

  ![HTTP.sys dahili ağ ile doğrudan iletişim kurar](httpsys/_static/httpsys-to-internal.png)

HTTP.sys, birçok saldırı türüne karşı koruma sağlayan ve tam özellikli bir web sunucusunun sağlamlığını, güvenliğini ve ölçeklenebilirliğini sağlayan olgun bir teknolojidir. IIS kendisi HTTP.sys üstüne bir HTTP dinleyici olarak çalışır.

## <a name="http2-support"></a>HTTP/2 desteği

Aşağıdaki temel gereksinimler karşılanırsa, ASP.NET Core uygulamaları için [HTTP/2](https://httpwg.org/specs/rfc7540.html) etkinleştirilir:

* Windows Server 2016/Windows 10 veya sonrası
* [Uygulama Katmanı Protokolü Anlaşması (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) bağlantısı
* TLS 1.2 veya daha sonra bağlantı

Bir HTTP/2 bağlantısı kuruluysa, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) bildirir. `HTTP/2`

HTTP/2 varsayılan olarak etkinleştirilir. BIR HTTP/2 bağlantısı kurulmazsa, bağlantı HTTP/1.1'e geri döner. Windows'un gelecekteki sürümünde, HTTP/2 yapılandırma bayrakları, HTTP.sys ile HTTP/2'yi devre dışı bırakma olanağı da dahil olmak üzere kullanılabilir olacaktır.

## <a name="kernel-mode-authentication-with-kerberos"></a>Kerberos ile Çekirdek modu kimlik doğrulaması

KERBEROS kimlik doğrulama protokolü ile çekirdek modu kimlik doğrulaması için HTTP.sys delegeler. Kullanıcı modu kimlik doğrulaması Kerberos ve HTTP.sys ile desteklenmez. Makine hesabı, Active Directory'den alınan ve kullanıcının kimliğini doğrulamak için istemci tarafından sunucuya iletilen Kerberos jetonunun/biletinin şifresini çözmek için kullanılmalıdır. Hizmet Ana Adı'nı (SPN) uygulamanın kullanıcısına değil, ana bilgisayara kaydedin.

## <a name="how-to-use-httpsys"></a>HTTP.sys nasıl kullanılır?

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a>ASP.NET Core uygulamasını HTTP.sys'i kullanacak şekilde yapılandırın

<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> Gerekli <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>olan her türlü . Aşağıdaki örnek seçenekleri varsayılan değerlerine göre ayarlar:

[!code-csharp[](httpsys/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=5-13)]

Ek HTTP.sys [yapılandırmakayıt defteri ayarları](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows)üzerinden işlenir.

**HTTP.sys seçenekleri**

| Özellik | Açıklama | Varsayılan |
| -------- | ----------- | :-----: |
| [AllowSynchronousIO](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | Senkron giriş/çıkış için izin verilip `HttpContext.Request.Body` `HttpContext.Response.Body`verilmediğini kontrol edin ve . | `false` |
| [Kimlik Doğrulama.AllowAnonymous](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | Anonim isteklere izin verin. | `true` |
| [Kimlik Doğrulama.Şemalar](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | İzin verilen kimlik doğrulama düzenlerini belirtin. Dinleyicinin atılmasından önce herhangi bir zamanda değiştirilebilir. Değerler [Kimlik Doğrulama Şemaları tarafından](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes)sağlanmaktadır : `Basic`, `NTLM` `Kerberos`, `Negotiate` `None`, ve . | `None` |
| [EtkinleştirYanıtCaching](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | Uygun üstbilgi içeren yanıtlar için [çekirdek modu](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) önbelleğe alma girişimi. Yanıt, üstbilgi `Set-Cookie` `Vary`veya `Pragma` üstbilgi içermeyebilir. Bir `Cache-Control` `public` üstbilgi ve bir `shared-max-age` veya `max-age` değer veya bir `Expires` üstbilgi içermelidir. | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | En fazla eşzamanlı kabul sayısı. | 5 &times; [Çevre.<br> İşlemci Sayısı](xref:System.Environment.ProcessorCount) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | Kabul etmek için eşzamanlı bağlantıların maksimum sayısı. Sonsuz `-1` için kullanın. Kayıt `null` defterinin makine çapındaki ayarını kullanmak için kullanın. | `null`<br>(makine çapında<br>ayarı) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | <a href="#maxrequestbodysize">MaxRequestBodySize</a> bölümüne bakın. | 30000000 bayt<br>(~28.6 MB) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | Sıraya alınabilecek en fazla istek sayısı. | 1000 |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | Yanıt gövdesi, istemci bağlantıları nedeniyle başarısız olan yazarsa özel durumlar atmalı veya normal olarak tamamlanmalıdır. | `false`<br>(normal olarak tamamlayın) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | Kayıt defterinde de <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> yapılandırılabilen HTTP.sys yapılandırmasını açığa alın. Varsayılan değerler de dahil olmak üzere her ayar hakkında daha fazla bilgi edinmek için API bağlantılarını izleyin:<ul><li>[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody) &ndash; Time HTTP Server API'nin varlık gövdesini Canlı Tut bağlantısında boşaltmasına izin verdi.</li><li>[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody) &ndash; Time istek varlık gövdesi gelmesi için izin verdi.</li><li>[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait) &ndash; Time HTTP Server API istek üstbilgiayrış ayrıştırmak için izin.</li><li>[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection) &ndash; Time boşta bağlantı için izin verilir.</li><li>[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond) &ndash; Yanıt için minimum gönderme oranı.</li><li>[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue) &ndash; Time, uygulama açmadan önce isteğin istek kuyruğunda kalmasına izin verdi.</li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> HTTP.sys'ye kaydolun. En kullanışlı [urlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), koleksiyona bir önek eklemek için kullanılır. Bunlar, dinleyicinin atılmasından önce herhangi bir zamanda değiştirilebilir. |  |

<a name="maxrequestbodysize"></a>

**MaxRequestBodySize**

Baytlarda herhangi bir istek gövdesinin izin verilen maksimum boyutu. `null`Ayarlandığında, maksimum istek gövde boyutu sınırsızdır. Bu sınırın, her zaman sınırsız olan yükseltilmiş bağlantılar üzerinde hiçbir etkisi yoktur.

Tek bir ASP.NET Core MVC uygulamasındasınırı geçersiz kılmak `IActionResult` için önerilen <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> yöntem, özniteliği bir eylem yönteminde kullanmaktır:

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

Uygulama isteği okumaya başladıktan sonra uygulama istek üzerindeki sınırı yapılandırmaya çalışırsa bir özel durum atılır. Özellik, `IsReadOnly` özelliğin `MaxRequestBodySize` salt okunur durumda olup olmadığını belirtmek için kullanılabilir, bu da sınırı yapılandırmak için çok geç olduğu anlamına gelir.

Uygulama istek başına <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> geçersiz kılınması durumunda, aşağıdakileri <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>kullanın:

[!code-csharp[](httpsys/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

Visual Studio kullanıyorsanız, uygulamanın IIS veya IIS Express'i çalıştıracak şekilde yapılandırılmadığından emin olun.

Visual Studio'da varsayılan başlatma profili IIS Express içindir. Projeyi konsol uygulaması olarak çalıştırmak için, aşağıdaki ekran görüntüsünde gösterildiği gibi seçili profili el ile değiştirin:

![Konsol uygulaması profilini seçin](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a>Windows Server'ı yapılandırma

1. Uygulama için açılacak bağlantı noktalarını belirleyin ve trafiğin HTTP.sys'ye ulaşmasını sağlamak için güvenlik duvarı bağlantı noktalarını açmak için [Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) veya [New-NetWallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet'i kullanın. Aşağıdaki komutlarda ve uygulama yapılandırmasında port 443 kullanılır.

1. Bir Azure VM'ye dağıtılırken, [Ağ Güvenlik Grubu'ndaki](/azure/virtual-machines/windows/nsg-quickstart-portal)bağlantı noktalarını açın. Aşağıdaki komutlarda ve uygulama yapılandırmasında port 443 kullanılır.

1. Gerekirse X.509 sertifikaları edinin ve yükleyin.

   Windows'da, [Yeni İmzalı Sertifika PowerShell cmdlet'i](/powershell/module/pkiclient/new-selfsignedcertificate)kullanarak kendi imzalı sertifikalar oluşturun. Desteklenmeyen bir örnek [için](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1)bkz.

   Sunucunun **Local Machine** > **Personal** deposuna kendi imzalı veya CA imzalı sertifikalar yükleyin.

1. Uygulama [çerçeveye bağımlı](/dotnet/core/deploying/#framework-dependent-deployments-fdd)bir dağıtımsa ,NET Core, .NET Framework veya her ikisini de yükleyin (uygulama .NET Framework'ü hedefleyen bir .NET Core uygulamasıysa).

   * **.NET Core** &ndash; Uygulama .NET Core [gerektiriyorsa, .NET Core İndirmelerden .NET Core](https://dotnet.microsoft.com/download) **Runtime** yükleyicisini edinin ve çalıştırın. Sunucuya tam SDK yüklemeyin.
   * **.NET Framework** &ndash; Uygulama .NET Framework gerektiriyorsa,.NET [Framework kurulum kılavuzuna](/dotnet/framework/install/)bakın. Gerekli .NET Framework'u yükleyin. En son .NET Framework için yükleyici [.NET Çekirdek İndirmeler](https://dotnet.microsoft.com/download) sayfasından edinilebilir.

   Uygulama [bağımsız](/dotnet/core/deploying/#self-contained-deployments-scd)bir dağıtımsa, uygulama dağıtımda çalışma süresini içerir. Sunucuda çerçeve yüklemesi gerekmez.

1. Uygulamadaki URL'leri ve bağlantı noktalarını yapılandırın.

   Varsayılan olarak, ASP.NET Core `http://localhost:5000`bağlanır. URL öneklerini ve bağlantı noktalarını yapılandırmak için seçenekler şunlardır:

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * `urls`komut satırı bağımsız değişkeni
   * `ASPNETCORE_URLS`çevre değişkeni
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   Aşağıdaki kod örneği, sunucunun 443 no'lu bağlantı noktasındaki yerel IP adresiyle <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> `10.0.0.4` nasıl kullanılacağını gösterir:

   [!code-csharp[](httpsys/samples_snapshot/3.x/Program.cs?highlight=7)]

   Bunun bir `UrlPrefixes` avantajı, yanlış biçimlendirilmiş öneekler için hemen bir hata iletisi oluşturulmasıdır.

   Geçersiz kılma `UrlPrefixes` `UseUrls` / `urls` / ayarlarındaki `ASPNETCORE_URLS` ayarlar. Bu nedenle, `UseUrls`bir `urls`avantajı `ASPNETCORE_URLS` , ve çevre değişkeni Kerkenez ve HTTP.sys arasında geçiş yapmak daha kolay olmasıdır.

   HTTP.sys [HTTP Server API UrlPrefix dize biçimlerini](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)kullanır.

   > [!WARNING]
   > Üst düzey joker karakter`http://*:80/` bağlamaları ( ve `http://+:80`) **kullanılmamalıdır.** Üst düzey joker karakter bağlamaları uygulama güvenliği güvenlik açıkları oluşturur. Bu hem güçlü hem de zayıf joker karakterler için geçerlidir. Joker karakterler yerine açık ana bilgisayar adları veya IP adresleri kullanın. Alt etki alanı joker karakter `*.mysub.com`bağlama (örneğin, ) tüm üst etki alanını denetlerseniz (bunun `*.com`aksine, savunmasız dır) bir güvenlik riski değildir. Daha fazla bilgi için [Bkz. RFC 7230: Bölüm 5.4: Ana Bilgisayar.](https://tools.ietf.org/html/rfc7230#section-5.4)

1. Sunucuda ön kayıt URL önekleri.

   HTTP.sys yapılandırmak için yerleşik araç *netsh.exe*olduğunu. *netsh.exe,* URL önekleri ayırmak ve X.509 sertifikalarını atamak için kullanılır. Araç yönetici ayrıcalıkları gerektirir.

   Uygulama için URL'leri kaydetmek için *netsh.exe* aracını kullanın:

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * `<URL>`&ndash; Tam nitelikli Tek düzen kaynak bulucu (URL). Joker karakter bağlama kullanmayın. Geçerli bir ana bilgisayar adı veya yerel IP adresi kullanın. *URL'de sondaki eğik çizgi içermelidir.*
   * `<USER>`&ndash; Kullanıcı veya kullanıcı grubu adını belirtir.

   Aşağıdaki örnekte, sunucunun yerel IP `10.0.0.4`adresi:

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   Bir URL kaydedildiğinde, araç `URL reservation successfully added`.

   Kayıtlı bir URL'yi `delete urlacl` silmek için aşağıdaki komutu kullanın:

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. X.509 sertifikalarını sunucuya kaydedin.

   Uygulama için sertifikaları kaydetmek için *netsh.exe* aracını kullanın:

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * `<IP>`&ndash; Bağlama için yerel IP adresini belirtir. Joker karakter bağlama kullanmayın. Geçerli bir IP adresi kullanın.
   * `<PORT>`&ndash; Bağlama için bağlantı noktasını belirtir.
   * `<THUMBPRINT>`&ndash; X.509 sertifikası parmak izi.
   * `<GUID>`&ndash; Uygulamayı bilgilendirme amacıyla temsil etmek üzere geliştirici tarafından oluşturulan GUID.

   Başvuru amacıyla GUID'i uygulamada paket etiketi olarak saklayın:

   * Görsel Stüdyo'da:
     * **Solution Explorer'daki** uygulamaya sağ tıklayarak ve **Özellikler'i**seçerek uygulamanın proje özelliklerini açın.
     * **Paket** sekmesini seçin.
     * **Etiketler** alanında oluşturduğunuz GUID'i girin.
   * Visual Studio'u kullanmadığı zaman:
     * Uygulamanın proje dosyasını açın.
     * Oluşturduğunuz `<PackageTags>` GUID ile yeni `<PropertyGroup>` veya varolan bir özellik ekleyin:

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   Aşağıdaki örnekte:

   * Sunucunun yerel IP `10.0.0.4`adresi.
   * Bir online rasgele GUID `appid` jeneratör değeri sağlar.

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   Bir sertifika kaydedildiğinde, araç `SSL Certificate successfully added`.

   Sertifika kaydını silmek `delete sslcert` için aşağıdaki komutu kullanın:

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   *netsh.exe*için referans belgeleri :

   * [Hypertext Transfer Protokolü için Netsh Komutları (HTTP)](https://technet.microsoft.com/library/cc725882.aspx)
   * [UrlPrefix Dizeleri](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)

1. Uygulamayı çalıştırın.

   Yönetici ayrıcalıkları, 1024'ten büyük bir bağlantı noktası numarasına sahip HTTP (HTTPS değil) kullanarak yerel barındırmaya bağlanırken uygulamayı çalıştırmak için gerekli değildir. Diğer yapılandırmalar için (örneğin, yerel bir IP adresi kullanarak veya bağlantı noktası 443'e bağlanmak), uygulamayı yönetici ayrıcalıklarıyla çalıştırın.

   Uygulama sunucunun genel IP adresinde yanıt verir. Bu örnekte, sunucuinternetten genel IP adresinden `104.214.79.47`ulaşılır.

   Bu örnekte bir geliştirme sertifikası kullanılır. Sayfa, tarayıcının güvenilmeyen sertifika uyarısını atladıktan sonra güvenli bir şekilde yüklenir.

   ![Uygulamanın Dizin sayfasını gösteren tarayıcı penceresi yüklendi](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a>Proxy sunucusu ve yük dengeleyici senaryoları

Internet'ten veya kurumsal bir ağdan gelen isteklerle etkileşimde bulunan HTTP.sys tarafından barındırılan uygulamalar için proxy sunucularının ve yük dengeleyicilerinin arkasında barındırma yaparken ek yapılandırma gerekebilir. Daha fazla bilgi için proxy [sunucuları ve yük dengeleyicileri ile çalışmak için ASP.NET Core'u yapılandırın'](xref:host-and-deploy/proxy-load-balancer)a bakın.

## <a name="additional-resources"></a>Ek kaynaklar

* [HTTP.sys ile Windows Kimlik Doğrulamasını Etkinleştirme](xref:security/authentication/windowsauth#httpsys)
* [HTTP Sunucu API'si](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx)
* [aspnet/HttpSysServer GitHub deposu (kaynak kodu)](https://github.com/aspnet/HttpSysServer/)
* [Ana bilgisayar](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[HTTP.sys,](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) [ASP.NET Core için](xref:fundamentals/servers/index) yalnızca Windows'da çalışan bir web sunucusudur. HTTP.sys Kerkenez sunucusuna alternatiftir ve [Kestrel'in](xref:fundamentals/servers/kestrel) sağlamadığı bazı özellikler sunar.

> [!IMPORTANT]
> HTTP.sys [ASP.NET Çekirdek Modülü](xref:host-and-deploy/aspnet-core-module) ile uyumlu değildir ve IIS veya IIS Express ile kullanılamaz.

HTTP.sys aşağıdaki özellikleri destekler:

* [Windows Kimlik Doğrulama](xref:security/authentication/windowsauth)
* Bağlantı noktası paylaşımı
* SNI ile HTTPS
* TLS üzerinden HTTP/2 (Windows 10 veya sonrası)
* Doğrudan dosya iletimi
* Yanıtları Önbelleğe Alma
* WebSockets (Windows 8 veya sonrası)

Desteklenen Windows sürümleri:

* Windows 7 veya sonraki
* Windows Server 2008 R2 veya sonraki sürümü

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ( nasıl[indirilir](xref:index#how-to-download-a-sample))

## <a name="when-to-use-httpsys"></a>HTTP.sys ne zaman kullanılır

HTTP.sys aşağıdaki durumlarda dağıtımlar için yararlıdır:

* IIS kullanmadan sunucuyu doğrudan Internet'e maruz bırakmak gerekir.

  ![HTTP.sys internet ile doğrudan iletişim kurar](httpsys/_static/httpsys-to-internet.png)

* Dahili dağıtım, Kestrel'de bulunmayan Windows [Kimlik Doğrulama](xref:security/authentication/windowsauth)gibi bir özellik gerektirir.

  ![HTTP.sys dahili ağ ile doğrudan iletişim kurar](httpsys/_static/httpsys-to-internal.png)

HTTP.sys, birçok saldırı türüne karşı koruma sağlayan ve tam özellikli bir web sunucusunun sağlamlığını, güvenliğini ve ölçeklenebilirliğini sağlayan olgun bir teknolojidir. IIS kendisi HTTP.sys üstüne bir HTTP dinleyici olarak çalışır.

## <a name="http2-support"></a>HTTP/2 desteği

Aşağıdaki temel gereksinimler karşılanırsa, ASP.NET Core uygulamaları için [HTTP/2](https://httpwg.org/specs/rfc7540.html) etkinleştirilir:

* Windows Server 2016/Windows 10 veya sonrası
* [Uygulama Katmanı Protokolü Anlaşması (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) bağlantısı
* TLS 1.2 veya daha sonra bağlantı

Bir HTTP/2 bağlantısı kuruluysa, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) bildirir. `HTTP/2`

HTTP/2 varsayılan olarak etkinleştirilir. BIR HTTP/2 bağlantısı kurulmazsa, bağlantı HTTP/1.1'e geri döner. Windows'un gelecekteki sürümünde, HTTP/2 yapılandırma bayrakları, HTTP.sys ile HTTP/2'yi devre dışı bırakma olanağı da dahil olmak üzere kullanılabilir olacaktır.

## <a name="kernel-mode-authentication-with-kerberos"></a>Kerberos ile Çekirdek modu kimlik doğrulaması

KERBEROS kimlik doğrulama protokolü ile çekirdek modu kimlik doğrulaması için HTTP.sys delegeler. Kullanıcı modu kimlik doğrulaması Kerberos ve HTTP.sys ile desteklenmez. Makine hesabı, Active Directory'den alınan ve kullanıcının kimliğini doğrulamak için istemci tarafından sunucuya iletilen Kerberos jetonunun/biletinin şifresini çözmek için kullanılmalıdır. Hizmet Ana Adı'nı (SPN) uygulamanın kullanıcısına değil, ana bilgisayara kaydedin.

## <a name="how-to-use-httpsys"></a>HTTP.sys nasıl kullanılır?

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a>ASP.NET Core uygulamasını HTTP.sys'i kullanacak şekilde yapılandırın

[Microsoft.AspNetCore.App metapaketini](xref:fundamentals/metapackage-app) [(nuget.org)](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)kullanırken proje dosyasında bir paket başvurusu gerekli değildir. `Microsoft.AspNetCore.App` Metapaketi kullanmadığınızda, [Microsoft.AspNetCore.Server.HttpSys'e](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/)bir paket başvurusu ekleyin.

<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> Gerekli <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>olan her türlü . Aşağıdaki örnek seçenekleri varsayılan değerlerine göre ayarlar:

[!code-csharp[](httpsys/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=4-12)]

Ek HTTP.sys [yapılandırmakayıt defteri ayarları](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows)üzerinden işlenir.

**HTTP.sys seçenekleri**

| Özellik | Açıklama | Varsayılan |
| -------- | ----------- | :-----: |
| [AllowSynchronousIO](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | Senkron giriş/çıkış için izin verilip `HttpContext.Request.Body` `HttpContext.Response.Body`verilmediğini kontrol edin ve . | `true` |
| [Kimlik Doğrulama.AllowAnonymous](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | Anonim isteklere izin verin. | `true` |
| [Kimlik Doğrulama.Şemalar](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | İzin verilen kimlik doğrulama düzenlerini belirtin. Dinleyicinin atılmasından önce herhangi bir zamanda değiştirilebilir. Değerler [Kimlik Doğrulama Şemaları tarafından](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes)sağlanmaktadır : `Basic`, `NTLM` `Kerberos`, `Negotiate` `None`, ve . | `None` |
| [EtkinleştirYanıtCaching](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | Uygun üstbilgi içeren yanıtlar için [çekirdek modu](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) önbelleğe alma girişimi. Yanıt, üstbilgi `Set-Cookie` `Vary`veya `Pragma` üstbilgi içermeyebilir. Bir `Cache-Control` `public` üstbilgi ve bir `shared-max-age` veya `max-age` değer veya bir `Expires` üstbilgi içermelidir. | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | En fazla eşzamanlı kabul sayısı. | 5 &times; [Çevre.<br> İşlemci Sayısı](xref:System.Environment.ProcessorCount) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | Kabul etmek için eşzamanlı bağlantıların maksimum sayısı. Sonsuz `-1` için kullanın. Kayıt `null` defterinin makine çapındaki ayarını kullanmak için kullanın. | `null`<br>(makine çapında<br>ayarı) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | <a href="#maxrequestbodysize">MaxRequestBodySize</a> bölümüne bakın. | 30000000 bayt<br>(~28.6 MB) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | Sıraya alınabilecek en fazla istek sayısı. | 1000 |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | Yanıt gövdesi, istemci bağlantıları nedeniyle başarısız olan yazarsa özel durumlar atmalı veya normal olarak tamamlanmalıdır. | `false`<br>(normal olarak tamamlayın) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | Kayıt defterinde de <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> yapılandırılabilen HTTP.sys yapılandırmasını açığa alın. Varsayılan değerler de dahil olmak üzere her ayar hakkında daha fazla bilgi edinmek için API bağlantılarını izleyin:<ul><li>[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody) &ndash; Time HTTP Server API'nin varlık gövdesini Canlı Tut bağlantısında boşaltmasına izin verdi.</li><li>[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody) &ndash; Time istek varlık gövdesi gelmesi için izin verdi.</li><li>[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait) &ndash; Time HTTP Server API istek üstbilgiayrış ayrıştırmak için izin.</li><li>[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection) &ndash; Time boşta bağlantı için izin verilir.</li><li>[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond) &ndash; Yanıt için minimum gönderme oranı.</li><li>[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue) &ndash; Time, uygulama açmadan önce isteğin istek kuyruğunda kalmasına izin verdi.</li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> HTTP.sys'ye kaydolun. En kullanışlı [urlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), koleksiyona bir önek eklemek için kullanılır. Bunlar, dinleyicinin atılmasından önce herhangi bir zamanda değiştirilebilir. |  |

<a name="maxrequestbodysize"></a>

**MaxRequestBodySize**

Baytlarda herhangi bir istek gövdesinin izin verilen maksimum boyutu. `null`Ayarlandığında, maksimum istek gövde boyutu sınırsızdır. Bu sınırın, her zaman sınırsız olan yükseltilmiş bağlantılar üzerinde hiçbir etkisi yoktur.

Tek bir ASP.NET Core MVC uygulamasındasınırı geçersiz kılmak `IActionResult` için önerilen <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> yöntem, özniteliği bir eylem yönteminde kullanmaktır:

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

Uygulama isteği okumaya başladıktan sonra uygulama istek üzerindeki sınırı yapılandırmaya çalışırsa bir özel durum atılır. Özellik, `IsReadOnly` özelliğin `MaxRequestBodySize` salt okunur durumda olup olmadığını belirtmek için kullanılabilir, bu da sınırı yapılandırmak için çok geç olduğu anlamına gelir.

Uygulama istek başına <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> geçersiz kılınması durumunda, aşağıdakileri <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>kullanın:

[!code-csharp[](httpsys/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

Visual Studio kullanıyorsanız, uygulamanın IIS veya IIS Express'i çalıştıracak şekilde yapılandırılmadığından emin olun.

Visual Studio'da varsayılan başlatma profili IIS Express içindir. Projeyi konsol uygulaması olarak çalıştırmak için, aşağıdaki ekran görüntüsünde gösterildiği gibi seçili profili el ile değiştirin:

![Konsol uygulaması profilini seçin](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a>Windows Server'ı yapılandırma

1. Uygulama için açılacak bağlantı noktalarını belirleyin ve trafiğin HTTP.sys'ye ulaşmasını sağlamak için güvenlik duvarı bağlantı noktalarını açmak için [Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) veya [New-NetWallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet'i kullanın. Aşağıdaki komutlarda ve uygulama yapılandırmasında port 443 kullanılır.

1. Bir Azure VM'ye dağıtılırken, [Ağ Güvenlik Grubu'ndaki](/azure/virtual-machines/windows/nsg-quickstart-portal)bağlantı noktalarını açın. Aşağıdaki komutlarda ve uygulama yapılandırmasında port 443 kullanılır.

1. Gerekirse X.509 sertifikaları edinin ve yükleyin.

   Windows'da, [Yeni İmzalı Sertifika PowerShell cmdlet'i](/powershell/module/pkiclient/new-selfsignedcertificate)kullanarak kendi imzalı sertifikalar oluşturun. Desteklenmeyen bir örnek [için](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1)bkz.

   Sunucunun **Local Machine** > **Personal** deposuna kendi imzalı veya CA imzalı sertifikalar yükleyin.

1. Uygulama [çerçeveye bağımlı](/dotnet/core/deploying/#framework-dependent-deployments-fdd)bir dağıtımsa ,NET Core, .NET Framework veya her ikisini de yükleyin (uygulama .NET Framework'ü hedefleyen bir .NET Core uygulamasıysa).

   * **.NET Core** &ndash; Uygulama .NET Core [gerektiriyorsa, .NET Core İndirmelerden .NET Core](https://dotnet.microsoft.com/download) **Runtime** yükleyicisini edinin ve çalıştırın. Sunucuya tam SDK yüklemeyin.
   * **.NET Framework** &ndash; Uygulama .NET Framework gerektiriyorsa,.NET [Framework kurulum kılavuzuna](/dotnet/framework/install/)bakın. Gerekli .NET Framework'u yükleyin. En son .NET Framework için yükleyici [.NET Çekirdek İndirmeler](https://dotnet.microsoft.com/download) sayfasından edinilebilir.

   Uygulama [bağımsız](/dotnet/core/deploying/#self-contained-deployments-scd)bir dağıtımsa, uygulama dağıtımda çalışma süresini içerir. Sunucuda çerçeve yüklemesi gerekmez.

1. Uygulamadaki URL'leri ve bağlantı noktalarını yapılandırın.

   Varsayılan olarak, ASP.NET Core `http://localhost:5000`bağlanır. URL öneklerini ve bağlantı noktalarını yapılandırmak için seçenekler şunlardır:

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * `urls`komut satırı bağımsız değişkeni
   * `ASPNETCORE_URLS`çevre değişkeni
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   Aşağıdaki kod örneği, sunucunun 443 no'lu bağlantı noktasındaki yerel IP adresiyle <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> `10.0.0.4` nasıl kullanılacağını gösterir:

   [!code-csharp[](httpsys/samples_snapshot/2.x/Program.cs?highlight=6)]

   Bunun bir `UrlPrefixes` avantajı, yanlış biçimlendirilmiş öneekler için hemen bir hata iletisi oluşturulmasıdır.

   Geçersiz kılma `UrlPrefixes` `UseUrls` / `urls` / ayarlarındaki `ASPNETCORE_URLS` ayarlar. Bu nedenle, `UseUrls`bir `urls`avantajı `ASPNETCORE_URLS` , ve çevre değişkeni Kerkenez ve HTTP.sys arasında geçiş yapmak daha kolay olmasıdır.

   HTTP.sys [HTTP Server API UrlPrefix dize biçimlerini](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)kullanır.

   > [!WARNING]
   > Üst düzey joker karakter`http://*:80/` bağlamaları ( ve `http://+:80`) **kullanılmamalıdır.** Üst düzey joker karakter bağlamaları uygulama güvenliği güvenlik açıkları oluşturur. Bu hem güçlü hem de zayıf joker karakterler için geçerlidir. Joker karakterler yerine açık ana bilgisayar adları veya IP adresleri kullanın. Alt etki alanı joker karakter `*.mysub.com`bağlama (örneğin, ) tüm üst etki alanını denetlerseniz (bunun `*.com`aksine, savunmasız dır) bir güvenlik riski değildir. Daha fazla bilgi için [Bkz. RFC 7230: Bölüm 5.4: Ana Bilgisayar.](https://tools.ietf.org/html/rfc7230#section-5.4)

1. Sunucuda ön kayıt URL önekleri.

   HTTP.sys yapılandırmak için yerleşik araç *netsh.exe*olduğunu. *netsh.exe,* URL önekleri ayırmak ve X.509 sertifikalarını atamak için kullanılır. Araç yönetici ayrıcalıkları gerektirir.

   Uygulama için URL'leri kaydetmek için *netsh.exe* aracını kullanın:

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * `<URL>`&ndash; Tam nitelikli Tek düzen kaynak bulucu (URL). Joker karakter bağlama kullanmayın. Geçerli bir ana bilgisayar adı veya yerel IP adresi kullanın. *URL'de sondaki eğik çizgi içermelidir.*
   * `<USER>`&ndash; Kullanıcı veya kullanıcı grubu adını belirtir.

   Aşağıdaki örnekte, sunucunun yerel IP `10.0.0.4`adresi:

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   Bir URL kaydedildiğinde, araç `URL reservation successfully added`.

   Kayıtlı bir URL'yi `delete urlacl` silmek için aşağıdaki komutu kullanın:

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. X.509 sertifikalarını sunucuya kaydedin.

   Uygulama için sertifikaları kaydetmek için *netsh.exe* aracını kullanın:

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * `<IP>`&ndash; Bağlama için yerel IP adresini belirtir. Joker karakter bağlama kullanmayın. Geçerli bir IP adresi kullanın.
   * `<PORT>`&ndash; Bağlama için bağlantı noktasını belirtir.
   * `<THUMBPRINT>`&ndash; X.509 sertifikası parmak izi.
   * `<GUID>`&ndash; Uygulamayı bilgilendirme amacıyla temsil etmek üzere geliştirici tarafından oluşturulan GUID.

   Başvuru amacıyla GUID'i uygulamada paket etiketi olarak saklayın:

   * Görsel Stüdyo'da:
     * **Solution Explorer'daki** uygulamaya sağ tıklayarak ve **Özellikler'i**seçerek uygulamanın proje özelliklerini açın.
     * **Paket** sekmesini seçin.
     * **Etiketler** alanında oluşturduğunuz GUID'i girin.
   * Visual Studio'u kullanmadığı zaman:
     * Uygulamanın proje dosyasını açın.
     * Oluşturduğunuz `<PackageTags>` GUID ile yeni `<PropertyGroup>` veya varolan bir özellik ekleyin:

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   Aşağıdaki örnekte:

   * Sunucunun yerel IP `10.0.0.4`adresi.
   * Bir online rasgele GUID `appid` jeneratör değeri sağlar.

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   Bir sertifika kaydedildiğinde, araç `SSL Certificate successfully added`.

   Sertifika kaydını silmek `delete sslcert` için aşağıdaki komutu kullanın:

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   *netsh.exe*için referans belgeleri :

   * [Hypertext Transfer Protokolü için Netsh Komutları (HTTP)](https://technet.microsoft.com/library/cc725882.aspx)
   * [UrlPrefix Dizeleri](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)

1. Uygulamayı çalıştırın.

   Yönetici ayrıcalıkları, 1024'ten büyük bir bağlantı noktası numarasına sahip HTTP (HTTPS değil) kullanarak yerel barındırmaya bağlanırken uygulamayı çalıştırmak için gerekli değildir. Diğer yapılandırmalar için (örneğin, yerel bir IP adresi kullanarak veya bağlantı noktası 443'e bağlanmak), uygulamayı yönetici ayrıcalıklarıyla çalıştırın.

   Uygulama sunucunun genel IP adresinde yanıt verir. Bu örnekte, sunucuinternetten genel IP adresinden `104.214.79.47`ulaşılır.

   Bu örnekte bir geliştirme sertifikası kullanılır. Sayfa, tarayıcının güvenilmeyen sertifika uyarısını atladıktan sonra güvenli bir şekilde yüklenir.

   ![Uygulamanın Dizin sayfasını gösteren tarayıcı penceresi yüklendi](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a>Proxy sunucusu ve yük dengeleyici senaryoları

Internet'ten veya kurumsal bir ağdan gelen isteklerle etkileşimde bulunan HTTP.sys tarafından barındırılan uygulamalar için proxy sunucularının ve yük dengeleyicilerinin arkasında barındırma yaparken ek yapılandırma gerekebilir. Daha fazla bilgi için proxy [sunucuları ve yük dengeleyicileri ile çalışmak için ASP.NET Core'u yapılandırın'](xref:host-and-deploy/proxy-load-balancer)a bakın.

## <a name="additional-resources"></a>Ek kaynaklar

* [HTTP.sys ile Windows Kimlik Doğrulamasını Etkinleştirme](xref:security/authentication/windowsauth#httpsys)
* [HTTP Sunucu API'si](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx)
* [aspnet/HttpSysServer GitHub deposu (kaynak kodu)](https://github.com/aspnet/HttpSysServer/)
* [Ana bilgisayar](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

[HTTP.sys,](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) [ASP.NET Core için](xref:fundamentals/servers/index) yalnızca Windows'da çalışan bir web sunucusudur. HTTP.sys Kerkenez sunucusuna alternatiftir ve [Kestrel'in](xref:fundamentals/servers/kestrel) sağlamadığı bazı özellikler sunar.

> [!IMPORTANT]
> HTTP.sys [ASP.NET Çekirdek Modülü](xref:host-and-deploy/aspnet-core-module) ile uyumlu değildir ve IIS veya IIS Express ile kullanılamaz.

HTTP.sys aşağıdaki özellikleri destekler:

* [Windows Kimlik Doğrulama](xref:security/authentication/windowsauth)
* Bağlantı noktası paylaşımı
* SNI ile HTTPS
* TLS üzerinden HTTP/2 (Windows 10 veya sonrası)
* Doğrudan dosya iletimi
* Yanıtları Önbelleğe Alma
* WebSockets (Windows 8 veya sonrası)

Desteklenen Windows sürümleri:

* Windows 7 veya sonraki
* Windows Server 2008 R2 veya sonraki sürümü

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ( nasıl[indirilir](xref:index#how-to-download-a-sample))

## <a name="when-to-use-httpsys"></a>HTTP.sys ne zaman kullanılır

HTTP.sys aşağıdaki durumlarda dağıtımlar için yararlıdır:

* IIS kullanmadan sunucuyu doğrudan Internet'e maruz bırakmak gerekir.

  ![HTTP.sys internet ile doğrudan iletişim kurar](httpsys/_static/httpsys-to-internet.png)

* Dahili dağıtım, Kestrel'de bulunmayan Windows [Kimlik Doğrulama](xref:security/authentication/windowsauth)gibi bir özellik gerektirir.

  ![HTTP.sys dahili ağ ile doğrudan iletişim kurar](httpsys/_static/httpsys-to-internal.png)

HTTP.sys, birçok saldırı türüne karşı koruma sağlayan ve tam özellikli bir web sunucusunun sağlamlığını, güvenliğini ve ölçeklenebilirliğini sağlayan olgun bir teknolojidir. IIS kendisi HTTP.sys üstüne bir HTTP dinleyici olarak çalışır.

## <a name="http2-support"></a>HTTP/2 desteği

Aşağıdaki temel gereksinimler karşılanırsa, ASP.NET Core uygulamaları için [HTTP/2](https://httpwg.org/specs/rfc7540.html) etkinleştirilir:

* Windows Server 2016/Windows 10 veya sonrası
* [Uygulama Katmanı Protokolü Anlaşması (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) bağlantısı
* TLS 1.2 veya daha sonra bağlantı

Bir HTTP/2 bağlantısı kuruluysa, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) bildirir. `HTTP/1.1`

HTTP/2 varsayılan olarak etkinleştirilir. BIR HTTP/2 bağlantısı kurulmazsa, bağlantı HTTP/1.1'e geri döner. Windows'un gelecekteki sürümünde, HTTP/2 yapılandırma bayrakları, HTTP.sys ile HTTP/2'yi devre dışı bırakma olanağı da dahil olmak üzere kullanılabilir olacaktır.

## <a name="kernel-mode-authentication-with-kerberos"></a>Kerberos ile Çekirdek modu kimlik doğrulaması

KERBEROS kimlik doğrulama protokolü ile çekirdek modu kimlik doğrulaması için HTTP.sys delegeler. Kullanıcı modu kimlik doğrulaması Kerberos ve HTTP.sys ile desteklenmez. Makine hesabı, Active Directory'den alınan ve kullanıcının kimliğini doğrulamak için istemci tarafından sunucuya iletilen Kerberos jetonunun/biletinin şifresini çözmek için kullanılmalıdır. Hizmet Ana Adı'nı (SPN) uygulamanın kullanıcısına değil, ana bilgisayara kaydedin.

## <a name="how-to-use-httpsys"></a>HTTP.sys nasıl kullanılır?

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a>ASP.NET Core uygulamasını HTTP.sys'i kullanacak şekilde yapılandırın

[Microsoft.AspNetCore.App metapaketini](xref:fundamentals/metapackage-app) [(nuget.org)](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)kullanırken proje dosyasında bir paket başvurusu gerekli değildir. `Microsoft.AspNetCore.App` Metapaketi kullanmadığınızda, [Microsoft.AspNetCore.Server.HttpSys'e](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/)bir paket başvurusu ekleyin.

<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> Gerekli <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>olan her türlü . Aşağıdaki örnek seçenekleri varsayılan değerlerine göre ayarlar:

[!code-csharp[](httpsys/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=4-12)]

Ek HTTP.sys [yapılandırmakayıt defteri ayarları](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows)üzerinden işlenir.

**HTTP.sys seçenekleri**

| Özellik | Açıklama | Varsayılan |
| -------- | ----------- | :-----: |
| [AllowSynchronousIO](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | Senkron giriş/çıkış için izin verilip `HttpContext.Request.Body` `HttpContext.Response.Body`verilmediğini kontrol edin ve . | `true` |
| [Kimlik Doğrulama.AllowAnonymous](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | Anonim isteklere izin verin. | `true` |
| [Kimlik Doğrulama.Şemalar](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | İzin verilen kimlik doğrulama düzenlerini belirtin. Dinleyicinin atılmasından önce herhangi bir zamanda değiştirilebilir. Değerler [Kimlik Doğrulama Şemaları tarafından](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes)sağlanmaktadır : `Basic`, `NTLM` `Kerberos`, `Negotiate` `None`, ve . | `None` |
| [EtkinleştirYanıtCaching](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | Uygun üstbilgi içeren yanıtlar için [çekirdek modu](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) önbelleğe alma girişimi. Yanıt, üstbilgi `Set-Cookie` `Vary`veya `Pragma` üstbilgi içermeyebilir. Bir `Cache-Control` `public` üstbilgi ve bir `shared-max-age` veya `max-age` değer veya bir `Expires` üstbilgi içermelidir. | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | En fazla eşzamanlı kabul sayısı. | 5 &times; [Çevre.<br> İşlemci Sayısı](xref:System.Environment.ProcessorCount) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | Kabul etmek için eşzamanlı bağlantıların maksimum sayısı. Sonsuz `-1` için kullanın. Kayıt `null` defterinin makine çapındaki ayarını kullanmak için kullanın. | `null`<br>(makine çapında<br>ayarı) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | <a href="#maxrequestbodysize">MaxRequestBodySize</a> bölümüne bakın. | 30000000 bayt<br>(~28.6 MB) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | Sıraya alınabilecek en fazla istek sayısı. | 1000 |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | Yanıt gövdesi, istemci bağlantıları nedeniyle başarısız olan yazarsa özel durumlar atmalı veya normal olarak tamamlanmalıdır. | `false`<br>(normal olarak tamamlayın) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | Kayıt defterinde de <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> yapılandırılabilen HTTP.sys yapılandırmasını açığa alın. Varsayılan değerler de dahil olmak üzere her ayar hakkında daha fazla bilgi edinmek için API bağlantılarını izleyin:<ul><li>[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody) &ndash; Time HTTP Server API'nin varlık gövdesini Canlı Tut bağlantısında boşaltmasına izin verdi.</li><li>[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody) &ndash; Time istek varlık gövdesi gelmesi için izin verdi.</li><li>[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait) &ndash; Time HTTP Server API istek üstbilgiayrış ayrıştırmak için izin.</li><li>[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection) &ndash; Time boşta bağlantı için izin verilir.</li><li>[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond) &ndash; Yanıt için minimum gönderme oranı.</li><li>[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue) &ndash; Time, uygulama açmadan önce isteğin istek kuyruğunda kalmasına izin verdi.</li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> HTTP.sys'ye kaydolun. En kullanışlı [urlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), koleksiyona bir önek eklemek için kullanılır. Bunlar, dinleyicinin atılmasından önce herhangi bir zamanda değiştirilebilir. |  |

<a name="maxrequestbodysize"></a>

**MaxRequestBodySize**

Baytlarda herhangi bir istek gövdesinin izin verilen maksimum boyutu. `null`Ayarlandığında, maksimum istek gövde boyutu sınırsızdır. Bu sınırın, her zaman sınırsız olan yükseltilmiş bağlantılar üzerinde hiçbir etkisi yoktur.

Tek bir ASP.NET Core MVC uygulamasındasınırı geçersiz kılmak `IActionResult` için önerilen <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> yöntem, özniteliği bir eylem yönteminde kullanmaktır:

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

Uygulama isteği okumaya başladıktan sonra uygulama istek üzerindeki sınırı yapılandırmaya çalışırsa bir özel durum atılır. Özellik, `IsReadOnly` özelliğin `MaxRequestBodySize` salt okunur durumda olup olmadığını belirtmek için kullanılabilir, bu da sınırı yapılandırmak için çok geç olduğu anlamına gelir.

Uygulama istek başına <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> geçersiz kılınması durumunda, aşağıdakileri <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>kullanın:

[!code-csharp[](httpsys/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

Visual Studio kullanıyorsanız, uygulamanın IIS veya IIS Express'i çalıştıracak şekilde yapılandırılmadığından emin olun.

Visual Studio'da varsayılan başlatma profili IIS Express içindir. Projeyi konsol uygulaması olarak çalıştırmak için, aşağıdaki ekran görüntüsünde gösterildiği gibi seçili profili el ile değiştirin:

![Konsol uygulaması profilini seçin](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a>Windows Server'ı yapılandırma

1. Uygulama için açılacak bağlantı noktalarını belirleyin ve trafiğin HTTP.sys'ye ulaşmasını sağlamak için güvenlik duvarı bağlantı noktalarını açmak için [Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) veya [New-NetWallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet'i kullanın. Aşağıdaki komutlarda ve uygulama yapılandırmasında port 443 kullanılır.

1. Bir Azure VM'ye dağıtılırken, [Ağ Güvenlik Grubu'ndaki](/azure/virtual-machines/windows/nsg-quickstart-portal)bağlantı noktalarını açın. Aşağıdaki komutlarda ve uygulama yapılandırmasında port 443 kullanılır.

1. Gerekirse X.509 sertifikaları edinin ve yükleyin.

   Windows'da, [Yeni İmzalı Sertifika PowerShell cmdlet'i](/powershell/module/pkiclient/new-selfsignedcertificate)kullanarak kendi imzalı sertifikalar oluşturun. Desteklenmeyen bir örnek [için](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1)bkz.

   Sunucunun **Local Machine** > **Personal** deposuna kendi imzalı veya CA imzalı sertifikalar yükleyin.

1. Uygulama [çerçeveye bağımlı](/dotnet/core/deploying/#framework-dependent-deployments-fdd)bir dağıtımsa ,NET Core, .NET Framework veya her ikisini de yükleyin (uygulama .NET Framework'ü hedefleyen bir .NET Core uygulamasıysa).

   * **.NET Core** &ndash; Uygulama .NET Core [gerektiriyorsa, .NET Core İndirmelerden .NET Core](https://dotnet.microsoft.com/download) **Runtime** yükleyicisini edinin ve çalıştırın. Sunucuya tam SDK yüklemeyin.
   * **.NET Framework** &ndash; Uygulama .NET Framework gerektiriyorsa,.NET [Framework kurulum kılavuzuna](/dotnet/framework/install/)bakın. Gerekli .NET Framework'u yükleyin. En son .NET Framework için yükleyici [.NET Çekirdek İndirmeler](https://dotnet.microsoft.com/download) sayfasından edinilebilir.

   Uygulama [bağımsız](/dotnet/core/deploying/#self-contained-deployments-scd)bir dağıtımsa, uygulama dağıtımda çalışma süresini içerir. Sunucuda çerçeve yüklemesi gerekmez.

1. Uygulamadaki URL'leri ve bağlantı noktalarını yapılandırın.

   Varsayılan olarak, ASP.NET Core `http://localhost:5000`bağlanır. URL öneklerini ve bağlantı noktalarını yapılandırmak için seçenekler şunlardır:

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * `urls`komut satırı bağımsız değişkeni
   * `ASPNETCORE_URLS`çevre değişkeni
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   Aşağıdaki kod örneği, sunucunun 443 no'lu bağlantı noktasındaki yerel IP adresiyle <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> `10.0.0.4` nasıl kullanılacağını gösterir:

   [!code-csharp[](httpsys/samples_snapshot/2.x/Program.cs?highlight=6)]

   Bunun bir `UrlPrefixes` avantajı, yanlış biçimlendirilmiş öneekler için hemen bir hata iletisi oluşturulmasıdır.

   Geçersiz kılma `UrlPrefixes` `UseUrls` / `urls` / ayarlarındaki `ASPNETCORE_URLS` ayarlar. Bu nedenle, `UseUrls`bir `urls`avantajı `ASPNETCORE_URLS` , ve çevre değişkeni Kerkenez ve HTTP.sys arasında geçiş yapmak daha kolay olmasıdır.

   HTTP.sys [HTTP Server API UrlPrefix dize biçimlerini](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)kullanır.

   > [!WARNING]
   > Üst düzey joker karakter`http://*:80/` bağlamaları ( ve `http://+:80`) **kullanılmamalıdır.** Üst düzey joker karakter bağlamaları uygulama güvenliği güvenlik açıkları oluşturur. Bu hem güçlü hem de zayıf joker karakterler için geçerlidir. Joker karakterler yerine açık ana bilgisayar adları veya IP adresleri kullanın. Alt etki alanı joker karakter `*.mysub.com`bağlama (örneğin, ) tüm üst etki alanını denetlerseniz (bunun `*.com`aksine, savunmasız dır) bir güvenlik riski değildir. Daha fazla bilgi için [Bkz. RFC 7230: Bölüm 5.4: Ana Bilgisayar.](https://tools.ietf.org/html/rfc7230#section-5.4)

1. Sunucuda ön kayıt URL önekleri.

   HTTP.sys yapılandırmak için yerleşik araç *netsh.exe*olduğunu. *netsh.exe,* URL önekleri ayırmak ve X.509 sertifikalarını atamak için kullanılır. Araç yönetici ayrıcalıkları gerektirir.

   Uygulama için URL'leri kaydetmek için *netsh.exe* aracını kullanın:

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * `<URL>`&ndash; Tam nitelikli Tek düzen kaynak bulucu (URL). Joker karakter bağlama kullanmayın. Geçerli bir ana bilgisayar adı veya yerel IP adresi kullanın. *URL'de sondaki eğik çizgi içermelidir.*
   * `<USER>`&ndash; Kullanıcı veya kullanıcı grubu adını belirtir.

   Aşağıdaki örnekte, sunucunun yerel IP `10.0.0.4`adresi:

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   Bir URL kaydedildiğinde, araç `URL reservation successfully added`.

   Kayıtlı bir URL'yi `delete urlacl` silmek için aşağıdaki komutu kullanın:

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. X.509 sertifikalarını sunucuya kaydedin.

   Uygulama için sertifikaları kaydetmek için *netsh.exe* aracını kullanın:

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * `<IP>`&ndash; Bağlama için yerel IP adresini belirtir. Joker karakter bağlama kullanmayın. Geçerli bir IP adresi kullanın.
   * `<PORT>`&ndash; Bağlama için bağlantı noktasını belirtir.
   * `<THUMBPRINT>`&ndash; X.509 sertifikası parmak izi.
   * `<GUID>`&ndash; Uygulamayı bilgilendirme amacıyla temsil etmek üzere geliştirici tarafından oluşturulan GUID.

   Başvuru amacıyla GUID'i uygulamada paket etiketi olarak saklayın:

   * Görsel Stüdyo'da:
     * **Solution Explorer'daki** uygulamaya sağ tıklayarak ve **Özellikler'i**seçerek uygulamanın proje özelliklerini açın.
     * **Paket** sekmesini seçin.
     * **Etiketler** alanında oluşturduğunuz GUID'i girin.
   * Visual Studio'u kullanmadığı zaman:
     * Uygulamanın proje dosyasını açın.
     * Oluşturduğunuz `<PackageTags>` GUID ile yeni `<PropertyGroup>` veya varolan bir özellik ekleyin:

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   Aşağıdaki örnekte:

   * Sunucunun yerel IP `10.0.0.4`adresi.
   * Bir online rasgele GUID `appid` jeneratör değeri sağlar.

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   Bir sertifika kaydedildiğinde, araç `SSL Certificate successfully added`.

   Sertifika kaydını silmek `delete sslcert` için aşağıdaki komutu kullanın:

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   *netsh.exe*için referans belgeleri :

   * [Hypertext Transfer Protokolü için Netsh Komutları (HTTP)](https://technet.microsoft.com/library/cc725882.aspx)
   * [UrlPrefix Dizeleri](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)

1. Uygulamayı çalıştırın.

   Yönetici ayrıcalıkları, 1024'ten büyük bir bağlantı noktası numarasına sahip HTTP (HTTPS değil) kullanarak yerel barındırmaya bağlanırken uygulamayı çalıştırmak için gerekli değildir. Diğer yapılandırmalar için (örneğin, yerel bir IP adresi kullanarak veya bağlantı noktası 443'e bağlanmak), uygulamayı yönetici ayrıcalıklarıyla çalıştırın.

   Uygulama sunucunun genel IP adresinde yanıt verir. Bu örnekte, sunucuinternetten genel IP adresinden `104.214.79.47`ulaşılır.

   Bu örnekte bir geliştirme sertifikası kullanılır. Sayfa, tarayıcının güvenilmeyen sertifika uyarısını atladıktan sonra güvenli bir şekilde yüklenir.

   ![Uygulamanın Dizin sayfasını gösteren tarayıcı penceresi yüklendi](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a>Proxy sunucusu ve yük dengeleyici senaryoları

Internet'ten veya kurumsal bir ağdan gelen isteklerle etkileşimde bulunan HTTP.sys tarafından barındırılan uygulamalar için proxy sunucularının ve yük dengeleyicilerinin arkasında barındırma yaparken ek yapılandırma gerekebilir. Daha fazla bilgi için proxy [sunucuları ve yük dengeleyicileri ile çalışmak için ASP.NET Core'u yapılandırın'](xref:host-and-deploy/proxy-load-balancer)a bakın.

## <a name="additional-resources"></a>Ek kaynaklar

* [HTTP.sys ile Windows Kimlik Doğrulamasını Etkinleştirme](xref:security/authentication/windowsauth#httpsys)
* [HTTP Sunucu API'si](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx)
* [aspnet/HttpSysServer GitHub deposu (kaynak kodu)](https://github.com/aspnet/HttpSysServer/)
* [Ana bilgisayar](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end
