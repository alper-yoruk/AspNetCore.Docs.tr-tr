---
title: ASP.NET Core 3.0'daki yenilikler
author: rick-anderson
description: ASP.NET Core 3.0'daki yeni özellikler hakkında bilgi edinin.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
- SignalR
uid: aspnetcore-3.0
ms.openlocfilehash: 4886673a9b16b8be8d9a0b0d5c7002a91760544e
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80976982"
---
# <a name="whats-new-in-aspnet-core-30"></a>ASP.NET Core 3.0'daki yenilikler

Bu makalede, ilgili belgelere bağlantılar ile ASP.NET Core 3.0 en önemli değişiklikleri vurgulamaktadır.

## Blazor

Blazor.NET ile etkileşimli istemci tarafı web Web Web Web Web'i oluşturmak için ASP.NET Core'da yeni bir çerçevedir:

* JavaScript yerine C# kullanarak zengin etkileşimli web'ler oluşturun.
* .NET'te yazılı sunucu ve istemci tarafı uygulama mantığını paylaşın.
* Mobil tarayıcılar da dahil olmak üzere geniş tarayıcı desteği için UI'yi HTML ve CSS olarak işleyin.

Blazorçerçeve desteklenen senaryolar:

* Yeniden kullanılabilir UI bileşenleri (Jilet bileşenleri)
* İstemci tarafı yönlendirme
* Bileşen düzenleri
* Bağımlılık enjeksiyonu desteği
* Formlar ve doğrulama
* Razor sınıf kitaplıkları içeren bileşen kitaplıkları oluşturma
* JavaScript ile birlikte çalışma

Daha fazla bilgi için bkz. <xref:blazor/index>.

### <a name="opno-locblazor-server"></a>BlazorSunucu

Blazorui güncelleştirmelerinin nasıl uygulandığından bileşen oluşturma mantığını ayırır. BlazorSunucu, ASP.NET Core uygulamasında Razor bileşenlerini sunucuda barındırma desteği sağlar. UI güncelleştirmeleri bir SignalR bağlantı üzerinden işlenir. BlazorSunucu ASP.NET Core 3.0 desteklenir.

### <a name="opno-locblazor-webassembly-preview"></a>BlazorWebAssembly (Önizleme)

Blazoruygulamalar, WebAssembly tabanlı .NET çalışma zamanı kullanılarak doğrudan tarayıcıda çalıştırılabilir. BlazorWebAssembly önizlemededir ve Core 3.0ASP.NET de *desteklenmez.* BlazorWebAssembly ASP.NET Core gelecekteki sürümünde desteklenecektir.

### <a name="razor-components"></a>Jilet bileşenleri

Blazoruygulamalar bileşenlerden oluşturulur. Bileşenler, sayfa, iletişim kutusu veya form gibi kullanıcı arabiriminin (UI) kendi kendine yeten parçalarıdır. Bileşenler, UI oluşturma mantığını ve istemci tarafı olay işleyicilerini tanımlayan normal .NET sınıflarıdır. JavaScript olmadan zengin etkileşimli web uygulamaları oluşturabilirsiniz.

Bileşenler Blazor genellikle HTML ve C#'ın doğal bir karışımı olan Razor sözdizimi kullanılarak yazılır. Jilet bileşenleri, her ikisinin de Razor kullandığı için Razor Pages ve MVC görünümlerine benzer. İstek yanıt modeline dayanan sayfa ve görünümlerin aksine, bileşenler özellikle Web-yüksek ödenşeyini işlemek için kullanılır.

## <a name="grpc"></a>gRPC

