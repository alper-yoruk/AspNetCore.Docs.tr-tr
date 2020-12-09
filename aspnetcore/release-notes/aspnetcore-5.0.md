---
title: ASP.NET Core 5,0 ' deki yenilikler
author: rick-anderson
description: ASP.NET Core 5,0 ' deki yeni özellikler hakkında bilgi edinin.
ms.author: riande
ms.custom: mvc
ms.date: 10/29/2020
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
- Kestrel
uid: aspnetcore-5.0
ms.openlocfilehash: d7ffcb67637593ab2909885a9e1f6de74a78361b
ms.sourcegitcommit: a71bb61f7add06acb949c9258fe506914dfe0c08
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 12/08/2020
ms.locfileid: "96855501"
---
# <a name="whats-new-in-aspnet-core-50"></a>ASP.NET Core 5,0 ' deki yenilikler

Bu makalede, ASP.NET Core 5,0 ' deki en önemli değişiklikler ilgili belgelerin bağlantılarıyla vurgulanır.

## <a name="aspnet-core-mvc-and-no-locrazor-improvements"></a>ASP.NET Core MVC ve Razor iyileştirmeler

### <a name="model-binding-datetime-as-utc"></a>Model bağlama DateTime değeri UTC olarak

Model bağlama artık UTC zaman dizelerinin bağlantısını destekler `DateTime` . İstek bir UTC zaman dizesi içeriyorsa, model bağlama onu bir UTC 'ye bağlar `DateTime` . Örneğin, aşağıdaki zaman dizesi UTC 'ye bağımlıdır `DateTime` : `https://example.com/mycontroller/myaction?time=2019-06-14T02%3A30%3A04.0576719Z`

### <a name="model-binding-and-validation-with-c-9-record-types"></a>C# 9 kayıt türleriyle model bağlama ve doğrulama

