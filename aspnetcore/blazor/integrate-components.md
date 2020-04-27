---
title: ASP.NET Core Razor bileşenlerini Razor Pages ve MVC uygulamalarıyla tümleştirin
author: guardrex
description: Blazor Uygulamalarda BILEŞENLER ve DOM öğeleri için veri bağlama senaryoları hakkında bilgi edinin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/25/2020
no-loc:
- Blazor
- SignalR
uid: blazor/integrate-components
ms.openlocfilehash: 282f96a4198d20b7fa38c186721b48ace5219ca6
ms.sourcegitcommit: c6f5ea6397af2dd202632cf2be66fc30f3357bcc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/26/2020
ms.locfileid: "82159612"
---
# <a name="integrate-aspnet-core-razor-components-into-razor-pages-and-mvc-apps"></a>ASP.NET Core Razor bileşenlerini Razor Pages ve MVC uygulamalarıyla tümleştirin

, [Luke Latham](https://github.com/guardrex) ve [Daniel Roth](https://github.com/danroth27) tarafından

Razor bileşenleri, Razor Pages ve MVC uygulamalarıyla tümleştirilebilir. Sayfa veya görünüm işlendiğinde, bileşenler aynı anda önceden alınabilir.

[Uygulamayı hazırladıktan](#prepare-the-app)sonra, uygulamanın gereksinimlerine bağlı olarak aşağıdaki bölümlerde yer alan kılavuzu kullanın:

* Kullanıcı isteklerinden doğrudan yönlendirilebilir bileşenler için yönlendirilebilir bileşenler &ndash; . Ziyaretçilerin, bir [`@page`](xref:mvc/views/razor#page) yönergesi olan bir bileşen için TARAYıCıLARıNDA bir http isteği yapabilmeleri gerektiğinde bu kılavuzu izleyin.
  * [Razor Pages uygulamasında yönlendirilebilir bileşenleri kullanma](#use-routable-components-in-a-razor-pages-app)
  * [MVC uygulamasında yönlendirilebilir bileşenleri kullanma](#use-routable-components-in-an-mvc-app)
* Kullanıcı isteklerinden doğrudan yönlendirilemeyen bileşenler için &ndash; [bir sayfadan veya görünümden bileşenleri işleme](#render-components-from-a-page-or-view) . Uygulama bileşenleri [bileşen etiketi Yardımcısı](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper)ile var olan sayfalara ve görünümlere eklerken bu kılavuzu izleyin.

## <a name="prepare-the-app"></a>Uygulamayı hazırlama

Mevcut bir Razor Pages veya MVC uygulaması, Razor bileşenlerini sayfalarla ve görünümleriyle tümleştirebilir:

1. Uygulamanın düzen dosyasında (*_Layout. cshtml*):

   * Aşağıdaki `<base>` etiketi `<head>` öğesine ekleyin:

     ```html
     <base href="~/" />
     ```

     Yukarıdaki `href` örnekte yer alan değer ( *uygulama temel yolu*), uygulamanın kök URL yolunda (`/`) bulunduğunu varsayar. Uygulama bir alt uygulama ise, <xref:host-and-deploy/blazor/index#app-base-path> makalenin *uygulama temel yolu* bölümündeki yönergeleri izleyin.

     *_Layout. cshtml* dosyası, bir MVC uygulamasında bir Razor Pages uygulamasının veya *görünümlerinin/paylaşılan* klasörünün *Sayfalar/paylaşılan* klasöründe bulunur.

   * Kapanış `</body>` etiketinden `<script>` hemen önce *blazor. Server. js* betiği için bir etiket ekleyin:

     ```html
     <script src="_framework/blazor.server.js"></script>
     ```

     Framework, *blazor. Server. js* betiğini uygulamaya ekler. Betiği uygulamaya el ile eklemeniz gerekmez.

1. Aşağıdaki içerikle projenin kök klasörüne bir *_Imports. Razor* dosyası ekleyin (son ad alanını `MyAppNamespace`uygulamanın ad alanına değiştirin):

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

1. İçinde `Startup.ConfigureServices`, Blazor sunucu hizmetini kaydedin:

   ```csharp
   services.AddServerSideBlazor();
   ```

1. İçinde `Startup.Configure`, Blazor hub uç noktasını şu şekilde `app.UseEndpoints`ekleyin:

   ```csharp
   endpoints.MapBlazorHub();
   ```

1. Bileşenleri herhangi bir sayfa veya görünümle tümleştirin. Daha fazla bilgi için, [bir sayfadan veya görünümden bileşenleri işleme](#render-components-from-a-page-or-view) bölümüne bakın.

## <a name="use-routable-components-in-a-razor-pages-app"></a>Razor Pages uygulamasında yönlendirilebilir bileşenleri kullanma

*Bu bölüm, Kullanıcı isteklerinden doğrudan yönlendirilebilir bileşenleri eklemeye aittir.*

Razor Pages uygulamalarda yönlendirilebilir Razor bileşenlerini desteklemek için:

1. [Uygulamayı hazırlama](#prepare-the-app) bölümündeki yönergeleri izleyin.

1. Aşağıdaki içeriğe sahip proje köküne bir *app. Razor* dosyası ekleyin:

   ```razor
   @using Microsoft.AspNetCore.Components.Routing

   <Router AppAssembly="typeof(Program).Assembly">
       <Found Context="routeData">
           <RouteView RouteData="routeData" />
       </Found>
       <NotFound>
           <h1>Page not found</h1>
           <p>Sorry, but there's nothing here!</p>
       </NotFound>
   </Router>
   ```

1. *Sayfalar* klasörüne aşağıdaki içeriğe sahip bir *_Host. cshtml* dosyası ekleyin:

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   Bileşenler, düzeni için paylaşılan *_Layout. cshtml* dosyasını kullanır.

   <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>`App` bileşenin şunları yapıp kullanmadığını yapılandırır:

   * , Sayfaya ön gönderilir.
   * , Sayfada statik HTML olarak veya Kullanıcı aracısından bir Blazor uygulamasını önyüklemek için gerekli bilgileri içeriyorsa.

   | Oluşturma modu | Açıklama |
   | ----------- | ----------- |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | `App` BILEŞENI statik HTML olarak işler ve bir Blazor Server uygulaması için işaret içerir. Kullanıcı Aracısı başladığında, bu işaretleyici bir Blazor uygulamasını önyüklemek için kullanılır. |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Bir Blazor sunucu uygulaması için işaretleyici işler. `App` Bileşen çıkışı dahil değildir. Kullanıcı Aracısı başladığında, bu işaretleyici bir Blazor uygulamasını önyüklemek için kullanılır. |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | `App` BILEŞENI statik HTML olarak işler. |

   Bileşen etiketi Yardımcısı hakkında daha fazla bilgi için bkz <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>..

1. *_Host. cshtml* sayfasına yönelik düşük öncelikli bir yolu, içindeki `Startup.Configure`uç nokta yapılandırmasına ekleyin:

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

MVC uygulamalarında yönlendirilebilir Razor bileşenlerini desteklemek için:

1. [Uygulamayı hazırlama](#prepare-the-app) bölümündeki yönergeleri izleyin.

1. Aşağıdaki içeriğe sahip projenin köküne bir *app. Razor* dosyası ekleyin:

   ```razor
   @using Microsoft.AspNetCore.Components.Routing

   <Router AppAssembly="typeof(Program).Assembly">
       <Found Context="routeData">
           <RouteView RouteData="routeData" />
       </Found>
       <NotFound>
           <h1>Page not found</h1>
           <p>Sorry, but there's nothing here!</p>
       </NotFound>
   </Router>
   ```

1. Aşağıdaki içeriğe sahip *Görünümler/giriş* klasörüne bir *_Host. cshtml* dosyası ekleyin:

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   Bileşenler, düzeni için paylaşılan *_Layout. cshtml* dosyasını kullanır.
   
   <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>`App` bileşenin şunları yapıp kullanmadığını yapılandırır:

   * , Sayfaya ön gönderilir.
   * , Sayfada statik HTML olarak veya Kullanıcı aracısından bir Blazor uygulamasını önyüklemek için gerekli bilgileri içeriyorsa.

   | Oluşturma modu | Açıklama |
   | ----------- | ----------- |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | `App` BILEŞENI statik HTML olarak işler ve Blazor sunucu uygulaması için bir işaret içerir. Kullanıcı Aracısı başladığında, bu işaretleyici bir Blazor uygulamayı önyüklemek için kullanılır. |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Blazor Sunucu uygulaması için bir işaret oluşturur. `App` Bileşen çıkışı dahil değildir. Kullanıcı Aracısı başladığında, bu işaretleyici bir Blazor uygulamayı önyüklemek için kullanılır. |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | `App` BILEŞENI statik HTML olarak işler. |

   Bileşen etiketi Yardımcısı hakkında daha fazla bilgi için bkz <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>..

1. Ana denetleyiciye bir eylem ekleyin:

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. İçindeki `Startup.Configure`uç nokta yapılandırmasına *_Host. cshtml* görünümünü döndüren denetleyici eylemi için düşük öncelikli bir yol ekleyin:

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController("Blazor", "Home");
   });
   ```

1. Bir *Sayfalar* klasörü oluşturun ve uygulamaya yönlendirilebilir bileşenler ekleyin. Örneğin:

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

Durum bilgisi olan etkileşimli bileşenler Razor sayfasına veya görünümüne eklenebilir.

Sayfa veya görünüm şunları işler:

* Bileşen sayfa veya görünümle birlikte kullanılır.
* Prerendering için kullanılan ilk bileşen durumu kayboldu.
* SignalR Bağlantı kurulduunda yeni bileşen durumu oluşturulur.

Aşağıdaki Razor sayfası bir `Counter` bileşeni işler:

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@functions {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

Daha fazla bilgi için bkz. <xref:xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.

### <a name="render-noninteractive-components"></a>Etkileşimsiz bileşenleri işle

Aşağıdaki Razor sayfasında, `Counter` bileşen bir form kullanılarak belirtilen bir başlangıç değeriyle statik olarak işlenir. Bileşen statik olarak işlendiğinden, bileşen etkileşimli değildir:

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

Daha fazla bilgi için bkz. <xref:xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.

## <a name="component-namespaces"></a>Bileşen ad alanları

Uygulamanın bileşenlerini tutmak için özel bir klasör kullanırken, klasörü/görünümü veya *_ViewImports. cshtml* dosyasını temsil eden ad alanını ekleyin. Aşağıdaki örnekte:

* `MyAppNamespace` Uygulamanın ad alanına geçin.
* Bileşenleri tutmak için *bileşen* adlı bir klasör kullanılmazsa, bileşenlerin bulunduğu klasöre geçin `Components` .

```cshtml
@using MyAppNamespace.Components
```

*_ViewImports. cshtml* dosyası, bir Razor Pages uygulamasının *Sayfalar* klasöründe veya bir MVC uygulamasının *views* klasöründe bulunur.

Daha fazla bilgi için bkz. <xref:blazor/components#import-components>.
