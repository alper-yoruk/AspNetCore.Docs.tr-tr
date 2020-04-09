---
title: Core Blazor Server uygulamalarını güvenli ASP.NET
author: guardrex
description: Sunucu uygulamalarına yönelik Blazor güvenlik tehditlerini nasıl azaltılamayı öğrenin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/02/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/server
ms.openlocfilehash: bd03f811d0425fdfdb7bbbc24fea5481b49b8ed3
ms.sourcegitcommit: 9675db7bf4b67ae269f9226b6f6f439b5cce4603
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80626017"
---
# <a name="secure-aspnet-core-blazor-server-apps"></a>Core Blazor Server uygulamaları ASP.NET güvenli

Yazar: [Javier Calvarro Nelson](https://github.com/javiercn)

Blazor Server uygulamaları, sunucu ve istemcinin uzun süreli bir ilişki sürdürdüğü *durumlu* bir veri işleme modelini benimser. Kalıcı durum, potansiyel olarak uzun ömürlü olan bağlantıları kaplayabilen bir [devre](xref:blazor/state-management)tarafından korunur.

Bir kullanıcı Blazor Server sitesini ziyaret ettiğinde, sunucu sunucunun belleğinde bir devre oluşturur. Devre, kullanıcının Kullanıcı Aracı'nda bir düğme seçmesi gibi olaylara hangi içeriğin işleneceğini ve bunlara tepki verir. Bu eylemleri gerçekleştirmek için bir devre, kullanıcının tarayıcısında javascript işlevlerini ve sunucudaki .NET yöntemlerini çağırır. Bu iki yönlü JavaScript tabanlı etkileşim [JavaScript interop (JS interop)](xref:blazor/call-javascript-from-dotnet)olarak adlandırılır.

JS interop Internet üzerinden oluşur ve istemci uzak bir tarayıcı kullandığından, Blazor Server uygulamaları çoğu web uygulaması güvenlik endişelerini paylaşır. Bu konu, Blazor Server uygulamalarına yönelik yaygın tehditleri açıklar ve Internet'e dönük uygulamalara odaklanmış tehdit azaltma kılavuzu sağlar.

Şirket ağları veya intranetler gibi kısıtlı ortamlarda, bazı azaltma kılavuzu aşağıdakilerden biri:

* Kısıtlı ortamda geçerli değildir.
* Güvenlik riski kısıtlı bir ortamda düşük olduğundan, uygulanması gereken maliyete değmez.

## <a name="blazor-server-project-template"></a>Blazor Server proje şablonu

Blazor Server proje şablonu, proje oluşturulduğunda kimlik doğrulaması için yapılandırılabilir.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Kimlik doğrulama mekanizmasına <xref:blazor/get-started> sahip yeni bir Blazor Server projesi oluşturmak için makaledeki Visual Studio kılavuzunu izleyin.

**Yeni bir ASP.NET Çekirdek Web Uygulaması Oluştur** iletişim kutusunda **Blazor Server App** şablonunu seçtikten sonra Kimlik **Doğrulama**altında **Değiştir'i** seçin.

Diğer ASP.NET Core projeleri için kullanılabilir aynı kimlik doğrulama mekanizmaları kümesini sunmak için bir iletişim kutusu açılır:

* **Kimlik Doğrulaması Yok**
* **Bireysel Kullanıcı Hesapları** &ndash; Kullanıcı hesapları saklanabilir:
  * ASP.NET Core'un [Kimlik](xref:security/authentication/identity) sistemini kullanarak uygulama içinde.
  * [Azure AD B2C](xref:security/authentication/azure-ad-b2c)ile .
* **İş veya Okul Hesapları**
* **Windows Kimlik Doğrulama**

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Kimlik doğrulama mekanizmasına sahip <xref:blazor/get-started> yeni bir Blazor Server projesi oluşturmak için makaledeki Visual Studio Code kılavuzunu izleyin:

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

İzin verilen kimlik doğrulama`{AUTHENTICATION}`değerleri ( ) aşağıdaki tabloda gösterilmiştir.

| Kimlik doğrulama mekanizması                                                                 | `{AUTHENTICATION}`Değer |
| ---------------------------------------------------------------------------------------- | :----------------------: |
| Kimlik Doğrulaması Yok                                                                        | `None`                   |
| Bireysel<br>Uygulamada ASP.NET Çekirdek Kimliği ile saklanan kullanıcılar.                        | `Individual`             |
| Bireysel<br>Azure AD [B2C'de](xref:security/authentication/azure-ad-b2c)depolanan kullanıcılar. | `IndividualB2C`          |
| İş veya Okul Hesapları<br>Tek bir kiracı için kuruluş kimlik doğrulaması.            | `SingleOrg`              |
| İş veya Okul Hesapları<br>Birden çok kiracı için kuruluş kimlik doğrulaması.           | `MultiOrg`               |
| Windows Kimlik Doğrulaması                                                                   | `Windows`                |

Komut, `{APP NAME}` yer tutucuiçin sağlanan değeri içeren bir klasör oluşturur ve klasör adını uygulamanın adı olarak kullanır. Daha fazla bilgi için .NET Core Guide'daki [dotnet yeni](/dotnet/core/tools/dotnet-new) komutuna bakın.

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

1. Makaledeki Mac kılavuzu için <xref:blazor/get-started> Visual Studio'yu izleyin.

1. Yeni **Blazor Server App adımınızı Yapılandır'da,** **Kimlik Doğrulama** açılır tarihinden Bireysel **Kimlik Doğrulama'yı (uygulama içi)** seçin.

1. Uygulama, ASP.NET Çekirdek Kimliği ile uygulamada saklanan bireysel kullanıcılar için oluşturulur.

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

Kimlik doğrulama mekanizmasına sahip yeni <xref:blazor/get-started> bir Blazor Server projesi oluşturmak için makaledeki .NET Core CLI kılavuzunu izleyin:

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

İzin verilen kimlik doğrulama`{AUTHENTICATION}`değerleri ( ) aşağıdaki tabloda gösterilmiştir.

| Kimlik doğrulama mekanizması                                                                 | `{AUTHENTICATION}`Değer |
| ---------------------------------------------------------------------------------------- | :----------------------: |
| Kimlik Doğrulaması Yok                                                                        | `None`                   |
| Bireysel<br>Uygulamada ASP.NET Çekirdek Kimliği ile saklanan kullanıcılar.                        | `Individual`             |
| Bireysel<br>Azure AD [B2C'de](xref:security/authentication/azure-ad-b2c)depolanan kullanıcılar. | `IndividualB2C`          |
| İş veya Okul Hesapları<br>Tek bir kiracı için kuruluş kimlik doğrulaması.            | `SingleOrg`              |
| İş veya Okul Hesapları<br>Birden çok kiracı için kuruluş kimlik doğrulaması.           | `MultiOrg`               |
| Windows Kimlik Doğrulaması                                                                   | `Windows`                |

Komut, `{APP NAME}` yer tutucuiçin sağlanan değeri içeren bir klasör oluşturur ve klasör adını uygulamanın adı olarak kullanır. Daha fazla bilgi için .NET Core Guide'daki [dotnet yeni](/dotnet/core/tools/dotnet-new) komutuna bakın.

---

## <a name="pass-tokens-to-a-blazor-server-app"></a>Jetonları Blazor Server uygulamasına aktarın

Blazor Server uygulamasını normal bir Razor Pages veya MVC uygulamasıyla doğrulayabilirsiniz. Belirteçleri kimlik doğrulama çerezine sağlama ve kaydetme. Örneğin:

```csharp
using Microsoft.AspNetCore.Authentication.OpenIdConnect;

...

services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
{
    options.ResponseType = "code";
    options.SaveTokens = true;

    options.Scope.Add("offline_access");
    options.Scope.Add("{SCOPE}");
    options.Resource = "{RESOURCE}";
});
```

Tam `Startup.ConfigureServices` bir örnek de dahil olmak üzere örnek kod [için, sunucu tarafındaki Blazor uygulamasına geçen belirteçlere](https://github.com/javiercn/blazor-server-aad-sample)bakın.

Başlangıç uygulama durumunda erişim ve yenileme belirteçleriyle geçmek için bir sınıf tanımlayın:

```csharp
public class InitialApplicationState
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

DI'deki belirteçleri çözmek için Blazor uygulaması içinde kullanılabilecek **kapsamlı** bir belirteç sağlayıcısı hizmeti tanımlayın:

```csharp
using System;
using System.Security.Claims;
using System.Threading.Tasks;

public class TokenProvider
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

In `Startup.ConfigureServices`, için hizmet ekle:

* `IHttpClientFactory`
* `TokenProvider`

```csharp
services.AddHttpClient();
services.AddScoped<TokenProvider>();
```

*_Host.cshtml* dosyasında, uygulamanın `InitialApplicationState` parametresi olarak oluşturun ve örnek lendirin ve aktarın:

```cshtml
@using Microsoft.AspNetCore.Authentication

...

@{
    var tokens = new InitialApplicationState
    {
        AccessToken = await HttpContext.GetTokenAsync("access_token"),
        RefreshToken = await HttpContext.GetTokenAsync("refresh_token")
    };
}

<app>
    <component type="typeof(App)" param-InitialState="tokens" 
        render-mode="ServerPrerendered" />
</app>
```

`App` Bileşende *(App.razor),* hizmeti çözün ve parametredeki verilerle başharfe alet edin:

```razor
@inject TokenProvider TokensProvider

...

@code {
    [Parameter]
    public InitialApplicationState InitialState { get; set; }

    protected override Task OnInitializedAsync()
    {
        TokensProvider.AccessToken = InitialState.AccessToken;
        TokensProvider.RefreshToken = InitialState.RefreshToken;

        return base.OnInitializedAsync();
    }
}
```

Güvenli bir API isteği nde bulunan hizmette, belirteç sağlayıcısına enjekte edin ve API'yi aramak için belirteci alın:

```csharp
public class WeatherForecastService
{
    private readonly TokenProvider _store;

    public WeatherForecastService(IHttpClientFactory clientFactory, 
        TokenProvider tokenProvider)
    {
        Client = clientFactory.CreateClient();
        _store = tokenProvider;
    }

    public HttpClient Client { get; }

    public async Task<WeatherForecast[]> GetForecastAsync(DateTime startDate)
    {
        var token = _store.AccessToken;
        var request = new HttpRequestMessage(HttpMethod.Get, 
            "https://localhost:5003/WeatherForecast");
        request.Headers.Add("Authorization", $"Bearer {token}");
        var response = await Client.SendAsync(request);
        response.EnsureSuccessStatusCode();

        return await response.Content.ReadAsAsync<WeatherForecast[]>();
    }
}
```

## <a name="resource-exhaustion"></a>Kaynak tükenmesi

Bir istemci sunucuyla etkileşimde ve sunucunun aşırı kaynak tüketmesine neden olduğunda kaynak tükenmesi oluşabilir. Aşırı kaynak tüketimi öncelikle etkiler:

* [CPU](#cpu)
* [Bellek](#memory)
* [İstemci bağlantıları](#client-connections)

Hizmet reddi (DoS) saldırıları genellikle bir uygulamanın veya sunucunun kaynaklarını tüketmeye yöneliktir. Ancak, kaynak tükenmesi mutlaka sisteme yapılan bir saldırının sonucu değildir. Örneğin, sonlu kaynaklar yüksek kullanıcı talebi nedeniyle tükenebilir. DoS, [Hizmet Reddi (DoS) saldırıları](#denial-of-service-dos-attacks) bölümünde daha fazla yer almaktadır.

Veritabanları ve dosya tutamaçları (dosyaları okumak ve yazmak için kullanılır) gibi Blazor çerçevesi dışındaki kaynaklar da kaynak tükenmesi yaşayabilir. Daha fazla bilgi için bkz. <xref:performance/performance-best-practices>.

### <a name="cpu"></a>CPU

CPU tükenmesi, bir veya daha fazla istemci sunucuyu yoğun CPU çalışması yapmaya zorladığında oluşabilir.

Örneğin, Bir *Fibonnacci numarası*hesaplayan bir Blazor Server uygulaması düşünün. Fibonnacci numarası, sıradaki her sayının önceki iki sayının toplamı olduğu bir Fibonnacci dizisinden üretilir. Yanıta ulaşmak için gereken çalışma miktarı dizinin uzunluğuna ve başlangıç değerinin boyutuna bağlıdır. Uygulama bir istemcinin isteğine sınır koymazsa, CPU yoğun hesaplamalar CPU'nun zamanına hakim olabilir ve diğer görevlerin performansını azaltabilir. Aşırı kaynak tüketimi kullanılabilirliği etkileyen bir güvenlik sorunudur.

CPU tükenmesi, genel kullanıma açık tüm uygulamalar için bir endişe kaynağıdır. Normal web uygulamalarında, istekler ve bağlantılar güvenlik önlemi olarak zaman alar, ancak Blazor Server uygulamaları aynı güvenlik önlemlerini sağlamaz. Blazor Server uygulamaları, CPU yoğun olabilecek bir çalışma gerçekleştirmeden önce uygun denetimleri ve sınırları içermelidir.

### <a name="memory"></a>Bellek

Bir veya daha fazla istemci sunucuyu büyük miktarda bellek tüketmeye zorladığında bellek tükenmesi oluşabilir.

Örneğin, öğelerin listesini kabul eden ve görüntüleyen bir bileşene sahip blazor sunucusu yan uygulamasını düşünün. Blazor uygulaması, izin verilen öğe sayısına veya istemciye geri işlenen öğe sayısına sınırlama lar koymuyorsa, bellek yoğun işlem ve işleme sunucunun performansının zarar gördüğü noktaya kadar sunucunun belleğinde hakim olabilir. Sunucu çökebilir veya çöktüğı gibi görünen noktaya kadar yavaşlayabilir.

Sunucuda olası bir bellek tükenmesenaryosuyla ilgili öğelerin listesini korumak ve görüntülemek için aşağıdaki senaryoyu göz önünde bulundurun:

* Bir `List<MyItem>` özellik veya alandaki öğeler sunucunun belleği kullanır. Uygulama, öğelerin listesinin sınırsız büyümesine izin veriyorsa, sunucunun belleği tükenebilir. Belleği bitmek üzere olan bu oturum, geçerli oturumun sona erdirilmesine (kilitlenme) neden olur ve bu sunucu örneğindeki eşzamanlı oturumların tümü bellek dışı bir özel durum alır. Bu senaryonun oluşmasını önlemek için, uygulamanın eşzamanlı kullanıcılara madde sınırı getiren bir veri yapısı kullanması gerekir.
* Görüntüleme için bir sayfalama düzeni kullanılmazsa, sunucu UI'de görünmeyen nesneler için ek bellek kullanır. Öğe sayısında bir sınırlama olmadan, bellek talepleri kullanılabilir sunucu belleği egzoz olabilir. Bu senaryoyu önlemek için aşağıdaki yaklaşımlardan birini kullanın:
  * İşleme yaparken paginated listeleri kullanın.
  * Yalnızca ilk 100 ila 1.000 öğeyi görüntüleyin ve görüntülenen öğelerin ötesinde öğeleri bulmak için kullanıcının arama ölçütlerini girmesini gerektirir.
  * Daha gelişmiş bir işleme senaryosu için, sanallaştırmayı destekleyen listeler veya ızgaralar *uygulayın.* Sanallaştırma yı kullanarak, listeler yalnızca kullanıcı tarafından görülebilen öğelerin bir alt kümesini işler. Kullanıcı Kullanıcı Arabirimi'ndeki kaydırma çubuğuyla etkileşimde olduğunda, bileşen yalnızca görüntülenmek için gereken öğeleri işler. Şu anda görüntülenmek için gerekli olmayan öğeler, ideal bir yaklaşım olan ikincil depolama da tutulabilir. Görüntülenmeyen öğeler de daha az ideal bellekte tutulabilir.

Blazor Server uygulamaları, WPF, Windows Forms veya Blazor WebAssembly gibi özel uygulamalar için diğer web hizmeti arabirimi çerçevelerine benzer bir programlama modeli sunar. Temel fark, ui çerçeveleri birkaç uygulama tarafından tüketilen bellek istemciye ait ve sadece bu bireysel istemci etkiler olmasıdır. Örneğin, blazor WebAssembly uygulaması tamamen istemciüzerinde çalışır ve yalnızca istemci bellek kaynaklarını kullanır. Blazor Server senaryosunda, uygulama tarafından tüketilen bellek sunucuya aittir ve sunucu örneğindeki istemciler arasında paylaşılır.

Sunucu tarafı bellek talepleri tüm Blazor Server uygulamaları için göz önünde bulundurulmalıdır. Ancak, web uygulamalarının çoğu durum dilemezdir ve yanıt döndürüldüğünde isteği işlerken kullanılan bellek serbest bırakılır. Genel bir öneri olarak, istemci bağlantılarını devam eden diğer sunucu tarafındaki uygulamalarda olduğu gibi, istemcilerin sınırsız miktarda bellek ayırmasına izin vermez. Blazor Server uygulaması tarafından tüketilen bellek, tek bir istekten daha uzun süre kalır.

> [!NOTE]
> Geliştirme sırasında, bir profil oluşturucu kullanılabilir veya istemcilerin bellek taleplerini değerlendirmek için bir izleme yakalanır. Bir profil oluşturucu veya izleme, belirli bir istemciye ayrılan belleği yakalamaz. Geliştirme sırasında belirli bir istemcinin bellek kullanımını yakalamak için, bir dökümü yakalayın ve kullanıcının devresine kök salan tüm nesnelerin bellek talebini inceleyin.

### <a name="client-connections"></a>İstemci bağlantıları

Bir veya daha fazla istemci sunucuya çok fazla eşzamanlı bağlantı açtığında bağlantı tükenmesi oluşabilir ve diğer istemcilerin yeni bağlantılar kurmasını engelleyebilir.

Blazor istemcileri oturum başına tek bir bağlantı kurar ve tarayıcı penceresi açık olduğu sürece bağlantıyı açık tutar. Sunucudaki tüm bağlantıların bakımı yla ilgili talepler Blazor uygulamalarına özgü değildir. Bağlantıların kalıcı yapısı ve Blazor Server uygulamalarının durum daki doğası göz önüne alındığında, bağlantı yorgunluğu uygulamanın kullanılabilirliği açısından daha büyük bir risktir.

Varsayılan olarak, blazor server uygulaması için kullanıcı başına bağlantı sayısında bir sınır yoktur. Uygulama bir bağlantı sınırı gerektiriyorsa, aşağıdaki yaklaşımlardan birini veya birkaçını alın:

* Yetkisiz kullanıcıların uygulamaya bağlanma yeteneğini doğal olarak sınırlayan kimlik doğrulaması gerektirir. Bu senaryonun etkili olabilmesi için, kullanıcıların yeni kullanıcılar alabilmeli şekilde sağlaması engellenmelidir.
* Kullanıcı başına bağlantı sayısını sınırlayın. Bağlantıları sınırlandırma aşağıdaki yaklaşımlarla gerçekleştirilebilir. Yasal kullanıcıların uygulamaya erişmesine izin vermek için dikkatli olun (örneğin, istemcinin IP adresine göre bir bağlantı sınırı oluşturulduğunda).
  * Uygulama düzeyinde:
    * Endpoint yönlendirme genişletilebilirlik.
    * Uygulamaya bağlanmak ve kullanıcı başına etkin oturumları izlemek için kimlik doğrulaması gerektirir.
    * Bir sınıra ulaştıktan sonra yeni oturumları reddedin.
    * Proxy WebSocket bağlantıları, istemcilerden bir uygulamaya bağlantıları birden fazlaya çarpan [Azure SinyalR Hizmeti](/azure/azure-signalr/signalr-overview) gibi bir proxy aracılığıyla bir uygulamaya bağlanır. Bu, tek bir istemcinin kurabileceğinden daha fazla bağlantı kapasitesine sahip bir uygulama sağlayarak istemcinin sunucuya olan bağlantıları tüketmesini önler.
  * Sunucu düzeyinde: Uygulamanın önünde bir proxy/ağ geçidi kullanın. Örneğin, [Azure Ön Kapı,](/azure/frontdoor/front-door-overview) web trafiğinin bir uygulamaya genel yönlendirmesini tanımlamanızı, yönetmenize ve izlemenize olanak tanır.

## <a name="denial-of-service-dos-attacks"></a>Hizmet reddi (DoS) saldırıları

Hizmet reddi (DoS) saldırıları, sunucunun bir veya daha fazla kaynağının tüketmesine ve uygulamanın kullanılamamasına neden olmasını içerir. Blazor Server uygulamaları bazı varsayılan sınırlar içerir ve DoS saldırılarına karşı korunmak için diğer ASP.NET Core ve SignalR sınırlarına güvenir:

| Blazor Server uygulama sınırı                            | Açıklama | Varsayılan |
| ------------------------------------------------------- | ----------- | ------- |
| `CircuitOptions.DisconnectedCircuitMaxRetained`         | Belirli bir sunucunun bir anda bellekte tuttuğu maksimum bağlantısız devre sayısı. | 100 |
| `CircuitOptions.DisconnectedCircuitRetentionPeriod`     | Bağlantısı kesilen bir devre nin parçalanmadan önce bellekte tutulduğu maksimum süre. | 3 dakika |
| `CircuitOptions.JSInteropDefaultCallTimeout`            | Sunucunun, eşzamanlı bir JavaScript işlev çağırması zamanlamasını zamanlamadan önce beklediği maksimum süre. | 1 dakika |
| `CircuitOptions.MaxBufferedUnacknowledgedRenderBatches` | Sağlam yeniden bağlantıyı desteklemek için sunucunun belirli bir zamanda devre başına bellekte tuttuğu maksimum onaylanmamış render toplu iş sayısı. Sınıra ulaştıktan sonra, sunucu istemci tarafından bir veya daha fazla toplu iş kabul edilene kadar yeni işişleme toplu oluşturmayı durdurur. | 10 |


| SignalR ve ASP.NET Çekirdek sınırı             | Açıklama | Varsayılan |
| ------------------------------------------ | ----------- | ------- |
| `CircuitOptions.MaximumReceiveMessageSize` | Tek bir ileti için ileti boyutu. | 32 KB |

## <a name="interactions-with-the-browser-client"></a>Tarayıcı ile etkileşimler (istemci)

İstemci, JS interop olay gönderme ve tamamlama render aracılığıyla sunucu ile etkileşim. JS interop iletişim JavaScript ve .NET arasında her iki yönde gider:

* Tarayıcı olayları istemciden sunucuya eşzamanlı bir şekilde gönderilir.
* Sunucu, kullanıcı ayını gerektiği gibi eşzamanlı olarak yeniden işlemeye yanıt verir.

### <a name="javascript-functions-invoked-from-net"></a>.NET'ten çağrılan JavaScript işlevleri

.NET yöntemlerinden JavaScript'e yapılan aramalar için:

* Tüm çağrıların, bir arayanın döndürülmesi, <xref:System.OperationCanceledException> başarısız oldukları yapılandırılabilir bir zaman acısı vardır.
  * Aramalar için varsayılan bir zaman ası vardır (`CircuitOptions.JSInteropDefaultCallTimeout`) bir dakika. Bu sınırı yapılandırmak <xref:blazor/call-javascript-from-dotnet#harden-js-interop-calls>için bkz.
  * İptal iamasını her çağrı başına kontrol etmek için bir iptal belirteci sağlanabilir. İptal belirteci sağlandığı takdirde, istemciye yapılan olası ve zamana bağlı herhangi bir çağrının mümkün olduğu durumlarda varsayılan arama zaman ayarı'na güvenin.
* JavaScript aramasının sonucuna güvenilemez. Tarayıcıda çalışan Blazor uygulama istemcisi, javascript işlevini çağırmak için arar. İşlev çağrılır ve sonuç veya hata üretilir. Kötü amaçlı bir istemci şunları yapmaya çalışabilir:
  * JavaScript işlevinden bir hata döndürerek uygulamada bir soruna neden olabilir.
  * JavaScript işlevinden beklenmeyen bir sonuç döndürerek sunucuda istenmeyen bir davranış alıbın.

Önceki senaryolara karşı korunmak için aşağıdaki önlemleri alın:

* Çağrılar sırasında oluşabilecek hataları hesaba katmak için [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) ifadeleri içindeki JS interop çağrılarını sarın. Daha fazla bilgi için bkz. <xref:blazor/handle-errors#javascript-interop>.
* Herhangi bir işlem yapmadan önce hata iletileri de dahil olmak üzere JS interop çağrılarından döndürülen verileri doğrulayın.

### <a name="net-methods-invoked-from-the-browser"></a>.TARAYıCıDAN çağrılan NET yöntemleri

JavaScript'ten .NET yöntemlerine yapılan çağrılara güvenmeyin. Bir .NET yöntemi JavaScript'e maruz kaldığında, .NET yönteminin nasıl çağrıldığını göz önünde bulundurun:

* JavaScript'e maruz kalan herhangi bir .NET yöntemini, uygulamanın genel bitiş noktası olarak ele alın.
  * Girişi doğrulayın.
    * Değerlerin beklenen aralıklarda olduğundan emin olun.
    * Kullanıcının istenen eylemi gerçekleştirmek için izni olduğundan emin olun.
  * .NET yöntemi çağırmasının bir parçası olarak aşırı miktarda kaynak ayırmayın. Örneğin, CPU ve bellek kullanımı için denetimler gerçekleştirin ve sınırlar yerleştirin.
  * Statik ve örnek yöntemlerinin JavaScript istemcilerine maruz kabileceğini göz önünde bulundurun. Tasarım, durumu uygun kısıtlamalarla paylaşmayı gerektiriyorsa, oturumlar arasında durum paylaşımından kaçının.
    * Örneğin, başlangıçta `DotNetReference` bağımlılık enjeksiyonu (DI) yoluyla oluşturulan nesneler aracılığıyla ortaya çıkarılan yöntemler, nesnelerin kapsamlı nesneler olarak kaydedilmesi gerekir. Bu, Sunucu uygulamasının Blazor kullandığı herhangi bir DI hizmeti için geçerlidir.
    * Statik yöntemler için, uygulama bir sunucu örneğindeki tüm kullanıcılar arasında durum by-design açıkça paylaşım olmadığı sürece istemciye kapsamlandırılamaz durum oluşturmaktan kaçının.
  * Kullanıcı tarafından sağlanan verileri parametrelerdeki JavaScript çağrılarına aktarmaktan kaçının. Parametrelerde veri aktarılması kesinlikle gerekiyorsa, JavaScript kodunun siteleri arası [komut dosyası (XSS)](#cross-site-scripting-xss) güvenlik açıklarını tanıtmadan verileri geçirmeyi işlediğinden emin olun. Örneğin, bir öğenin özelliğini `innerHTML` ayarlayarak kullanıcı tarafından sağlanan verileri Belge Nesnesi Modeline (DOM) yazmayın. Devre dışı ve diğer güvenli olmayan JavaScript `eval` ilkellerini devre dışı katmak için [İçerik Güvenlik İlkesi 'ni (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) kullanmayı düşünün.
* Çerçevenin gönderme uygulamasının üstüne .NET çağrılarının özel olarak gönderilmesini uygulamaktan kaçının. .NET yöntemlerinin tarayıcıya teşhir edilmesi, genel Blazor geliştirme için önerilmeyen gelişmiş bir senaryodur.

### <a name="events"></a>Olaylar

Olaylar, sunucu Blazor uygulamasına bir giriş noktası sağlar. Web uygulamalarında uç noktaları korumak için aynı kurallar Blazor Sunucu uygulamalarında olay işleme için de geçerlidir. Kötü niyetli bir istemci, bir olay için yük olarak göndermek istediği verileri gönderebilir.

Örneğin:

* Bir değişiklik `<select>` olayı, uygulamanın istemciye sunduğu seçenekler arasında olmayan bir değer gönderebilir.
* İstemci tarafı doğrulaması atlayarak sunucuya herhangi bir `<input>` metin verisi gönderebilirsiniz.

Uygulama, uygulamanın işlediği herhangi bir olay için verileri doğrulamalıdır. Çerçeve Blazor [formları bileşenleri](xref:blazor/forms-validation) temel doğrulamaları gerçekleştirir. Uygulama özel form bileşenleri kullanıyorsa, olay verilerini uygun şekilde doğrulamak için özel kod yazılmalıdır.

BlazorSunucu olayları eşzamanlıdır, bu nedenle uygulama yeni bir render oluşturarak tepki vermek için zaman bulamadan birden çok olay sunucuya gönderilebilir. Bu göz önünde bulundurulması gereken bazı güvenlik etkileri vardır. Uygulamadaki istemci eylemlerini sınırlama, olay işleyicileri içinde gerçekleştirilmeli ve geçerli işlenen görünüm durumuna bağlı olmamalıdır.

Bir kullanıcının sayaç sayacını en fazla üç kez artmasını sağlayacak bir sayaç bileşeni düşünün. Sayacı nitremi leştirmek için düğme aşağıdakilerin `count`değerine bağlıdır:

```razor
<p>Count: @count<p>

@if (count < 3)
{
    <button @onclick="IncrementCount" value="Increment count" />
}

@code 
{
    private int count = 0;

    private void IncrementCount()
    {
        count++;
    }
}
```

Bir istemci, çerçeve bu bileşenin yeni bir işlemesini oluşturmadan önce bir veya daha fazla artış olayı gönderebilirsiniz. Sonuç, düğme `count` Kullanıcı Arabirimi tarafından yeterince hızlı bir şekilde kaldırılmadığı ndan kullanıcı tarafından *üç kat* daha fazla artımlanolabilir. Üç `count` artış sınırına ulaşmanın doğru yolu aşağıdaki örnekte gösterilmiştir:

```razor
<p>Count: @count<p>

@if (count < 3)
{
    <button @onclick="IncrementCount" value="Increment count" />
}

@code 
{
    private int count = 0;

    private void IncrementCount()
    {
        if (count < 3)
        {
            count++;
        }
    }
}
```

Denetimi `if (count < 3) { ... }` işleyicinin içine ekleyerek, artış `count` kararı geçerli uygulama durumuna bağlıdır. Karar, önceki örnekte olduğu gibi geçici olarak bayat olabilecek Kişisel BirA'nın durumuna dayalı değildir.

### <a name="guard-against-multiple-dispatches"></a>Birden fazla sevkiyata karşı koruma

Bir olay geri araması, harici bir hizmetten veya veritabanından veri alma gibi uzun süren bir işlemi eşit olarak çağırırsa, bir koruma kullanmayı düşünün. Koruma, işlem görsel geri bildirimle devam ederken kullanıcının birden çok işlemi sıraya girmesini engelleyebilir. Aşağıdaki bileşen kodu, `true` `GetForecastAsync` sunucudan veri elde ederken ayarlar. `isLoading` `isLoading` Ise, `true`düğme UI devre dışı bırakılır:

```razor
@page "/fetchdata"
@using BlazorServerSample.Data
@inject WeatherForecastService ForecastService

<button disabled="@isLoading" @onclick="UpdateForecasts">Update</button>

@code {
    private bool isLoading;
    private WeatherForecast[] forecasts;

    private async Task UpdateForecasts()
    {
        if (!isLoading)
        {
            isLoading = true;
            forecasts = await ForecastService.GetForecastAsync(DateTime.Now);
            isLoading = false;
        }
    }
}
```

Önceki örnekte gösterilen koruma deseni, arka plan işlemi `async` - `await` desenle eşzamanlı olarak yürütülürse çalışır.

### <a name="cancel-early-and-avoid-use-after-dispose"></a>Erken iptal edin ve kullanım sonrası elden çıkarmadan kaçının

Birden fazla sevkiyat bölümüne [karşı Guard'da](#guard-against-multiple-dispatches) açıklandığı gibi bir <xref:System.Threading.CancellationToken> koruma kullanmanın yanı sıra, bileşen imha edildiğinde uzun süren işlemleri iptal etmek için bir koruma kullanmayı düşünün. Bu yaklaşım, bileşenlerde *kullanım sonrası elden çıkarmaktan* kaçınmanın ek yararına sahiptir:

```razor
@implements IDisposable

...

@code {
    private readonly CancellationTokenSource TokenSource = 
        new CancellationTokenSource();

    private async Task UpdateForecasts()
    {
        ...

        forecasts = await ForecastService.GetForecastAsync(DateTime.Now, 
            TokenSource.Token);

        if (TokenSource.Token.IsCancellationRequested)
        {
           return;
        }

        ...
    }

    public void Dispose()
    {
        TokenSource.Cancel();
    }
}
```

### <a name="avoid-events-that-produce-large-amounts-of-data"></a>Büyük miktarda veri üreten olaylardan kaçının

Gibi bazı DOM `oninput` olayları, büyük `onscroll`miktarda veri üretebilir. Sunucu uygulamalarında Blazor bu olayları kullanmaktan kaçının.

## <a name="additional-security-guidance"></a>Ek güvenlik kılavuzu

Core uygulamaları ASP.NET güvenliğini sağlama Blazor kılavuzu Server uygulamalarına uygulanır ve aşağıdaki bölümlerde ele alınmıştır:

* [Günlüğe kaydetme ve hassas veriler](#logging-and-sensitive-data)
* [HTTPS ile geçiş sırasındaki bilgileri koruyun](#protect-information-in-transit-with-https)
* [Site arası komut dosyası (XSS)](#cross-site-scripting-xss))
* [Çapraz orijin koruması](#cross-origin-protection)
* [Tıklama kaçırma](#click-jacking)
* [Açık yönlendirmeler](#open-redirects)

### <a name="logging-and-sensitive-data"></a>Günlüğe kaydetme ve hassas veriler

İstemci ve sunucu arasındaki JS interop etkileşimleri sunucunun <xref:Microsoft.Extensions.Logging.ILogger> günlüklerinde örneklerle kaydedilir. Blazorgerçek olaylar veya JS interop giriş ve çıkışları gibi hassas bilgilerin günlüğe kaydedilmesini önler.

Sunucuda bir hata oluştuğunda, çerçeve istemciyi bildirer ve oturumu yıkın. Varsayılan olarak, istemci tarayıcının geliştirici araçlarında görülebilen genel bir hata iletisi alır.

İstemci tarafı hatası çağrı yığınını içermez ve hatanın nedeni hakkında ayrıntı sağlamaz, ancak sunucu günlükleri bu tür bilgiler içerir. Geliştirme amacıyla, hassas hata bilgileri ayrıntılı hataları etkinleştirerek istemcinin kullanımına sunulabilir.

Aşağıdakilerle ayrıntılı hataları etkinleştirme

* `CircuitOptions.DetailedErrors`.
* `DetailedErrors`yapılandırma anahtarı. Örneğin, ortam `ASPNETCORE_DETAILEDERRORS` değişkenini bir `true`değere ayarlayın.

> [!WARNING]
> Hata bilgilerinin Internet'te istemcilere teşhir edilmesi her zaman kaçınılması gereken bir güvenlik riskidir.

### <a name="protect-information-in-transit-with-https"></a>HTTPS ile geçiş sırasındaki bilgileri koruyun

BlazorSunucu SignalR istemci ve sunucu arasındaki iletişim için kullanır. BlazorSunucu normalde SignalR genellikle WebSockets olan anlaşmayapan aktarım kullanır.

BlazorSunucu, sunucu ve istemci arasında gönderilen verilerin bütünlüğünü ve gizliliğini sağlamaz. Her zaman HTTPS kullanın.

### <a name="cross-site-scripting-xss"></a>Site arası komut dosyası (XSS)

Site arası komut dosyası (XSS), yetkisiz bir tarafın tarayıcı bağlamında rasgele mantık yürütmesine olanak tanır. Gizliliği ihlal edilen bir uygulama istemcide rasgele kod çalıştırabilir. Güvenlik açığı, sunucuya karşı bir dizi kötü amaçlı eylem gerçekleştirme potansiyeline sahip olarak kullanılabilir:

* Sunucuya sahte/geçersiz olaylar gönder.
* Gönderme başarısız /geçersiz render tamamlamaları.
* Render tamamlamalarını göndermekten kaçının.
* JavaScript'ten .NET'e interop çağrıları gönder.
* .NET'ten JavaScript'e interop çağrılarının yanıtını değiştirin.
* .NET'i JS interop sonuçlarına göndermekten kaçının.

Blazor Sunucu çerçevesi, önceki tehditlere karşı koruma sağlamak için adımlar atsA:

* İstemci toplu iş oluşturmayı kabul etmiyorsa yeni UI güncelleştirmeleri oluşturmayı durdurur. ' ile `CircuitOptions.MaxBufferedUnacknowledgedRenderBatches`yapılandırılan.
* İstemciden yanıt almadan bir dakika sonra herhangi bir .NET'i JavaScript aramasına zaman sızdırın. ' ile `CircuitOptions.JSInteropDefaultCallTimeout`yapılandırılan.
* JS interop sırasında tarayıcıdan gelen tüm girişlerde temel doğrulama gerçekleştirir:
  * .NET başvuruları geçerlidir ve .NET yöntemi tarafından beklenen türdendir.
  * Veriler yanlış biçimlendirilmiş değil.
  * Yöntem için doğru sayıda bağımsız değişken yükü nde bulunur.
  * Bağımsız değişkenler veya sonuç, yöntemi aramadan önce doğru şekilde deserialed edilebilir.
* Gönderilen olaylardan tarayıcıdan gelen tüm girişlerde temel doğrulama yı gerçekleştirir:
  * Olayın geçerli bir türü vardır.
  * Olay için veriler deserialized olabilir.
  * Olayla ilişkili bir olay işleyicisi vardır.

Çerçevenin uyguladığı güvenlik önlemlerine ek olarak, uygulama nın tehditlere karşı korunmak ve uygun eylemleri uygulamak için geliştirici tarafından kodlanmalıdır:

* Olayları işlerken verileri her zaman doğrulayın.
* Geçersiz veri aldıktan sonra uygun eylemi gerçekleştirin:
  * Verileri yoksayın ve döndürün. Bu, uygulamanın istekleri işlemeye devam etmesini sağlar.
  * Uygulama girişin gayrimeşru olduğunu ve yasal istemci tarafından üretilemediğini belirlerse, bir özel durum atın. Bir istisna atma devre aşağı gözyaşları ve oturumu sona erer.
* Günlüklerde yer alan toplu iş tamamlanmalarını işlemek tarafından sağlanan hata iletisi güvenmeyin. Hata istemci tarafından sağlanır ve istemci tehlikeye olabilir gibi genellikle güvenilir olamaz.
* JavaScript ve .NET yöntemleri arasında her iki yönde de JS interop çağrıları nın girişine güvenmeyin.
* Uygulama, bağımsız değişkenler veya sonuçlar doğru şekilde deserialized olsa bile, bağımsız değişkenler ve sonuçların içeriğigeçerli olduğunu doğrulamak için sorumludur.

Bir XSS açığının mevcut olması için uygulamanın işlenen sayfaya kullanıcı girişi dahil etmesi gerekir. BlazorSunucu bileşenleri, *.jilet* dosyasındaki biçimlendirmenin yordamsal C# mantığına dönüştürüldüğü bir derleme zamanı adımı yürütür. Çalışma zamanında, C# mantığı öğeleri, metni ve alt bileşenleri açıklayan bir *render ağacı* oluşturur. Bu JavaScript yönergeleri bir dizi ile tarayıcının DOM uygulanır (veya önceden işleme durumunda HTML serileştirilmiş):

* Normal Razor sözdizimi (örneğin,) aracılığıyla `@someStringValue`işlenen kullanıcı girişi, yalnızca metin yazabilen komutlarla DOM'a eklenmiştir, çünkü XSS güvenlik açığını ortaya çıkarmaz. Değer HTML biçimlendirmesi içerse bile, değer statik metin olarak görüntülenir. Ön işleme yapılırken, çıktı HTML kodlanır ve bu da içeriği statik metin olarak görüntüler.
* Komut dosyası etiketlerine izin verilmez ve uygulamanın bileşen oluşturma ağacına dahil edilmemelidir. Bir komut dosyası etiketi bileşenin biçimlendirmesinde yer alırsa, derleme zamanı hatası oluşturulur.
* Bileşen yazarları, Razor kullanmadan C# bileşenlerini yazabilirler. Bileşen yazarı çıktı yayırken doğru API'leri kullanmaktan sorumludur. Örneğin, ikincisi `builder.AddContent(0, someUserSuppliedString)` bir XSS güvenlik açığı oluşturabileceğinden, kullanmayın ve *kullanmayın.* `builder.AddMarkupContent(0, someUserSuppliedString)`

XSS saldırılarına karşı korumanın bir parçası olarak, İçerik Güvenlik [Politikası (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP)gibi XSS azaltıcı etkenleri uygulamayı düşünün.

Daha fazla bilgi için bkz. <xref:security/cross-site-scripting>.

### <a name="cross-origin-protection"></a>Çapraz orijin koruması

Çapraz kaynak saldırıları, farklı bir kökenden gelen bir istemcinin sunucuya karşı bir eylem gerçekleştirmesini içerir. Kötü amaçlı eylem genellikle bir GET isteği veya bir form POST (Çapraz Site İstek Sahteciliği, CSRF), ancak kötü amaçlı bir WebSocket açılması da mümkündür. BlazorSunucu [uygulamaları, hub protokolü'nü SignalR kullanan diğer uygulamaların sunduğu garantilerle aynı garantiyi](xref:signalr/security)sunar:

* BlazorSunucu uygulamalarına, bunu önlemek için ek önlemler alınmadığı sürece çapraz orijinerişilebilir. Çapraz orijin erişimini devre dışı kalarak, cors ara yazılımını boru hattına ekleyerek ve `DisableCorsAttribute` Blazor bitiş noktası meta verilerine ekleyerek veya [başlangıçlar SignalR arası kaynak paylaşımı için yapılandırarak](xref:signalr/security#cross-origin-resource-sharing)izin verilen kaynak kümesini sınırlandırarak CORS'i bitiş noktasında devre dışı bırakabilirsiniz.
* CORS etkinse, CORS yapılandırmasına bağlı olarak uygulamayı korumak için ek adımlar gerekebilir. CORS genel olarak etkinse, çağrıdan Blazor `DisableCorsAttribute` `hub.MapBlazorHub()`sonra meta verileri uç nokta meta verilerine ekleyerek Sunucu hub'ı için CORS devre dışı edilebilir.

Daha fazla bilgi için bkz. <xref:security/anti-request-forgery>.

### <a name="click-jacking"></a>Tıklama kaçırma

Tıklama kaçırma, kullanıcıyı saldırı altındaki `<iframe>` sitede eylemler gerçekleştirmesi için kandırmak için bir siteyi farklı bir kaynaktan gelen bir sitenin içinde olarak görüntülemeyi içerir.

Bir uygulamayı bir `<iframe>`, İçerik Güvenlik [İlkesi (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) `X-Frame-Options` ve üstbilginin içinde işlemekten korumak için kullanın. Daha fazla bilgi için [MDN web dokümanlarına bakın: X-Frame-Options.](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options)

### <a name="open-redirects"></a>Açık yönlendirmeler

Sunucu Blazor uygulaması oturumu başladığında, sunucu oturumu başlatmanın bir parçası olarak gönderilen URL'lerin temel doğrulanmasını gerçekleştirir. Çerçeve, devreyi kurmadan önce temel URL'nin geçerli URL'nin bir üst öğesi olup olmadığını denetler. Çerçeve tarafından ek denetimler yapılmaz.

Bir kullanıcı istemcide bir bağlantı seçtiğinde, bağlantının URL'si sunucuya gönderilir ve bu da hangi eylemin gerçekleşip gerçekleşsüreceğini belirler. Örneğin, uygulama istemci tarafında gezinme gerçekleştirebilir veya tarayıcıya yeni konuma gitmesini gösterebilir.

Bileşenler ayrıca, '' kullanımı yoluyla navigasyon `NavigationManager`isteklerini programlı olarak tetikleyebilir. Bu tür senaryolarda, uygulama istemci tarafında gezinme gerçekleştirebilir veya tarayıcıya yeni konuma gitmesini gösterebilir.

Bileşenler şunları yapmalı:

* Gezinti çağrısı bağımsız değişkenlerinin bir parçası olarak kullanıcı girişi kullanmaktan kaçının.
* Hedefin uygulama tarafından izin verildiğinden emin olmak için bağımsız değişkenleri doğrulayın.

Aksi takdirde, kötü amaçlı bir kullanıcı tarayıcıyı saldırgan kontrolündeki bir siteye gitmeye zorlayabilir. Bu senaryoda, saldırgan `NavigationManager.Navigate` yöntemin çağrılması bir parçası olarak bazı kullanıcı girişi kullanarak uygulama hileler.

Bu öneri, uygulamanın bir parçası olarak bağlantıları işlerken de geçerlidir:

* Mümkünse, göreli bağlantılar kullanın.
* Mutlak bağlantı hedeflerinin bir sayfaya dahil etmeden önce geçerli olduğunu doğrulayın.

Daha fazla bilgi için bkz. <xref:security/preventing-open-redirects>.

## <a name="authentication-and-authorization"></a>Kimlik doğrulama ve yetkilendirme

Kimlik doğrulama ve yetkilendirme ile <xref:security/blazor/index>ilgili kılavuz için bkz.

## <a name="security-checklist"></a>Güvenlik denetim listesi

Aşağıdaki güvenlik konuları listesi kapsamlı değildir:

* Olaylardan bağımsız değişkenleri doğrulayın.
* JS interop aramalarından gelen girişleri ve sonuçları doğrulayın.
* .NET to JS interop aramaları için kullanıcı girişini kullanmaktan (veya önceden doğrulamaktan) kaçının.
* İstemcinin bağlanmamış miktarda bellek ayırmasını engelleyin.
  * Bileşen içindeki veriler.
  * `DotNetObject`başvurular istemciye döndürülür.
* Birden fazla sevkiyata karşı korunun.
* Bileşen imha edildiğinde uzun süren işlemleri iptal edin.
* Büyük miktarda veri üreten olaylardan kaçının.
* Url'ler `NavigationManager.Navigate` için kullanıcı girdisini, kaçınılmaz sayılmaması durumunda önce izin verilen bir dizi menşee karşı kullanıcı girişinin bir parçası olarak kullanmaktan kaçının.
* Yetkilendirme kararlarını Kullanıcı G'ın durumuna göre değil, yalnızca bileşen durumundan alın.
* XSS saldırılarına karşı korumak için [İçerik Güvenlik Politikası 'nı (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) kullanmayı düşünün.
* Tıklama kaçırmaya karşı korumak için CSP ve [X-Frame Seçenekleri](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options) kullanmayı düşünün.
* CORS'i etkinleştirirken veya uygulamalar için Blazor CORS'i açıkça devre dışı kalırken CORS ayarlarının uygun olduğundan emin olun.
* Uygulamanın sunucu tarafı sınırlarının kabul Blazor edilemez risk düzeyleri olmadan kabul edilebilir bir kullanıcı deneyimi sağladığından emin olmak için sınayın.