[gRPC](https://grpc.io/):

* Popüler, yüksek performanslı bir RPC (uzaktan yordam çağrısı) çerçevesidir.
* API geliştirme için dik kafalı bir sözleşme ilk yaklaşım sunar.
* Şu lar gibi modern teknolojileri kullanır:

  * Ulaşım için HTTP/2.
  * Arabirim açıklama dili olarak protokol arabellekleri.
  * İkili serileştirme biçimi.
* Şu gibi özellikler sağlar:

  * Kimlik Doğrulaması
  * Çift yönlü akış ve akış kontrolü.
  * İptal ve zaman zaman ları.

core 3.0 ASP.NET gRPC işlevselliği içerir:

* [Grpc.AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) &ndash; gRPC hizmetleri barındırma için ASP.NET Core çerçeve. ASP.NET Core'daki gRPC, günlük ASP.NET, bağımlılık enjeksiyonu (DI), kimlik doğrulama ve yetkilendirme gibi standart ASP.NET Core özellikleriyle bütünleşir.
* [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) &ndash; tanıdık `HttpClient`üzerine inşa .NET Core için bir gRPC istemcisi .
* [Grpc.Net.ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) &ndash; gRPC istemci `HttpClientFactory`entegrasyonu ile .

Daha fazla bilgi için bkz. <xref:grpc/index>.

## SignalR

Geçiş yönergeleri için [ SignalR güncelleştirme koduna](xref:migration/22-to-30#signalr) bakın. SignalRşimdi `System.Text.Json` JSON iletilerini serihale/deserialize etmek için kullanır. Bkz. Tabanlı serileştiriciyi geri yüklemek `Newtonsoft.Json`için talimatlar için [Newtonsoft.Json'a geç.](xref:migration/22-to-30#switch-to-newtonsoftjson)

JavaScript ve .NET Istemcileri için SignalR, destek otomatik yeniden bağlantı için eklendi. Varsayılan olarak, istemci hemen yeniden bağlanmaya çalışır ve gerekirse 2, 10 ve 30 saniye sonra yeniden deneyin. İstemci başarılı bir şekilde yeniden bağlanırsa, yeni bir bağlantı kimliği alır. Otomatik yeniden bağlanma devre dışı bırakma dır:

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

Yeniden bağlantı aralıkları milisaniye tabanlı süreler dizisi geçerek belirtilebilir:

```javascript
.withAutomaticReconnect([0, 3000, 5000, 10000, 15000, 30000])
//.withAutomaticReconnect([0, 2000, 10000, 30000]) The default intervals.
```

Yeniden bağlantı aralıklarının tam denetimi için özel bir uygulama geçirilebilir.

Son yeniden bağlanma aralığından sonra yeniden bağlantı başarısız olursa:

* İstemci, bağlantının çevrimdışı olduğunu düşünüyor.
* İstemci yeniden bağlanmaya çalışmayı durdurur.

Yeniden bağlantı denemeleri sırasında, kullanıcıya yeniden bağlantının denendiğini bildirmek için uygulama Kullanıcı Arabirimi'ni güncelleştirin.

Bağlantı kesildiğinde UI geri bildirimi SignalR sağlamak için istemci API'sı aşağıdaki olay işleyicilerini içerecek şekilde genişletildi:

* `onreconnecting`: Geliştiricilere Kullanıcı Arabirimi'ni devre dışı bırakma veya kullanıcılara uygulamanın çevrimdışı olduğunu bilmeleri için bir fırsat verir.
* `onreconnected`: Bağlantı yeniden kurulduktan sonra geliştiricilere Kullanıcı Arabirimi'ni güncelleştirme fırsatı verir.

Aşağıdaki kod, `onreconnecting` bağlanmaya çalışırken Kullanıcı Arabirimi'ni güncelleştirmek için kullanır:

```javascript
connection.onreconnecting((error) => {
    const status = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messageInput").disabled = true;
    document.getElementById("sendButton").disabled = true;
    document.getElementById("connectionStatus").innerText = status;
});
```

Bağlantıda Kullanıcı `onreconnected` Arabirimi'ni güncelleştirmek için aşağıdaki kod kullanır:

```javascript
connection.onreconnected((connectionId) => {
    const status = `Connection reestablished. Connected.`;
    document.getElementById("messageInput").disabled = false;
    document.getElementById("sendButton").disabled = false;
    document.getElementById("connectionStatus").innerText = status;
});
```

SignalR3.0 ve daha sonra bir hub yöntemi yetkilendirme gerektirdiğinde yetkilendirme işleyicileri için özel bir kaynak sağlar. Kaynak bir `HubInvocationContext`örneğidir. Aşağıdakileri `HubInvocationContext` içerir:

* `HubCallerContext`
* Çağrılan hub yönteminin adı.
* Hub yöntemine bağımsız değişkenler.

Azure Active Directory üzerinden birden çok kuruluş oturum açma izni veren bir sohbet odası uygulamasının aşağıdaki örneğini göz önünde bulundurun. Microsoft hesabı olan herkes sohbet etmek için oturum açabilir, ancak yalnızca sahip olan kuruluşun üyeleri kullanıcıları yasaklayabilir veya kullanıcıların sohbet geçmişlerini görüntüleyebilir. Uygulama belirli kullanıcılardan belirli işlevleri kısıtlayabilir.

```csharp
public class DomainRestrictedRequirement :
    AuthorizationHandler<DomainRestrictedRequirement, HubInvocationContext>,
    IAuthorizationRequirement
{
    protected override Task HandleRequirementAsync(AuthorizationHandlerContext context,
        DomainRestrictedRequirement requirement,
        HubInvocationContext resource)
    {
        if (context.User?.Identity?.Name == null)
        {
            return Task.CompletedTask;
        }

        if (IsUserAllowedToDoThis(resource.HubMethodName, context.User.Identity.Name))
        {
            context.Succeed(requirement);
        }

        return Task.CompletedTask;
    }

    private bool IsUserAllowedToDoThis(string hubMethodName, string currentUsername)
    {
        if (hubMethodName.Equals("banUser", StringComparison.OrdinalIgnoreCase))
        {
            return currentUsername.Equals("bob42@jabbr.net", StringComparison.OrdinalIgnoreCase);
        }

        return currentUsername.EndsWith("@jabbr.net", StringComparison.OrdinalIgnoreCase));
    }
}
```

Önceki kodda, `DomainRestrictedRequirement` bir özel `IAuthorizationRequirement`olarak hizmet vermektedir. Kaynak `HubInvocationContext` parametresi geçirildiği için, iç mantık şunları yapabilir:

* Hub'ın çağrıldığı bağlamı denetleyin.
* Kullanıcının tek tek Hub yöntemlerini yürütmesine izin verme konusunda kararlar verin.

Tek tek Hub yöntemleri, kodun çalışma zamanında denetleyebileceği ilkenin adı ile işaretlenebilir. İstemciler tek tek Hub `DomainRestrictedRequirement` yöntemlerini çağırmaya çalıştıkça, işleyici metotlara erişimi çalıştırAr ve denetler. Denetimlerin erişim `DomainRestrictedRequirement` şekline bağlı olarak:

* Tüm oturum açmış kullanıcılar `SendMessage` yöntemi arayabilir.
* Yalnızca bir `@jabbr.net` e-posta adresiyle giriş yapmış olan kullanıcılar kullanıcıların geçmişlerini görüntüleyebilir.
* Kullanıcıları `bob42@jabbr.net` yalnızca sohbet odasından yasaklayabilir.

```csharp
[Authorize]
public class ChatHub : Hub
{
    public void SendMessage(string message)
    {
    }

    [Authorize("DomainRestricted")]
    public void BanUser(string username)
    {
    }

    [Authorize("DomainRestricted")]
    public void ViewUserHistory(string username)
    {
    }
}
```

İlke `DomainRestricted` oluşturma şunları içerebilir:

* *Startup.cs,* yeni ilke ekleyerek.
* Parametre `DomainRestrictedRequirement` olarak özel gereksinimi sağlayın.
* Yetkilendirme `DomainRestricted` aracıile kayıt.

```csharp
services
    .AddAuthorization(options =>
    {
        options.AddPolicy("DomainRestricted", policy =>
        {
            policy.Requirements.Add(new DomainRestrictedRequirement());
        });
    });
```

SignalRhub'lar [Endpoint Yönlendirme'yi](xref:fundamentals/routing)kullanır. SignalRhub bağlantısı daha önce açıkça yapıldı:

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("hubs/chat");
});
```

Önceki sürümde, geliştiricilerin denetleyicileri, Razor sayfalarını ve hub'ları çeşitli yerlere bağlamaları gerekiyordu. Açık bağlantı, neredeyse aynı yönlendirme segmentleri bir dizi sonuçlanır:

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("hubs/chat");
});

app.UseRouting(routes =>
{
    routes.MapRazorPages();
});
```

