---
title: Güvenli ASP.NET Blazor Çekirdek WebAssembly
author: guardrex
description: WebAssemlby uygulamalarını Tek Sayfa Uygulamaları (SPA) olarak nasıl güvene Blazor aldığınızı öğrenin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/19/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/index
ms.openlocfilehash: b82341f3d1d0665d4ee31bb6d967ccf305bae9c3
ms.sourcegitcommit: 5547d920f322e5a823575c031529e4755ab119de
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81661791"
---
# <a name="secure-aspnet-core-opno-locblazor-webassembly"></a>Güvenli ASP.NET Blazor Çekirdek WebAssembly

Yazar: [Javier Calvarro Nelson](https://github.com/javiercn)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

BlazorWebAssembly uygulamaları, Tek Sayfa uygulamaları (SPA' lar) ile aynı şekilde güvenli hale alınır. Kullanıcıların SP'lere doğruluğunu doğrulamak için çeşitli yaklaşımlar vardır, ancak en yaygın ve kapsamlı yaklaşım, Açık Kimlik Bağlantısı [(OIDC)](https://openid.net/connect/)gibi [OAuth 2.0 protokolüne](https://oauth.net/)dayalı bir uygulama kullanmaktır.

## <a name="authentication-library"></a>Kimlik doğrulama kitaplığı

BlazorWebAssembly, kitaplık üzerinden OIDC kullanarak uygulamaların `Microsoft.AspNetCore.Components.WebAssembly.Authentication` doğrulanmasını ve yetkilendirilmesini destekler. Kitaplık, ASP.NET Core arka uçlarına karşı sorunsuz bir şekilde doğrulanması için bir dizi ilkel lik sağlar. Kitaplık, ASP.NET Çekirdek Kimlik'i [Identity Server'ın](https://identityserver.io/)üzerine inşa edilmiş API yetkilendirme desteğiyle bütünleştirir. Kitaplık, OpenID Sağlayıcıları (OP) olarak adlandırılan OIDC'yi destekleyen herhangi bir üçüncü taraf Kimlik Sağlayıcısına (IP) karşı kimlik doğrulaması yapabilir.

WebAssembly'deki Blazor kimlik doğrulama desteği, temel kimlik doğrulama iletişim kuralı ayrıntılarını işlemek için kullanılan *oidc-client.js* kitaplığı üzerine oluşturulur.

SameSite tanımlama bilgilerinin kullanımı gibi spa'ların doğrulamasını doğrulamak için başka seçenekler de vardır. Ancak, WebAssembly'in Blazor mühendislik tasarımı, WebAssembly uygulamalarında Blazor kimlik doğrulama için en iyi seçenek olarak OAuth ve OIDC'ye bağlıdır. [JSON Web Belirteçleri 'ne (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) dayalı [belirteç tabanlı kimlik doğrulama,](xref:security/anti-request-forgery#token-based-authentication) işlevsel ve güvenlik nedenleriyle [çerez tabanlı kimlik doğrulaması](xref:security/anti-request-forgery#cookie-based-authentication) üzerinden seçildi:

* Belirteçtabanlı bir protokol kullanmak, belirteçler tüm isteklerde gönderilmeyin diye daha küçük bir saldırı yüzey alanı sunar.
* Belirteçler açıkça gönderildiğinden, sunucu uç noktaları [Siteleri Arası İstek Sahteciliği'ne (CSRF)](xref:security/anti-request-forgery) karşı koruma gerektirmez. Bu, WebAssembly Blazor uygulamalarını MVC veya Razor sayfaları uygulamalarının yanında barındırmanıza olanak tanır.
* Belirteçlerin tanımlama bilgilerinden daha dar izinleri vardır. Örneğin, söz konusu işlev açıkça uygulanmadığı sürece belirteçler kullanıcı hesabını yönetmek veya kullanıcının parolasını değiştirmek için kullanılamaz.
* Belirteçlerin, varsayılan olarak bir saatlik kısa bir kullanım ömrü vardır ve bu da saldırı penceresini sınırlar. Jetonlar da herhangi bir zamanda iptal edilebilir.
* Bağımsız JWT'ler, kimlik doğrulama işlemi yle ilgili istemciye ve sunucuya garanti ler sunar. Örneğin, istemci, aldığı belirteçlerin meşru olduğunu ve belirli bir kimlik doğrulama işleminin bir parçası olarak yayımlandığını algılama ve doğrulama aracına sahiptir. Üçüncü bir taraf kimlik doğrulama işleminin ortasında bir belirteci değiştirmeye çalışırsa, istemci anahtarlanan belirteci algılayabilir ve kullanmaktan kaçınabilir.
* OAuth ve OIDC ile belirteçler, uygulamanın güvenli olduğundan emin olmak için kullanıcı aracısının doğru şekilde davranmasına güvenmez.
* OAuth ve OIDC gibi belirteç tabanlı protokoller, barındırılan ve bağımsız uygulamaların aynı güvenlik özelliklerine sahip doğrulanmasına ve yetkilendirilmesine olanak tanır.

## <a name="authentication-process-with-oidc"></a>OIDC ile kimlik doğrulama işlemi

Kitaplık, `Microsoft.AspNetCore.Components.WebAssembly.Authentication` OIDC kullanarak kimlik doğrulama ve yetkilendirme yi uygulamak için birkaç ilkel uygulama sunar. Genel anlamda, kimlik doğrulama aşağıdaki gibi çalışır:

* Anonim bir kullanıcı giriş düğmesini seçtiğinde veya [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) özniteliği uygulanan bir sayfa istediğinde, kullanıcı uygulamanın giriş`/authentication/login`sayfasına yönlendirilir ( ).
* Giriş sayfasında, kimlik doğrulama kitaplığı yetkilendirme bitiş noktasına yeniden yönlendirmeye hazırlanır. Yetkilendirme bitiş noktası WebAssembly Blazor uygulamasının dışındadır ve ayrı bir kaynaktan barındırılabilir. Bitiş noktası, kullanıcının kimliğinin doğrulanıp doğrulanmadığını belirlemekten ve yanıt olarak bir veya daha fazla belirteç vermekten sorumludur. Kimlik doğrulama kitaplığı, kimlik doğrulama yanıtını almak için bir oturum açma geri araması sağlar.
  * Kullanıcının kimliği doğrulanmıyorsa, kullanıcı genellikle Temel Kimlik ASP.NET olan temel kimlik doğrulama sistemine yönlendirilir.
  * Kullanıcı nın kimliği zaten doğrulanmışsa, yetkilendirme bitiş noktası uygun belirteçleri oluşturur ve tarayıcıyı giriş`/authentication/login-callback`geri arama bitiş noktasına geri yönlendirir ( ).
* Blazor WebAssembly uygulaması oturum açma geri arama bitiş`/authentication/login-callback`noktasını yüklediğinde , kimlik doğrulama yanıtı işlenir.
  * Kimlik doğrulama işlemi başarıyla tamamlanırsa, kullanıcı kimlik doğrulaması yapılır ve isteğe bağlı olarak kullanıcının istediği orijinal korumalı URL'ye geri gönderilir.
  * Kimlik doğrulama işlemi herhangi bir nedenle başarısız olursa, kullanıcı oturum`/authentication/login-failed`açma başarısız sayfasına gönderilir ve bir hata görüntülenir.

## <a name="support-prerendering-with-authentication"></a>Kimlik doğrulama ile ön oluşturmayı destekleyin

Barındırılan Blazor WebAssembly uygulama konularından birinde yönergeleri takip ettikten sonra, aşağıdaki yönergeleri kullanarak aşağıdaki yönergeleri kullanarak aşağıdaki leri uygulayın:

* Yetkilendirme gerektirmeyen ön işleme yolları.
* Yetkilendirmenin gerekli olduğu yolları önceden oluşturmaz.

İstemci uygulamasının `Program` sınıfında *(Program.cs),* ortak hizmet kayıtlarını ayrı bir `ConfigureCommonServices`yönteme (örneğin,) faktör:

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.RootComponents.Add<App>("app");

        builder.Services.AddSingleton(new HttpClient 
        {
            BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
        });

        services.Add...;

        ConfigureCommonServices(builder.Services);

        await builder.Build().RunAsync();
    }

    public static void ConfigureCommonServices(IServiceCollection services)
    {
        // Common service registrations
    }
}
```

Sunucu `Startup.ConfigureServices`uygulamasında, aşağıdaki ek hizmetleri kaydedin:

```csharp
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Server;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddRazorPages();
    services.AddScoped<AuthenticationStateProvider, ServerAuthenticationStateProvider>();
    services.AddScoped<SignOutSessionStateManager>();

    Client.Program.ConfigureCommonServices(services);
}
```

Sunucu uygulamasının `Startup.Configure` yönteminde aşağıdakilerle değiştirin: `endpoints.MapFallbackToFile("index.html")` `endpoints.MapFallbackToPage("/_Host")`

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

Sunucu uygulamasında, yoksa bir *Sayfalar* klasörü oluşturun. Sunucu uygulamasının *Sayfalar* klasöründe *bir _Host.cshtml* sayfası oluşturun. İstemci uygulamasının *wwwroot/index.html* dosyasındaki içeriği *Pages/_Host.cshtml* dosyasına yapıştırın. Dosyanın içeriğini güncelleştirin:

* Dosyanın üstüne ekleyin. `@page "_Host"`
* Etiketi `<app>Loading...</app>` aşağıdakilerle değiştirin:

  ```cshtml
  <app>
      @if (!HttpContext.Request.Path.StartsWithSegments("/authentication"))
      {
          <component type="typeof(Wasm.Authentication.Client.App)" render-mode="Static" />
      }
      else
      {
          <text>Loading...</text>
      }
  </app>
  ```
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a>Barındırılan uygulamalar ve üçüncü taraf giriş sağlayıcıları için seçenekler

Barındırılan Blazor bir WebAssembly uygulamasını üçüncü taraf bir sağlayıcıyla doğrularken ve yetkilendirirken, kullanıcının kimliğini doğrulamak için çeşitli seçenekler vardır. Hangisini seçeceğiniz senaryonuza bağlıdır.

Daha fazla bilgi için bkz. <xref:security/authentication/social/additional-claims>.

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a>Yalnızca korumalı üçüncü taraf API'lerini aramak için kullanıcıları kimlik doğrulaması

Üçüncü taraf API sağlayıcısına karşı istemci tarafı OAuth akışı ile kullanıcının doğrulamasını:

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 Bu senaryoda:

* Uygulamayı barındıran sunucu bir rol oynamaz.
* Sunucudaki API'ler korunamıyor.
* Uygulama yalnızca korumalı üçüncü taraf API'leri arayabilir.

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a>Üçüncü taraf sağlayıcıyla kullanıcıları doğrulave ana bilgisayar sunucusunda ve üçüncü tarafta korumalı API'leri arayın

Kimliği bir üçüncü taraf giriş sağlayıcısıyla yapılandırın. Üçüncü taraf API erişimi için gerekli belirteçleri edinin ve bunları saklayın.

Bir kullanıcı oturum açtığınızda, Identity kimlik doğrulama işleminin bir parçası olarak erişim toplar ve belirteçleri yeniler. Bu noktada, üçüncü taraf API'lere API çağrıları yapmak için birkaç yaklaşım mevcuttur.

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a>Üçüncü taraf erişim jetonunu almak için sunucu erişim jetonunu kullanma

Sunucu API bitiş noktasından üçüncü taraf erişim belirteci almak için sunucuda oluşturulan erişim belirteci kullanın. Buradan, üçüncü taraf API kaynaklarını doğrudan kimlikten istemciden aramak için üçüncü taraf erişim belirteci'ni kullanın.

Bu yaklaşımı önermiyoruz. Bu yaklaşım, üçüncü taraf erişim belirteci bir ortak istemci için oluşturulmuş gibi ele gerektirir. OAuth açısından, genel uygulamanın bir istemci sırrı yoktur, çünkü sırları güvenli bir şekilde depolamak için güvenilir değildir ve erişim belirteci gizli bir istemci için üretilir. Gizli istemci, istemci sırrı olan ve sırları güvenli bir şekilde saklayabildiği varsayılan bir istemcidir.

* Üçüncü taraf erişim belirteci, üçüncü tarafın daha güvenilir bir istemci için belirteci yaydığı gerçeğine dayanarak hassas işlemleri gerçekleştirmek için ek kapsamlar verilebilir.
* Benzer şekilde, diğer kısıtlamalar konmadığı sürece istemciye sınırsız erişim sağladığından, belirteçleri güvenilir olmayan bir istemciye verilmemelidir.

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a>Üçüncü taraf API'lerini aramak için istemciden sunucu API'sine API çağrıları yapma

İstemciden sunucu API'sine bir API çağrısı yapın. Sunucudan, üçüncü taraf API kaynağının erişim belirtecisini alın ve gerekli olan çağrıyı sorun.

Bu yaklaşım, üçüncü taraf API'yi çağırmak için sunucuüzerinden fazladan bir ağ atlama gerektirir, ancak sonuçta daha güvenli bir deneyim sağlar:

* Sunucu yenileme belirteçlerini depolayabilir ve uygulamanın üçüncü taraf kaynaklarına erişimini kaybetmemesini sağlayabilir.
* Uygulama, daha hassas izinler içerebilecek erişim belirteçlerini sunucudan sızdıramaz.
