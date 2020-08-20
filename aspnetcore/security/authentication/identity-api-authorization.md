---
title: ASP.NET Core tek sayfalı uygulamalar için kimlik doğrulamaya giriş
author: javiercn
description: IdentityASP.NET Core uygulamasının içinde barındırılan tek sayfalı bir uygulamayla kullanın.
monikerRange: '>= aspnetcore-3.0'
ms.author: scaddie
ms.custom: mvc
ms.date: 11/08/2019
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
uid: security/authentication/identity/spa
ms.openlocfilehash: 913f8f1e43586ce71353c080e72be3b80f4c0573
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634273"
---
# <a name="authentication-and-authorization-for-spas"></a>Maça kimlik doğrulaması ve yetkilendirme

ASP.NET Core 3,0 veya üzeri, API yetkilendirmesi desteğini kullanarak tek sayfalı uygulamalarda (Spaon) kimlik doğrulaması sunmaktadır. ASP.NET Core Identitykimlik doğrulamak ve depolamak için, OpenID Connect 'i uygulamak üzere [ Identity sunucu](https://identityserver.io/) ile birleştirilir.

Bir kimlik doğrulama parametresi, **angular** 'a eklenmiştir ve **Web uygulamasındaki (model-görünüm-denetleyici)** (MVC) ve **Web uygulaması** (sayfalar) proje şablonlarında kimlik doğrulama parametresine benzer olan proje şablonlarına **tepki** verir Razor . İzin verilen parametre değerleri **none** ve **bireysel**. **React.js ve Redux** proje şablonu, kimlik doğrulama parametresini Şu anda desteklemiyor.

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

