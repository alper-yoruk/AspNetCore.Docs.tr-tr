---
title: PreRender ve ASP.NET Core Razor bileşenleri tümleştirme
author: guardrex
description: Razor Blazor Sunucu üzerindeki bileşenler prerendering dahil olmak üzere uygulamalar için bileşen tümleştirme senaryoları hakkında bilgi edinin Razor .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/29/2020
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
uid: blazor/components/prerendering-and-integration
zone_pivot_groups: blazor-hosting-models
ms.openlocfilehash: 3402117334548f9d90880d4f536e8baa288e7bc9
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/04/2021
ms.locfileid: "97506987"
---
# <a name="prerender-and-integrate-aspnet-core-no-locrazor-components"></a>PreRender ve ASP.NET Core Razor bileşenleri tümleştirme

, [Luke Latham](https://github.com/guardrex) ve [Daniel Roth](https://github.com/danroth27) tarafından

::: zone pivot="webassembly"

::: moniker range=">= aspnetcore-5.0"

Razor bileşenler, Razor barındırılan bir çözümde sayfalarla ve MVC uygulamalarıyla tümleştirilebilir Blazor WebAssembly . Sayfa veya görünüm işlendiğinde, bileşenler aynı anda önceden alınabilir.

## <a name="configuration"></a>Yapılandırma

Bir uygulama için prerendering ayarlamak için Blazor WebAssembly :

1. Blazor WebAssemblyUygulamayı bir ASP.NET Core uygulamasında barındırın. Tek başına bir Blazor WebAssembly uygulama ASP.NET Core çözümüne eklenebilir veya Blazor WebAssembly Blazor barındırılan proje şablonundan oluşturulmuş bir barındırılan uygulama kullanabilirsiniz.

1. Varsayılan statik `wwwroot/index.html` dosyayı Blazor WebAssembly istemci projesinden kaldırın.

1. İstemci projesinde aşağıdaki satırı silin `Program.Main` :

   ```csharp
   builder.RootComponents.Add<App>("#app");
   ```

1. `Pages/_Host.cshtml`Sunucu projesine bir dosya ekleyin. Bir `_Host.cshtml` Blazor Server komut kabuğunda komutuyla şablondan oluşturulan bir uygulamadan dosya elde edebilirsiniz `dotnet new blazorserver -o BlazorServer` . `Pages/_Host.cshtml`Dosyayı barındırılan çözümün sunucu uygulamasına yerleştirdikten sonra Blazor WebAssembly , dosyasında aşağıdaki değişiklikleri yapın:

   * Ad alanını sunucu uygulamasının `Pages` klasörüne (örneğin, `@namespace BlazorHosted.Server.Pages` ) ayarlayın.
   * [`@using`](xref:mvc/views/razor#using)İstemci projesi için bir yönerge ayarlayın (örneğin, `@using BlazorHosted.Client` ).
   * Stil sayfası bağlantılarını, WebAssembly uygulamasının stil sayfasına işaret etmek üzere güncelleştirin. Aşağıdaki örnekte, istemci uygulamanın ad alanı şu şekilde olur `BlazorHosted.Client` :

     ```cshtml
     <link href="css/app.css" rel="stylesheet" />
     <link href="BlazorHosted.Client.styles.css" rel="stylesheet" />
     ```

   * `render-mode` [Bileşen etiketi Yardımcısı](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) ' nı, kök bileşeni olan PreRender 'dan güncelleştirin `App` :

     ```cshtml
     <component type="typeof(App)" render-mode="WebAssemblyPrerendered" />
     ```

   * Komut dosyası Blazor kaynağını istemci tarafı komut dosyasını kullanacak şekilde güncelleştirin Blazor WebAssembly :

     ```cshtml
     <script src="_framework/blazor.webassembly.js"></script>
     ```

1. `Startup.Configure` `Startup.cs` Sunucu projesinin içindeki ():

   * `UseDeveloperExceptionPage`Geliştirme ortamında uygulama tasarımcısında çağırın.
   * `UseBlazorFrameworkFiles`Uygulama Oluşturucu 'da çağırın.
   * `index.html`Sayfadan () geri dönüşü değiştirin `endpoints.MapFallbackToFile("index.html");` `_Host.cshtml` .

   ```csharp
   public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
   {
       if (env.IsDevelopment())
       {
           app.UseDeveloperExceptionPage();
           app.UseWebAssemblyDebugging();
       }
       else
       {
           app.UseExceptionHandler("/Error");
           app.UseHsts();
       }

       app.UseHttpsRedirection();
       app.UseBlazorFrameworkFiles();
       app.UseStaticFiles();

       app.UseRouting();

       app.UseEndpoints(endpoints =>
       {
           endpoints.MapRazorPages();
           endpoints.MapControllers();
           endpoints.MapFallbackToPage("/_Host");
       });
   }
   ```

## <a name="render-components-in-a-page-or-view-with-the-component-tag-helper"></a>Bileşen etiketi Yardımcısı ile bir sayfada veya görünümde bileşenleri işleme

Bileşen etiketi Yardımcısı, bir sayfadaki veya görünümdeki bir bileşeni işlemek için iki işleme modunu destekler Blazor WebAssembly :

* `WebAssembly`: Bir Blazor WebAssembly uygulamanın tarayıcıya yüklendiğinde etkileşimli bir bileşeni içermesi için kullanılacak bir işaret oluşturur. Bileşen ön işlenmiş değildir. Bu seçenek farklı sayfalarda farklı bileşenlerin işlenmesine daha kolay hale gelir Blazor WebAssembly .
* `WebAssemblyPrerendered`: Bileşeni statik HTML 'ye ön ekler ve Blazor WebAssembly daha sonra, tarayıcıya yüklendiğinde bileşeni etkileşimli hale getirmek için bir uygulamanın işaretçisini içerir.

Aşağıdaki Razor Sayfalar örneğinde, `Counter` bileşen bir sayfada işlenir. Bileşeni etkileşimli hale getirmek için, Blazor WebAssembly komut dosyası sayfanın [render bölümüne](xref:mvc/views/layout#sections)eklenir. Bileşen etiketi Yardımcısı () ile bileşen için tam ad alanını kullanmaktan kaçınmak için `Counter` `{APP ASSEMBLY}.Pages.Counter` , [`@using`](xref:mvc/views/razor#using) istemci uygulamanın ad alanı için bir yönerge ekleyin `Pages` . Aşağıdaki örnekte, istemci uygulamanın ad alanı şu şekilde olur `BlazorHosted.Client` :

```cshtml
...
@using BlazorHosted.Client.Pages

<h1>@ViewData["Title"]</h1>

<component type="typeof(Counter)" render-mode="WebAssemblyPrerendered" />

@section Scripts {
    <script src="_framework/blazor.webassembly.js"></script>
}
```

<xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> bileşenin şunları yapıp kullanmadığını yapılandırır:

* , Sayfaya ön gönderilir.
* , Sayfada statik HTML olarak veya Kullanıcı aracısından bir uygulamayı önyüklemek için gerekli bilgileri içeriyorsa Blazor .

Bileşen etiketi Yardımcısı hakkında, parametreleri ve yapılandırmayı geçirme dahil daha fazla bilgi için <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> bkz <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper> ..

Yukarıdaki örnekte, sunucu uygulamasının düzeninin ( `_Layout.cshtml` ) kapanış etiketi içindeki betik için bir [render bölümü](xref:mvc/views/layout#sections) () bulunmalıdır <xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.RenderSection%2A> `</body>` .

```cshtml
    ...

    @RenderSection("Scripts", required: false)
</body>
```

Dosya, bir `_Layout.cshtml` `Pages/Shared` MVC uygulamasında bir Razor Sayfalar uygulamasının veya klasörünün klasöründe bulunur `Views/Shared` .

Uygulama aynı zamanda uygulama stillerinin bulunduğu bileşenlere stil Blazor WebAssembly eklemek istiyorsanız, uygulamanın stillerini `_Layout.cshtml` dosyaya ekleyin. Aşağıdaki örnekte, istemci uygulamanın ad alanı şu şekilde olur `BlazorHosted.Client` :

```cshtml
<head>
    ...

    <link href="css/app.css" rel="stylesheet" />
    <link href="BlazorHosted.Client.styles.css" rel="stylesheet" />
</head>
```

## <a name="render-components-in-a-page-or-view-with-a-css-selector"></a>Bir sayfada veya görünümde bir CSS seçiciyle bileşenleri işleme

() İçindeki *istemci* projesine kök bileşenleri ekleyin `Program.Main` `Program.cs` . Aşağıdaki örnekte bileşen, eşleşen bir `Counter` öğeyi seçen CSS seçiciyle bir kök bileşen olarak bildirilmiştir `id` `my-counter` . Aşağıdaki örnekte, istemci uygulamanın ad alanı şu şekilde olur `BlazorHosted.Client` :

```csharp
using BlazorHosted.Client.Pages;

...

builder.RootComponents.Add<Counter>("#my-counter");
```

Aşağıdaki Razor Sayfalar örneğinde, `Counter` bileşen bir sayfada işlenir. Bileşeni etkileşimli hale getirmek için, Blazor WebAssembly komut dosyası sayfanın [render bölümüne](xref:mvc/views/layout#sections)eklenir:

```cshtml
...

<h1>@ViewData["Title"]</h1>

<div id="my-counter">Loading...</div>

@section Scripts {
    <script src="_framework/blazor.webassembly.js"></script>
}
```

Yukarıdaki örnekte, sunucu uygulamasının düzeninin ( `_Layout.cshtml` ) kapanış etiketi içindeki betik için bir [render bölümü](xref:mvc/views/layout#sections) () bulunmalıdır <xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.RenderSection%2A> `</body>` .

```cshtml
    ...

    @RenderSection("Scripts", required: false)
</body>
```

Dosya, bir `_Layout.cshtml` `Pages/Shared` MVC uygulamasında bir Razor Sayfalar uygulamasının veya klasörünün klasöründe bulunur `Views/Shared` .

Uygulama aynı zamanda uygulama stillerinin bulunduğu bileşenlere stil Blazor WebAssembly eklemek istiyorsanız, uygulamanın stillerini `_Layout.cshtml` dosyaya ekleyin. Aşağıdaki örnekte, istemci uygulamanın ad alanı şu şekilde olur `BlazorHosted.Client` :

```cshtml
<head>
    ...

    <link href="css/app.css" rel="stylesheet" />
    <link href="BlazorHosted.Client.styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

Razor Razor Barındırılan bir çözümde bileşenleri sayfalar ve MVC uygulamalarıyla tümleştirmek Blazor WebAssembly , .NET 5 veya sonraki sürümlerde ASP.NET Core desteklenir. Bu makalenin .NET 5 veya sonraki bir sürümünü seçin.

::: moniker-end

::: zone-end

::: zone pivot="server"

Razor bileşenler, Razor bir uygulamadaki sayfalarla ve MVC uygulamalarıyla tümleştirilebilir Blazor Server . Sayfa veya görünüm işlendiğinde, bileşenler aynı anda önceden alınabilir.

[Uygulamayı yapılandırdıktan](#configuration)sonra, uygulamanın gereksinimlerine bağlı olarak aşağıdaki bölümlerde yer alan kılavuzu kullanın:

* Yönlendirilebilir bileşenler: Kullanıcı isteklerinden doğrudan yönlendirilebilir bileşenler Için. Ziyaretçilerin, bir yönergesi olan bir bileşen için tarayıcılarında bir HTTP isteği yapabilmeleri gerektiğinde bu kılavuzu izleyin [`@page`](xref:mvc/views/razor#page) .
  * [Bir sayfalar uygulamasında yönlendirilebilir bileşenleri kullanma Razor](#use-routable-components-in-a-razor-pages-app)
  * [MVC uygulamasında yönlendirilebilir bileşenleri kullanma](#use-routable-components-in-an-mvc-app)
* [Bir sayfadan veya görünümden bileşenleri işleme](#render-components-from-a-page-or-view): doğrudan Kullanıcı isteklerinden yönlendirilemeyen bileşenler için. Uygulama bileşenleri [bileşen etiketi Yardımcısı](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper)ile var olan sayfalara ve görünümlere eklerken bu kılavuzu izleyin.

## <a name="configuration"></a>Yapılandırma

Mevcut Razor Sayfalar ve MVC uygulamaları, Razor bileşenleri sayfalar ve görünümler ile tümleştirilebilir:

1. Uygulamanın düzen dosyasında ( `_Layout.cshtml` ):

   * Aşağıdaki `<base>` etiketi `<head>` öğesine ekleyin:

     ```html
     <base href="~/" />
     ```

     `href`Yukarıdaki örnekte yer alan değer ( *uygulama temel yolu*), uygulamanın kök URL yolunda () bulunduğunu varsayar `/` . Uygulama bir alt uygulama ise, makalenin *uygulama temel yolu* bölümündeki yönergeleri izleyin <xref:blazor/host-and-deploy/index#app-base-path> .

     Dosya, bir `_Layout.cshtml` `Pages/Shared` MVC uygulamasında bir Razor Sayfalar uygulamasının veya klasörünün klasöründe bulunur `Views/Shared` .

   * `<script>` `blazor.server.js` Render bölümünden hemen önce betik için bir etiket ekleyin `Scripts` :

     ```html
         ...
         <script src="_framework/blazor.server.js"></script>

         @await RenderSectionAsync("Scripts", required: false)
     </body>
     ```

     Çerçeve, `blazor.server.js` betiği uygulamaya ekler. Betiği uygulamaya el ile eklemeniz gerekmez.

1. `_Imports.razor`Aşağıdaki içeriğe sahip projenin kök klasörüne bir dosya ekleyin (son ad alanını `MyAppNamespace` uygulamanın ad alanına değiştirin):

   ```razor
   @using System.Net.Http
   @using Microsoft.AspNetCore.Authorization
   @using Microsoft.AspNetCore.Components.Authorization
   @using Microsoft.AspNetCore.Components.Forms
   @using Microsoft.AspNetCore.Components.Routing
   @using Microsoft.AspNetCore.Components.Web
   @using Microsoft.JSInterop
   @using MyAppNamespace
   ```

1. `Startup.ConfigureServices`' De, Blazor Server hizmeti kaydedin:

   ```csharp
   services.AddServerSideBlazor();
   ```

1. İçinde `Startup.Configure` , Blazor hub bitiş noktasını şu şekilde ekleyin `app.UseEndpoints` :

   ```csharp
   endpoints.MapBlazorHub();
   ```

1. Bileşenleri herhangi bir sayfa veya görünümle tümleştirin. Daha fazla bilgi için, [bir sayfadan veya görünümden bileşenleri işleme](#render-components-from-a-page-or-view) bölümüne bakın.

## <a name="use-routable-components-in-a-no-locrazor-pages-app"></a>Bir sayfalar uygulamasında yönlendirilebilir bileşenleri kullanma Razor

*Bu bölüm, Kullanıcı isteklerinden doğrudan yönlendirilebilir bileşenleri eklemeye aittir.*

RazorSayfalar uygulamalarında yönlendirilebilir bileşenleri desteklemek için Razor :

1. [Yapılandırma](#configuration) bölümündeki yönergeleri izleyin.

1. `App.razor`Aşağıdaki içeriğe sahip proje köküne bir dosya ekleyin:

   ```razor
   @using Microsoft.AspNetCore.Components.Routing

   <Router AppAssembly="@typeof(Program).Assembly">
       <Found Context="routeData">
           <RouteView RouteData="routeData" />
       </Found>
       <NotFound>
           <h1>Page not found</h1>
           <p>Sorry, but there's nothing here!</p>
       </NotFound>
   </Router>
   ```

   [!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

1. `_Host.cshtml` `Pages` Klasöre aşağıdaki içeriğe sahip bir dosya ekleyin:

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   Bileşenler, `_Layout.cshtml` düzen için paylaşılan dosyayı kullanır.

   <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> bileşenin şunları yapıp kullanmadığını yapılandırır `App` :

   * , Sayfaya ön gönderilir.
   * , Sayfada statik HTML olarak veya Kullanıcı aracısından bir uygulamayı önyüklemek için gerekli bilgileri içeriyorsa Blazor .

   Bileşen etiketi Yardımcısı hakkında, parametreleri ve yapılandırmayı geçirme dahil daha fazla bilgi için <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> bkz <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper> ..

1. `_Host.cshtml`İçindeki uç nokta yapılandırmasına sayfanın düşük öncelikli bir yolunu ekleyin `Startup.Configure` :

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. Uygulamaya yönlendirilebilir bileşenler ekleyin. Örneğin:

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

Ad alanları hakkında daha fazla bilgi için [bileşen ad alanları](#component-namespaces) bölümüne bakın.

## <a name="use-routable-components-in-an-mvc-app"></a>MVC uygulamasında yönlendirilebilir bileşenleri kullanma

*Bu bölüm, Kullanıcı isteklerinden doğrudan yönlendirilebilir bileşenleri eklemeye aittir.*

RazorMVC uygulamalarında yönlendirilebilir bileşenleri desteklemek için:

1. [Yapılandırma](#configuration) bölümündeki yönergeleri izleyin.

1. `App.razor`Aşağıdaki içeriğe sahip projenin köküne bir dosya ekleyin:

   ```razor
   @using Microsoft.AspNetCore.Components.Routing

   <Router AppAssembly="@typeof(Program).Assembly">
       <Found Context="routeData">
           <RouteView RouteData="routeData" />
       </Found>
       <NotFound>
           <h1>Page not found</h1>
           <p>Sorry, but there's nothing here!</p>
       </NotFound>
   </Router>
   ```

   [!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

1. `_Host.cshtml` `Views/Home` Klasöre aşağıdaki içeriğe sahip bir dosya ekleyin:

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   Bileşenler, `_Layout.cshtml` düzen için paylaşılan dosyayı kullanır.

   <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> bileşenin şunları yapıp kullanmadığını yapılandırır `App` :

   * , Sayfaya ön gönderilir.
   * , Sayfada statik HTML olarak veya Kullanıcı aracısından bir uygulamayı önyüklemek için gerekli bilgileri içeriyorsa Blazor .

   Bileşen etiketi Yardımcısı hakkında, parametreleri ve yapılandırmayı geçirme dahil daha fazla bilgi için <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> bkz <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper> ..

1. Ana denetleyiciye bir eylem ekleyin:

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. `_Host.cshtml`' Deki uç nokta yapılandırmasına görünümü döndüren denetleyici eylemi için düşük öncelikli bir yol ekleyin `Startup.Configure` :

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController("Blazor", "Home");
   });
   ```

1. Bir `Pages` klasör oluşturun ve uygulamaya yönlendirilebilir bileşenler ekleyin. Örneğin:

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

Ad alanları hakkında daha fazla bilgi için [bileşen ad alanları](#component-namespaces) bölümüne bakın.

## <a name="render-components-from-a-page-or-view"></a>Bir sayfadan veya görünümden bileşenleri işleme

*Bu bölüm, bileşenlerin Kullanıcı isteklerinden doğrudan yönlendirilemeyen sayfalara veya görünümlere bileşen eklenmesine aittir.*

Bir sayfadan veya görünümden bir bileşeni işlemek için [bileşen etiketi yardımcısını](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper)kullanın.

### <a name="render-stateful-interactive-components"></a>Durum bilgisi olan etkileşimli bileşenleri işle

Durum bilgisi olan etkileşimli bileşenler, bir Razor sayfaya veya görünüme eklenebilir.

Sayfa veya görünüm şunları işler:

* Bileşen sayfa veya görünümle birlikte kullanılır.
* Prerendering için kullanılan ilk bileşen durumu kayboldu.
* Bağlantı kurulduunda yeni bileşen durumu oluşturulur SignalR .

Aşağıdaki Razor sayfa bir bileşeni işler `Counter` :

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@functions {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

Daha fazla bilgi için bkz. <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.

### <a name="render-noninteractive-components"></a>Etkileşimsiz bileşenleri işle

Aşağıdaki Razor sayfada, `Counter` bileşen bir form kullanılarak belirtilen bir başlangıç değeriyle statik olarak işlenir. Bileşen statik olarak işlendiğinden, bileşen etkileşimli değildir:

```cshtml
<h1>My Razor Page</h1>

<form>
    <input type="number" asp-for="InitialValue" />
    <button type="submit">Set initial value</button>
</form>

<component type="typeof(Counter)" render-mode="Static" 
    param-InitialValue="InitialValue" />

@functions {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

Daha fazla bilgi için bkz. <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.

## <a name="component-namespaces"></a>Bileşen ad alanları

Uygulamanın bileşenlerini tutmak için özel bir klasör kullanırken, klasörü/görünümü ya da dosyaya veya dosyayı temsil eden ad alanını ekleyin `_ViewImports.cshtml` . Aşağıdaki örnekte:

* `MyAppNamespace`Uygulamanın ad alanına geçin.
* Adlı bir klasör, `Components` bileşenleri tutmak için kullanılmazsa, `Components` bileşenlerin bulunduğu klasöre geçin.

```cshtml
@using MyAppNamespace.Components
```

`_ViewImports.cshtml`Dosya, `Pages` bir Razor Sayfalar uygulamasının KLASÖRÜNDE veya `Views` bir MVC uygulamasının klasöründe bulunur.

Daha fazla bilgi için bkz. <xref:blazor/components/index#namespaces>.

::: zone-end
