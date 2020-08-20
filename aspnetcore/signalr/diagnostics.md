---
title: ASP.NET Core 'de günlüğe kaydetme ve tanılama SignalR
author: anurse
description: ASP.NET Core uygulamanızdan tanılamayı nasıl toplayacağınızı öğrenin SignalR .
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: devx-track-csharp, signalr
ms.date: 06/12/2020
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
uid: signalr/diagnostics
ms.openlocfilehash: 649398a3868117b2e7f3358aa25544c99cc625b3
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88631348"
---
# <a name="logging-and-diagnostics-in-aspnet-core-no-locsignalr"></a>ASP.NET Core 'de günlüğe kaydetme ve tanılama SignalR

, [Andrew Stanton-nurte](https://twitter.com/anurse)

Bu makalede, SignalR sorunları gidermeye yardımcı olmak için ASP.NET Core uygulamanızdan tanılama toplamaya yönelik rehberlik sunulmaktadır.

## <a name="server-side-logging"></a>Sunucu tarafında günlüğe kaydetme

> [!WARNING]
> Sunucu tarafı günlükleri, uygulamanızdan önemli bilgiler içerebilir. Ham günlükleri **hiçbir** şekilde üretim uygulamalarından GitHub gibi genel forumlara nakletmeyin.

SignalRASP.NET Core bir parçası olduğundan, ASP.NET Core günlük sistemini kullanır. Varsayılan yapılandırmada SignalR çok az bilgi günlüğe kaydedilir, ancak bu yapılandırılabilir. ASP.NET Core günlüğü yapılandırma hakkında ayrıntılar için [ASP.NET Core günlüğe kaydetme](xref:fundamentals/logging/index#configuration) hakkındaki belgelere bakın.

SignalR İki Günlükçü kategorisi kullanır:

* `Microsoft.AspNetCore.SignalR`: Merkez protokolleriyle ilgili Günlükler için, hub 'Ları etkinleştirme, yöntemleri çağırma ve hub ile ilgili diğer etkinlikler için.
* `Microsoft.AspNetCore.Http.Connections`: WebSockets, uzun yoklama, sunucu tarafından gönderilen olaylar ve alt düzey altyapı gibi aktarımlarıyla ilgili Günlükler için SignalR .

Ayrıntılı günlükleri ' den etkinleştirmek için SignalR , `Debug` aşağıdaki öğeleri içindeki alt bölümüne ekleyerek önceki ön eklerin her ikisini de *appsettings.jsdosya üzerinde* bir düzeye yapılandırın `LogLevel` `Logging` :

[!code-json[](diagnostics/logging-config.json?highlight=7-8)]

Ayrıca, bu kodu yönteminizin içinde de yapılandırabilirsiniz `CreateWebHostBuilder` :

[!code-csharp[](diagnostics/logging-config-code.cs?highlight=5-6)]

JSON tabanlı yapılandırma kullanmıyorsanız, yapılandırma sisteminizde aşağıdaki yapılandırma değerlerini ayarlayın:

* `Logging:LogLevel:Microsoft.AspNetCore.SignalR` = `Debug`
* `Logging:LogLevel:Microsoft.AspNetCore.Http.Connections` = `Debug`

İç içe yapılandırma değerlerinin nasıl belirleneceğini belirlemek için yapılandırma sisteminizin belgelerini denetleyin. Örneğin, ortam değişkenleri kullanılırken, `_` yerine iki karakter kullanılır `:` (örneğin, `Logging__LogLevel__Microsoft.AspNetCore.SignalR` ).

`Debug`Uygulamanız için daha ayrıntılı tanılama toplanırken düzeyin kullanılmasını öneririz. `Trace`Düzey, çok düşük düzey Tanılamalar üretir ve uygulamanızdaki sorunları tanılamak için nadiren gereklidir.

## <a name="access-server-side-logs"></a>Sunucu tarafı günlüklerine erişin

Sunucu tarafı günlüklerine erişme, çalıştırdığınız ortama bağlıdır.

### <a name="as-a-console-app-outside-iis"></a>IIS dışında bir konsol uygulaması olarak

Konsol uygulamasında çalıştırıyorsanız, [konsol günlükçüsü](xref:fundamentals/logging/index#console) varsayılan olarak etkinleştirilmelidir. SignalR Günlükler konsolunda görünür.

### <a name="within-iis-express-from-visual-studio"></a>Visual Studio 'dan IIS Express içinde

Visual Studio **çıktı** penceresinde günlük çıktısını görüntüler. **ASP.NET Core Web sunucusu** açılır seçeneğini belirleyin.

### <a name="azure-app-service"></a>Azure App Service

Azure App Service portalının **tanılama günlükleri** bölümünde **uygulama günlüğü (dosya sistemi)** seçeneğini etkinleştirin ve **düzeyini** olarak yapılandırın `Verbose` . Günlükler **günlük akış** hizmetinden ve App Service dosya sistemindeki günlüklerde kullanılabilir olmalıdır. Daha fazla bilgi için bkz. [Azure günlük akışı](xref:fundamentals/logging/index#azure-log-streaming).

### <a name="other-environments"></a>Diğer ortamlar

Uygulama başka bir ortama (örneğin, Docker, Kubernetes veya Windows hizmeti) dağıtılırsa, <xref:fundamentals/logging/index> ortama uygun günlük sağlayıcılarının nasıl yapılandırılacağı hakkında daha fazla bilgi için bkz..

## <a name="javascript-client-logging"></a>JavaScript istemci günlüğü

> [!WARNING]
> İstemci tarafı günlükleri, uygulamanızdan önemli bilgiler içerebilir. Ham günlükleri **hiçbir** şekilde üretim uygulamalarından GitHub gibi genel forumlara nakletmeyin.

JavaScript istemcisini kullanırken, oturum açma seçeneklerini kullanarak şu `configureLogging` yöntemi kullanabilirsiniz `HubConnectionBuilder` :

[!code-javascript[](diagnostics/logging-config-js.js?highlight=3)]

Günlüğe kaydetmeyi tamamen devre dışı bırakmak için `signalR.LogLevel.None` `configureLogging` yönteminde belirtin.

Aşağıdaki tabloda JavaScript istemcisi için kullanılabilir olan günlük düzeyleri gösterilmektedir. Günlük düzeyinin bu değerlerden birine ayarlanması, bu düzeyde ve tabloda üzerindeki tüm düzeylerde günlüğe kaydetmeyi sağlar.

| Düzey | Açıklama |
| ----- | ----------- |
| `None` | Hiçbir ileti günlüğe kaydedilmez. |
| `Critical` | Uygulamanın tamamında bir hata olduğunu gösteren mesajlar. |
| `Error` | Geçerli işlemdeki bir hatayı gösteren mesajlar. |
| `Warning` | Önemli olmayan bir sorunu belirten mesajlar. |
| `Information` | Bilgi iletileri. |
| `Debug` | Tanılama iletileri hata ayıklama için yararlıdır. |
| `Trace` | Belirli sorunları tanılamak için tasarlanan çok ayrıntılı tanılama iletileri. |

Ayrıntı düzeyini yapılandırdıktan sonra, Günlükler tarayıcı konsoluna yazılır (veya bir NodeJS uygulamasında standart çıkış).

Günlükleri özel bir günlüğe kaydetme sistemine göndermek istiyorsanız, arabirimini uygulayan bir JavaScript nesnesi sağlayabilirsiniz `ILogger` . Uygulanması gereken tek yöntem `log` , olay düzeyini ve olayla ilişkili iletiyi alır. Örnek:

[!code-typescript[](diagnostics/custom-logger.ts?highlight=3-7,13)]

## <a name="net-client-logging"></a>.NET istemci günlüğü

> [!WARNING]
> İstemci tarafı günlükleri, uygulamanızdan önemli bilgiler içerebilir. Ham günlükleri **hiçbir** şekilde üretim uygulamalarından GitHub gibi genel forumlara nakletmeyin.

.NET istemcisinden günlükleri almak için, `ConfigureLogging` üzerinde yöntemini kullanabilirsiniz `HubConnectionBuilder` . Bu, `ConfigureLogging` ve üzerindeki yöntemiyle aynı şekilde çalışmaktadır `WebHostBuilder` `HostBuilder` . ASP.NET Core ' de kullandığınız günlük sağlayıcılarını yapılandırabilirsiniz. Ancak, bireysel günlük sağlayıcıları için NuGet paketlerini el ile yükleyip etkinleştirmeniz gerekir.

Bir uygulamaya .NET istemci günlüğü eklemek için Blazor WebAssembly , bkz <xref:blazor/fundamentals/logging#blazor-webassembly-signalr-net-client-logging> ..

### <a name="console-logging"></a>Konsol günlüğü

Konsol günlüğünü etkinleştirmek için [Microsoft. Extensions. Logging. Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) paketini ekleyin. Ardından, `AddConsole` konsol günlükçüsü 'yi yapılandırmak için yöntemini kullanın:

[!code-csharp[](diagnostics/net-client-console-log.cs?highlight=6)]

### <a name="debug-output-window-logging"></a>Çıkış penceresi günlüğüne hata ayıkla

Günlükleri, Visual Studio 'daki **Çıkış** penceresine gitmek için de yapılandırabilirsiniz. [Microsoft. Extensions. Logging. Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) paketini yükleyip `AddDebug` yöntemi kullanın:

[!code-csharp[](diagnostics/net-client-debug-log.cs?highlight=6)]

### <a name="other-logging-providers"></a>Diğer günlüğe kaydetme sağlayıcıları

SignalR Serilog, seq, NLog gibi diğer günlük sağlayıcılarını veya ile tümleştirilen diğer bir günlük sistemini destekler `Microsoft.Extensions.Logging` . Günlük sisteminiz bir sağlıyorsa `ILoggerProvider` , şunu kullanarak kaydedebilirsiniz `AddProvider` :

[!code-csharp[](diagnostics/net-client-custom-log.cs?highlight=6)]

### <a name="control-verbosity"></a>Denetim ayrıntı düzeyi

Uygulamanızdaki diğer yerlerden oturum açıyorsanız, varsayılan düzeyin olarak değiştirilmesi `Debug` çok ayrıntılı olabilir. Günlükler için günlüğe kaydetme düzeyini yapılandırmak üzere bir filtre kullanabilirsiniz SignalR . Bu, sunucuda olduğu şekilde kodda yapılabilir:

[!code-csharp[Controlling verbosity in .NET client](diagnostics/logging-config-client-code.cs?highlight=9-10)]

## <a name="network-traces"></a>Ağ izlemeleri

> [!WARNING]
> Bir ağ izlemesi, uygulamanız tarafından gönderilen her iletinin tam içeriğini içerir. Ham ağ izlemelerini, üretim uygulamalarından GitHub gibi genel forumlara **hiçbir** şekilde yayımlayamazsınız.

Bir sorunla karşılaşırsanız, ağ izleme bazen yararlı olabilecek çok sayıda bilgi sağlayabilir. Sorun izleyicimizde bir sorun oluşturacaksanız bu özellikle yararlı olur.

## <a name="collect-a-network-trace-with-fiddler-preferred-option"></a>Fiddler ile ağ izleme toplama (tercih edilen seçenek)

Bu yöntem tüm uygulamalar için geçerlidir.

Fiddler, HTTP izlemelerinin toplanması için çok güçlü bir araçtır. [Telerik.com/Fiddler](https://www.telerik.com/fiddler)adresinden yükleyip uygulamayı çalıştırın ve sorunu yeniden oluşturun. Fiddler Windows için kullanılabilir ve macOS ve Linux için beta sürümleri mevcuttur.

HTTPS kullanarak bağlanıyorsanız, Fiddler 'ın HTTPS trafiğinin şifresini çözebilmesini sağlamaya yönelik bazı ek adımlar vardır. Daha ayrıntılı bilgi için bkz. [Fiddler belgeleri](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS).

İzlemeyi topladıktan sonra, **Dosya**  >  **Save**  >  **tüm oturumları** menü çubuğundan Kaydet ' i seçerek izlemeyi dışarı aktarabilirsiniz.

![Fiddler 'tan tüm oturumlar dışarı aktarılıyor](diagnostics/fiddler-export.png)

## <a name="collect-a-network-trace-with-tcpdump-macos-and-linux-only"></a>Tcpdump ile bir ağ izlemesi toplayın (yalnızca macOS ve Linux)

Bu yöntem tüm uygulamalar için geçerlidir.

Komut kabuğundan aşağıdaki komutu çalıştırarak, tcpdump kullanarak ham TCP izlemeleri toplayabilirsiniz. `root`Bir izin hatası alırsanız, komutuna sahip olmanız veya komuta önek uygulamanız gerekebilir `sudo` :

```console
tcpdump -i [interface] -w trace.pcap
```

`[interface]`Yakalamak istediğiniz ağ arabirimiyle değiştirin. Genellikle bu, `/dev/eth0` (Standart Ethernet arabiriminiz için) veya `/dev/lo0` (localhost trafiği için) gibi bir şeydir. Daha fazla bilgi için bkz `tcpdump` . ana bilgisayar sisteminizdeki adam sayfası.

## <a name="collect-a-network-trace-in-the-browser"></a>Tarayıcıda bir ağ izlemesi toplayın

Bu yöntem yalnızca tarayıcı tabanlı uygulamalar için geçerlidir.

Çoğu tarayıcı Geliştirici Araçları, tarayıcı ve sunucu arasında ağ etkinliğini yakalamanızı sağlayan bir "ağ" sekmesi vardır. Ancak, bu izlemeler WebSocket ve sunucu tarafından gönderilen olay iletilerini içermez. Bu taşımaları kullanıyorsanız, Fiddler veya TcpDump (aşağıda açıklanmıştır) gibi bir araç kullanmak daha iyi bir yaklaşımdır.

### <a name="microsoft-edge-and-internet-explorer"></a>Microsoft Edge ve Internet Explorer

(Yönergeler hem Edge hem de Internet Explorer için aynıdır)

1. Geliştirici araçlarını açmak için F12 tuşuna basın
2. Ağ sekmesine tıklayın
3. Sayfayı (gerekirse) yenileyin ve sorunu yeniden oluşturun
4. İzlemeyi "HAR" dosyası olarak dışarı aktarmak için araç çubuğundaki Kaydet simgesine tıklayın:

![Microsoft Edge geliştirme araçları Ağ sekmesinde Kaydet simgesi](diagnostics/ie-edge-har-export.png)

### <a name="google-chrome"></a>Google Chrome

1. Geliştirici araçlarını açmak için F12 tuşuna basın
2. Ağ sekmesine tıklayın
3. Sayfayı (gerekirse) yenileyin ve sorunu yeniden oluşturun
4. İstek listesinde herhangi bir yere sağ tıklayın ve "İçerikle HAR olarak Kaydet" i seçin:

![Google Chrome geliştirme araçları Ağ sekmesinde "Içerik ile HAR olarak Kaydet" seçeneği](diagnostics/chrome-har-export.png)

### <a name="mozilla-firefox"></a>Mozilla Firefox

1. Geliştirici araçlarını açmak için F12 tuşuna basın
2. Ağ sekmesine tıklayın
3. Sayfayı (gerekirse) yenileyin ve sorunu yeniden oluşturun
4. İstek listesinde herhangi bir yere sağ tıklayın ve "tümünü HAR olarak Kaydet" i seçin

![Mozilla Firefox geliştirme araçları Ağ sekmesinde "tümünü HAR olarak Kaydet" seçeneği](diagnostics/firefox-har-export.png)

## <a name="attach-diagnostics-files-to-github-issues"></a>GitHub sorunlarına tanılama dosyaları iliştirme

Tanılama dosyalarını, bir uzantıya sahip olacak şekilde yeniden adlandırarak `.txt` ve sonra sorunu üzerine sürükleyip bırakarak GitHub sorunlarına iliştirebilirsiniz.

> [!NOTE]
> Lütfen günlük dosyalarının veya ağ izlemelerinin içeriğini bir GitHub sorununa yapıştırmayın. Bu Günlükler ve izlemeler oldukça büyük olabilir ve GitHub genellikle bunları keser.

![Günlük dosyalarını bir GitHub sorununa sürükleme](diagnostics/attaching-diagnostics-files.png)

## <a name="metrics"></a>Ölçümler

Ölçümler, zaman aralıklarıyla veri ölçümlerinin bir gösterimidir. Örneğin, saniye başına istek. Ölçüm verileri, yüksek düzeyde bir uygulamanın durumunun gözlemde yapılmasına izin verir. .NET gRPC ölçümleri kullanılarak dağıtılır <xref:System.Diagnostics.Tracing.EventCounter> .

### <a name="no-locsignalr-server-metrics"></a>SignalR Sunucu ölçümleri

SignalR Sunucu ölçümleri <xref:Microsoft.AspNetCore.Http.Connections> olay kaynağında raporlanır.

| Ad                    | Açıklama                 |
|-------------------------|-----------------------------|
| `connections-started`   | Toplam bağlantı sayısı   |
| `connections-stopped`   | Durdurulan toplam bağlantı sayısı   |
| `connections-timed-out` | Zaman aşımına uğrayan toplam bağlantı sayısı |
| `current-connections`   | Geçerli bağlantılar         |
| `connections-duration`  | Ortalama bağlantı süresi |

### <a name="observe-metrics"></a>Ölçümleri gözlemleyin

[DotNet sayaçları](/dotnet/core/diagnostics/dotnet-counters) , geçici sistem durumu izleme ve ilk düzey performans araştırması için bir performans izleme aracıdır. Sağlayıcı adı olarak bir .NET uygulamasını izleyin `Microsoft.AspNetCore.Http.Connections` . Örnek:

```console
> dotnet-counters monitor --process-id 37016 Microsoft.AspNetCore.Http.Connections

Press p to pause, r to resume, q to quit.
    Status: Running
[Microsoft.AspNetCore.Http.Connections]
    Average Connection Duration (ms)       16,040.56
    Current Connections                         1
    Total Connections Started                   8
    Total Connections Stopped                   7
    Total Connections Timed Out                 0
```

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:signalr/configuration>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