Aşağıdaki kod örnekleri, [Microsoft. AspNetCore. ApiAuthorization öğesine bağımlıdır. Identity Sunucu](https://www.nuget.org/packages/Microsoft.AspNetCore.ApiAuthorization.IdentityServer) NuGet paketi. Örnekler, <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> ve genişletme yöntemlerini kullanarak API kimlik doğrulaması ve yetkilendirme yapılandırır <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiResourceCollection.AddIdentityServerJwt%2A> . Kimlik doğrulamasıyla tepki verme veya angular SPA proje şablonlarını kullanan projeler, bu pakete bir başvuru içerir.

`Startup`Sınıfı aşağıdaki eklemelere sahiptir:

* Yöntemin içinde `Startup.ConfigureServices` :
  * Identity Varsayılan Kullanıcı arabirimi ile:

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * Identity`AddApiAuthorization`Sunucu üst kısmında bazı varsayılan ASP.NET Core kuralları ayarlayan ek bir yardımcı yöntemi olan sunucu Identity :

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * `AddIdentityServerJwt`Sunucu tarafından ÜRETILEN JWT belirteçlerini doğrulamak üzere uygulamayı yapılandıran ek bir yardımcı yöntem ile kimlik doğrulaması Identity :

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* Yöntemin içinde `Startup.Configure` :
  * İstek kimlik bilgilerini doğrulamadan ve Kullanıcı istek bağlamında ayarlamaktan sorumlu kimlik doğrulama ara yazılımı:

    ```csharp
    app.UseAuthentication();
    ```

  * IdentityOpenID Connect uç noktalarını kullanıma sunan sunucu ara yazılımı:

    ```csharp
    app.UseIdentityServer();
    ```

### <a name="addapiauthorization"></a>Addadpiauthorization

Bu yardımcı yöntem, Identity sunucuyu desteklenen yapılandırmamızı kullanacak şekilde yapılandırır. IdentitySunucu, uygulama güvenliği sorunlarını işlemeye yönelik güçlü ve genişletilebilir bir çerçevedir. Aynı zamanda, en yaygın senaryolar için gereksiz karmaşıklık sunan. Sonuç olarak, size iyi bir başlangıç noktası olarak kabul edilen bir dizi kural ve yapılandırma seçeneği sağlanır. Kimlik doğrulamanın değişmesi için, sunucunun tam gücü, Identity kimlik doğrulamasını gereksinimlerinize uyacak şekilde özelleştirmek için hala kullanılabilir.

### <a name="addno-locidentityserverjwt"></a>IdentityServerjwt Ekle

Bu yardımcı yöntemi, varsayılan kimlik doğrulama işleyicisi olarak uygulama için bir ilke düzeni yapılandırır. İlke, Identity Identity "/" URL alanındaki herhangi bir alt yolda yönlendirilen tüm isteklerin işlemesini sağlamak üzere yapılandırılmıştır Identity . `JwtBearerHandler`Diğer tüm istekleri işler. Ayrıca, bu yöntem, bir `<<ApplicationName>>API` API kaynağını Identity varsayılan kapsamına sahip sunucuya kaydeder ve bu `<<ApplicationName>>API` Identity uygulama için sunucu tarafından VERILEN belirteçleri doğrulamak üzere JWT taşıyıcı belirteç ara yazılımını yapılandırır.

### <a name="weatherforecastcontroller"></a>Dalgalı bir denetleyici

*Controllers\dalgalı therforebir Controller.cs* dosyasında, `[Authorize]` kullanıcıya kaynağa erişim için varsayılan ilkeye göre yetkilendirilmiş olması gerektiğini belirten sınıfa uygulanan özniteliğe dikkat edin. Varsayılan yetkilendirme ilkesi, yukarıda belirtilen ilke şemasına tarafından yapılandırılan varsayılan kimlik doğrulama şemasını kullanacak şekilde yapılandırılmıştır `AddIdentityServerJwt` `JwtBearerHandler` . Bu, bu tür bir yardımcı yöntemi tarafından yapılandırılmış bir uygulama istekleri için varsayılan işleyicidir.

### <a name="applicationdbcontext"></a>ApplicationDbContext

*Data\applicationdbcontext.cs* dosyasında, ' `DbContext` ın Identity `ApiAuthorizationDbContext` genişlettiği özel durumla (öğesinden daha fazla türetilmiş bir sınıf `IdentityDbContext` ) sunucu için şemayı dahil etmek için Identity aynı olduğuna dikkat edin.

Veritabanı şemasının tam denetimini elde etmek için, kullanılabilir Identity `DbContext` sınıflardan birini ve Identity yöntemi çağırarak şemayı içerecek şekilde yapılandırın `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` `OnModelCreating` .

### <a name="oidcconfigurationcontroller"></a>Oıdcconfigurationcontroller

*Controllers\oıdcconfigurationcontroller.cs* dosyasında, istemcinin kullanması gereken OIDC parametrelerine hizmeti sağlaması için sağlanan uç noktaya dikkat edin.

### <a name="appsettingsjson"></a>Üzerinde appsettings.js

Proje kökünün dosyasındaki *appsettings.js* , `IdentityServer` yapılandırılmış istemciler listesini açıklayan yeni bir bölüm vardır. Aşağıdaki örnekte, tek bir istemci vardır. İstemci adı, uygulama adına karşılık gelir ve kural tarafından OAuth `ClientId` parametresine eşlenir. Profil, yapılandırılan uygulama türünü gösterir. Sunucu için yapılandırma işlemini basitleştiren kuralları yönlendirmek için dahili olarak kullanılır. [Uygulama profilleri](#application-profiles) bölümünde açıklandığı gibi çeşitli profiller mevcuttur.

```json
"IdentityServer": {
  "Clients": {
    "angularindividualpreview3final": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

### <a name="appsettingsdevelopmentjson"></a>Üzerinde appsettings.Development.js

Proje kökünün dosyasındaki *appsettings.Development.js* , `IdentityServer` belirteçleri imzalamak için kullanılan anahtarı açıklayan bir bölüm vardır. Üretime dağıtım yaparken, [üretime dağıtma](#deploy-to-production) bölümünde açıklandığı gibi bir anahtarın uygulamayla birlikte sağlanması ve dağıtılması gerekir.

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
* `AuthorizeGuard`Rotalara eklenebilen ve yolu ziyaret etmeden önce bir kullanıcının kimliğinin doğrulanmasını gerektiren bir Route koruyucusu.
* `AuthorizeInterceptor`Kullanıcının kimlik doğrulaması yapıldığında API 'yi hedefleyen gıden http isteklerine erişim belirtecini bağlayan BIR http yakalayıcısı.
* `AuthorizeService`Kimlik doğrulama işleminin alt düzey ayrıntılarını işleyen ve kimliği doğrulanmış kullanıcı hakkındaki bilgileri, tüketim için uygulamanın geri kalanına getiren bir hizmet.
* Uygulamanın kimlik doğrulama bölümleriyle ilişkili yolları tanımlayan angular modülü. Oturum açma menü bileşeni, yakalayıcısı, koruyucu ve uygulamanın geri kalanından tüketim için hizmeti sunar.

## <a name="general-description-of-the-react-app"></a>Tepki verme uygulamasının genel açıklaması

Yanıt verme şablonunda kimlik doğrulama ve API yetkilendirmesi desteği *Clientapp\src\\disk api-Authorization* dizininde bulunur. Şu öğelerden oluşur:

* 4 bileşen:
  * *Login.js*: uygulamanın oturum açma akışını işler.
  * *Logout.js*: uygulamanın oturum kapatma akışını işler.
  * *LoginMenu.js*: aşağıdaki bağlantı kümelerinden birini görüntüleyen pencere öğesi:
    * Kullanıcı profili yönetimi ve kullanıcının kimlik doğrulaması yapıldığında oturum kapatma bağlantıları.
    * Kullanıcının kimlik doğrulaması olmadığında kayıt ve oturum açma bağlantıları.
  * *AuthorizeRoute.js*: bir kullanıcının, parametresinde belirtilen bileşeni işlemeden önce kimliğinin doğrulanmasını gerektiren bir rota bileşeni `Component` .
* `authService` `AuthorizeService` Kimlik doğrulama işleminin alt düzey ayrıntılarını işleyen ve kimliği doğrulanmış kullanıcı hakkındaki bilgileri, tüketim için uygulamanın geri kalanına getiren, dışa aktarılmış bir sınıf örneği.

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

API 'nin JWT işleyicisi, kullanarak kimlik doğrulama işlemi üzerinde denetimi etkinleştiren olaylar oluşturur `JwtBearerEvents` . API yetkilendirmesi için destek sağlamak üzere `AddIdentityServerJwt` kendi olay işleyicilerini kaydeder.

Bir olayın işlenmesini özelleştirmek için, var olan olay işleyicisini gereken ek mantığla sarın. Örnek:

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

`AuthorizeRoute`Düz bileşen yerine bileşeni kullanarak bir istemci tarafı yolunu koruyun `Route` . Örneğin, `fetch-data` yolun bileşen içinde nasıl yapılandırıldığına dikkat edin `App` :

```jsx
<AuthorizeRoute path='/fetch-data' component={FetchData} />
```

Bir yolu koruma:

* Gerçek uç noktayı korumaz (yine de `[Authorize]` ona uygulanan bir özniteliği gerektirir).
* Yalnızca kullanıcının kimlik doğrulaması olmadığında verilen istemci tarafı rotasında gezinmelerini engeller.

## <a name="authenticate-api-requests-react"></a>API isteklerinin kimliğini doğrulama (tepki)

Yanıt vererek istekleri kimlik doğrulama işlemi, önce örneği öğesinden içeri aktarılarak yapılır `authService` `AuthorizeService` . Erişim belirteci konumundan alınır `authService` ve aşağıda gösterildiği gibi isteğe iliştirilir. Yanıt verme bileşenlerinde, bu çalışma genellikle `componentDidMount` yaşam döngüsü yönteminde veya bazı Kullanıcı etkileşiminden elde edilen sonuç olarak yapılır.

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

* Kullanıcı hesaplarını depolamak için bir veritabanı Identity ve Identity sunucu verir.
* Belirteçleri imzalamak için kullanılacak bir üretim sertifikası.
  * Bu sertifika için belirli bir gereksinim yoktur; otomatik olarak imzalanan bir sertifika veya bir CA yetkilisi tarafından sağlanan bir sertifika olabilir.
  * PowerShell veya OpenSSL gibi standart araçlarla oluşturulabilir.
  * Hedef makinelerdeki sertifika deposuna yüklenebilir veya güçlü bir parolayla bir *. pfx* dosyası olarak dağıtılabilir.

### <a name="example-deploy-to-azure-app-service"></a>Örnek: Azure App Service dağıt

Bu bölümde, sertifika deposunda depolanan bir sertifikayı kullanarak Azure App Service uygulamanın dağıtımı açıklanmaktadır. Uygulamayı sertifika deposundan bir sertifika yükleyecek şekilde değiştirmek için, daha sonraki bir adımda Azure portal uygulamayı yapılandırdığınızda standart bir katman hizmeti planı veya daha iyi bir seçenektir.

Uygulamanın dosyada *appsettings.js* , `IdentityServer` önemli ayrıntıları içerecek şekilde bölümünü değiştirin:

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
* Depolama konumu, sertifikanın nereden yükleneceğini (veya) temsil eder `CurrentUser` `LocalMachine` .
* Sertifikadaki ad özelliği, sertifikanın ayırt edici konusuna karşılık gelir.

Azure App Service dağıtmak için, [uygulamayı Azure 'A dağıtma](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure)' daki adımları Izleyerek gerekli Azure kaynaklarının nasıl oluşturulduğunu ve uygulamayı üretime dağıtmayı açıklar.

Yukarıdaki yönergeleri uyguladıktan sonra, uygulama Azure 'a dağıtılır ancak henüz işlevsel değildir. Uygulama tarafından kullanılan sertifikanın Azure portal yapılandırılması gerekir. Sertifika için parmak izini bulun ve [sertifikalarınızı yükleme](/azure/app-service/app-service-web-ssl-cert-load#load-the-certificate-in-code)bölümünde açıklanan adımları izleyin.

Bu adımlar SSL 'den bahsetirken, uygulama ile kullanmak üzere sağlanan sertifikayı karşıya yükleyebileceğiniz Azure portal **özel sertifikalar** bölümü vardır.

Azure portal uygulamayı ve uygulamanın ayarlarını yapılandırdıktan sonra, uygulamayı portalda yeniden başlatın.

## <a name="other-configuration-options"></a>Diğer yapılandırma seçenekleri

API yetkilendirmesi desteği, Identity bir dizi kural, varsayılan değer ve, maça deneyimini basitleştirmek için geliştirmeler kümesi ile sunucu üzerinde oluşturulur. Daha az ki, Identity ASP.NET Core tümleştirmeler senaryonuzu kapsamadıysanız sunucunun tam gücü arka planda kullanılabilir. ASP.NET Core desteği, tüm uygulamaların kuruluşumuza göre oluşturulduğu ve dağıtıldığı "birinci taraf" uygulamalara odaklanır. Bu nedenle, izin veya Federasyon gibi şeyler için destek sunulmaz. Bu senaryolar için sunucu ' yı kullanın Identity ve belgelerini izleyin.

### <a name="application-profiles"></a>Uygulama profilleri

Uygulama profilleri, parametrelerini daha fazla tanımlayan uygulamalar için önceden tanımlanmış yapılandırlardır. Şu anda, aşağıdaki profiller desteklenir:

* `IdentityServerSPA`: Identity Tek bir birim olarak sunucu ile barındırılan BIR Spa 'yı temsil eder.
  * `redirect_uri`Varsayılan olarak olur `/authentication/login-callback` .
  * `post_logout_redirect_uri`Varsayılan olarak olur `/authentication/logout-callback` .
  * Kapsam kümesi `openid` , `profile` , ve uygulamadaki API 'ler için tanımlanan tüm kapsamları içerir.
  * İzin verilen OıDC yanıt türleri kümesi, `id_token token` tek tek ( `id_token` , `token` ).
  * İzin verilen yanıt modu `fragment` .
* `SPA`: Sunucusu ile barındırılmayan bir SPA 'yı temsil eder Identity .
  * Kapsam kümesi `openid` , `profile` , ve uygulamadaki API 'ler için tanımlanan tüm kapsamları içerir.
  * İzin verilen OıDC yanıt türleri kümesi, `id_token token` tek tek ( `id_token` , `token` ).
  * İzin verilen yanıt modu `fragment` .
* `IdentityServerJwt`: Sunucusuyla birlikte barındırılan bir API 'YI temsil eder Identity .
  * Uygulama, uygulama adı için varsayılan olarak kullanılan tek bir kapsama sahip olacak şekilde yapılandırılmıştır.
* `API`: Sunucusu ile barındırılmayan bir API 'YI temsil eder Identity .
  * Uygulama, uygulama adı için varsayılan olarak kullanılan tek bir kapsama sahip olacak şekilde yapılandırılmıştır.

### <a name="configuration-through-appsettings"></a>AppSettings aracılığıyla yapılandırma

Uygulamaları, veya listesine ekleyerek yapılandırma sistemi aracılığıyla yapılandırın `Clients` `Resources` .

`redirect_uri` `post_logout_redirect_uri` Aşağıdaki örnekte gösterildiği gibi, her bir istemcinin ve özelliğini yapılandırın:

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

Ayrıca, `AddApiAuthorization` seçeneklerini yapılandırmak için bir eylem alan aşırı yüklemesini kullanarak, istemcileri ve kaynakları kod aracılığıyla da yapılandırabilirsiniz.

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
