---
title: ASP.NET Core tek sayfalı uygulamalar için kimlik doğrulamaya giriş
author: javiercn
description: ASP.NET Core Identity uygulamasının Içinde barındırılan tek sayfalı bir uygulamayla kullanın.
monikerRange: '>= aspnetcore-3.0'
ms.author: scaddie
ms.custom: mvc
ms.date: 11/08/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/identity/spa
ms.openlocfilehash: 178f85df0d35027cddb4314f9dabe26af8483ce6
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775680"
---
# <a name="authentication-and-authorization-for-spas"></a>Maça kimlik doğrulaması ve yetkilendirme

ASP.NET Core 3,0 veya üzeri, API yetkilendirmesi desteğini kullanarak tek sayfalı uygulamalarda (Spaon) kimlik doğrulaması sunmaktadır. Kimlik Identity doğrulama ve depolama için ASP.NET Core, açık kimlik Connect uygulama Için [IdentityServer](https://identityserver.io/) ile birleştirilir.

Bir kimlik doğrulama parametresi, **angular** 'a eklenmiştir ve **Web uygulamasındaki (model-görünüm-denetleyici)** (MVC) ve **Web uygulaması** (Razor sayfalar) proje şablonlarında kimlik doğrulama parametresine benzer olan proje şablonlarına **tepki** verir. İzin verilen parametre değerleri **none** ve **bireysel**. **Tepki. js ve Redux** proje şablonu şu anda kimlik doğrulama parametresini desteklemiyor.

## <a name="create-an-app-with-api-authorization-support"></a>API yetkilendirme desteğiyle uygulama oluşturma

Kullanıcı kimlik doğrulaması ve yetkilendirme, hem angular ile hem de maça 'Ları ile kullanılabilir. Bir komut kabuğu açın ve şu komutu çalıştırın:

**Angular**:

```dotnetcli
dotnet new angular -o <output_directory_name> -au Individual
```

**Tepki**verme:

```dotnetcli
dotnet new react -o <output_directory_name> -au Individual
```

Yukarıdaki komut, SPA 'yı içeren *clientapp* dizinine sahip bir ASP.NET Core uygulaması oluşturur.

## <a name="general-description-of-the-aspnet-core-components-of-the-app"></a>Uygulamanın ASP.NET Core bileşenlerinin genel açıklaması

Aşağıdaki bölümlerde, kimlik doğrulama desteği dahil edildiğinde projenin eklemeleri açıklanır:

### <a name="startup-class"></a>Başlangıç sınıfı

`Startup` Sınıfı aşağıdaki eklemelere sahiptir:

* `Startup.ConfigureServices` Yöntemin içinde:
  * IdentityVarsayılan Kullanıcı arabirimi ile:

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
        .AddDefaultUI(UIFramework.Bootstrap4)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * IdentityServer 'ın en üstünde `AddApiAuthorization` bazı varsayılan ASP.NET Core kuralları ayarlayan ek bir yardımcı yöntemi olan IdentityServer:

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * Kimliği, IdentityServer `AddIdentityServerJwt` tarafından üretilen JWT belirteçlerini doğrulamak üzere uygulamayı yapılandıran ek bir yardımcı yöntem ile kimlik doğrulaması:

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* `Startup.Configure` Yöntemin içinde:
  * İstek kimlik bilgilerini doğrulamadan ve Kullanıcı istek bağlamında ayarlamaktan sorumlu kimlik doğrulama ara yazılımı:

    ```csharp
    app.UseAuthentication();
    ```

  * Açık KIMLIK bağlantı noktalarını kullanıma sunan IdentityServer ara yazılımı:

    ```csharp
    app.UseIdentityServer();
    ```

### <a name="addapiauthorization"></a>Addadpiauthorization

Bu yardımcı yöntemi, IdentityServer 'ı desteklenen yapılandırmamızı kullanacak şekilde yapılandırır. IdentityServer, uygulama güvenliği sorunlarını işlemeye yönelik güçlü ve genişletilebilir bir çerçevedir. Aynı zamanda, en yaygın senaryolar için gereksiz karmaşıklık sunan. Sonuç olarak, size iyi bir başlangıç noktası olarak kabul edilen bir dizi kural ve yapılandırma seçeneği sağlanır. Kimlik doğrulama gereksinimleriniz değiştikçe, IdentityServer 'ın tam gücü, kimlik doğrulamasını gereksinimlerinize uyacak şekilde özelleştirmek için hala kullanılabilir.

### <a name="addidentityserverjwt"></a>Addentityserverjwt

Bu yardımcı yöntemi, varsayılan kimlik doğrulama işleyicisi olarak uygulama için bir ilke düzeni yapılandırır. İlke, "/ Identity Identity Identity" URL alanındaki herhangi bir alt yolda yönlendirilen tüm isteklerin işlemesini sağlamak üzere yapılandırılmıştır. Diğer `JwtBearerHandler` tüm istekleri işler. Ayrıca, bu yöntem, IdentityServer ile bir `<<ApplicationName>>API` API kaynağını varsayılan kapsamına kaydeder `<<ApplicationName>>API` ve bu uygulama için IdentityServer tarafından VERILEN belirteçleri doğrulamak üzere JWT taşıyıcı belirteç ara yazılımını yapılandırır.

### <a name="weatherforecastcontroller"></a>Dalgalı bir denetleyici

*Controllers\dalgalı therforebir Controller.cs* dosyasında, kullanıcıya kaynağa erişim için `[Authorize]` varsayılan ilkeye göre yetkilendirilmiş olması gerektiğini belirten sınıfa uygulanan özniteliğe dikkat edin. Varsayılan yetkilendirme ilkesi, yukarıda belirtilen ilke şemasına tarafından `AddIdentityServerJwt` `JwtBearerHandler` yapılandırılan varsayılan kimlik doğrulama şemasını kullanacak şekilde yapılandırılmıştır. Bu, bu tür bir yardımcı yöntemi tarafından yapılandırılmış bir uygulama istekleri için varsayılan işleyicidir.

### <a name="applicationdbcontext"></a>ApplicationDbContext

*Data\applicationdbcontext.cs* `DbContext` dosyasında, ' Identity nin `ApiAuthorizationDbContext` (öğesinden `IdentityDbContext`daha fazla türetilmiş bir sınıf) IdentityServer şemasını dahil etmek için kullandığı özel durum ile birlikte kullanıldığına dikkat edin.

Veritabanı şemasının tam denetimini elde etmek Identity `DbContext` için, kullanılabilir sınıflardan birini ve Identity `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` `OnModelCreating` yöntemi çağırarak şemayı içerecek şekilde yapılandırın.

### <a name="oidcconfigurationcontroller"></a>Oıdcconfigurationcontroller

*Controllers\oıdcconfigurationcontroller.cs* dosyasında, istemcinin kullanması gereken OIDC parametrelerine hizmeti sağlaması için sağlanan uç noktaya dikkat edin.

### <a name="appsettingsjson"></a>appSettings. JSON

Proje kökünün *appSettings. JSON* dosyasında, yapılandırılmış istemciler listesini açıklayan yeni `IdentityServer` bir bölüm vardır. Aşağıdaki örnekte, tek bir istemci vardır. İstemci adı, uygulama adına karşılık gelir ve kural tarafından OAuth `ClientId` parametresine eşlenir. Profil, yapılandırılan uygulama türünü gösterir. Sunucu için yapılandırma işlemini basitleştiren kuralları yönlendirmek için dahili olarak kullanılır. [Uygulama profilleri](#application-profiles) bölümünde açıklandığı gibi çeşitli profiller mevcuttur.

```json
"IdentityServer": {
  "Clients": {
    "angularindividualpreview3final": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

### <a name="appsettingsdevelopmentjson"></a>appSettings. Development. JSON

, *AppSettings 'de. Proje kökünün geliştirme. JSON* dosyası, belirteçleri imzalamak için kullanılan anahtarı `IdentityServer` açıklayan bir bölüm vardır. Üretime dağıtım yaparken, [üretime dağıtma](#deploy-to-production) bölümünde açıklandığı gibi bir anahtarın uygulamayla birlikte sağlanması ve dağıtılması gerekir.

```json
"IdentityServer": {
  "Key": {
    "Type": "Development"
  }
}
```

## <a name="general-description-of-the-angular-app"></a>Angular uygulamasının genel açıklaması

Angular şablonundaki kimlik doğrulama ve API yetkilendirme desteği *Clientapp\src\api-Authorization* dizinindeki kendi angular modülünde yer alır. Modül aşağıdaki öğelerden oluşur:

* 3 bileşen:
  * *login. Component. TS*: uygulamanın oturum açma akışını işler.
  * *Logout. Component. TS*: uygulamanın oturum kapatma akışını işler.
  * *login-Menu. Component. TS*: aşağıdaki bağlantı kümelerinden birini görüntüleyen pencere öğesi:
    * Kullanıcı profili yönetimi ve kullanıcının kimlik doğrulaması yapıldığında oturum kapatma bağlantıları.
    * Kullanıcının kimlik doğrulaması olmadığında kayıt ve oturum açma bağlantıları.
* Rotalara eklenebilen `AuthorizeGuard` ve yolu ziyaret etmeden önce bir kullanıcının kimliğinin doğrulanmasını gerektiren bir Route koruyucusu.
* Kullanıcının kimlik doğrulaması yapıldığında `AuthorizeInterceptor` API 'yi hedefleyen giden http isteklerine erişim belirtecini bağlayan bir http yakalayıcısı.
* Kimlik doğrulama `AuthorizeService` işleminin alt düzey ayrıntılarını işleyen ve kimliği doğrulanmış kullanıcı hakkındaki bilgileri, tüketim için uygulamanın geri kalanına getiren bir hizmet.
* Uygulamanın kimlik doğrulama bölümleriyle ilişkili yolları tanımlayan angular modülü. Oturum açma menü bileşeni, yakalayıcısı, koruyucu ve uygulamanın geri kalanından tüketim için hizmeti sunar.

## <a name="general-description-of-the-react-app"></a>Tepki verme uygulamasının genel açıklaması

Yanıt verme şablonunda kimlik doğrulama ve API yetkilendirmesi desteği *Clientapp\src\\disk api-Authorization* dizininde bulunur. Şu öğelerden oluşur:

* 4 bileşen:
  * *Login. js*: uygulamanın oturum açma akışını işler.
  * *Logout. js*: uygulamanın oturum kapatma akışını işler.
  * *Loginmenu. js*: aşağıdaki bağlantı kümelerinden birini görüntüleyen pencere öğesi:
    * Kullanıcı profili yönetimi ve kullanıcının kimlik doğrulaması yapıldığında oturum kapatma bağlantıları.
    * Kullanıcının kimlik doğrulaması olmadığında kayıt ve oturum açma bağlantıları.
  * *Authorizeroute. js*: `Component` parametrede belirtilen bileşeni işlemeden önce kullanıcının kimliğinin doğrulanmasını gerektiren bir rota bileşeni.
* Kimlik doğrulama `authService` işleminin alt düzey `AuthorizeService` ayrıntılarını işleyen ve kimliği doğrulanmış kullanıcı hakkındaki bilgileri, tüketim için uygulamanın geri kalanına getiren, dışa aktarılmış bir sınıf örneği.

Artık çözümün ana bileşenlerini gördüğünüze göre, uygulama için ayrı senaryolara daha ayrıntılı bir şekilde göz atabilirsiniz.

## <a name="require-authorization-on-a-new-api"></a>Yeni bir API 'de yetkilendirme gerektir

Varsayılan olarak, sistem yeni API 'Ler için kolayca yetkilendirme gerektirecek şekilde yapılandırılmıştır. Bunu yapmak için yeni bir denetleyici oluşturun ve `[Authorize]` özniteliği Controller sınıfına veya denetleyici içindeki herhangi bir eyleme ekleyin.

## <a name="customize-the-api-authentication-handler"></a>API kimlik doğrulama işleyicisini özelleştirme

API 'nin JWT işleyicisinin yapılandırmasını özelleştirmek için, <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> örneğini yapılandırın:

```csharp
services.AddAuthentication()
    .AddIdentityServerJwt();

services.Configure<JwtBearerOptions>(
    IdentityServerJwtConstants.IdentityServerJwtBearerScheme,
    options =>
    {
        ...
    });
```

API 'nin JWT işleyicisi, kullanarak `JwtBearerEvents`kimlik doğrulama işlemi üzerinde denetimi etkinleştiren olaylar oluşturur. API yetkilendirmesi için destek sağlamak üzere kendi `AddIdentityServerJwt` olay işleyicilerini kaydeder.

Bir olayın işlenmesini özelleştirmek için, var olan olay işleyicisini gereken ek mantığla sarın. Örneğin:

```csharp
services.Configure<JwtBearerOptions>(
    IdentityServerJwtConstants.IdentityServerJwtBearerScheme,
    options =>
    {
        var onTokenValidated = options.Events.OnTokenValidated;       
        
        options.Events.OnTokenValidated = async context =>
        {
            await onTokenValidated(context);
            ...
        }
    });
```

Önceki kodda, `OnTokenValidated` olay işleyicisi özel bir uygulamayla değiştirilmiştir. Bu uygulama:

1. API yetkilendirme desteği tarafından sunulan özgün uygulamayı çağırır.
1. Kendi özel mantığını çalıştırın.

## <a name="protect-a-client-side-route-angular"></a>İstemci tarafı yolunu koruma (angular)

İstemci tarafı bir yolu korumak, yetkilendirme koruyucusu bir rota yapılandırılırken çalıştırılacak korumalara listesine eklenerek yapılır. Örnek olarak, `fetch-data` yolun ana uygulama angular modülü içinde nasıl yapılandırıldığını görebilirsiniz:

```typescript
RouterModule.forRoot([
  // ...
  { path: 'fetch-data', component: FetchDataComponent, canActivate: [AuthorizeGuard] },
])
```

Bir yolu korumanın gerçek uç noktayı korumadığını (buna hala bir `[Authorize]` öznitelik uygulanacağını), ancak kullanıcının kimlik doğrulaması olmadığında verilen istemci tarafı rotasında gezinmelerini önlediği bahsetmek önemlidir.

## <a name="authenticate-api-requests-angular"></a>API isteklerinin kimliğini doğrulama (angular)

Uygulama ile barındırılan API 'lere yönelik kimlik doğrulama istekleri, uygulama tarafından tanımlanan HTTP istemci yakalayıcısı kullanılarak otomatik olarak yapılır.

## <a name="protect-a-client-side-route-react"></a>İstemci tarafı yolunu koruma (tepki verme)

Düz `AuthorizeRoute` `Route` bileşen yerine bileşeni kullanarak bir istemci tarafı yolunu koruyun. Örneğin, `fetch-data` yolun `App` bileşen içinde nasıl yapılandırıldığına dikkat edin:

```jsx
<AuthorizeRoute path='/fetch-data' component={FetchData} />
```

Bir yolu koruma:

* Gerçek uç noktayı korumaz (yine de ona uygulanan bir `[Authorize]` özniteliği gerektirir).
* Yalnızca kullanıcının kimlik doğrulaması olmadığında verilen istemci tarafı rotasında gezinmelerini engeller.

## <a name="authenticate-api-requests-react"></a>API isteklerinin kimliğini doğrulama (tepki)

Yanıt vererek istekleri kimlik doğrulama işlemi, `authService` `AuthorizeService`önce örneği öğesinden içeri aktarılarak yapılır. Erişim belirteci konumundan alınır `authService` ve aşağıda gösterildiği gibi isteğe iliştirilir. Yanıt verme bileşenlerinde, bu çalışma genellikle `componentDidMount` yaşam döngüsü yönteminde veya bazı Kullanıcı etkileşiminden elde edilen sonuç olarak yapılır.

### <a name="import-the-authservice-into-your-component"></a>AuthService 'i bileşeninizdeki içeri aktarın

```javascript
import authService from './api-authorization/AuthorizeService'
```

### <a name="retrieve-and-attach-the-access-token-to-the-response"></a>Erişim belirtecini alma ve yanıta iliştirme

```javascript
async populateWeatherData() {
  const token = await authService.getAccessToken();
  const response = await fetch('api/SampleData/WeatherForecasts', {
    headers: !token ? {} : { 'Authorization': `Bearer ${token}` }
  });
  const data = await response.json();
  this.setState({ forecasts: data, loading: false });
}
```

## <a name="deploy-to-production"></a>Üretime dağıtma

Uygulamayı üretime dağıtmak için aşağıdaki kaynakların sağlanması gerekir:

* Identity Kullanıcı hesaplarını depolamak için bir veritabanı ve IdentityServer izin verir.
* Belirteçleri imzalamak için kullanılacak bir üretim sertifikası.
  * Bu sertifika için belirli bir gereksinim yoktur; otomatik olarak imzalanan bir sertifika veya bir CA yetkilisi tarafından sağlanan bir sertifika olabilir.
  * PowerShell veya OpenSSL gibi standart araçlarla oluşturulabilir.
  * Hedef makinelerdeki sertifika deposuna yüklenebilir veya güçlü bir parolayla bir *. pfx* dosyası olarak dağıtılabilir.

### <a name="example-deploy-to-azure-websites"></a>Örnek: Azure Web sitelerine dağıtma

Bu bölümde, sertifika deposunda depolanan bir sertifika kullanılarak uygulamanın Azure Web sitelerine dağıtımı açıklanmaktadır. Uygulamayı sertifika deposundan bir sertifika yükleyecek şekilde değiştirmek için, daha sonraki bir adımda yapılandırdığınızda App Service planının en azından Standart katmanda olması gerekir. Uygulamanın *appSettings. JSON* dosyasında, önemli ayrıntıları dahil etmek için `IdentityServer` bölümünü değiştirin:

```json
"IdentityServer": {
  "Key": {
    "Type": "Store",
    "StoreName": "My",
    "StoreLocation": "CurrentUser",
    "Name": "CN=MyApplication"
  }
}
```

* Mağaza adı, sertifikanın depolandığı sertifika deposunun adını temsil eder. Bu durumda, kişisel Kullanıcı deposuna işaret eder.
* Depolama konumu, sertifikanın nereden yükleneceğini (`CurrentUser` veya `LocalMachine`) temsil eder.
* Sertifikadaki ad özelliği, sertifikanın ayırt edici konusuna karşılık gelir.

Azure Web siteleri 'ne dağıtmak için, gerekli Azure kaynaklarını oluşturmak ve uygulamayı üretime dağıtmak üzere uygulamayı [Azure 'A dağıtma](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure) bölümündeki adımları izleyerek uygulamayı dağıtın.

Yukarıdaki yönergeleri uyguladıktan sonra, uygulama Azure 'a dağıtılır ancak henüz işlevsel değildir. Uygulama tarafından kullanılan sertifikanın hala ayarlanması gerekiyor. Kullanılacak sertifika için parmak izini bulun ve [sertifikalarınızı yükleme](/azure/app-service/app-service-web-ssl-cert-load#load-the-certificate-in-code)bölümünde açıklanan adımları izleyin.

Bu adımlar SSL 'den bahsetirken portalda, uygulama ile kullanmak üzere sağlanan sertifikayı karşıya yükleyebileceğiniz **özel sertifikalar** bölümü vardır.

Bu adımdan sonra uygulamayı yeniden başlatın ve çalışır olması gerekir.

## <a name="other-configuration-options"></a>Diğer yapılandırma seçenekleri

API yetkilendirmesi desteği, IdentityServer 'ın en üstünde bir dizi kural, varsayılan değer ve, maça deneyimini basitleştirecek geliştirmeler oluşturur. Daha az ki, ASP.NET Core tümleştirmeler senaryonuzu kapsamadıysanız, IdentityServer 'ın tam gücü arka planda kullanılabilir. ASP.NET Core desteği, tüm uygulamaların kuruluşumuza göre oluşturulduğu ve dağıtıldığı "birinci taraf" uygulamalara odaklanır. Bu nedenle, izin veya Federasyon gibi şeyler için destek sunulmaz. Bu senaryolar için IdentityServer kullanın ve belgelerini izleyin.

### <a name="application-profiles"></a>Uygulama profilleri

Uygulama profilleri, parametrelerini daha fazla tanımlayan uygulamalar için önceden tanımlanmış yapılandırlardır. Şu anda, aşağıdaki profiller desteklenir:

* `IdentityServerSPA`: IdentityServer 'ın yanı sıra tek bir birim olarak barındırılan bir SPA 'yı temsil eder.
  * `redirect_uri` Varsayılan olarak `/authentication/login-callback`olur.
  * `post_logout_redirect_uri` Varsayılan olarak `/authentication/logout-callback`olur.
  * Kapsam kümesi `openid`, `profile`, ve uygulamadaki API 'ler için tanımlanan tüm kapsamları içerir.
  * İzin verilen OıDC yanıt türleri `id_token token` kümesi, tek tek (`id_token`, `token`).
  * İzin verilen yanıt modu `fragment`.
* `SPA`: IdentityServer ile barındırılmayan bir SPA 'yı temsil eder.
  * Kapsam kümesi `openid`, `profile`, ve uygulamadaki API 'ler için tanımlanan tüm kapsamları içerir.
  * İzin verilen OıDC yanıt türleri `id_token token` kümesi, tek tek (`id_token`, `token`).
  * İzin verilen yanıt modu `fragment`.
* `IdentityServerJwt`: IdentityServer ile birlikte barındırılan bir API 'YI temsil eder.
  * Uygulama, uygulama adı için varsayılan olarak kullanılan tek bir kapsama sahip olacak şekilde yapılandırılmıştır.
* `API`: IdentityServer ile barındırılmayan bir API 'YI temsil eder.
  * Uygulama, uygulama adı için varsayılan olarak kullanılan tek bir kapsama sahip olacak şekilde yapılandırılmıştır.

### <a name="configuration-through-appsettings"></a>AppSettings aracılığıyla yapılandırma

Uygulamaları, `Clients` veya `Resources`listesine ekleyerek yapılandırma sistemi aracılığıyla yapılandırın.

Aşağıdaki örnekte gösterildiği gibi `redirect_uri` , `post_logout_redirect_uri` her bir istemcinin ve özelliğini yapılandırın:

```json
"IdentityServer": {
  "Clients": {
    "MySPA": {
      "Profile": "SPA",
      "RedirectUri": "https://www.example.com/authentication/login-callback",
      "LogoutUri": "https://www.example.com/authentication/logout-callback"
    }
  }
}
```

Kaynakları yapılandırırken, kaynak için kapsamları aşağıda gösterildiği gibi yapılandırabilirsiniz:

```json
"IdentityServer": {
  "Resources": {
    "MyExternalApi": {
      "Profile": "API",
      "Scopes": "a b c"
    }
  }
}
```

### <a name="configuration-through-code"></a>Kod aracılığıyla yapılandırma

Ayrıca, seçeneklerini yapılandırmak için bir eylem alan aşırı yüklemesini `AddApiAuthorization` kullanarak, istemcileri ve kaynakları kod aracılığıyla da yapılandırabilirsiniz.

```csharp
AddApiAuthorization<ApplicationUser, ApplicationDbContext>(options =>
{
    options.Clients.AddSPA(
        "My SPA", spa =>
        spa.WithRedirectUri("http://www.example.com/authentication/login-callback")
           .WithLogoutRedirectUri(
               "http://www.example.com/authentication/logout-callback"));

    options.ApiResources.AddApiResource("MyExternalApi", resource =>
        resource.WithScopes("a", "b", "c"));
});
```

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:spa/angular>
* <xref:spa/react>
* <xref:security/authentication/scaffold-identity>
