---
title: ASP.NET Core 3,0 ' deki yenilikler
author: rick-anderson
description: ASP.NET Core 3,0 ' deki yeni özellikler hakkında bilgi edinin.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- appsettings.json
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
uid: aspnetcore-3.0
ms.openlocfilehash: 235daac5c08248ca2052de6b44e66a8162ce23ad
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051245"
---
# <a name="whats-new-in-aspnet-core-30"></a>ASP.NET Core 3,0 ' deki yenilikler

Bu makalede, ASP.NET Core 3,0 ' deki en önemli değişiklikler ilgili belgelerin bağlantılarıyla vurgulanır.

## Blazor

Blazor , .NET ile etkileşimli istemci tarafı Web Kullanıcı arabirimi oluşturmak için ASP.NET Core yeni bir çerçevedir:

* JavaScript yerine C# kullanarak zengin etkileşimli uo 'lar oluşturun.
* .NET ' te yazılmış sunucu tarafı ve istemci tarafı uygulama mantığını paylaşabilirsiniz.
* Mobil tarayıcılar dahil olmak üzere geniş tarayıcı desteği için Kullanıcı arabirimini HTML ve CSS olarak işleme.

Blazor çerçeve tarafından desteklenen senaryolar:

* Yeniden kullanılabilir kullanıcı arabirimi bileşenleri ( Razor bileşenler)
* İstemci tarafı yönlendirme
* Bileşen düzenleri
* Bağımlılık ekleme desteği
* Formlar ve doğrulama
* Sınıf kitaplıklarıyla bileşen kitaplıkları derleme Razor
* JavaScript ile birlikte çalışma

Daha fazla bilgi için bkz. <xref:blazor/index>.

### Blazor Server

Blazor Kullanıcı arabirimi güncelleştirmelerinin uygulanma, bileşen işleme mantığını ayırır. Blazor Server ASP.NET Core uygulamasında sunucuda barındırma bileşenleri için destek sağlar Razor . Kullanıcı Arabirimi güncelleştirmeleri bir bağlantı üzerinden işlenir SignalR . Blazor Server ASP.NET Core 3,0 ' de desteklenir.

### <a name="no-locblazor-webassembly-preview"></a>Blazor WebAssembly Önizle

Blazor uygulamalar, bir WebAssembly tabanlı .NET çalışma zamanı kullanarak doğrudan tarayıcıda da çalıştırılabilir. Blazor WebAssembly önizlemededir *ve ASP.NET Core 3,0 ' de* desteklenmez. Blazor WebAssembly ASP.NET Core gelecek bir sürümünde desteklenecektir.

### <a name="no-locrazor-components"></a>Razor bileşenleri

Blazor uygulamalar bileşenlerden oluşturulmuştur. Bileşenler, bir sayfa, iletişim kutusu veya form gibi kullanıcı arabirimi (UI) için kendi içinde yer alan öbeklerdir. Bileşenler, Kullanıcı arabirimi işleme mantığını ve istemci tarafı olay işleyicilerini tanımlayan normal .NET sınıflarıdır. JavaScript olmadan zengin etkileşimli Web uygulamaları oluşturabilirsiniz.

İçindeki bileşenler Blazor genellıkle Razor HTML ve C# ' nin doğal bir karışımı olan sözdizimi kullanılarak yazılır. Razor bileşenler, Razor her ikisi de kullandıkları sayfalara ve MVC görünümlerine benzerdir Razor . Bir istek-yanıt modelini temel alan sayfaların ve görünümlerin aksine, bileşenler Kullanıcı arabirimi oluşturmayı işlemek için kullanılır.

## <a name="grpc"></a>gRPC