[C# 9 kayıt türleri](/dotnet/csharp/whats-new/csharp-9#record-types) , bir MVC denetleyicisi veya sayfasında model bağlama ile birlikte kullanılabilir Razor . Kayıt türleri, ağ üzerinden aktarılan verileri modeletmenin iyi bir yoludur.

Örneğin, aşağıdaki `PersonController` `Person` model bağlama ve form doğrulama ile kayıt türünü kullanır:

```csharp
public record Person([Required] string Name, [Range(0, 150)] int Age);

public class PersonController
{
   public IActionResult Index() => View();

   [HttpPost]
   public IActionResult Index(Person person)
   {
          // ...
   }
}
```

`Person/Index.cshtml`Dosya:

```cshtml
@model Person

Name: <input asp-for="Model.Name" />
<span asp-validation-for="Model.Name" />

Age: <input asp-for="Model.Age" />
<span asp-validation-for="Model.Age" />
```

### <a name="improvements-to-dynamicroutevaluetransformer"></a>Dynamikro Utevaluetranseski geliştirmeleri

ASP.NET Core 3,1, bir <xref:Microsoft.AspNetCore.Mvc.Routing.DynamicRouteValueTransformer> MVC denetleyicisi eylemini veya sayfasını dinamik olarak seçmek için özel uç nokta kullanmanın bir yolu olarak sunulmuştur Razor . ASP.NET Core 5,0 uygulamaları, durum ' a geçirebilir `DynamicRouteValueTransformer` ve seçilen uç nokta kümesini filtreleyebilir.

### <a name="miscellaneous"></a>Çeşitli

* [[Compare]](xref:System.ComponentModel.DataAnnotations.CompareAttribute) özniteliği bir Razor sayfa modelindeki özelliklere uygulanabilir.
* Gövdeden bağlantılı parametreler ve özellikler varsayılan olarak gerekli kabul edilir. <!-- Review: How is this different from 3.1
The validation system in .NET Core 3.0 and later treats non-nullable parameters or bound properties as if they had a [Required] attribute.
see https://docs.microsoft.com/aspnet/core/mvc/models/validation?view=aspnetcore-3.1   
-->

## <a name="web-api"></a>Web API

### <a name="openapi-specification-on-by-default"></a>Varsayılan olarak üzerinde Openapı belirtimi

[Openapı belirtimi](http://spec.openapis.org/oas/v3.0.3) , HTTP API 'lerini açıklamak ve bunları karmaşık iş süreçleriyle veya üçüncü taraflarla tümleştirmeye yönelik bir sektör standardıdır. Openapı, tüm bulut sağlayıcıları ve birçok API kayıt defterleri tarafından yaygın olarak desteklenir. Web API 'Lerinden Openapı belgelerinin yaydıkları uygulamalar, bu API 'Lerin kullanılabileceği çeşitli yeni fırsatlara sahiptir. Açık kaynaklı projenin, [swashbuckle. AspNetCore](https://www.nuget.org/packages/Swashbuckle.AspNetCore/)'un bakım ve ASP.NET Core API şablonu, [swashbuckle](https://github.com/domaindrivendev/Swashbuckle.AspNetCore)üzerinde bir NuGet bağımlılığı içerir. Swashbuckle, Openapı belgelerini dinamik olarak yayar, popüler bir açık kaynaklı NuGet paketidir. Swashbuckle bunu API denetleyicileri üzerinden introspecting ve çalışma zamanında Openapı belgesini veya swashbuckle CLı kullanarak derleme zamanında oluşturur.

ASP.NET Core 5,0 ' de Web API 'SI şablonları, Openapı desteğini varsayılan olarak etkinleştirir. Openapı 'yi devre dışı bırakmak için:

* Komut satırından:

    ```dotnetcli
    dotnet new webapi --no-openapi true
    ```
* Visual Studio 'dan: **Openapı desteğini etkinleştir** seçeneğinin işaretini kaldırın.

Web API projeleri için oluşturulan tüm *. csproj* dosyaları [swashbuckle. aspnetcore](https://www.nuget.org/packages/Swashbuckle.AspNetCore/) NuGet paketi başvurusunu içerir.

```xml
<ItemGroup>
    <PackageReference Include="Swashbuckle.AspNetCore" Version="5.5.1" />
</ItemGroup>
```

Şablon tarafından oluşturulan kod, `Startup.ConfigureServices` Openapı belge oluşturmayı etkinleştiren ' de kod içerir:

[!code-csharp[](~/release-notes/sample/StartupSwagger.cs?name=snippet)]

`Startup.Configure`Yöntemi, şunu sağlayan swashbuckle ara yazılımını ekler:

* Belge oluşturma işlemi.
* Varsayılan olarak, geliştirme modunda Swagger Kullanıcı arabirimi sayfası.

Şablon tarafından oluşturulan kod yanlışlıkla üretime yayımlarken API 'nin açıklamasını sunmaz.

[!code-csharp[](~/release-notes/sample/StartupSwagger.cs?name=snippet2)]

#### <a name="azure-api-management-import"></a>Azure API Management Içeri aktarma

ASP.NET Core API projeleri Openapı 'yi etkinleştirdeğiştiğinde, Visual Studio 2019 sürüm 16,8 ve sonraki sürümlerde yayımlama akışında ek bir adım otomatik olarak sunulur. [Azure API Management](xref:tutorials/publish-to-azure-api-management-using-vs) kullanan geliştiriciler, yayımlama akışı sırasında API 'leri otomatik olarak Azure API Management 'ye içeri aktarmaya yönelik bir fırsata sahiptir:

![Azure API Management Içeri aktarma VS yayımlama](~/release-notes/static/publish-to-apim.png)

### <a name="better-launch-experience-for-web-api-projects"></a>Web API projeleri için daha iyi başlatma deneyimi

Openapı varsayılan olarak etkinken, Web API geliştiricileri için uygulama başlatma deneyimi (F5) önemli ölçüde gelişir. ASP.NET Core 5,0 ile Web API şablonu, Swagger Kullanıcı Arabirimi sayfasını yüklemek için önceden yapılandırılmış olarak gelir. Swagger Kullanıcı arabirimi sayfası, hem yayımlanan API için eklenen belgeleri hem de API 'Lerin tek bir tıklama ile test edilmesini sağlar.

![Swagger/index.html görünümü](~/release-notes/static/swagger-ui-page-rc1.png)

## Blazor

### <a name="performance-improvements"></a>Performans geliştirmeleri

.NET 5 için, Blazor WebAssembly karmaşık UI işleme ve JSON serileştirmesi üzerinde belirli bir odak ile çalışma zamanı performansı için önemli geliştirmeler yaptık. Performans testlerimizde, Blazor WebAssembly .NET 5 ' te çoğu senaryo için iki-üç kat daha hızlıdır. Daha fazla bilgi için bkz. [ASP.net Blog: .NET 5 Release Candidate 1 ' de ASP.NET Core Updates](https://devblogs.microsoft.com/aspnet/asp-net-core-updates-in-net-5-release-candidate-1/#blazor-webassembly-performance-improvements).

### <a name="css-isolation"></a>CSS yalıtımı

Blazor artık belirli bir bileşen kapsamındaki CSS stillerinin tanımlanmasını destekler. Bileşene özgü CSS stilleri, bir uygulamadaki stiller hakkında sebebini kolaylaştırır ve genel stillerin yanlışlıkla yan etkileriyle kaçınılmasını önler. Daha fazla bilgi için bkz. <xref:blazor/components/css-isolation>.

### <a name="new-inputfile-component"></a>Yeni `InputFile` bileşen

`InputFile`Bileşen, bir kullanıcı tarafından karşıya yüklenmek üzere seçilen bir veya daha fazla dosyanın okunmasına izin verir. Daha fazla bilgi için bkz. <xref:blazor/file-uploads>.

### <a name="new-inputradio-and-inputradiogroup-components"></a>Yeni `InputRadio` ve `InputRadioGroup` Bileşenler

Blazor`InputRadio` `InputRadioGroup` Tümleşik doğrulama ile radyo düğmesi gruplarına veri bağlamayı kolaylaştıran yerleşik ve bileşenlere sahiptir. Daha fazla bilgi için bkz. <xref:blazor/forms-validation>.

### <a name="component-virtualization"></a>Bileşen sanallaştırma

Framework 'ün yerleşik sanallaştırma desteğini kullanarak bileşen işlemenin algılanan performansını geliştirir Blazor . Daha fazla bilgi için bkz. <xref:blazor/components/virtualization>.

### <a name="ontoggle-event-support"></a>`ontoggle` olay desteği

Blazor Olaylar artık `ontoggle` Dom olayını destekliyor. Daha fazla bilgi için bkz. <xref:blazor/components/event-handling#event-argument-types>.

### <a name="set-ui-focus-in-no-locblazor-apps"></a>Uygulamalarda UI odağı ayarlama Blazor

`FocusAsync`UI odağını bu öğeye ayarlamak için öğe başvurularında kolaylık olan yöntemi kullanın. Daha fazla bilgi için bkz. <xref:blazor/components/event-handling#focus-an-element>.

### <a name="custom-validation-class-attributes"></a>Özel doğrulama sınıfı öznitelikleri

Özel doğrulama sınıfı adları, önyükleme gibi CSS çerçeveleri ile tümleştirilirken faydalıdır. Daha fazla bilgi için bkz. <xref:blazor/forms-validation#custom-validation-class-attributes>.

### <a name="iasyncdisposable-support"></a>IAsyncDisposable desteği

Blazor bileşenler artık <xref:System.IAsyncDisposable> ayrılmış kaynakların zaman uyumsuz sürümünün arabirimini destekler.

### <a name="javascript-isolation-and-object-references"></a>JavaScript yalıtımı ve nesne başvuruları

Blazor Standart [JavaScript modüllerinde](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules)JavaScript yalıtımına izin vermez. Daha fazla bilgi için bkz. <xref:blazor/call-javascript-from-dotnet#blazor-javascript-isolation-and-object-references>.

### <a name="form-components-support-display-name"></a>Form bileşenleri destek görünen adı

Aşağıdaki yerleşik bileşenler parametresiyle görünen adları destekler `DisplayName` :

* `InputDate`
* `InputNumber`
* `InputSelect`

Daha fazla bilgi için bkz. <xref:blazor/forms-validation#display-name-support>.

### <a name="catch-all-route-parameters"></a>Catch-all Route parametreleri

Birden çok klasör sınırlarındaki yolları yakalayan catch-all yol parametreleri, bileşenlerinde desteklenir. Daha fazla bilgi için bkz. <xref:blazor/fundamentals/routing#catch-all-route-parameters>.

### <a name="debugging-improvements"></a>Hata ayıklama geliştirmeleri

Blazor WebAssemblyASP.NET Core 5,0 ' de uygulamalarda hata ayıklama geliştirildi. Ayrıca, hata ayıklama artık Mac için Visual Studio desteklenir. Daha fazla bilgi için bkz. <xref:blazor/debug>.

### <a name="microsoft-no-locidentity-v20-and-msal-v20"></a>Microsoft Identity v 2.0 ve msal v 2.0

Blazor Güvenlik artık Microsoft Identity v 2.0 ( [`Microsoft.Identity.Web`](https://www.nuget.org/packages/Microsoft.Identity.Web) ve [`Microsoft.Identity.Web.UI`](https://www.nuget.org/packages/Microsoft.Identity.Web.UI) ) ve msal v 2.0 kullanır. Daha fazla bilgi için [ Blazor güvenlik ve Identity düğümdeki](xref:blazor/security/index)konulara bakın.

### <a name="protected-browser-storage-for-no-locblazor-server"></a>İçin korumalı tarayıcı depolaması Blazor Server

Blazor Server uygulamalar artık uygulama durumunun ASP.NET Core veri koruma kullanılarak yapılan değişikliklere karşı korunması için yerleşik desteğini kullanabilir. Veriler, yerel tarayıcı depolama veya oturum depolama alanında depolanabilir. Daha fazla bilgi için bkz. <xref:blazor/state-management>.

### <a name="no-locblazor-webassembly-prerendering"></a>Blazor WebAssembly prerendering

Bileşen tümleştirme, barındırma modelleri arasında geliştirilmiştir ve Blazor WebAssembly uygulamalar artık sunucuda bir çıktıyı açabilir. <!-- UNCOMMENT AFTER https://github.com/dotnet/AspNetCore.Docs/pull/19887 MERGES: For more information, see <xref:blazor/components/integrate-components-into-razor-pages-and-mvc-apps> and <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>. -->

### <a name="trimminglinking-improvements"></a>Kırpma/bağlama iyileştirmeleri

Blazor WebAssembly uygulamanın çıkış derlemelerinden gereksiz Il 'yi kırpmak için bir derleme sırasında ara dil (IL) kırpması/bağlamayı gerçekleştirir. ASP.NET Core 5,0 sürümü ile Blazor WebAssembly ek yapılandırma seçenekleriyle Gelişmiş kırpma gerçekleştirir. Daha fazla bilgi için bkz <xref:blazor/host-and-deploy/configure-trimmer> . ve [kırpma seçenekleri](/dotnet/core/deploying/trimming-options).

### <a name="browser-compatibility-analyzer"></a>Tarayıcı uyumluluğu Çözümleyicisi

Blazor WebAssembly uygulamalar tam .NET API yüzey alanını hedefletir, ancak tüm .NET API 'Leri, tarayıcı korumalı alan kısıtlamaları nedeniyle WebAssembly üzerinde desteklenmez. <xref:System.PlatformNotSupportedException>WebAssembly üzerinde çalışırken desteklenmeyen API 'ler oluşturur. Uygulama, uygulamanın hedef platformları tarafından desteklenmeyen API 'Ler kullandığında, bir platform uyumluluğu Çözümleyicisi geliştiriciyi uyarır. Daha fazla bilgi için bkz. <xref:blazor/components/class-libraries#browser-compatibility-analyzer-for-blazor-webassembly>.

### <a name="lazy-load-assemblies"></a>Yavaş yük derlemeleri

Blazor WebAssembly Uygulama başlatma performansı, bazı uygulama derlemelerinin gerekli olana kadar yüklenmesi erteleyerek artırılabilir. Daha fazla bilgi için bkz. <xref:blazor/webassembly-lazy-load-assemblies>.

### <a name="updated-globalization-support"></a>Genelleştirme desteği güncelleştirildi

Genelleştirme desteği, Blazor WebAssembly Unicode (ıCU) Için Uluslararası bileşenlere bağlı olarak kullanılabilir. Daha fazla bilgi için bkz. <xref:blazor/globalization-localization>.

## <a name="grpc"></a>gRPC

[GRPC](https://grpc.io/)'de çok preformance geliştirmeleri yapılmıştır. Daha fazla bilgi için bkz. [.NET 5 ' teki GRPC performansı geliştirmeleri](https://devblogs.microsoft.com/aspnet/grpc-performance-improvements-in-net-5/).

Daha fazla gRPC bilgisi için bkz <xref:grpc/index> ..

## SignalR

### <a name="no-locsignalr-hub-filters"></a>SignalR Hub filtreleri

SignalR ASP.NET ' de hub işlem hatları adı verilen Merkez filtreleri, SignalR kodun hub yöntemleri çağrılmadan önce ve sonra çalışmasına izin veren bir özelliktir. Hub yöntemleri çağrılmadan önce ve sonra kodu çalıştırmak, ara yazılımın bir HTTP isteğinden önce ve sonra kodu çalıştırma yeteneğine benzer. Ortak kullanımlar arasında günlük kaydı, hata işleme ve bağımsız değişken doğrulama.

Daha fazla bilgi için bkz. [ASP.NET Core SignalR hub filtrelerini kullanma ](xref:signalr/hub-filters).

### <a name="no-locsignalr-parallel-hub-invocations"></a>SignalR paralel hub etkinleştirmeleri

ASP.NET Core SignalR artık paralel hub çağırmaları işleyebilir. Varsayılan davranış, istemcilerin tek seferde birden fazla hub yöntemi çağırmasına izin verecek şekilde değiştirilebilir:

[!code-csharp[](~/release-notes/sample/StartupSignalRhubs.cs?name=snippet)]

### <a name="added-messagepack-support-in-no-locsignalr-java-client"></a>Java istemcisinde MessagePack desteği eklendi SignalR

Yeni bir [com. Microsoft. SignalR. MessagePack](https://mvnrepository.com/artifact/com.microsoft.signalr.messagepack)paketi, Java Istemcisine MessagePack desteği ekler SignalR . MessagePack hub protokolünü kullanmak için `.withHubProtocol(new MessagePackHubProtocol())` bağlantı oluşturucusuna ekleyin:

```java
HubConnection hubConnection = HubConnectionBuilder.create(
                           "http://localhost:53353/MyHub")
               .withHubProtocol(new MessagePackHubProtocol())
               .build();
```

<!--
See [Update SignalR code](xref:migration/31-to-50#signalr) for migration instructions.
-->

## Kestrel

* Yapılandırma aracılığıyla yeniden yüklenebilir uç noktalar: Kestrel var olan uç noktaların [ KestrelServerOptions.Config](xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Configure%2A) Me ve bağını çözme ve parametre olduğunda bir uygulamanın yeniden başlatılmasına gerek kalmadan yeni uç noktalara bağlama gibi yapılandırma değişikliklerini algılayabilir `reloadOnChange` `true` . Veya kullanırken varsayılan olarak <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults%2A> <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> , Kestrel etkin olan [" Kestrel "](https://github.com/dotnet/aspnetcore/blob/7e9e03b70124784b1de5564c573bd65cdaccbfcc/src/DefaultBuilder/src/WebHost.cs#L226) yapılandırma alt bölümüne bağlanır `reloadOnChange` . `reloadOnChange: true` `KestrelServerOptions.Configure` Yeniden yüklenebilir uç noktaları almak için uygulamalar el ile çağrılırken geçmesi gerekir.
* HTTP/2 yanıt üst bilgileri geliştirmeleri. Daha fazla bilgi için, sonraki bölümde [performans iyileştirmeleri](#performance-improvements) bölümüne bakın.
* Yuva aktarımında ek uç nokta türleri için destek: ' de tanıtılan yeni API 'ye ekleme <xref:System.Net.Sockets?displayProperty=nameWithType> , içindeki yuva varsayılan taşıması, [Kestrel](xref:fundamentals/servers/kestrel) var olan dosya tanıtıcıları ve UNIX etki alanı yuvaları bağlamaya olanak sağlar. Mevcut dosya işleyicilerine bağlama desteği `Systemd` , taşıma gerekmeden mevcut tümleştirmeyi kullanmayı sağlar `libuv` .
* İçinde özel üst bilgi kodu çözme Kestrel : uygulamalar, <xref:System.Text.Encoding> UTF-8 için varsayılan olarak, üst bilgi adına göre gelen üstbilgileri yorumlamak için hangisinin kullanılacağını belirtebilir. Şunu ayarlayın <!--<xref:Microsoft.AspNetCore.Server.Kestrel.KestrelServerOptions.RequestHeaderEncodingSelector> --> `Microsoft.AspNetCore.Server.Kestrel.KestrelServerOptions.RequestHeaderEncodingSelector` kullanılacak kodlamayı belirtmek için özelliği:
 
  ```csharp
  public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(options =>
            {
                options.RequestHeaderEncodingSelector = encoding =>
                {
                      return encoding switch
                      {
                          "Host" => System.Text.Encoding.Latin1,
                          _ => System.Text.Encoding.UTF8,
                      };
                };
            });
            webBuilder.UseStartup<Startup>();
        });
  ```

### <a name="no-lockestrel-endpoint-specific-options-via-configuration"></a>Kestrel yapılandırma aracılığıyla uç noktaya özel seçenekler

Kestrel [Yapılandırma](xref:fundamentals/configuration/index)yoluyla uç noktaya özel seçenekleri yapılandırmak için destek eklenmiştir. Uç noktaya özgü yapılandırmalarda şunları içerir:

* Kullanılan HTTP protokolleri
* Kullanılan TLS protokolleri
* Sertifika seçildi
* İstemci sertifikası modu

Yapılandırma, belirtilen sunucu adına göre hangi sertifikanın seçili olduğunu belirtmenizi sağlar. Sunucu adı, istemci tarafından gösterilen TLS protokolüne Sunucu Adı Belirtme (SNı) uzantısının bir parçasıdır. Kestrelyapılandırması aynı zamanda ana bilgisayar adında bir joker öneki de destekler.

Aşağıdaki örnek, bir yapılandırma dosyası kullanarak uç noktaya özel belirtme gösterir:

```json
{
  "Kestrel": {
    "Endpoints": {
      "EndpointName": {
        "Url": "https://*",
        "Sni": {
          "a.example.org": {
            "Protocols": "Http1AndHttp2",
            "SslProtocols": [ "Tls11", "Tls12"],
            "Certificate": {
              "Path": "testCert.pfx",
              "Password": "testPassword"
            },
            "ClientCertificateMode" : "NoCertificate"
          },
          "*.example.org": {
            "Certificate": {
              "Path": "testCert2.pfx",
              "Password": "testPassword"
            }
          },
          "*": {
            // At least one sub-property needs to exist per
            // SNI section or it cannot be discovered via
            // IConfiguration
            "Protocols": "Http1",
          }
        }
      }
    }
  }
}
```

Sunucu Adı Belirtme (SNı), SSL anlaşmasının bir parçası olarak sanal etki alanı dahil etmek için bir TLS uzantısıdır. Bunun ne kadar etkili olduğu, sanal etki alanı adının veya ana bilgisayar adının ağ uç noktasını tanımlamak için kullanılabileceği anlamına gelir.

## <a name="performance-improvements"></a>Performans geliştirmeleri

### <a name="http2"></a>HTTP/2

* HTTP/2 kod yolundaki ayırmalarda önemli indirimler.
* İçindeki HTTP/2 yanıt üst bilgilerinin [HPack dinamik sıkıştırması](https://tools.ietf.org/html/rfc7541) desteği [Kestrel](xref:fundamentals/servers/kestrel) . Daha fazla bilgi için bkz. [üst bilgi tablosu boyutu](xref:fundamentals/servers/kestrel#header-table-size) ve [HPack: http/2 sessiz Killer (özellik)](https://blog.cloudflare.com/hpack-the-silent-killer-feature-of-http-2/).
* HTTP/2 PING çerçeveleri gönderme: HTTP/2, boşta bağlantının hala çalışır durumda olduğundan emin olmak için PING çerçeveleri göndermeye yönelik bir mekanizmaya sahiptir. Uygun bir bağlantının, genellikle boşta olan uzun süreli akışlar ile çalışırken yararlı olması, ancak örneğin gRPC akışları gibi yalnızca zaman aralıklı etkinlik olması durumunda faydalıdır. Uygulamalar, ' de sınırları ayarlayarak düzenli olarak PING çerçeveleri gönderebilir [Kestrel](xref:fundamentals/servers/kestrel) <xref:Microsoft.AspNetCore.Server.Kestrel.KestrelServerOptions> :

   ```csharp
   public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(options =>
            {
                options.Limits.Http2.KeepAlivePingInterval =
                                               TimeSpan.FromSeconds(10);
                options.Limits.Http2.KeepAlivePingTimeout =
                                               TimeSpan.FromSeconds(1);
            });
            webBuilder.UseStartup<Startup>();
        });
   ```
   <!-- review: KeepAlivePingInterval not found in RC1. Try testing with RC1. See https://github.com/dotnet/aspnetcore/pull/22565/files see C:/Users/riande/source/repos/WebApplication128/WebApplication128 -->

### <a name="containers"></a>Kapsayıcılar

.NET 5,0 ' den önce, bir ASP.NET Core uygulaması için bir *Dockerfile* oluşturup yayımlarken, tüm .NET Core SDK ve ASP.NET Core görüntüsünün çekmesini sağlar. Bu sürümle birlikte, SDK görüntüleri baytları çekililerek ASP.NET Core görüntü için çekilen baytlar büyük ölçüde ortadan kalkar. Daha fazla bilgi için bkz. [Bu GitHub sorun açıklaması](https://github.com/dotnet/dotnet-docker/issues/1814#issuecomment-625294750).

## <a name="authentication-and-authorization"></a>Kimlik doğrulama ve yetkilendirme

### <a name="azure-active-directory-authentication-with-microsoftno-locidentityweb"></a>Microsoft ile kimlik doğrulaması Azure Active Directory. Identity . Web

ASP.NET Core proje şablonları, <xref:Microsoft.Identity.Web?displayProperty=fullName> [Azure Active Directory](/azure/active-directory/fundamentals/active-directory-whatis) (Azure AD) ile kimlik doğrulamayı işlemek için ile tümleşir. [Microsoft. Identity . Web paketi](https://www.nuget.org/packages/Microsoft.Identity.Web/) şunları sağlar:

* Azure AD ile kimlik doğrulaması için daha iyi bir deneyim.
* [Microsoft Graph](/graph/overview)dahil olmak üzere, kullanıcılarınız adına Azure kaynaklarına erişmenin daha kolay bir yolu. Bkz [. Microsoft. Identity . ](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)Temel bir oturum açma ile başlayan ve Azure API 'leri kullanarak, Microsoft Graph kullanarak ve kendi API 'lerinizi koruyan Web örneği. `Microsoft.Identity.Web` , .NET 5 ile birlikte kullanılabilir.

### <a name="allow-anonymous-access-to-an-endpoint"></a>Bir uç noktaya anonim erişime izin ver

`AllowAnonymous`Uzantı yöntemi bir uç noktaya anonim erişime izin verir:

[!code-csharp[](~/release-notes/sample/StartupAnonEndpoint.cs?name=snippet)]

### <a name="custom-handling-of-authorization-failures"></a>Özel yetkilendirme hatalarının işlenmesi

Yetkilendirme hatalarının özel işlenmesi artık, [Yetkilendirme](xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A) [ara yazılımı](xref:fundamentals/middleware/index)tarafından çağrılan yeni [IAuthorizationMiddlewareResultHandler](https://github.com/dotnet/aspnetcore/blob/v5.0.0-rc.1.20451.17/src/Security/Authorization/Policy/src/IAuthorizationMiddlewareResultHandler.cs) arabirimi ile daha kolay olur. Varsayılan uygulama aynı kalır, ancak özel bir işleyici, kimlik doğrulaması neden başarısız olduğuna göre özel HTTP yanıtlarına izin veren bağımlılık ekleme ' ye kaydedilebilir. Öğesinin kullanımını gösteren [Bu örneğe](https://github.com/dotnet/aspnetcore/blob/master/src/Security/samples/CustomAuthorizationFailureResponse/Authorization/SampleAuthorizationMiddlewareResultHandler.cs) bakın `IAuthorizationMiddlewareResultHandler` .

### <a name="authorization-when-using-endpoint-routing"></a>Endpoint Routing kullanılırken yetkilendirme

Endpoint Routing kullanılırken yetkilendirme, artık `HttpContext` uç nokta örneği yerine alır. Bu, yetkilendirme ara yazılımı tarafından `RouteData` erişilemeyen öğesinin ve diğer özelliklerine erişmesine olanak tanır `HttpContext` <xref:Microsoft.AspNetCore.Http.Endpoint> . Uç nokta bağlam kullanılarak içerikten getirilebilir [. GetEndpoint](xref:Microsoft.AspNetCore.Http.EndpointHttpContextExtensions.GetEndpoint%2A).

### <a name="role-based-access-control-with-kerberos-authentication-and-ldap-on-linux"></a>Linux üzerinde Kerberos kimlik doğrulaması ve LDAP ile rol tabanlı erişim denetimi

Bkz. [Kerberos kimlik doğrulaması ve rol tabanlı erişim denetimi (RBAC)](xref:security/authentication/windowsauth#rbac)

## <a name="api-improvements"></a>API geliştirmeleri

### <a name="json-extension-methods-for-httprequest-and-httpresponse"></a>HttpRequest ve HttpResponse için JSON uzantısı yöntemleri

JSON verileri, ve ' dan ' a okunabilir ve yazılabilir ve `HttpRequest` `HttpResponse` Yeni <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> ve `WriteAsJsonAsync` genişletme yöntemleri kullanılarak yapılabilir. Bu uzantı yöntemleri JSON verilerini işlemek için serileştiricide [System.Text.Js](xref:System.Text.Json) kullanır. Yeni `HasJsonContentType` genişletme yöntemi bir ISTEĞIN JSON içerik türüne sahip olup olmadığını da denetleyebilir.

JSON uzantısı yöntemleri, JSON API 'Leri bir [endpoint routing](xref:fundamentals/routing) programlama stilinde, * *.**koda yol*** çağırıyoruz. Basit bir şekilde temel JSON API 'Leri oluşturmak isteyen geliştiriciler için yeni bir seçenektir. Örneğin, yalnızca birkaç uç nokta içeren bir Web uygulaması, ASP.NET Core MVC 'nin tüm işlevleri yerine kod yolunu kullanmayı seçebilir:

```csharp
endpoints.MapGet("/weather/{city:alpha}", async context =>
{
    var city = (string)context.Request.RouteValues["city"];
    var weather = GetFromDatabase(city);

    await context.Response.WriteAsJsonAsync(weather);
});
```

Yeni JSON uzantısı yöntemleri ve koda yönlendirme hakkında daha fazla bilgi için bkz. [bu .net Show](https://channel9.msdn.com/Shows/On-NET/ASPNET-Core-Series-Route-to-Code).

### <a name="systemdiagnosticsactivity"></a>System. Diagnostics. Activity

Varsayılan biçimi varsayılan <xref:System.Diagnostics.Activity?displayProperty=fullName> olarak W3C biçimindedir. Bu, dağıtılmış izleme desteğini varsayılan olarak daha fazla çerçeve ile ASP.NET Core birlikte çalışabilir hale getirir.

### <a name="frombodyattribute"></a>FromBodyAttribute

<xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute> Artık bu parametrelerin veya özelliklerin isteğe bağlı olarak kabul edilmesine izin veren bir seçeneğin yapılandırılmasını destekler:

```csharp
public IActionResult Post([FromBody(EmptyBodyBehavior = EmptyBodyBehavior.Allow)]
                          MyModel model)
{
    ...
}
```

## <a name="miscellaneous-improvements"></a>Çeşitli geliştirmeler

ASP.NET Core derlemelerine [null yapılabilir ek açıklamalar](/dotnet/csharp/nullable-references#attributes-describe-apis) uygulamaya başladık. .NET 5 çerçevesinin ortak genel API yüzeyinin çoğuna açıklama eklemek için planlıyoruz. <!-- Review: what's the impact of this? How does it work? Need more info.  Check the link I added -->

### <a name="control-startup-class-activation"></a>Başlangıç sınıfı etkinleştirmeyi denetleme

<xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseStartup%2A>Uygulamanın sınıf etkinleştirmesini denetlemek için bir fabrika yöntemi sağlamasına imkan tanıyan ek bir aşırı yükleme eklenmiştir `Startup` . `Startup`Sınıf etkinleştirmeyi denetlemek, `Startup` ana bilgisayarla birlikte başlatılan ek parametreleri geçirmek için yararlıdır:

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var logger = CreateLogger();
        var host = Host.CreateDefaultBuilder()
            .ConfigureWebHost(builder =>
            {
                builder.UseStartup(context => new Startup(logger));
            })
            .Build();

        await host.RunAsync();
    }
}
```

### <a name="auto-refresh-with-dotnet-watch"></a>DotNet Watch ile otomatik yenileme

.NET 5 ' te, bir ASP.NET Core projesi üzerinde [DotNet izleme](xref:tutorials/dotnet-watch) çalıştırmak, her ikisi de varsayılan tarayıcıyı başlatır ve kodda değişiklik yapıldığında tarayıcıyı otomatik olarak yeniler. Yani şunları yapabilirsiniz:

_ Metin düzenleyicisinde bir ASP.NET Core projesi açın.
* Şu komutu çalıştırın: `dotnet watch`.
* Araç, uygulamayı yeniden oluşturma, yeniden başlatma ve yeniden yükleme işlemini gerçekleştirirken kod değişikliğine odaklanırsınız.

### <a name="console-logger-formatter"></a>Konsol günlükçü biçimlendiricisi

Kitaplığındaki konsol günlüğü sağlayıcısında geliştirmeler yapılmıştır `Microsoft.Extensions.Logging` . Geliştiriciler artık `ConsoleFormatter` konsol çıktısının biçimlendirilmesi ve renklendirme üzerinde denetimi tamamen denetleyebilmesi için özel bir uygulama uygulayabilir. Biçimlendirici API 'Leri, VT-100 kaçış sıralarının bir alt kümesini uygulayarak zengin biçimlendirme sağlar. VT-100, çoğu modern terminalde desteklenir. Konsol günlükçüsü, geliştiricilerin tüm terminallerde tek bir biçimlendirici yazarmasına izin veren desteklenmeyen terminallerde kaçış dizilerini ayrıştırabilirler.

### <a name="json-console-logger"></a>JSON konsol günlükçüsü

Özel formatlayıcılar için desteğe ek olarak, yapılandırılmış JSON günlüklerini konsola yayan bir yerleşik JSON biçimlendirici de ekledik. Aşağıdaki kod, varsayılan günlükçü 'dan JSON 'a nasıl geçiş yapılacağını gösterir:

[!code-csharp[](~/release-notes/sample/ProgramJsonLog.cs?name=snippet)]

Konsola yayılan günlük iletileri JSON olarak biçimlendirilir:

```json
{
  "EventId": 0,
  "LogLevel": "Information",
  "Category": "Microsoft.Hosting.Lifetime",
  "Message": "Now listening on: https://localhost:5001",
  "State": {
    "Message": "Now listening on: https://localhost:5001",
    "address": "https://localhost:5001",
    "{OriginalFormat}": "Now listening on: {address}"
  }
}
```