SignalR3.0 hub'ları uç nokta yönlendirme yoluyla yönlendirilebilir. Uç nokta yönlendirmeile, genellikle tüm yönlendirme şu şekilde `UseRouting`yapılandırılabilir:

```csharp
app.UseRouting(routes =>
{
    routes.MapRazorPages();
    routes.MapHub<ChatHub>("hubs/chat");
});
```

ASP.NET Core 3.0 SignalR ekledi:

İstemciden sunucuya akış. İstemciden sunucuya akışla, sunucu tarafı yöntemleri bir `IAsyncEnumerable<T>` `ChannelReader<T>`veya . Aşağıdaki C# örneğinde, `UploadStream` Hub'daki yöntem istemciden bir dize akışı alır:

```csharp
public async Task UploadStream(IAsyncEnumerable<string> stream)
{
    await foreach (var item in stream)
    {
        // process content
    }
}
```

.NET istemci uygulamaları, `IAsyncEnumerable<T>` yukarıdaki `ChannelReader<T>` `UploadStream` Hub `stream` yönteminin bağımsız değişkeni olarak bir veya örnek ten geçebilir.

`for` Döngü tamamlandıktan ve yerel işlev çıktıktan sonra akış tamamlama gönderilir:

```csharp
async IAsyncEnumerable<string> clientStreamData()
{
    for (var i = 0; i < 5; i++)
    {
        var data = await FetchSomeData();
        yield return data;
    }
}

await connection.SendAsync("UploadStream", clientStreamData());
```