[GRPC](https://grpc.io/):

* Popüler, yüksek performanslı bir RPC (uzak yordam çağrısı) çerçevesidir.
* , API geliştirmeye yönelik olarak yapılan bir sözleşmenin ilk yaklaşımını sağlar.
* , Gibi modern teknolojiler kullanır:

  * Taşıma için HTTP/2.
  * Arabirim açıklaması dili olarak protokol arabellekleri.
  * İkili serileştirme biçimi.
* Şöyle özellikler sağlar:

  * Kimlik Doğrulaması
  * Çift yönlü akış ve akış denetimi.
  * İptal ve zaman aşımları.

ASP.NET Core 3,0 ' deki gRPC işlevselliği şunları içerir:

* [GRPC. AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore): GRPC hizmetlerini barındırmak için bir ASP.NET Core çerçevesi. gRPC on ASP.NET Core, günlüğe kaydetme, bağımlılık ekleme (dı), kimlik doğrulama ve yetkilendirme gibi standart ASP.NET Core özelliklerle tümleştirilir.
* [GRPC .net. Client](https://www.nuget.org/packages/Grpc.Net.Client): .NET Core için tanıdık bir GRPC istemcisi `HttpClient` .
* [GRPC .net. ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory): GRPC istemcisi ile tümleştirme `HttpClientFactory` .

Daha fazla bilgi için bkz. <xref:grpc/index>.

## SignalR

Bkz. geçiş yönergeleri için [ SignalR kodu güncelleştirme](xref:migration/22-to-30#signalr) . SignalR Artık `System.Text.Json` JSON iletilerini seri hale getirmek/seri durumdan çıkarmak için kullanır. Tabanlı seri hale getirici 'yi geri yükleme yönergeleri için bkz. [Newtonsoft.Js'ye geçme](xref:migration/22-to-30#switch-to-newtonsoftjson) `Newtonsoft.Json` .

İçin JavaScript ve .NET Istemcilerinde SignalR , otomatik yeniden bağlanma için destek eklenmiştir. Varsayılan olarak, istemci hemen yeniden bağlanmaya çalışır ve gerekirse 2, 10 ve 30 saniye sonra yeniden dener. İstemci başarıyla yeniden bağlanırsa, yeni bir bağlantı KIMLIĞI alır. Otomatik yeniden bağlanma kabul etme:

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect()
    .build();
```

Yeniden bağlantı aralıkları bir dizi milisaniyelik tabanlı süre geçirerek belirtilebilir:

```javascript
.withAutomaticReconnect([0, 3000, 5000, 10000, 15000, 30000])
//.withAutomaticReconnect([0, 2000, 10000, 30000]) The default intervals.
```

Yeniden bağlantı aralıklarının tam denetimi için özel bir uygulama geçirilebilir.

Son yeniden bağlanma aralığından sonra yeniden bağlantı başarısız olursa:

* İstemci, bağlantının çevrimdışı olduğunu varsayar.
* İstemci yeniden bağlanmayı denemeyi durduruyor.

Yeniden bağlanma denemeleri sırasında, kullanıcıya yeniden bağlantı denenmekte olduğunu bildirmek için uygulama kullanıcı arabirimini güncelleştirin.

Bağlantı kesildiğinde UI geri bildirimi sağlamak için, SignalR istemci API 'si aşağıdaki olay işleyicilerini içerecek şekilde genişletilir:

* `onreconnecting`: Geliştiricilere Kullanıcı arabirimini devre dışı bırakma veya uygulamanın çevrimdışı olduğunu bilmesini sağlayan bir fırsat sağlar.
* `onreconnected`: Bağlantı kurulduktan sonra geliştiricilere Kullanıcı arabirimini güncelleştirme fırsatı verir.

Aşağıdaki kod, `onreconnecting` bağlanmaya çalışırken kullanıcı arabirimini güncelleştirmek için kullanır:

```javascript
connection.onreconnecting((error) => {
    const status = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messageInput").disabled = true;
    document.getElementById("sendButton").disabled = true;
    document.getElementById("connectionStatus").innerText = status;
});
```

Aşağıdaki kod, `onreconnected` bağlantıda Kullanıcı arabirimini güncelleştirmek için kullanır:

```javascript
connection.onreconnected((connectionId) => {
    const status = `Connection reestablished. Connected.`;
    document.getElementById("messageInput").disabled = false;
    document.getElementById("sendButton").disabled = false;
    document.getElementById("connectionStatus").innerText = status;
});
```

SignalR 3,0 ve üzeri, bir hub yöntemi yetkilendirme gerektirdiğinde, yetkilendirme işleyicilerine özel bir kaynak sağlar. Kaynak bir örneğidir `HubInvocationContext` . `HubInvocationContext`Şunları içerir:

* `HubCallerContext`
* Çağrılan hub yönteminin adı.
* Hub yöntemi için bağımsız değişkenler.

Azure Active Directory aracılığıyla birden çok kuruluşun oturum açmasına izin veren bir sohbet odası uygulamasının aşağıdaki örneğini göz önünde bulundurun. Microsoft hesabı herkes sohbet için oturum açabilir, ancak yalnızca sahip olunan kuruluşun üyeleri kullanıcıları veya kullanıcıların sohbet geçmişlerini görüntüleyebilir. Uygulama belirli kullanıcılardan belirli işlevleri kısıtlayabilir.

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

Yukarıdaki kodda `DomainRestrictedRequirement` özel işlevi görür `IAuthorizationRequirement` . `HubInvocationContext`Kaynak parametresi geçirildiğinden iç mantık şunları yapabilir:

* Hub 'ın çağrıldığı bağlamı inceleyin.
* Kullanıcının bireysel hub yöntemlerini yürütmesine izin verirken kararlar alın.

Tek tek hub yöntemleri, çalışma zamanında kodun denetlediği ilkenin adıyla işaretlenebilir. İstemciler tek tek hub yöntemlerini çağırmayı denediğinden, `DomainRestrictedRequirement` işleyici çalışır ve yöntemlere erişimi denetler. `DomainRestrictedRequirement`Denetimlerin erişim yöntemine göre:

* Tüm oturum açmış kullanıcılar `SendMessage` yöntemini çağırabilir.
* Yalnızca bir `@jabbr.net` e-posta adresiyle oturum açan kullanıcılar, kullanıcıların geçmişlerini görüntüleyebilir.
* Yalnızca `bob42@jabbr.net` sohbet odasından kullanıcıları Ban yapabilir.

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

İlkeyi oluşturmak `DomainRestricted` şunları içerebilir:

* *Startup.cs* ' de, yeni ilkeyi ekleme.
* Özel `DomainRestrictedRequirement` gereksinimi parametre olarak belirtin.
* `DomainRestricted`Yetkilendirme ara yazılımı ile kaydetme.

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

SignalR Hub [uç nokta yönlendirme](xref:fundamentals/routing)kullanır. SignalR Hub bağlantısı daha önce açık olarak yapıldı:

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("hubs/chat");
});
```

Önceki sürümde, geliştiriciler, Razor Sayfalar ve hub 'ları çeşitli yerlerde bağlamak için gereklidir. Açık bağlantı, neredeyse aynı bir dizi yönlendirme segmentine neden olur:

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

SignalR 3,0 hub 'ları, uç nokta yönlendirme aracılığıyla yönlendirilebilir. Uç nokta yönlendirme ile, genellikle tüm yönlendirme ' de yapılandırılabilir `UseRouting` :

```csharp
app.UseRouting(routes =>
{
    routes.MapRazorPages();
    routes.MapHub<ChatHub>("hubs/chat");
});
```

ASP.NET Core 3,0 SignalR eklendi:

İstemciden sunucuya akış. İstemciden sunucuya akışla, sunucu tarafı yöntemleri veya örneklerinden birini alabilir `IAsyncEnumerable<T>` `ChannelReader<T>` . Aşağıdaki C# örneğinde, `UploadStream` hub 'daki Yöntem istemciden dizelerin akışını alacaktır:

```csharp
public async Task UploadStream(IAsyncEnumerable<string> stream)
{
    await foreach (var item in stream)
    {
        // process content
    }
}
```

.NET istemci uygulamaları, `IAsyncEnumerable<T>` `ChannelReader<T>` `stream` Yukarıdaki hub yönteminin bağımsız değişkeni olarak bir ya da örneği geçirebilir `UploadStream` .

Döngü tamamlandıktan `for` ve yerel işlev çıktıktan sonra, akış tamamlama gönderilir:

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

JavaScript istemci uygulamaları, SignalR `Subject` Yukarıdaki hub yönteminin bağımsız değişkeni olarak (veya bir [Rxjs konusu](https://rxjs.dev/api/index/class/Subject)) kullanır `stream` `UploadStream` .

```javascript
let subject = new signalR.Subject();
await connection.send("StartStream", "MyAsciiArtStream", subject);
```

JavaScript kodu, `subject.next` yakalandıkları ve sunucuya gönderilmeye hazırlanıyor gibi dizeleri işlemek için yöntemini kullanabilir.

```javascript
subject.next("example");
subject.complete();
```

Önceki iki kod parçacığı gibi kodu kullanarak gerçek zamanlı akış deneyimleri oluşturulabilir.

## <a name="new-json-serialization"></a>Yeni JSON serileştirmesi

ASP.NET Core 3,0 artık <xref:System.Text.Json> JSON serileştirme için varsayılan olarak kullanır:

* JSON 'yi zaman uyumsuz olarak okur ve yazar.
* UTF-8 metni için iyileştirilmiştir.
* Genellikle daha yüksek performans `Newtonsoft.Json` .

ASP.NET Core 3,0 ' ye Json.NET eklemek için bkz. [Newtonsoft.Jstabanlı JSON biçimi desteği ekleme](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support).

## <a name="new-no-locrazor-directives"></a>Yeni Razor yönergeler

Aşağıdaki listede yeni yönergeler yer almaktadır Razor :

* [`@attribute`](xref:mvc/views/razor#attribute): `@attribute` Yönergesi verilen özniteliği oluşturulan sayfanın veya görünümün sınıfına uygular. Örneğin, `@attribute [Authorize]`.
* [`@implements`](xref:mvc/views/razor#implements): `@implements` Yönerge, oluşturulan sınıf için bir arabirim uygular. Örneğin, `@implements IDisposable`.

## <a name="no-locidentityserver4-supports-authentication-and-authorization-for-web-apis-and-spas"></a>IdentityServer4, Web API 'Leri ve maça 'Ları için kimlik doğrulama ve yetkilendirmeyi destekler

ASP.NET Core 3,0, Web API yetkilendirmesi desteğini kullanarak tek sayfalı uygulamalarda (Spaon) kimlik doğrulaması sunmaktadır. ASP.NET Core Identitykimlik doğrulama ve depolama kullanıcıları için OpenID Connect 'i uygulamak üzere [ Identity Server4](https://identityserver.io/) ile birleştirilir.

IdentityServer4, ASP.NET Core 3,0 için bir OpenID Connect ve OAuth 2,0 çerçevesidir. Aşağıdaki güvenlik özelliklerini sunar:

* Hizmet olarak kimlik doğrulaması (AaaS)
* Birden çok uygulama türü üzerinde çoklu oturum açma/kapatma (SSO)
* API 'Ler için erişim denetimi
* Federasyon ağ geçidi

Daha fazla bilgi için bkz [. Identity Server4 belgeleri](http://docs.identityserver.io/en/latest/index.html) veya [kimlik doğrulaması ve kimlik doğrulama ve yetkilendirme](xref:security/authentication/identity/spa).

## <a name="certificate-and-kerberos-authentication"></a>Sertifika ve Kerberos kimlik doğrulaması

Sertifika kimlik doğrulaması şunları gerektirir:

* Sunucu, sertifikaları kabul edecek şekilde yapılandırılıyor.
* Kimlik doğrulama ara yazılımı ' ye ekleme `Startup.Configure` .
* ' De sertifika kimlik doğrulama hizmeti ekleniyor `Startup.ConfigureServices` .

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

Sertifika kimlik doğrulaması seçenekleri şunları yapabilme:

* Otomatik olarak imzalanan sertifikaları kabul edin.
* Sertifika iptali olup olmadığını denetleyin.
* Profili oluşturulan sertifikanın, içinde doğru kullanım bayrakları olduğunu denetleyin.

Varsayılan bir Kullanıcı sorumlusu, sertifika özelliklerinden oluşturulur. Kullanıcı sorumlusu, sorumlunun takıma veya değiştirilmesine izin veren bir olay içerir. Daha fazla bilgi için bkz. <xref:security/authentication/certauth>.

[Windows kimlik doğrulaması](/windows-server/security/windows-authentication/windows-authentication-overview) , Linux ve MacOS üzerine genişletildi. Önceki sürümlerde, Windows kimlik doğrulaması [IIS](xref:host-and-deploy/iis/index) ve [httpsys](xref:fundamentals/servers/httpsys)ile sınırlandırıldı. ASP.NET Core 3,0 ' de [Kestrel](xref:fundamentals/servers/kestrel) , Windows etki alanına katılmış konaklar için Windows, Linux ve MacOS üzerinde Negotiate, [Kerberos](/windows-server/security/kerberos/kerberos-authentication-overview)ve [NTLM](/windows-server/security/kerberos/ntlm-overview)kullanma olanağına sahiptir. Bu kimlik doğrulama düzenlerinin Kestrel desteği [Microsoft. AspNetCore. Authentication. Negotiate NuGet](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) paketi tarafından sağlanır. Diğer kimlik doğrulama hizmetlerinde olduğu gibi, kimlik doğrulama uygulaması genelinde yapılandırın ve ardından hizmeti yapılandırın:

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

* Windows Konakları, uygulamayı barındıran Kullanıcı hesabına eklenmiş [hizmet sorumlusu adlarına](/windows/win32/ad/service-principal-names) (SPN) sahip olmalıdır.
* Linux ve macOS makineleri etki alanına katılmalıdır.
  * Web işlemi için SPN oluşturulması gerekir.
  * Ana makinede [keytab dosyalarının](/archive/blogs/pie/all-you-need-to-know-about-keytab-files) oluşturulup yapılandırılması gerekir.

Daha fazla bilgi için bkz. <xref:security/authentication/windowsauth>.

## <a name="template-changes"></a>Şablon değişiklikleri

Web UI şablonları ( Razor Sayfalar, denetleyici ve GÖRÜNÜMLERLE Mvc) aşağıdaki gibi kaldırılmıştır:

* cookieOnay Kullanıcı arabirimi artık dahil değildir. cookieASP.NET Core 3,0 şablon tarafından oluşturulan bir uygulamada izin özelliğini etkinleştirmek için, bkz <xref:security/gdpr> ..
* Betiklerin ve ilgili statik varlıkların artık CDNs kullanmak yerine yerel dosyalar olarak başvuruluyor. Daha fazla bilgi için, bkz. [betikler ve ilgili statik varlıklar artık geçerli ortama (ASPNET/AspNetCore.Docs #14350) göre CDNs kullanmak yerine yerel dosyalar olarak başvuruluyor](https://github.com/dotnet/AspNetCore.Docs/issues/14350).

Angular şablonu, angular 8 ' i kullanacak şekilde güncelleştirildi.

RazorSınıf kitaplığı (RCL) şablonu Razor Varsayılan olarak varsayılan olarak bileşen geliştirme olur. Visual Studio 'da yeni bir şablon seçeneği, sayfalar ve görünümler için şablon desteği sağlar. Bir komut kabuğunda şablondan RCL oluştururken, `--support-pages-and-views` () seçeneğini geçirin `dotnet new razorclasslib --support-pages-and-views` .

## <a name="generic-host"></a>Genel Konak

ASP.NET Core 3,0 şablonları kullanılır <xref:fundamentals/host/generic-host> . Önceki sürümler kullanıldı <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> . .NET Core Genel Host () kullanımı, <xref:Microsoft.Extensions.Hosting.HostBuilder> Web 'e özgü olmayan diğer sunucu senaryolarıyla ASP.NET Core uygulamaları daha iyi tümleştirme sağlar. Daha fazla bilgi için bkz. [Hostbuilder WebHostBuilder 'ın yerini alır](xref:migration/22-to-30?view=aspnetcore-2.2#hostbuilder-replaces-webhostbuilder).

### <a name="host-configuration"></a>Konak yapılandırması

ASP.NET Core 3,0 ' nin yayınlanmasından önce, `ASPNETCORE_` Web konağının ana bilgisayar yapılandırması için, öneki olan ortam değişkenleri yüklendi. 3,0 ' de, `AddEnvironmentVariables` `DOTNET_` ile konak yapılandırması için önekli ortam değişkenlerini yüklemek için kullanılır `CreateDefaultBuilder` .

### <a name="changes-to-startup-constructor-injection"></a>Başlangıç Oluşturucu Ekleme değişiklikleri

Genel konak yalnızca Oluşturucu ekleme için aşağıdaki türleri destekler `Startup` :

* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

Tüm hizmetler yine de yönteme bağımsız değişken olarak eklenebilir `Startup.Configure` . Daha fazla bilgi için bkz. [genel ana bilgisayar başlangıç Oluşturucu Ekleme (ASPNET/duyurular #353)](https://github.com/aspnet/Announcements/issues/353).

## <a name="kestrel"></a>Kestrel

* Kestrel yapılandırması, genel konağa geçiş için güncelleştirildi. 3,0 ' de, Kestrel tarafından sağlanmış Web ana bilgisayar Oluşturucu üzerinde yapılandırılır `ConfigureWebHostDefaults` .
* Bağlantı bağdaştırıcıları, Kestrel adresinden kaldırılmıştır ve bağlantı ara yazılımı ile değiştirilmiştir ve bu, ASP.NET Core işlem hattındaki HTTP ara hattına benzer ancak alt düzey bağlantılar için kullanılır.
* Kestrel aktarım katmanı, içinde ortak bir arabirim olarak kullanıma sunuldu `Connections.Abstractions` .
* Sondaki üstbilgiler yeni bir koleksiyona taşınarak üstbilgiler ve tanıtımları arasındaki belirsizlik çözüldü.
* Gibi zaman uyumlu g/ç API 'Leri `HttpRequest.Body.Read` , uygulama kilitlenmelerine neden olan yaygın bir iş parçacığı kaynağıdır. 3,0 ' de `AllowSynchronousIO` Varsayılan olarak devre dışıdır.

Daha fazla bilgi için bkz. <xref:migration/22-to-30#kestrel>.

## <a name="http2-enabled-by-default"></a>Varsayılan olarak etkin HTTP/2

HTTP/2, HTTPS uç noktaları için Kestrel içinde varsayılan olarak etkindir. IIS veya HTTP.sys için HTTP/2 desteği, işletim sistemi tarafından desteklendikleri zaman etkindir.

## <a name="eventcounters-on-request"></a>İstek üzerine EventCounters

EventSource barındırma, `Microsoft.AspNetCore.Hosting` <xref:System.Diagnostics.Tracing.EventCounter> gelen isteklerle ilgili aşağıdaki yeni türleri yayar:

* `requests-per-second`
* `total-requests`
* `current-requests`
* `failed-requests`

## <a name="endpoint-routing"></a>Uç nokta yönlendirme

Çerçeveler 'in (örneğin, MVC) ara yazılım ile iyi çalışmasına izin veren uç nokta yönlendirme geliştirildi:

* Ara yazılım ve uç noktaların sırası istek işleme ardışık düzeninde yapılandırılabilir `Startup.Configure` .
* Uç noktalar ve ara yazılımlar, sistem durumu denetimleri gibi diğer ASP.NET Core tabanlı teknolojilerle birlikte.
* Uç noktalar, hem yazılım hem de MVC 'de CORS veya yetkilendirme gibi bir ilke uygulayabilir.
* Filtreler ve öznitelikler, denetleyicilerde yöntemler üzerine yerleştirilebilir.

Daha fazla bilgi için bkz. <xref:fundamentals/routing#routing-basics>.

## <a name="health-checks"></a>Sistem durumu denetimleri

Sistem durumu denetimleri, genel ana bilgisayar ile Endpoint Routing kullanır. ' De, uç nokta `Startup.Configure` `MapHealthChecks` URL 'si veya göreli yol ile Endpoint Builder ' ı çağırın:

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
});
```

Durum denetimleri uç noktaları şunları yapabilir:

* İzin verilen bir veya daha fazla Konakları/bağlantı noktasını belirtin.
* Yetkilendirme gerektir.
* CORS gerektir.

Daha fazla bilgi için aşağıdaki makalelere bakın:

* <xref:migration/22-to-30#health-checks>
* <xref:host-and-deploy/health-checks>

## <a name="pipes-on-httpcontext"></a>HttpContext üzerindeki kanallar

Artık istek gövdesini okumak ve yanıt gövdesini API kullanarak yazmak mümkündür <xref:System.IO.Pipelines> . Bu <!-- <xref:Microsoft.AspNetCore.Http.HttpRequest.BodyReader> --> `HttpRequest.BodyReader` özelliği <xref:System.IO.Pipelines.PipeReader> , istek gövdesini okumak için kullanılabilecek bir sağlar. Bu <!-- <xref:Microsoft.AspNetCore.Http.> --> `HttpResponse.BodyWriter` özelliği <xref:System.IO.Pipelines.PipeWriter> , yanıt gövdesini yazmak için kullanılabilecek bir sağlar. `HttpRequest.BodyReader` akışın analog bir `HttpRequest.Body` kadır. `HttpResponse.BodyWriter` akışın analog bir `HttpResponse.Body` kadır.

<!-- indirectly related, https://github.com/dotnet/docs/pull/14414 won't be published by 9/23  -->

## <a name="improved-error-reporting-in-iis"></a>IIS 'de geliştirilmiş hata raporlama

IIS 'de ASP.NET Core uygulamalar barındırırken başlatma hataları artık daha zengin Tanılama verileri oluşturuyor. Bu hatalar, geçerli her yerde yığın izlemelerle Windows olay günlüğü 'ne bildirilir. Ayrıca, tüm uyarılar, hatalar ve işlenmemiş özel durumlar Windows olay günlüğü 'ne kaydedilir.

## <a name="worker-service-and-worker-sdk"></a>Çalışan hizmeti ve çalışan SDK 'Sı

.NET Core 3,0 yeni çalışan hizmeti uygulama şablonunu tanıtır. Bu şablon, .NET Core 'da uzun süre çalışan hizmetler yazmak için bir başlangıç noktası sağlar.

Daha fazla bilgi için bkz.

* [Windows Hizmetleri olarak .NET Core çalışanları](https://devblogs.microsoft.com/aspnet/net-core-workers-as-windows-services/)
* <xref:fundamentals/host/hosted-services>
* <xref:host-and-deploy/windows-service>

## <a name="forwarded-headers-middleware-improvements"></a>İletilen üstbilgiler ara yazılımı geliştirmeleri

Önceki ASP.NET Core sürümlerinde, <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*>  <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> bir Azure Linux 'a DAĞıTıLıRKEN veya IIS dışında herhangi bir ters proxy 'nin arkasında sorun. Önceki sürümlere yönelik düzeltmeler, [Linux ve IIS olmayan ters proxy 'lerin düzenini iletme](xref:host-and-deploy/proxy-load-balancer#forward-the-scheme-for-linux-and-non-iis-reverse-proxies)bölümünde belgelenmiştir.

Bu senaryo ASP.NET Core 3,0 ' de düzeltilmiştir. Ortam değişkeni olarak ayarlandığında, ana bilgisayar [Iletilen üstbilgiler ara yazılımını](xref:host-and-deploy/proxy-load-balancer#forwarded-headers-middleware-options) sağlar `ASPNETCORE_FORWARDEDHEADERS_ENABLED` `true` . `ASPNETCORE_FORWARDEDHEADERS_ENABLED` , `true` kapsayıcı görüntülerimizde olarak ayarlanır.

## <a name="performance-improvements"></a>Performans iyileştirmeleri

ASP.NET Core 3,0, bellek kullanımını azaltan ve üretilen işi geliştiren birçok geliştirme içerir:

* Kapsamlı hizmetler için yerleşik bağımlılık ekleme kapsayıcısını kullanırken bellek kullanımında azaltma.
* Ara yazılım senaryoları ve yönlendirme dahil olmak üzere çerçeve genelinde ayırmalarda azaltma.
* WebSocket bağlantıları için bellek kullanımında azaltma.
* HTTPS bağlantıları için bellek azaltma ve verimlilik geliştirmeleri.
* Yeni iyileştirilmiş ve tamamen zaman uyumsuz JSON serileştiricisi.
* Form ayrıştırılırken bellek kullanımı ve üretilen iş iyileştirmeleri azaltılması.

## <a name="aspnet-core-30-only-runs-on-net-core-30"></a>ASP.NET Core 3,0 yalnızca .NET Core 3,0 üzerinde çalışır

ASP.NET Core 3,0 itibariyle, .NET Framework artık desteklenen bir hedef çerçeve değildir. .NET Framework hedefleyen projeler [.NET Core 2,1 LTS sürümünü](https://dotnet.microsoft.com/download/dotnet-core/2.1)kullanarak tam olarak desteklenen bir biçimde devam edebilir. ASP.NET Core 2.1. x ile ilgili paketlerin çoğu, .NET Core 2,1 için üç yıllık LTS döneminin ötesinde süresiz olarak desteklenecektir.

Geçiş bilgileri için bkz. [kodunuzu .NET Core 'a .NET Framework](/dotnet/core/porting/).

## <a name="use-the-aspnet-core-shared-framework"></a>ASP.NET Core paylaşılan çerçevesini kullanma

[Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app)içinde bulunan ASP.NET Core 3,0 paylaşılan çerçevesi artık `<PackageReference />` Proje dosyasında açık bir öğe gerektirmez. Proje dosyasında SDK kullanılırken paylaşılan çerçeveye otomatik olarak başvurulur `Microsoft.NET.Sdk.Web` :

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

## <a name="assemblies-removed-from-the-aspnet-core-shared-framework"></a>ASP.NET Core paylaşılan çerçevesinden kaldırılan derlemeler

ASP.NET Core 3,0 paylaşılan çerçevesinden çıkarılan en önemli derlemeler şunlardır:

* [Newtonsoft.Js](https://www.nuget.org/packages/Newtonsoft.Json/) (JSON.net). ASP.NET Core 3,0 ' ye Json.NET eklemek için bkz. [Newtonsoft.Jstabanlı JSON biçimi desteği ekleme](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support). ASP.NET Core 3,0 `System.Text.Json` , JSON okuma ve yazma için tanıtılmıştır. Daha fazla bilgi için bu belgede [yenı JSON serileştirmesi](#new-json-serialization) bölümüne bakın.
* [Entity Framework Core](/ef/core/)

Paylaşılan çerçeveden kaldırılan derlemelerin tamamen listesi için bkz [. Microsoft. AspNetCore. App 3,0 ' den kaldırılan derlemeler](https://github.com/dotnet/AspNetCore/issues/3755). Bu değişiklik için mosyon hakkında daha fazla bilgi için bkz. [3,0 'de Microsoft. AspNetCore. app 'e yönelik son değişiklikler](https://github.com/aspnet/Announcements/issues/325) ve [ASP.NET Core 3,0 ' de gelen değişikliklere ilk bakış](https://devblogs.microsoft.com/aspnet/a-first-look-at-changes-coming-in-asp-net-core-3-0/).

<!-- 
## Additional information
For the complete list of changes, see the [ASP.NET Core 3.0 Release Notes](WHERE IS THIS????).
-->
