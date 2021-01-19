---
title: ASP.NET Core barındırma ve dağıtma Blazor WebAssembly
author: guardrex
description: BlazorASP.NET Core, Içerik teslim ağları (CDN), dosya sunucuları ve GitHub sayfalarını kullanarak bir uygulamayı nasıl barındırılacağını ve dağıtacağınızı öğrenin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/12/2021
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
uid: blazor/host-and-deploy/webassembly
ms.openlocfilehash: 2b464c2b6ca434ce4c3b559480da69945266ff69
ms.sourcegitcommit: cb984e0d7dc23a88c3a4121f23acfaea0acbfe1e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/19/2021
ms.locfileid: "98570979"
---
# <a name="host-and-deploy-aspnet-core-no-locblazor-webassembly"></a>ASP.NET Core barındırma ve dağıtma Blazor WebAssembly

[Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), [ben Adams](https://twitter.com/ben_a_adams)ve [Safia Abdalla](https://safia.rocks) tarafından

[ Blazor WebAssembly Barındırma modeliyle](xref:blazor/hosting-models#blazor-webassembly):

* BlazorUygulama, bağımlılıkları ve .NET çalışma zamanı, tarayıcıya paralel olarak indirilir.
* Uygulama doğrudan tarayıcı kullanıcı arabirimi iş parçacığında yürütülür.

Aşağıdaki dağıtım stratejileri desteklenir:

* BlazorUygulama, bir ASP.NET Core uygulaması tarafından sunulur. Bu strateji [ASP.NET Core bölümünde barındırılan dağıtımda](#hosted-deployment-with-aspnet-core) ele alınmıştır.
* BlazorUygulama, bir statik barındırma Web sunucusuna veya hizmetine yerleştirilir, burada .NET uygulamayı sunmak için kullanılmaz Blazor . Bu strateji, bir uygulamayı IIS alt uygulaması olarak barındırma hakkında bilgi içeren [tek başına dağıtım](#standalone-deployment) bölümünde ele alınmıştır Blazor WebAssembly .

## <a name="compression"></a>Sıkıştırma

Bir Blazor WebAssembly uygulama yayımlandığında, çıkış sırasında uygulamanın boyutunu azaltmak ve çalışma zamanı sıkıştırması için ek yükü kaldırmak üzere çıkış sırasında statik olarak sıkıştırılır. Aşağıdaki sıkıştırma algoritmaları kullanılır:

* [Brotli](https://tools.ietf.org/html/rfc7932) (en yüksek düzey)
* [Gzip](https://tools.ietf.org/html/rfc1952)

Blazor , uygun sıkıştırılmış dosyaları sunacak ana bilgisayarı kullanır. ASP.NET Core barındırılan bir proje kullanırken, konak proje içerik anlaşması yapabilir ve statik olarak sıkıştırılmış dosyalara hizmet verebilir. Blazor WebAssemblyTek başına bir uygulamayı barındırırken, statik olarak sıkıştırılmış dosyaların sunulmasını sağlamak için ek çalışma gerekebilir:

* IIS `web.config` sıkıştırma yapılandırması Için [IIS: Brotli ve gzip sıkıştırma](#brotli-and-gzip-compression) bölümüne bakın. 
* GitHub sayfaları gibi statik olarak sıkıştırılmış dosya içeriği anlaşmasını desteklemeyen statik barındırma çözümlerinde barındırırken, Brotli sıkıştırılan dosyaları getirmek ve kodunu çözmek üzere uygulamayı yapılandırmayı düşünün:

  * [Google/Brotli GitHub deposundan](https://github.com/google/brotli)JavaScript Brotli kod çözücüsünü edinin. Kod çözücü dosyasının adı `decode.min.js` ve deponun [ `js` klasöründe](https://github.com/google/brotli/tree/master/js)bulunur.

  * Kod çözücüyü kullanmak için uygulamayı güncelleştirin. İçindeki kapanış etiketinin içindeki biçimlendirmeyi `<body>` aşağıdaki gibi değiştirin `wwwroot/index.html` :
  
    ```html
    <script src="decode.min.js"></script>
    <script src="_framework/blazor.webassembly.js" autostart="false"></script>
    <script>
      Blazor.start({
        loadBootResource: function (type, name, defaultUri, integrity) {
          if (type !== 'dotnetjs' && location.hostname !== 'localhost') {
            return (async function () {
              const response = await fetch(defaultUri + '.br', { cache: 'no-cache' });
              if (!response.ok) {
                throw new Error(response.statusText);
              }
              const originalResponseBuffer = await response.arrayBuffer();
              const originalResponseArray = new Int8Array(originalResponseBuffer);
              const decompressedResponseArray = BrotliDecode(originalResponseArray);
              const contentType = type === 
                'dotnetwasm' ? 'application/wasm' : 'application/octet-stream';
              return new Response(decompressedResponseArray, 
                { headers: { 'content-type': contentType } });
            })();
          }
        }
      });
    </script>
    ```
 
Sıkıştırmayı devre dışı bırakmak için `BlazorEnableCompression` uygulamanın proje dosyasına MSBuild özelliğini ekleyin ve değeri şu şekilde ayarlayın `false` :

```xml
<PropertyGroup>
  <BlazorEnableCompression>false</BlazorEnableCompression>
</PropertyGroup>
```

`BlazorEnableCompression`Özelliği [`dotnet publish`](/dotnet/core/tools/dotnet-publish) komut kabuğunda aşağıdaki söz dizimi ile komuta geçirilebilir:

```dotnetcli
dotnet publish -p:BlazorEnableCompression=false
```

## <a name="rewrite-urls-for-correct-routing"></a>Doğru yönlendirme için URL 'Leri yeniden yazın

Bir uygulamadaki sayfa bileşenlerine yönelik yönlendirme istekleri Blazor WebAssembly , barındırılan bir uygulamadaki yönlendirme istekleri kadar basittir Blazor Server . Blazor WebAssemblyİki bileşeni olan bir uygulamayı göz önünde bulundurun:

* `Main.razor`: Uygulamanın köküne yükler ve `About` bileşene () bir bağlantı içerir `href="About"` .
* `About.razor`: `About` bileşen.

Uygulamanın varsayılan belgesi, tarayıcının adres çubuğu kullanılarak istendiğinde (örneğin, `https://www.contoso.com/` ):

1. Tarayıcı bir istek yapar.
1. Varsayılan sayfa döndürülür, genellikle `index.html` .
1. `index.html` uygulamayı önyükleme.
1. Blazoruygulamasının yönlendirici yüklenir ve Razor `Main` bileşen işlenir.

Ana sayfada, `About` Blazor yönlendirici tarayıcıyı Internet 'te için bir istek yapmayı durdurduğundan `www.contoso.com` `About` ve Işlenmiş `About` bileşenin kendisini sunan ana sayfada, istemci üzerinde çalışır. *Blazor WebAssembly Uygulamadaki* iç uç noktalara yönelik tüm istekler aynı şekilde çalışır: istekler tarayıcı tabanlı istekleri Internet 'teki sunucu tarafından barındırılan kaynaklara tetiklemez. Yönlendirici istekleri dahili olarak işler.

Tarayıcının adres çubuğu kullanılarak bir istek yapılırsa `www.contoso.com/About` , istek başarısız olur. Uygulamanın Internet ana bilgisayarında böyle bir kaynak yok, bu nedenle *404-bulunamayan* bir yanıt döndürülür.

Tarayıcılar, istemci tarafı sayfaları için Internet tabanlı konaklara istek yaptığından, Web sunucuları ve barındırma hizmetleri sunucuda fiziksel olarak olmayan tüm kaynak isteklerini sayfaya yeniden yazması gerekir `index.html` . `index.html`Döndürüldüğünde, uygulamanın Blazor yönlendiricisi doğru kaynakla sürer ve yanıt verir.

Bir IIS sunucusuna dağıtım yaparken, URL yeniden yazma modülünü uygulamanın yayımlanan dosyasıyla birlikte kullanabilirsiniz `web.config` . Daha fazla bilgi için [IIS](#iis) bölümüne bakın.

## <a name="hosted-deployment-with-aspnet-core"></a>ASP.NET Core ile barındırılan dağıtım

*Barındırılan bir dağıtım* , Blazor WebAssembly uygulamayı bir Web sunucusu üzerinde çalışan bir [ASP.NET Core](xref:index) uygulamasından tarayıcılarına sunar.

İstemci uygulaması, sunucu uygulamasının Blazor WebAssembly `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` diğer statik Web varlıklarıyla birlikte sunucu uygulamasının klasörüne yayımlanır. İki uygulama birlikte dağıtılır. ASP.NET Core uygulamasını barındırabilen bir Web sunucusu gereklidir. Barındırılan bir dağıtım için, Visual Studio **Blazor WebAssembly uygulama** proje şablonunu (komutunu kullanırken `blazorwasm` şablon [`dotnet new`](/dotnet/core/tools/dotnet-new) ), **`Hosted`** seçeneği belirlendiğinde ( `-ho|--hosted` `dotnet new` komutunu kullanırken) içerir.

Uygulama barındırma ve dağıtım ASP.NET Core hakkında daha fazla bilgi için bkz <xref:host-and-deploy/index> ..

Azure App Service dağıtma hakkında daha fazla bilgi için bkz <xref:tutorials/publish-to-azure-webapp-using-vs> ..

## <a name="hosted-deployment-with-multiple-no-locblazor-webassembly-apps"></a>Birden çok uygulama ile barındırılan dağıtım Blazor WebAssembly

### <a name="app-configuration"></a>Uygulama yapılandırması

Barındırılan Blazor çözümler birden çok Blazor WebAssembly uygulamayı kullanabilir.

> [!NOTE]
> Bu bölümdeki örnek, bir Visual Studio *çözümünün* kullanımına başvurur, ancak birden fazla istemci uygulamasının barındırılan uygulamalar senaryosunda çalışması Için Visual Studio ve Visual Studio çözümünün kullanımı gerekli değildir Blazor WebAssembly . Visual Studio kullanmıyorsanız, `{SOLUTION NAME}.sln` dosyayı ve Visual Studio için oluşturulan diğer dosyaları yoksayın.

Aşağıdaki örnekte:

* İlk (ilk) istemci uygulaması, proje şablonundan oluşturulan bir çözümün varsayılan istemci projem Blazor WebAssembly . İlk istemci uygulamasına, `/FirstApp` bağlantı noktası 5001 ya da bir KONAĞıNDAN URL 'den bir tarayıcıda erişilebilir `firstapp.com` .
* Çözüme ikinci bir istemci uygulaması eklenir `SecondBlazorApp.Client` . İkinci istemci uygulamasına, `/SecondApp` bağlantı noktası 5002 ya da bir KONAĞıNDAN URL 'den bir tarayıcıda erişilebilir `secondapp.com` .

BlazorBarındırılan proje şablonundan mevcut bir barındırılan çözümü kullanın veya yeni bir çözüm oluşturun Blazor :

* İstemci uygulamasının proje dosyasında, `<StaticWebAssetBasePath>` `<PropertyGroup>` `FirstApp` projenin statik varlıkların temel yolunu ayarlamak için değeri ile öğesine bir özelliği ekleyin:

  ```xml
  <PropertyGroup>
    ...
    <StaticWebAssetBasePath>FirstApp</StaticWebAssetBasePath>
  </PropertyGroup>
  ```

* Çözüme ikinci bir istemci uygulaması ekleyin:

  * Çözümün klasörüne adlı bir klasör ekleyin `SecondClient` . Proje şablonundan oluşturulan çözüm klasörü, klasör eklendikten sonra aşağıdaki çözüm dosya ve klasörlerini içerir `SecondClient` :
  
    * `Client` klasörde
    * `SecondClient` klasörde
    * `Server` klasörde
    * `Shared` klasörde
    * `{SOLUTION NAME}.sln` dosyasýný
    
    Yer tutucu `{SOLUTION NAME}` çözümün adıdır.

  * Blazor WebAssembly `SecondBlazorApp.Client` `SecondClient` Proje şablonundan klasöründe adlı bir uygulama oluşturun Blazor WebAssembly .

  * `SecondBlazorApp.Client`Uygulamanın proje dosyasında:

    * `<StaticWebAssetBasePath>`Değerine sahip öğesine bir özelliği ekleyin `<PropertyGroup>` `SecondApp` :

      ```xml
      <PropertyGroup>
        ...
        <StaticWebAssetBasePath>SecondApp</StaticWebAssetBasePath>
      </PropertyGroup>
      ```

    * Projeye bir proje başvurusu ekleyin `Shared` :

      ```xml
      <ItemGroup>
        <ProjectReference Include="..\Shared\{SOLUTION NAME}.Shared.csproj" />
      </ItemGroup>
      ```

      Yer tutucu `{SOLUTION NAME}` çözümün adıdır.

* Sunucu uygulamasının proje dosyasında, eklenen istemci uygulaması için bir proje başvurusu oluşturun `SecondBlazorApp.Client` :

  ```xml
  <ItemGroup>
    <ProjectReference Include="..\Client\{SOLUTION NAME}.Client.csproj" />
    <ProjectReference Include="..\SecondClient\SecondBlazorApp.Client.csproj" />
    <ProjectReference Include="..\Shared\{SOLUTION NAME}.Shared.csproj" />
  </ItemGroup>
  ```
  
  Yer tutucu `{SOLUTION NAME}` çözümün adıdır.

* Sunucu uygulamasının `Properties/launchSettings.json` dosyasında, `applicationUrl` `{SOLUTION NAME}.Server` 5001 ve 5002 bağlantı noktalarında istemci uygulamalarına erişmek için Kestrel profili () öğesini yapılandırın:

  ```json
  "applicationUrl": "https://localhost:5001;https://localhost:5002",
  ```

* Sunucu uygulamasının `Startup.Configure` yönteminde ( `Startup.cs` ), çağrısından sonra görünen aşağıdaki satırları kaldırın <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A> :

  ```csharp
  app.UseBlazorFrameworkFiles();
  app.UseStaticFiles();

  app.UseRouting();

  app.UseEndpoints(endpoints =>
  {
      endpoints.MapRazorPages();
      endpoints.MapControllers();
      endpoints.MapFallbackToFile("index.html");
  });
  ```

  İstekleri istemci uygulamalarına eşleyen ara yazılım ekleme. Aşağıdaki örnek, şu durumlarda ara yazılımı çalıştıracak şekilde yapılandırır:

  * İstek bağlantı noktası, özgün istemci uygulaması için 5001 ya da eklenen istemci uygulaması için 5002 ' dir.
  * İstek Konağı, `firstapp.com` özgün istemci uygulaması ya da `secondapp.com` eklenen istemci uygulaması için.

    > [!NOTE]
    > Bu bölümde gösterilen örnekte, için ek yapılandırma gerekir:
    >
    > * Örnek ana bilgisayar etki alanlarındaki uygulamalara erişme `firstapp.com` ve `secondapp.com` .
    > * TLS güvenliğini etkinleştirmek için istemci uygulamalarına yönelik Sertifikalar (HTTPS).
    >
    > Gerekli yapılandırma Bu makalenin kapsamı dışındadır ve çözümün nasıl barındırıldığını bağlıdır. Daha fazla bilgi için bkz. [konak ve dağıtım makaleleri](xref:host-and-deploy/index).

  Aşağıdaki kodu satırların daha önce kaldırıldığı yere yerleştirin:

  ```csharp
  app.MapWhen(ctx => ctx.Request.Host.Port == 5001 || 
      ctx.Request.Host.Equals("firstapp.com"), first =>
  {
      first.Use((ctx, nxt) =>
      {
          ctx.Request.Path = "/FirstApp" + ctx.Request.Path;
          return nxt();
      });

      first.UseBlazorFrameworkFiles("/FirstApp");
      first.UseStaticFiles();
      first.UseStaticFiles("/FirstApp");
      first.UseRouting();

      first.UseEndpoints(endpoints =>
      {
          endpoints.MapControllers();
          endpoints.MapFallbackToFile("/FirstApp/{*path:nonfile}", 
              "FirstApp/index.html");
      });
  });
  
  app.MapWhen(ctx => ctx.Request.Host.Port == 5002 || 
      ctx.Request.Host.Equals("secondapp.com"), second =>
  {
      second.Use((ctx, nxt) =>
      {
          ctx.Request.Path = "/SecondApp" + ctx.Request.Path;
          return nxt();
      });

      second.UseBlazorFrameworkFiles("/SecondApp");
      second.UseStaticFiles();
      second.UseStaticFiles("/SecondApp");
      second.UseRouting();

      second.UseEndpoints(endpoints =>
      {
          endpoints.MapControllers();
          endpoints.MapFallbackToFile("/SecondApp/{*path:nonfile}", 
              "SecondApp/index.html");
      });
  });
  ```

* Sunucu uygulamasının Hava durumu tahmin denetleyicisi 'nde ( `Controllers/WeatherForecastController.cs` ), var olan yolu ( `[Route("[controller]")]` ) `WeatherForecastController` aşağıdaki yollarla değiştirin:

  ```csharp
  [Route("FirstApp/[controller]")]
  [Route("SecondApp/[controller]")]
  ```

  Sunucu uygulamasının yöntemine eklenen ara yazılım, `Startup.Configure` gelen istekleri `/WeatherForecast` `/FirstApp/WeatherForecast` `/SecondApp/WeatherForecast` bağlantı noktasına (5001/5002) veya etki alanına () bağlı olarak ya da öğesine göre değiştirir `firstapp.com` / `secondapp.com` . Sunucu uygulamasından istemci uygulamalarına Hava durumu verilerini döndürmek için önceki denetleyici yolları gereklidir.

### <a name="static-assets-and-class-libraries"></a>Statik varlıklar ve sınıf kitaplıkları

Statik varlıklar için aşağıdaki yaklaşımları kullanın:

* Varlık, istemci uygulamasının `wwwroot` klasöründe olduğunda, kendi yollarını normal olarak sağlayın:

  ```razor
  <img alt="..." src="/{ASSET FILE NAME}" />
  ```

* Varlık `wwwroot` bir [ Razor sınıf kitaplığı (RCL)](xref:blazor/components/class-libraries)klasöründe olduğunda, [RCL makalesindeki](xref:razor-pages/ui-class#consume-content-from-a-referenced-rcl)kılavuzluk uyarınca istemci uygulamasındaki statik varlığa başvurun:

  ```razor
  <img alt="..." src="_content/{LIBRARY NAME}/{ASSET FILE NAME}" />
  ```

<!-- HOLD for reactivation at 5.x

::: moniker range=">= aspnetcore-5.0"

Components provided to a client app by a class library are referenced normally. If any components require stylesheets or JavaScript files, use either of the following approaches to obtain the static assets:

* The client app's `wwwroot/index.html` file can link (`<link>`) to the static assets.
* The component can use the framework's [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) to obtain the static assets.

The preceding approaches are demonstrated in the following examples.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

-->

Bir sınıf kitaplığı tarafından bir istemci uygulamasına verilen bileşenlere normal olarak başvurulur. Herhangi bir bileşen stil sayfaları veya JavaScript dosyaları gerektiriyorsa, istemci uygulamanın `wwwroot/index.html` dosyası doğru statik varlık bağlantılarını içermelidir. Bu yaklaşımlar aşağıdaki örneklerde gösterilmiştir.

<!-- HOLD for reactivation at 5.x

::: moniker-end

-->

Aşağıdaki `Jeep` bileşeni istemci uygulamalarından birine ekleyin. `Jeep`Bileşen şunları kullanır:

* İstemci uygulamanın `wwwroot` klasöründen () bir resim `jeep-cj.png` .
* [Eklenen Razor Bileşen kitaplığı](xref:blazor/components/class-libraries) () `JeepImage` `wwwroot` klasöründen () bir resim `jeep-yj.png` .
* Örnek bileşen ( `Component1` ), kitaplık çözüme eklendiğinde RCL proje şablonu tarafından otomatik olarak oluşturulur `JeepImage` .

```razor
@page "/Jeep"

<h1>1979 Jeep CJ-5&trade;</h1>

<p>
    <img alt="1979 Jeep CJ-5&trade;" src="/jeep-cj.png" />
</p>

<h1>1991 Jeep YJ&trade;</h1>

<p>
    <img alt="1991 Jeep YJ&trade;" src="_content/JeepImage/jeep-yj.png" />
</p>

<p>
    <em>Jeep CJ-5</em> and <em>Jeep YJ</em> are a trademarks of 
    <a href="https://www.fcagroup.com">Fiat Chrysler Automobiles</a>.
</p>

<JeepImage.Component1 />
```

> [!WARNING]
> Görüntülerin sahibi olmadığınız takdirde, taşıtlar görüntülerini **herkese yayımlamayın** . Aksi takdirde, telif hakkı ihlali riski vardır.

<!-- HOLD for reactivation at 5.x

::: moniker range=">= aspnetcore-5.0"

The library's `jeep-yj.png` image can also be added to the library's `Component1` component (`Component1.razor`). To provide the `my-component` CSS class to the client app's page, link to the library's stylesheet using the framework's [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements):

```razor
<div class="my-component">
    <Link href="_content/JeepImage/styles.css" rel="stylesheet" />

    <h1>JeepImage.Component1</h1>

    <p>
        This Blazor component is defined in the <strong>JeepImage</strong> package.
    </p>

    <p>
        <img alt="1991 Jeep YJ&trade;" src="_content/JeepImage/jeep-yj.png" />
    </p>
</div>
```

An alternative to using the [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) is to load the stylesheet from the client app's `wwwroot/index.html` file. This approach makes the stylesheet available to all of the components in the client app:

```html
<head>
    ...
    <link href="_content/JeepImage/styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

-->

Kitaplığın `jeep-yj.png` görüntüsü kitaplığın bileşenine de eklenebilir `Component1` ( `Component1.razor` ):

```razor
<div class="my-component">
    <h1>JeepImage.Component1</h1>

    <p>
        This Blazor component is defined in the <strong>JeepImage</strong> package.
    </p>

    <p>
        <img alt="1991 Jeep YJ&trade;" src="_content/JeepImage/jeep-yj.png" />
    </p>
</div>
```

İstemci uygulamasının dosyası, `wwwroot/index.html` aşağıdaki eklenmiş etikete sahip kitaplığın stil sayfasını ister `<link>` :

```html
<head>
    ...
    <link href="_content/JeepImage/styles.css" rel="stylesheet" />
</head>
```

<!-- HOLD for reactivation at 5.x

::: moniker-end

-->

`Jeep`İstemci uygulamasının bileşeninde bileşene gezinti ekleyin `NavMenu` ( `Shared/NavMenu.razor` ):

```razor
<li class="nav-item px-3">
    <NavLink class="nav-link" href="Jeep">
        <span class="oi oi-list-rich" aria-hidden="true"></span> Jeep
    </NavLink>
</li>
```

RCLs hakkında daha fazla bilgi için bkz.

* <xref:blazor/components/class-libraries>
* <xref:razor-pages/ui-class>

## <a name="standalone-deployment"></a>Tek başına dağıtım

*Tek başına dağıtım* , Blazor WebAssembly uygulamaya doğrudan istemciler tarafından istenen statik dosyalar kümesi olarak hizmet verir. Herhangi bir statik dosya sunucusu, Blazor uygulamayı sunabilir.

Tek başına dağıtım varlıkları `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` klasörüne yayımlanır.

### <a name="azure-app-service"></a>Azure App Service

Blazor WebAssembly uygulamalar, uygulamayı [IIS](#iis)'de barındıran Windows üzerinde Azure Uygulama Hizmetleri 'ne dağıtılabilir.

Tek başına bir Blazor WebAssembly uygulamanın Linux için Azure App Service dağıtımı şu anda desteklenmemektedir. Uygulamayı barındıracak bir Linux sunucu görüntüsü şu anda kullanılamıyor. Bu senaryoyu etkinleştirmek için iş devam ediyor.

### <a name="iis"></a>IIS

IIS, uygulamalar için özellikli bir statik dosya sunucusudur Blazor . IIS 'yi barındıracak şekilde yapılandırmak için Blazor bkz. [IIS 'de statik Web sitesi oluşturma](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).

Yayımlanan varlıklar `/bin/Release/{TARGET FRAMEWORK}/publish` klasöründe oluşturulur. `publish`Web sunucusunda veya barındırma hizmetinde klasörün içeriğini barındırın.

#### <a name="webconfig"></a>web.config

Bir Blazor Proje yayımlandığında, `web.config` aşağıdaki IIS yapılandırmasıyla bir dosya oluşturulur:

* MIME türleri aşağıdaki dosya uzantıları için ayarlanır:
  * `.dll`: `application/octet-stream`
  * `.json`: `application/json`
  * `.wasm`: `application/wasm`
  * `.woff`: `application/font-woff`
  * `.woff2`: `application/font-woff`
* Aşağıdaki MIME türleri için HTTP sıkıştırması etkindir:
  * `application/octet-stream`
  * `application/wasm`
* URL yeniden yazma modülü kuralları oluşturuldu:
  * Uygulamanın statik varlıklarının bulunduğu alt dizini ( `wwwroot/{PATH REQUESTED}` ) sunar.
  * Dosya olmayan varlıklar için isteklerin statik varlıklar klasöründe () uygulamanın varsayılan belgesine yeniden yönlendirilmesi için SPA geri dönüş yönlendirmesi oluşturun `wwwroot/index.html` .
  
#### <a name="use-a-custom-webconfig"></a>Özel bir web.config kullanma

Özel bir dosya kullanmak için `web.config` , özel `web.config` dosyayı proje klasörünün köküne yerleştirin. Projeyi uygulamanın proje dosyasında kullanarak IIS 'e özgü varlıkları yayımlayacak şekilde yapılandırın `PublishIISAssets` ve projeyi yayımlayın:

```xml
<PropertyGroup>
  <PublishIISAssets>true</PublishIISAssets>
</PropertyGroup>
```

#### <a name="install-the-url-rewrite-module"></a>URL yeniden yazma modülünü yükler

[URL yeniden yazma modülü](https://www.iis.net/downloads/microsoft/url-rewrite) , URL 'leri yeniden yazmak için gereklidir. Modül varsayılan olarak yüklenmez ve bir Web sunucusu (IIS) rol hizmeti özelliği olarak yükleme için kullanılamaz. Modül IIS Web sitesinden indirilmelidir. Modülünü yüklemek için Web Platformu Yükleyicisi 'ni kullanın:

1. Yerel olarak, [URL yeniden yazma modülü İndirmeleri sayfasına](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads)gidin. Ingilizce sürümünde, WebPI yükleyicisini indirmek için **WebPI** ' ı seçin. Diğer diller için, yükleyiciyi indirmek üzere sunucu için uygun mimariyi (x86/x64) seçin.
1. Yükleyiciyi sunucuya kopyalayın. Yükleyiciyi çalıştırın. , **Install** düğmesini seçin ve lisans koşullarını kabul edin. Yüklemesi tamamlandıktan sonra sunucu yeniden başlatması gerekli değildir.

#### <a name="configure-the-website"></a>Web sitesini yapılandırma

Web sitesinin **fiziksel yolunu** uygulamanın klasörüne ayarlayın. Klasör şunları içerir:

* `web.config`Web sitesini yapılandırmak için gereken yeniden yönlendirme kuralları ve dosya içerik türleri dahil olmak üzere IIS tarafından kullanılan dosya.
* Uygulamanın statik varlık klasörü.

#### <a name="host-as-an-iis-sub-app"></a>IIS alt uygulaması olarak barındırma

Tek başına bir uygulama bir IIS alt uygulaması olarak barındırılıyorsa, aşağıdakilerden birini yapın:

* Devralınan ASP.NET Core modülü işleyicisini devre dışı bırakın.

  Blazor `web.config` Dosyaya bir bölüm ekleyerek uygulamanın yayımlanmış dosyasındaki işleyiciyi kaldırın `<handlers>` :

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* Şu `<system.webServer>` şekilde ayarlanmış bir öğe kullanarak kök (üst) uygulamanın devralınmasını devre dışı bırakın `<location>` `inheritInChildApplications` `false` :

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <configuration>
    <location path="." inheritInChildApplications="false">
      <system.webServer>
        <handlers>
          <add name="aspNetCore" ... />
        </handlers>
        <aspNetCore ... />
      </system.webServer>
    </location>
  </configuration>
  ```

İşleyicinin kaldırılması veya devralma devre dışı bırakılması, [uygulamanın temel yolunun yapılandırılmasına](xref:blazor/host-and-deploy/index#app-base-path)ek olarak gerçekleştirilir. Uygulamanın dosyasındaki uygulama temel yolunu, `index.html` IIS 'de alt uygulamayı yapılandırırken kullanılan IIS diğer adına ayarlayın.

#### <a name="brotli-and-gzip-compression"></a>Brotli ve gzip sıkıştırması

*Bu bölüm yalnızca tek başına uygulamalar için geçerlidir Blazor WebAssembly . barındırılan Blazor uygulamalar `web.config` , bu bölümde bağlantılı dosyayı değil, varsayılan bir ASP.NET Core uygulama dosyası kullanır.*

IIS, `web.config` Blazor tek başına uygulamalar için Brotli veya gzip ile sıkıştırılmış varlıklar sunacak şekilde yapılandırılabilir Blazor WebAssembly . Örnek bir yapılandırma dosyası için bkz [`web.config`](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/host-and-deploy/webassembly/_samples/web.config?raw=true) ..

Örnek dosyanın ek yapılandırması `web.config` aşağıdaki senaryolarda gerekli olabilir:

* Uygulamanın belirtimi aşağıdakilerden birini çağırır:
  * Örnek dosya tarafından yapılandırılmayan sıkıştırılmış dosyalara hizmet sunma `web.config` .
  * Sıkıştırılmış dosyalara, sıkıştırılmamış biçimde örnek dosya tarafından yapılandırılmış olarak sunma `web.config` .
* Sunucunun IIS yapılandırması (örneğin, `applicationHost.config` ) sunucu DÜZEYINDE IIS Varsayılanları sağlar. Sunucu düzeyindeki yapılandırmaya bağlı olarak, uygulama örnek dosyanın içerenden farklı bir IIS yapılandırması gerektirebilir `web.config` .

#### <a name="troubleshooting"></a>Sorun giderme

*500-Iç sunucu hatası* ALıNMıŞSA ve IIS Yöneticisi Web sitesinin yapılandırmasına erişmeye çalışırken hatalar OLUŞTURURSA, URL yeniden yazma modülünün yüklü olduğunu doğrulayın. Modül yüklü olmadığında, `web.config` Dosya IIS tarafından ayrıştırılamaz. Bu, IIS yöneticisinin Web sitesinin yapılandırmasını ve Web sitesini, statik dosyaları hizmet olarak yüklemesini engeller Blazor .

IIS ile dağıtım sorunlarını giderme hakkında daha fazla bilgi için bkz <xref:test/troubleshoot-azure-iis> ..

### <a name="azure-storage"></a>Azure Storage

[Azure depolama](/azure/storage/) statik dosya barındırma, sunucusuz Blazor uygulama barındırmayı sağlar. Özel etki alanı adları, Azure Content Delivery Network (CDN) ve HTTPS desteklenir.

Blob hizmeti bir depolama hesabında barındırılan statik Web sitesi için etkinleştirildiğinde:

* **Dizin belgesi adını** olarak ayarlayın `index.html` .
* **Hata belge yolunu** olarak ayarlayın `index.html` . Razor bileşenler ve diğer dosya olmayan uç noktaları, blob hizmeti tarafından depolanan statik içerikte fiziksel yollarda yer vermez. Yönlendiricinin işlemesi gereken bu kaynaklardan birine yönelik bir istek alındığında Blazor , blob hizmeti tarafından oluşturulan *404-bulunamayan* hata, isteği **hata belge yoluna** yönlendirir. `index.html`BLOB döndürülür ve Blazor yönlendirici yolu yükler ve işler.

Dosyalar ' üst bilgilerinde uygunsuz MIME türleri nedeniyle çalışma zamanında dosya yüklenmemişse `Content-Type` , aşağıdaki eylemlerden birini gerçekleştirin:

* Araçlar, dosyalar dağıtıldığında doğru MIME türlerini (üstbilgiler) ayarlamak üzere yapılandırın `Content-Type` .
* `Content-Type`Uygulama dağıtıldıktan sonra dosyalar IÇIN MIME türlerini (üstbilgiler) değiştirin.

  Her dosya için Depolama Gezgini (Azure portal):
  
  1. Dosyaya sağ tıklayın ve **Özellikler**' i seçin.
  1. **ContentType** ' ı ayarlayın ve **Kaydet** düğmesini seçin.

Daha fazla bilgi için bkz. [Azure Storage 'Da statik Web sitesi barındırma](/azure/storage/blobs/storage-blob-static-website).

### <a name="nginx"></a>Nginx

Aşağıdaki `nginx.conf` Dosya, NGINX 'in `index.html` diskteki karşılık gelen bir dosyayı bulamadığı her seferinde dosyayı göndermek üzere nasıl yapılandırılacağını göstermek için basitleştirilmiştir.

```
events { }
http {
    server {
        listen 80;

        location / {
            root      /usr/share/nginx/html;
            try_files $uri $uri/ /index.html =404;
        }
    }
}
```

[NGINX veri bloğu hızı sınırı](https://www.nginx.com/blog/rate-limiting-nginx/#bursts) ile ayarlandığında [`limit_req`](https://nginx.org/docs/http/ngx_http_limit_req_module.html#limit_req) , Blazor WebAssembly uygulamalar `burst` bir uygulama tarafından yapılan görece çok sayıda isteği karşılamak için büyük bir parametre değeri gerektirebilir. Başlangıçta değeri en az 60 olarak ayarlayın:

```
http {
    server {
        ...

        location / {
            ...

            limit_req zone=one burst=60 nodelay;
        }
    }
}
```

Tarayıcı geliştirici araçları veya ağ trafiği Aracı, isteklerin *503 servis dışı* bir durum kodu aldığını gösteriyorsa değeri artırın.

Üretim NGINX web sunucusu yapılandırması hakkında daha fazla bilgi için bkz. [NGINX Plus ve NGINX yapılandırma dosyaları oluşturma](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).

### <a name="apache"></a>Apache

Bir Blazor WebAssembly uygulamayı CentOS 7 veya sonraki bir sürüme dağıtmak için:

1. Apache yapılandırma dosyasını oluşturun. Aşağıdaki örnek, Basitleştirilmiş bir yapılandırma dosyasıdır ( `blazorapp.config` ):

   ```
   <VirtualHost *:80>
       ServerName www.example.com
       ServerAlias *.example.com

       DocumentRoot "/var/www/blazorapp"
       ErrorDocument 404 /index.html

       AddType application/wasm .wasm
       AddType application/octet-stream .dll
   
       <Directory "/var/www/blazorapp">
           Options -Indexes
           AllowOverride None
       </Directory>

       <IfModule mod_deflate.c>
           AddOutputFilterByType DEFLATE text/css
           AddOutputFilterByType DEFLATE application/javascript
           AddOutputFilterByType DEFLATE text/html
           AddOutputFilterByType DEFLATE application/octet-stream
           AddOutputFilterByType DEFLATE application/wasm
           <IfModule mod_setenvif.c>
           BrowserMatch ^Mozilla/4 gzip-only-text/html
           BrowserMatch ^Mozilla/4.0[678] no-gzip
           BrowserMatch bMSIE !no-gzip !gzip-only-text/html
       </IfModule>
       </IfModule>

       ErrorLog /var/log/httpd/blazorapp-error.log
       CustomLog /var/log/httpd/blazorapp-access.log common
   </VirtualHost>
   ```

1. Apache yapılandırma dosyasını `/etc/httpd/conf.d/` , CentOS 7 ' de varsayılan Apache yapılandırma dizini olan dizine yerleştirin.

1. Uygulamanın dosyalarını `/var/www/blazorapp` dizine yerleştirin ( `DocumentRoot` yapılandırma dosyasında belirtilen konum).

1. Apache hizmetini yeniden başlatın.

Daha fazla bilgi için bkz [`mod_mime`](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) [`mod_deflate`](https://httpd.apache.org/docs/current/mod/mod_deflate.html) . ve.

### <a name="github-pages"></a>GitHub Pages

URL yeniden işlemesini işlemek için, `wwwroot/404.html` isteği sayfaya yönlendirmeyi işleyen bir betiği olan bir dosya ekleyin `index.html` . Bir örnek için bkz. [Stevesandersonms/ Blazor ongithubpages GitHub deposu](https://github.com/SteveSandersonMS/BlazorOnGitHubPages):

* [`wwwroot/404.html`](https://github.com/SteveSandersonMS/BlazorOnGitHubPages/blob/master/wwwroot/404.html)
* [Canlı site](https://stevesandersonms.github.io/BlazorOnGitHubPages/))

Bir kuruluş sitesi yerine bir proje sitesi kullanırken, `<base>` içindeki etiketini güncelleştirin `wwwroot/index.html` . `href`Öznitelik değerini GitHub deposu adına sondaki eğik çizgiyle (örneğin, `/my-repository/` ) ayarlayın. [Stevesandersonms/ Blazor ongithubpages GitHub deposunda](https://github.com/SteveSandersonMS/BlazorOnGitHubPages), temel `href` [ `.github/workflows/main.yml` yapılandırma dosyası](https://github.com/SteveSandersonMS/BlazorOnGitHubPages/blob/master/.github/workflows/main.yml)tarafından yayımlama sırasında güncelleştirilir.

> [!NOTE]
> [Stevesandersonms/ Blazor ongithubpages GitHub deposu](https://github.com/SteveSandersonMS/BlazorOnGitHubPages) , .net Foundation veya Microsoft tarafından sahip değil, korunmuyor veya desteklenmiyor.

## <a name="host-configuration-values"></a>Ana bilgisayar yapılandırma değerleri

[ Blazor WebAssembly uygulamalar](xref:blazor/hosting-models#blazor-webassembly) , geliştirme ortamındaki çalışma zamanında komut satırı bağımsız değişkenleri olarak aşağıdaki ana bilgisayar yapılandırma değerlerini kabul edebilir.

### <a name="content-root"></a>İçerik kökü

`--contentroot`Bağımsız değişkeni, uygulamanın içerik dosyalarını ([içerik kökü](xref:fundamentals/index#content-root)) içeren dizinin mutlak yolunu ayarlar. Aşağıdaki örneklerde, `/content-root-path` uygulamanın içerik kök yolu bulunur.

* Uygulamayı bir komut isteminde yerel olarak çalıştırırken bağımsız değişkenini geçirin. Uygulamanın dizininden şunu yürütün:

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* IIS Express profilindeki uygulamanın dosyasına bir giriş ekleyin `launchSettings.json` .  Bu ayar, uygulama Visual Studio hata ayıklayıcısı ve ile bir komut isteminden çalıştırıldığında kullanılır `dotnet run` .

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* Visual Studio 'da, **Özellikler**  >  **hata ayıklama**  >  **uygulama bağımsız değişkenlerinde** bağımsız değişkenini belirtin. Visual Studio özellik sayfasında bağımsız değişkeni ayarlamak, bağımsız değişkenini `launchSettings.json` dosyaya ekler.

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a>Yol tabanı

`--pathbase`Bağımsız değişkeni, kök olmayan göreli BIR URL yoluyla yerel olarak çalışan bir uygulamanın uygulama temeli yolunu ayarlar ( `<base>` etiket, `href` `/` hazırlama ve üretim için dışında bir yola ayarlanır). Aşağıdaki örneklerde, `/relative-URL-path` uygulamanın yol tabanı bulunur. Daha fazla bilgi için bkz. [uygulama temel yolu](xref:blazor/host-and-deploy/index#app-base-path).

> [!IMPORTANT]
> Etiketinde belirtilen yolun aksine `href` `<base>` , `/` bağımsız değişken değeri geçirilirken sondaki eğik çizgi () eklemeyin `--pathbase` . Etikette uygulama temel yolu `<base>` `<base href="/CoolApp/">` (sondaki eğik çizgi içeriyorsa) olarak sağlanmışsa, komut satırı bağımsız değişken değerini `--pathbase=/CoolApp` (sondaki eğik çizgi yok) olarak geçirin.

* Uygulamayı bir komut isteminde yerel olarak çalıştırırken bağımsız değişkenini geçirin. Uygulamanın dizininden şunu yürütün:

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* IIS Express profilindeki uygulamanın dosyasına bir giriş ekleyin `launchSettings.json` .  Bu ayar, uygulamayı Visual Studio hata ayıklayıcıyla ve ile bir komut isteminden çalıştırırken kullanılır `dotnet run` .

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* Visual Studio 'da, **Özellikler**  >  **hata ayıklama**  >  **uygulama bağımsız değişkenlerinde** bağımsız değişkenini belirtin. Visual Studio özellik sayfasında bağımsız değişkeni ayarlamak, bağımsız değişkenini `launchSettings.json` dosyaya ekler.

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a>URL’ler

`--urls`Bağımsız değişkeni, istekler için dinlemek üzere bağlantı noktaları ve protokollerle IP adreslerini veya konak adreslerini ayarlar.

* Uygulamayı bir komut isteminde yerel olarak çalıştırırken bağımsız değişkenini geçirin. Uygulamanın dizininden şunu yürütün:

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* IIS Express profilindeki uygulamanın dosyasına bir giriş ekleyin `launchSettings.json` .  Bu ayar, uygulamayı Visual Studio hata ayıklayıcıyla ve ile bir komut isteminden çalıştırırken kullanılır `dotnet run` .

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* Visual Studio 'da, **Özellikler**  >  **hata ayıklama**  >  **uygulama bağımsız değişkenlerinde** bağımsız değişkenini belirtin. Visual Studio özellik sayfasında bağımsız değişkeni ayarlamak, bağımsız değişkenini `launchSettings.json` dosyaya ekler.

  ```console
  --urls=http://127.0.0.1:0
  ```

::: moniker range=">= aspnetcore-5.0"

## <a name="configure-the-trimmer"></a>Kesiciyi yapılandırma

Blazor çıkış derlemelerinden gereksiz Il 'yi kaldırmak için her sürüm derlemesinde ara dil (IL) kırpılmasını gerçekleştirir. Daha fazla bilgi için bkz. <xref:blazor/host-and-deploy/configure-trimmer>.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

## <a name="configure-the-linker"></a>Bağlayıcıyı yapılandırma

Blazor çıkış derlemelerinden gereksiz Il 'yi kaldırmak için her sürüm derlemesinde ara dil (IL) bağlamayı gerçekleştirir. Daha fazla bilgi için bkz. <xref:blazor/host-and-deploy/configure-linker>.

::: moniker-end

## <a name="custom-boot-resource-loading"></a>Özel önyükleme kaynağı yükleme

Blazor WebAssemblyUygulama, `loadBootResource` yerleşik önyükleme kaynağı yükleme mekanizmasını geçersiz kılmak için işleviyle başlatılabilir. `loadBootResource`Aşağıdaki senaryolar için kullanın:

* Kullanıcıların saat dilimi verileri veya bir CDN 'den statik kaynakları yüklemesine izin verin `dotnet.wasm` .
* Bir HTTP isteği kullanarak sıkıştırılmış derlemeler yükleyin ve sunucudan sıkıştırılmış içerik getirmeyi desteklemeyen konaklar için istemcide sıkıştırmasını açın.
* Her isteği yeni bir ada yönlendirerek farklı bir ada diğer ad kaynakları `fetch` .

`loadBootResource` Parametreler aşağıdaki tabloda görüntülenir.

| Parametre    | Açıklama |
| ------------ | ----------- |
| `type`       | Kaynağın türü. İzinleri olan türler: `assembly` , `pdb` , `dotnetjs` , `dotnetwasm` , `timezonedata` |
| `name`       | Kaynağın adı. |
| `defaultUri` | Kaynağın göreli veya mutlak URI 'SI. |
| `integrity`  | Yanıtta beklenen içeriği temsil eden bütünlük dizesi. |

`loadBootResource` yükleme işlemini geçersiz kılmak için aşağıdakilerden herhangi birini döndürür:

* URI dizesi. Aşağıdaki örnekte ( `wwwroot/index.html` ), aşağıdaki dosyalar ' de BIR CDN 'den sunulur `https://my-awesome-cdn.com/` :

  * `dotnet.*.js`
  * `dotnet.wasm`
  * Saat dilimi verileri

  ```html
  ...

  <script src="_framework/blazor.webassembly.js" autostart="false"></script>
  <script>
    Blazor.start({
      loadBootResource: function (type, name, defaultUri, integrity) {
        console.log(`Loading: '${type}', '${name}', '${defaultUri}', '${integrity}'`);
        switch (type) {
          case 'dotnetjs':
          case 'dotnetwasm':
          case 'timezonedata':
            return `https://my-awesome-cdn.com/blazorwebassembly/3.2.0/${name}`;
        }
      }
    });
  </script>
  ```

* `Promise<Response>`. `integrity`Varsayılan bütünlük denetimi davranışını korumak için parametreyi bir üstbilgiye geçirin.

  Aşağıdaki örnek ( `wwwroot/index.html` ) giden isteklere özel bır http üst bilgisi ekler ve `integrity` parametresini `fetch` çağrıya geçirir:
  
  ```html
  <script src="_framework/blazor.webassembly.js" autostart="false"></script>
  <script>
    Blazor.start({
      loadBootResource: function (type, name, defaultUri, integrity) {
        return fetch(defaultUri, { 
          cache: 'no-cache',
          integrity: integrity,
          headers: { 'MyCustomHeader': 'My custom value' }
        });
      }
    });
  </script>
  ```

* `null`/`undefined`Bu, varsayılan yükleme davranışına neden olur.

Dış kaynaklar, tarayıcılar arası kaynak yüklemeye izin vermek için gereken CORS üst bilgilerini döndürmelidir. CDNs, genellikle gerekli üst bilgileri varsayılan olarak sağlar.

Yalnızca özel davranışlar için türleri belirtmeniz yeterlidir. İçin belirtilmemiş türler `loadBootResource` , varsayılan yükleme davranışları başına Framework tarafından yüklenir.

## <a name="change-the-filename-extension-of-dll-files"></a>DLL dosyalarının dosya adı uzantısını değiştirme

Uygulamanın yayımlanmış dosyalarının dosya adı uzantılarını değiştirmeniz gerekiyorsa `.dll` , bu bölümdeki yönergeleri izleyin.

Uygulamayı yayımladıktan sonra, `.dll` farklı bir dosya uzantısı kullanmak üzere dosyaları yeniden adlandırmak için bir kabuk betiği veya DevOps derleme işlem hattı kullanın. `.dll` `wwwroot` Uygulamanın yayınlanan çıktısının dizinindeki dosyaları hedefleyin (örneğin, `{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot` ).

Aşağıdaki örneklerde `.dll` Dosyalar dosya uzantısını kullanacak şekilde yeniden adlandırılır `.bin` .

Windows'da:

```powershell
dir .\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content .\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content .\_framework\blazor.boot.json
```

Hizmet çalışanı varlıkları da kullanılıyorsa, aşağıdaki komutu ekleyin:

```powershell
((Get-Content .\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content .\service-worker-assets.js
```

Linux veya macOS 'ta:

```console
for f in _framework/_bin/*; do mv "$f" "`echo $f | sed -e 's/\.dll/.bin/g'`"; done
sed -i 's/\.dll"/.bin"/g' _framework/blazor.boot.json
```

Hizmet çalışanı varlıkları da kullanılıyorsa, aşağıdaki komutu ekleyin:

```console
sed -i 's/\.dll"/.bin"/g' service-worker-assets.js
```
   
Farklı bir dosya uzantısını kullanmak için `.bin` `.bin` Yukarıdaki komutlarda değiştirin.

Sıkıştırılmış `blazor.boot.json.gz` ve dosyaları çözmek için `blazor.boot.json.br` aşağıdaki yaklaşımlardan birini benimseyin:

* Sıkıştırılmış `blazor.boot.json.gz` ve dosyaları kaldırın `blazor.boot.json.br` . Sıkıştırma bu yaklaşım ile devre dışı bırakıldı.
* Güncelleştirilmiş dosyayı yeniden sıkıştırın `blazor.boot.json` .

Yukarıdaki kılavuz, hizmet çalışanı varlıkları kullanımda olduğunda da geçerlidir. Ve ' i kaldırın veya yeniden sıkıştırın `wwwroot/service-worker-assets.js.br` `wwwroot/service-worker-assets.js.gz` . Aksi halde, tarayıcıda dosya bütünlüğü denetimleri başarısız olur.

Aşağıdaki Windows örneği, projenin köküne yerleştirilmiş bir PowerShell betiği kullanır.

`ChangeDLLExtensions.ps1:`:

```powershell
param([string]$filepath,[string]$tfm)
dir $filepath\bin\Release\$tfm\wwwroot\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json
Remove-Item $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json.gz
```

Hizmet çalışanı varlıkları da kullanılıyorsa, aşağıdaki komutu ekleyin:

```powershell
((Get-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js
```

Proje dosyasında, komut dosyası uygulama yayımlandıktan sonra çalıştırılır:

```xml
<Target Name="ChangeDLLFileExtensions" AfterTargets="Publish" Condition="'$(Configuration)'=='Release'">
  <Exec Command="powershell.exe -command &quot;&amp; { .\ChangeDLLExtensions.ps1 '$(SolutionDir)' '$(TargetFramework)'}&quot;" />
</Target>
```

> [!NOTE]
> Aynı derlemeleri yeniden adlandırırken ve geç yüklerken, içindeki kılavuza bakın <xref:blazor/webassembly-lazy-load-assemblies#onnavigateasync-events-and-renamed-assembly-files> .

## <a name="resolve-integrity-check-failures"></a>Bütünlük denetimi başarısızlıklarını çözümleyin

Blazor WebAssemblyUygulamanın başlangıç dosyalarını indirdiğinde, tarayıcıya yanıtlar üzerinde bütünlük denetimleri gerçekleştirmesini söyler. `blazor.boot.json` `.dll` , Ve diğer dosyalar IÇIN beklenen SHA-256 karma değerlerini belirtmek için dosyadaki bilgileri kullanır `.wasm` . Bu, aşağıdaki nedenlerle faydalıdır:

* Örneğin, Kullanıcı uygulama dosyalarını indirme sürecinde olduğunda, Web sunucunuza yeni bir dağıtım uygulanmışsa, tutarsız bir dosya kümesini yükleme riskini almanızı sağlar. Tutarsız dosyalar tanımsız davranışa yol açabilir.
* Kullanıcının tarayıcısının hiçbir şekilde tutarsız veya geçersiz yanıtları önbelleklemesini sağlar, bu da sayfayı el ile yenilese bile uygulamayı başlatmalarını engelleyebilir.
* Yanıtları önbelleğe alma işlemini güvenli hale getirir ve beklenen SHA-256 karmaları değişene kadar sunucu tarafı değişikliklerini kontrol etmez, sonraki sayfa yüklemeleri daha az istek içerir ve çok daha hızlı tamamlanır.

Web sunucunuz beklenen SHA-256 karmalarıyla eşleşmeyen yanıtlar döndürürse, tarayıcının geliştirici konsolunda aşağıdakine benzer bir hata görürsünüz:

> https://myapp.example.com/\_framework/My Blazor Hesaplanan SHA-256 bütünlüğü ' IIa70iwvmEg5WiDV17OpQ5eCztNYqL186J56852RpJY = ' ile 'App.dll ' kaynağı için ' Integrity ' özniteliğinde geçerli bir Özet bulunamadı. Kaynak engellendi.

Çoğu durumda bu, bütünlük denetimi ile ilgili bir sorun *değildir* . Bunun yerine, başka bir sorun olduğu anlamına gelir ve bu sorunu gidermek için bütünlük denetimi size uyarı verebilir.

### <a name="diagnosing-integrity-problems"></a>Bütünlük sorunlarını tanılama

Bir uygulama oluşturulduğunda, oluşturulan `blazor.boot.json` bildirim, derleme çıktısının oluşturulduğu sırada önyükleme KAYNAKLARıNıZıN SHA-256 karmalarını (örneğin,, `.dll` `.wasm` ve diğer dosyaları) açıklar. ' Deki SHA-256 karmaları `blazor.boot.json` tarayıcıya teslim edilen dosyalarla eşleştiği sürece bütünlük denetimi geçirilir.

Bunun başarısız olmasının yaygın nedenleri:

 * Web sunucusunun yanıtı, tarayıcı istenen dosya yerine bir hatadır (örneğin, bir *404-bulunamadı* veya *500-Internal Server Error*). Bu, tarayıcı tarafından bir bütünlük denetimi hatası olarak bildirilir ve yanıt hatası olarak değildir.
 * Dosya içeriğini tarayıcıya, dosyaların oluşturulması ve teslimi arasında değiştiren bir sorun. Bu durum oluşabilir:
   * Ya da yapı araçları derleme çıkışını el ile değiştirir.
   * Dağıtım işleminin bazı bir yönü dosyaları değiştirdiyseniz. Örneğin, git tabanlı bir dağıtım mekanizması kullanıyorsanız, Windows üzerinde dosya oluşturup Linux 'ta kullanıma alırsanız git 'in Windows stili satır sonlarını şeffaf olarak UNIX stili satır sonlarına dönüştürdüğünü göz önünde bulundurun. Dosya satır sonlarını değiştirmek için SHA-256 karmaları değiştirin. Bu sorundan kaçınmak için, [ `.gitattributes` Yapı yapıtlarını `binary` dosya olarak değerlendirmek üzere](https://git-scm.com/book/en/v2/Customizing-Git-Git-Attributes)kullanmayı düşünün.
   * Web sunucusu, bu dosyaları sunma bölümünde dosya içeriğini değiştirir. Örneğin, bazı içerik dağıtım ağları (CDNs) otomatik olarak HTML 'yi [küçültmeye](xref:client-side/bundling-and-minification#minification) çalışır ve bu sayede onu değiştirir. Bu tür özellikleri devre dışı bırakmanız gerekebilir.

Bu durumun hangi durumlarda geçerli olduğunu tanılamak için:

 1. Hatanın hata iletisini okuyarak tetikleneceği dosyayı aklınızda bulunur.
 1. Tarayıcınızın geliştirici araçlarını açın ve *ağ* sekmesine bakın. Gerekirse, istek ve yanıtların listesini görmek için sayfayı yeniden yükleyin. Bu listedeki hatayı tetikleyen dosyayı bulun.
 1. Yanıttaki HTTP durum kodunu denetleyin. Sunucu *200-Tamam* (veya başka bir 2xx durum kodu) dışında bir şey döndürürse, tanılamaya yönelik bir sunucu tarafı sorununuz vardır. Örneğin, 403 durum kodu bir yetkilendirme sorunu olduğu anlamına gelirken durum kodu 500, sunucunun belirtilmeyen bir şekilde başarısız olduğu anlamına gelir. Uygulamayı tanılamak ve onarmak için sunucu tarafı günlüklerine başvurun.
 1. Durum kodu kaynak için *200-Tamam* ise, tarayıcının geliştirici araçlarındaki yanıt içeriğine bakın ve içeriğin beklenen verilerle eşleşip eşleşmediğini denetleyin. Örneğin, yaygın bir sorun, isteklerin `index.html` diğer dosyalar için bile verilerinizi döndürmesi için yönlendirmeyi yanlış yapılandırmaktır. `.wasm`İsteklerin yanıtlarının, Weelsembly ikilileriyle ve `.dll` isteklerin yanıtlarının .NET Derleme ikili dosyalarına sahip olduğundan emin olun. Aksi takdirde, tanılamaya yönelik bir sunucu tarafı yönlendirme sorununuz vardır.
 1. [Sorun giderme bütünlüğü PowerShell betiği](#troubleshoot-integrity-powershell-script)ile uygulamanın yayımlanmış ve dağıtılan çıkışını doğrulamak için arama yapın.

Sunucunun sürekli doğru verileri döndürdüğünü onaylamak için, dosyanın oluşturulması ve teslimi arasındaki içerikleri değiştirmek için başka bir şey olması gerekir. Bunu araştırmak için:

 * Dosyalar derlendikten sonra dosyaları değiştirirken derleme araç zinciri ve dağıtım mekanizmasını inceleyin. Git 'in, daha önce açıklandığı gibi dosya satır sonlarını dönüştürerken buna bir örnektir.
 * Yanıtları dinamik olarak değiştirmek üzere ayarlanmış olmaları durumunda Web sunucusunu veya CDN yapılandırmasını inceleyin (örneğin, HTML 'yi küçültmeye çalışırken). Web sunucusunun HTTP sıkıştırması uygulaması (örneğin, döndürme `content-encoding: br` veya döndürme `content-encoding: gzip` ), bu nedenle bu, sıkıştırmayı açma işleminden sonra sonucu etkilemez. Ancak, Web sunucusunun sıkıştırılmamış verileri değiştirmesi iyi *değildir* .

### <a name="troubleshoot-integrity-powershell-script"></a>Bütünlük PowerShell betiği sorunlarını giderme

[`integrity.ps1`](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/host-and-deploy/webassembly/_samples/integrity.ps1?raw=true)Yayımlanmış ve dağıtılan bir uygulamayı doğrulamak için PowerShell betiğini kullanın Blazor . Komut dosyası, uygulamanın, Blazor Framework 'ün tanımlayamıyorum bütünlük sorunları olduğunda bir başlangıç noktası olarak sağlanır. Uygulamanız için betiğin özelleştirilmesi gerekebilir.

Betik, klasördeki dosyaları denetler `publish` ve bütünlük karmalarını içeren farklı bildirimlerde sorunları algılamak için dağıtılan uygulamadan indirilir. Bu denetimler en yaygın sorunları algılamamalıdır:

* Yayımlanan çıktıda bir dosyayı gerçekleştirmeden değiştirdiniz.
* Uygulama, dağıtım hedefine doğru şekilde dağıtılmadı veya dağıtım hedefinin ortamında bir değişiklik yapılamayabilir.
* Dağıtılan uygulamayla çıkış arasında uygulamanın yayımlaması arasında farklılıklar vardır.

PowerShell komut kabuğu 'nda aşağıdaki komutla betiği çağırın:

```powershell
.\integrity.ps1 {BASE URL} {PUBLISH OUTPUT FOLDER}
```

İçeremez

* `{BASE URL}`: Dağıtılan uygulamanın URL 'SI.
* `{PUBLISH OUTPUT FOLDER}`: Uygulamanın `publish` dağıtım için yayımlandığı klasörün veya konumun yolu.

> [!NOTE]
> `dotnet/AspNetCore.Docs`GitHub deposunu [BitDefender](https://www.bitdefender.com) virüs tarayıcısını kullanan bir sisteme kopyalamak için, komut dosyası için BitDefender 'a bir özel durum ekleyin `integrity.ps1` . Betiği, virüs tarayıcısı tarafından karantinaya almadan önce depoyu kopyalamadan önce BitDefender ' a özel durum ekleyin. Aşağıdaki örnek, bir Windows sistemindeki kopyalanmış depo için bir komut dosyasının tipik yoludur. Yolu gereken şekilde ayarlayın. Yer tutucu, `{USER}` kullanıcının yol segmentinde yer tutucudur.
>
> ```
> C:\Users\{USER}\Documents\GitHub\AspNetCore.Docs\aspnetcore\blazor\host-and-deploy\webassembly\_samples\integrity.ps1
> ```

### <a name="disable-integrity-checking-for-non-pwa-apps"></a>PWA olmayan uygulamalar için bütünlük denetimini devre dışı bırakma

Çoğu durumda, bütünlük denetimini devre dışı bırakın. Bütünlük denetimini devre dışı bırakmak, beklenmeyen yanıtlara neden olan temeldeki sorunu çözmez ve daha önce listelenen avantajları kaybetme sonucunu vermez.

Web sunucusunun tutarlı yanıtlar döndürecek şekilde güvenmeme, ancak hiçbir seçim olmadığı ancak bütünlük denetimlerini devre dışı bırakabileceği durumlar olabilir. Bütünlük denetimlerini devre dışı bırakmak için aşağıdakini proje dosyasındaki bir özellik grubuna ekleyin Blazor WebAssembly `.csproj` :

```xml
<BlazorCacheBootResources>false</BlazorCacheBootResources>
```

`BlazorCacheBootResources`Blazor `.dll` , `.wasm` özelliği SHA-256 karmalarını temel alarak,, ve diğer dosyaları önbelleğe almanın varsayılan davranışını devre dışı bırakır çünkü özellik SHA-256 karmalarını doğruluk açısından güvenemediğini belirtir. Bu ayar ile birlikte, tarayıcının normal HTTP önbelleği bu dosyaları önbelleğe almaya devam edebilir, ancak bunun gerçekleşmediğine bakılmaksızın Web sunucusu yapılandırmanıza ve `cache-control` hizmet verdiği üstbilgilere göre değişir.

> [!NOTE]
> `BlazorCacheBootResources`Özelliği, [aşamalı Web uygulamaları (PWAs)](xref:blazor/progressive-web-app)için bütünlük denetimlerini devre dışı bırakmıyor. PWAs ile ilgili rehberlik için [PWAs için bütünlük denetimini devre dışı bırakma](#disable-integrity-checking-for-pwas) bölümüne bakın.

### <a name="disable-integrity-checking-for-pwas"></a>PWAs için bütünlük denetimini devre dışı bırak

BlazorAşamalı Web uygulaması (PWA) şablonu, `service-worker.published.js` çevrimdışı kullanım için uygulama dosyalarını getirmekten ve saklamaktan sorumlu önerilen bir dosya içerir. Bu, normal uygulama başlangıç mekanizmasından ayrı bir işlemdir ve kendi ayrı bütünlük denetimi mantığına sahiptir.

Dosya içinde `service-worker.published.js` Aşağıdaki satır mevcuttur:

```javascript
.map(asset => new Request(asset.url, { integrity: asset.hash }));
```

Bütünlük denetimini devre dışı bırakmak için, `integrity` satırı aşağıdaki şekilde değiştirerek parametreyi kaldırın:

```javascript
.map(asset => new Request(asset.url));
```

Daha sonra, bütünlük denetimini devre dışı bırakmak, bütünlük denetimi tarafından sunulan güvenlik garantisi kaybetmeniz anlamına gelir. Örneğin, kullanıcının tarayıcısı uygulamayı yeni bir sürümünü dağıttığınız andan itibaren önbelleğe alıyorsa, eski dağıtımdan ve bazıları yeni dağıtımdan bazı dosyaları önbelleğe alabilir, bu da bir risk vardır. Bu durumda, daha fazla güncelleştirme dağıtana kadar uygulama bozuk bir durumda takılmasına neden olur.