JavaScript istemci uygulamaları SignalR `Subject` yukarıdaki `stream` `UploadStream` Hub yönteminin bağımsız değişkeni için (veya [RxJS Konusu)](https://rxjs.dev/api/index/class/Subject)kullanır.

```javascript
let subject = new signalR.Subject();
await connection.send("StartStream", "MyAsciiArtStream", subject);
```

JavaScript kodu, dizeleri yakalanan ve sunucuya gönderilmeye hazır olarak işlemek için `subject.next` yöntemi kullanabilirsiniz.

```javascript
subject.next("example");
subject.complete();
```

Önceki iki parçacık gibi kod kullanılarak, gerçek zamanlı akış deneyimleri oluşturulabilir.

## <a name="new-json-serialization"></a>Yeni JSON serileştirme

ASP.NET Core 3.0 <xref:System.Text.Json> şimdi Varsayılan olarak JSON serileştirme için kullanır:

* Okur ve JSON asynchronously yazıyor.
* UTF-8 metni için optimize edi.
* Genellikle `Newtonsoft.Json`daha yüksek performans .

Core 3.0'ASP.NET Json.NET eklemek için Bkz. [Newtonsoft.Json tabanlı JSON format desteği ekle.](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support)

## <a name="new-razor-directives"></a>Yeni Razor direktifleri

Aşağıdaki liste yeni Razor yönergeleri içerir:

* [`@attribute`](xref:mvc/views/razor#attribute)&ndash; Yönerge, `@attribute` verilen özniteliği oluşturulan sayfa veya görünümün sınıfına uygular. Örneğin, `@attribute [Authorize]`.
* [`@implements`](xref:mvc/views/razor#implements)&ndash; Yönerge, `@implements` oluşturulan sınıf için bir arabirim uygular. Örneğin, `@implements IDisposable`.

## <a name="identityserver4-supports-authentication-and-authorization-for-web-apis-and-spas"></a>IdentityServer4 web API'leri ve SP'leri için kimlik doğrulamayı ve yetkilendirmeyi destekler

ASP.NET Core 3.0, web API yetkilendirme desteğini kullanarak Tek Sayfalı Uygulamalarda (SPA' lar) kimlik doğrulaması sunar. ASP.NET Kullanıcıların kimlik doğrulaması ve depolanması için Temel Kimlik, Open ID Connect'i uygulamak için [IdentityServer4](https://identityserver.io/) ile birleştirilir.

IdentityServer4 Core 3.0 ASP.NET için bir OpenID Connect ve OAuth 2.0 çerçevesidir. Aşağıdaki güvenlik özelliklerini sağlar:

* Hizmet Olarak Kimlik Doğrulama (AaaS)
* Birden fazla uygulama türü üzerinde tek oturum açma/kapatma (SSO)
* API'ler için erişim denetimi
* Federasyon Ağ Geçidi

Daha fazla bilgi [için, IdentityServer4 belgelerine](http://docs.identityserver.io/en/latest/index.html) veya [SP'ler için kimlik doğrulama ve yetkilendirmeye](xref:security/authentication/identity/spa)bakın.

## <a name="certificate-and-kerberos-authentication"></a>Sertifika ve Kerberos kimlik doğrulaması

Sertifika kimlik doğrulaması şunları gerektirir:

* Sunucuyu sertifika kabul etmek üzere yapılandırma.
* Kimlik doğrulama ara sını `Startup.Configure`ekleme.
* Sertifika kimlik doğrulama hizmetini `Startup.ConfigureServices`'ye ekleme

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(
        CertificateAuthenticationDefaults.AuthenticationScheme)
            .AddCertificate();
    // Other service configuration removed.
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseAuthentication();
    // Other app configuration removed.
}
```

Sertifika kimlik doğrulaması için seçenekler şunları içerir:

* İmzalı sertifikaları kabul edin.
* Sertifika iptalini denetleyin.
* Sunulan sertifikanın içinde doğru kullanım bayrakları olduğundan kontrol edin.

Varsayılan kullanıcı ilkesi sertifika özelliklerinden oluşturulur. Kullanıcı ilkesi, anaparanın eklanmasını veya değiştirilmesini sağlayan bir olay içerir. Daha fazla bilgi için bkz. <xref:security/authentication/certauth>.

[Windows Kimlik Doğrulama](/windows-server/security/windows-authentication/windows-authentication-overview) Linux ve macOS üzerine genişletildi. Önceki sürümlerde, Windows Kimlik Doğrulama [IIS](xref:host-and-deploy/iis/index) ve [HttpSys](xref:fundamentals/servers/httpsys)ile sınırlıydı. Core 3.0ASP.NET, [Kestrel](xref:fundamentals/servers/kestrel) Windows, Linux ve MacOS Windows etki alanı birleştirilmiş ana bilgisayarlar için Müzakere, [Kerberos](/windows-server/security/kerberos/kerberos-authentication-overview)ve [NTLM](/windows-server/security/kerberos/ntlm-overview)kullanma yeteneğine sahiptir. Bu kimlik doğrulama şemalarının kerkenez desteği [Microsoft.AspNetCore.Authentication.Negotiate NuGet](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) paketi tarafından sağlanır. Diğer kimlik doğrulama hizmetlerinde olduğu gibi, kimlik doğrulama uygulamasını geniş bir şekilde yapılandırın ve hizmeti yapılandırın:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(NegotiateDefaults.AuthenticationScheme)
        .AddNegotiate();
    // Other service configuration removed.
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseAuthentication();
    // Other app configuration removed.
}
```

Ana bilgisayar gereksinimleri:

* Windows ana bilgisayarlarının, uygulamayı barındıran kullanıcı hesabına [Hizmet Ana](/windows/win32/ad/service-principal-names) Adı (SPNs) eklenmeleri gerekir.
* Linux ve macOS makineleri etki alanına birleştirilmelidir.
  * Web işlemi için SPN'ler oluşturulmalıdır.
  * [Anahtar sekme dosyaları](https://blogs.technet.microsoft.com/pie/2018/01/03/all-you-need-to-know-about-keytab-files/) ana bilgisayarda oluşturulmalı ve yapılandırılmalıdır.

Daha fazla bilgi için bkz. <xref:security/authentication/windowsauth>.

## <a name="template-changes"></a>Şablon değişiklikleri

Web UI şablonları (Razor Pages, MVC denetleyici ve görünümler) aşağıdaki kaldırılmış var:

* Çerez onayı UI artık dahil değildir. ASP.NET Core 3.0 şablonu oluşturulan bir uygulamada çerez <xref:security/gdpr>onayı özelliğini etkinleştirmek için bkz.
* Komut dosyaları ve ilgili statik varlıklar artık CDN'ler kullanmak yerine yerel dosyalar olarak başvurulmaktadır. Daha fazla bilgi için, bkz [#14350.](https://github.com/dotnet/AspNetCore.Docs/issues/14350)

Açısal 8'i kullanmak üzere güncelleştirilmiş Açısal şablon.

Razor sınıf kitaplığı (RCL) şablonu varsayılan olarak Razor bileşen geliştirme için varsayılan dır. Visual Studio'da yeni bir şablon seçeneği, sayfalar ve görünümler için şablon desteği sağlar. Komut kabuğundaki şablondan Bir RCL oluştururken, `--support-pages-and-views` `dotnet new razorclasslib --support-pages-and-views`seçeneği ().

## <a name="generic-host"></a>Genel Konak

Core 3.0 şablonlarının <xref:fundamentals/host/generic-host>ASP.NET kullanılır. Önceki sürümler kullanılır. <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> .NET Core Generic Host<xref:Microsoft.Extensions.Hosting.HostBuilder>() kullanarak web'e özgü olmayan diğer sunucu senaryoları ile ASP.NET Core uygulamaları daha iyi entegrasyon sağlar. Daha fazla bilgi için, [HostBuilder WebHostBuilder yerini](xref:migration/22-to-30?view=aspnetcore-2.2#hostbuilder-replaces-webhostbuilder)bakın.

### <a name="host-configuration"></a>Ana bilgisayar yapılandırması

ASP.NET Core 3.0 yayımlanmasından önce, önceden belirlenmiş `ASPNETCORE_` ortam değişkenleri Web Barındıran Kişi'nin ana bilgisayar yapılandırması için yüklenmiş. 3.0'da, `AddEnvironmentVariables` ana bilgisayar yapılandırması `DOTNET_` için önceden belirlenmiş `CreateDefaultBuilder`ortam değişkenlerini yüklemek için kullanılır.

### <a name="changes-to-startup-constructor-injection"></a>Başlangıç yapıcı enjeksiyonunda yapılan değişiklikler

Genel Ana Bilgisayar yalnızca yapıcı `Startup` enjeksiyon için aşağıdaki türleri destekler:

* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

Tüm hizmetler yine de `Startup.Configure` doğrudan yönteme bağımsız değişken olarak enjekte edilebilir. Daha fazla bilgi için bkz [#353.](https://github.com/aspnet/Announcements/issues/353)

## <a name="kestrel"></a>Kestrel

* Kerkenez yapılandırması Genel Ana Bilgisayar'a geçiş için güncelleştirildi. 3.0 yılında, Kerkenez tarafından `ConfigureWebHostDefaults`sağlanan web barındırma oluşturucu üzerinde yapılandırılmıştır.
* Bağlantı Bağdaştırıcıları Kerkenez'den kaldırıldı ve ASP.NET Core ardışık ASP.NET'daki ANCAK alt düzey bağlantılar için BENZER Olan Bağlantı Middleware ile değiştirildi.
* Kerkenez aktarım katmanı, 'de `Connections.Abstractions`genel bir arayüz olarak ortaya çıkmıştır.
* Üstbilgi ve römorklar arasındaki belirsizlik, izleyen üstbilginin yeni bir koleksiyona taşınmasıyla giderildi.
* Senkron G/Ç API'leri, `HttpRequest.Body.Read`örneğin, uygulama çökmelerine yol açan iş parçacığı açlığı için ortak bir kaynaktır. 3.0'da, `AllowSynchronousIO` varsayılan olarak devre dışı bırakılır.

Daha fazla bilgi için bkz. <xref:migration/22-to-30#kestrel>.

## <a name="http2-enabled-by-default"></a>VARSAYıLAN OLARAK ETKINLEŞTIRILEN HTTP/2

HTTPS uç noktaları için Kestrel'de varsayılan olarak HTTP/2 etkinleştirilir. İşletim sistemi tarafından desteklendiğinde IIS veya HTTP.sys için HTTP/2 desteği etkinleştirilir.

## <a name="eventcounters-on-request"></a>İstek üzerine EventCounters

Hosting EventSource, `Microsoft.AspNetCore.Hosting`gelen isteklerle ilgili <xref:System.Diagnostics.Tracing.EventCounter> aşağıdaki yeni türleri yayır:

* `requests-per-second`
* `total-requests`
* `current-requests`
* `failed-requests`

## <a name="endpoint-routing"></a>Uç nokta yönlendirme

Çerçevelerin (örneğin, MVC) ara yazılımla iyi çalışmasını sağlayan Uç Nokta Yönlendirmesi geliştirildi:

* Ara yazılım ve uç noktaların `Startup.Configure`sırası, istek işleme ardışık düzeninde yapılandırılabilir.
* Uç noktalar ve ara yazılımlar, Sağlık Denetimleri gibi diğer ASP.NET Çekirdek tabanlı teknolojiler ile iyi bir şekilde oluşur.
* Uç noktalar, hem ara yazılımda hem de MVC'de CORS veya yetkilendirme gibi bir ilke uygulayabilir.
* Filtreler ve öznitelikler denetleyicilerde yöntemler üzerine yerleştirilebilir.

Daha fazla bilgi için bkz. <xref:fundamentals/routing#routing-basics>.

## <a name="health-checks"></a>Sistem durumu denetimleri

Sistem Durumu Denetimleri, Genel Ana Bilgisayar ile uç nokta yönlendirmeyi kullanır. In, `Startup.Configure` `MapHealthChecks` bitiş noktası URL'si veya göreli yolu olan bitiş noktası oluşturucuyu arayın:

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
});
```

Sistem Durumu Denetimleri bitiş noktaları şunları yapabilir:

* İzin verilen bir veya daha fazla ana bilgisayar/bağlantı noktası belirtin.
* Yetkilendirme gerektirir.
* CORS gerektirir.

Daha fazla bilgi için aşağıdaki makalelere bakın:

* <xref:migration/22-to-30#health-checks>
* <xref:host-and-deploy/health-checks>

## <a name="pipes-on-httpcontext"></a>HttpContext üzerinde Borular

Artık istek gövdesini okumak ve <xref:System.IO.Pipelines> YANıT gövdesini API'yi kullanarak yazmak mümkündür. Sanal Makineye (VM) bağlı bir veya birden çok işletim sistemi diski içerdiği için <!-- <xref:Microsoft.AspNetCore.Http.HttpRequest.BodyReader> --> `HttpRequest.BodyReader`özelliği istek <xref:System.IO.Pipelines.PipeReader> gövdesini okumak için kullanılabilecek bir özellik sağlar. Sanal Makineye (VM) bağlı bir veya birden çok işletim sistemi diski içerdiği için <!-- <xref:Microsoft.AspNetCore.Http.> --> `HttpResponse.BodyWriter`özelliği yanıt <xref:System.IO.Pipelines.PipeWriter> gövdesi yazmak için kullanılabilecek bir sağlar. `HttpRequest.BodyReader`akımın `HttpRequest.Body` bir analogudur. `HttpResponse.BodyWriter`akımın `HttpResponse.Body` bir analogudur.

<!-- indirectly related, https://github.com/dotnet/docs/pull/14414 won't be published by 9/23  -->

## <a name="improved-error-reporting-in-iis"></a>IIS'de geliştirilmiş hata raporlaması

IIS'de ASP.NET Core uygulamalarını barındırırken ki başlangıç hataları artık daha zengin tanılama verileri üretir. Bu hatalar, uygun olan her yerde yığın izleriyle Birlikte Windows Olay Günlüğü'ne bildirilir. Ayrıca, tüm uyarılar, hatalar ve işlenmemiş özel durumlar Windows Olay Günlüğü'ne günlüğe kaydedilir.

## <a name="worker-service-and-worker-sdk"></a>İşçi Hizmeti ve İşçi SDK

.NET Core 3.0 yeni İşçi Hizmeti uygulaması şablonu tanıttı. Bu şablon, .NET Core'da uzun süre çalışan hizmetleri yazmak için bir başlangıç noktası sağlar.

Daha fazla bilgi için bkz.

* [.NET Çekirdek İşçiler Windows Hizmetleri olarak](https://devblogs.microsoft.com/aspnet/net-core-workers-as-windows-services/)
* <xref:fundamentals/host/hosted-services>
* <xref:host-and-deploy/windows-service>

## <a name="forwarded-headers-middleware-improvements"></a>İleşli Üstbilgi Ara ware geliştirmeleri

ASP.NET Core'un önceki <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> sürümlerinde, bir Azure Linux'a veya IIS dışındaki herhangi bir ters proxy'nin arkasına dağıtıldığında arama yapmak ve <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> sorunludur. Önceki sürümleri için düzeltme [İleri Linux ve Non-IIS ters yakınlıklar için düzeni](xref:host-and-deploy/proxy-load-balancer#forward-the-scheme-for-linux-and-non-iis-reverse-proxies)belgelenmiştir.

Bu senaryo Core 3.0 ASP.NET düzeltilir. Ana bilgisayar, [Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers-middleware-options) `ASPNETCORE_FORWARDEDHEADERS_ENABLED` ortam değişkeni `true`. `ASPNETCORE_FORWARDEDHEADERS_ENABLED`bizim konteyner `true` görüntüleri ayarlanır.

## <a name="performance-improvements"></a>Performans iyileştirmeleri

ASP.NET Core 3.0 bellek kullanımını azaltmak ve iş verimi geliştirmek birçok iyileştirmeler içerir:

* Kapsamlı hizmetler için yerleşik bağımlılık enjeksiyon konteyneri kullanırken bellek kullanımında azalma.
* Ara yazılım senaryoları ve yönlendirme de dahil olmak üzere çerçeve genelinde ayırmalarda azalma.
* WebSocket bağlantıları için bellek kullanımında azalma.
* HTTPS bağlantıları için bellek azaltma ve iş geliştirmeleri.
* Yeni optimize edilmiş ve tamamen asynchronous JSON serializer.
* Form ayrıştırma bellek kullanımı ve iş geliştirmeleri azalma.

## <a name="aspnet-core-30-only-runs-on-net-core-30"></a>ASP.NET Core 3.0 yalnızca .NET Core 3.0 üzerinde çalışır

Core 3.0ASP.NET itibariyle .NET Framework artık desteklenen bir hedef çerçevesi değildir. .NET Framework'u hedefleyen projeler [.NET Core 2.1 LTS sürümü](https://dotnet.microsoft.com/download/dotnet-core/2.1)kullanılarak tam destekli bir şekilde devam edebilir. Core 2.1.x ile ilgili paketlerin çoğu ASP.NET .NET Core 2.1 için üç yıllık LTS döneminin ötesinde süresiz olarak desteklenecektir.

Geçiş bilgileri için [kodunuzun .NET Framework'den .NET Core'a geçişine](/dotnet/core/porting/)bakın.

## <a name="use-the-aspnet-core-shared-framework"></a>ASP.NET Core paylaşılan çerçevesini kullanma

ASP.NET Core 3.0 paylaşılan çerçeve, [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app)bulunan, `<PackageReference />` artık proje dosyasında açık bir öğe gerektirir. Paylaşılan çerçeve, proje dosyasında `Microsoft.NET.Sdk.Web` SDK kullanırken otomatik olarak başvurulur:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

## <a name="assemblies-removed-from-the-aspnet-core-shared-framework"></a>ASP.NET Core paylaşılan çerçevesinden kaldırılan derlemeler

Core 3.0 paylaşılan ASP.NET'nden kaldırılan en önemli derlemeler şunlardır:

* [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/) (Json.NET). Core 3.0'ASP.NET Json.NET eklemek için Bkz. [Newtonsoft.Json tabanlı JSON format desteği ekle.](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support) ASP.NET Core 3.0 `System.Text.Json` okuma ve JSON yazmak için tanıttı. Daha fazla bilgi için bu belgede [Yeni JSON serileştirme](#new-json-serialization) bakın.
* [Entity Framework Core](/ef/core/)

Paylaşılan çerçeveden kaldırılan derlemelerin tam listesi için [bkz.](https://github.com/dotnet/AspNetCore/issues/3755) Bu değişikliğin motivasyonu hakkında daha fazla bilgi için, [3.0'da Microsoft.AspNetCore.App'teki Breaking değişiklikleri](https://github.com/aspnet/Announcements/issues/325) ve [Core 3.0'ASP.NET gelen değişikliklere ilk bakışta](https://devblogs.microsoft.com/aspnet/a-first-look-at-changes-coming-in-asp-net-core-3-0/)bakın.

<!-- 
## Additional information
For the complete list of changes, see the [ASP.NET Core 3.0 Release Notes](WHERE IS THIS????).
-->
 