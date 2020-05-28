---
title: ' ASP.NET Core Razor bileşenleri sayfalarla tümleştirin Razor ve MVC Apps ' Author: Açıklama: ' uygulamalar içindeki BILEŞENLER ve DOM öğeleri için veri bağlama senaryoları hakkında bilgi edinin Blazor . '
monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

---
# <a name="integrate-aspnet-core-razor-components-into-razor-pages-and-mvc-apps"></a>ASP.NET Core Razor bileşenlerini Razor Sayfalar ve MVC uygulamalarıyla tümleştirme

, [Luke Latham](https://github.com/guardrex) ve [Daniel Roth](https://github.com/danroth27) tarafından

Razorbileşenler, Razor sayfalarla ve MVC uygulamalarıyla tümleştirilebilir. Sayfa veya görünüm işlendiğinde, bileşenler aynı anda önceden alınabilir.

[Uygulamayı hazırladıktan](#prepare-the-app)sonra, uygulamanın gereksinimlerine bağlı olarak aşağıdaki bölümlerde yer alan kılavuzu kullanın:

* Yönlendirilebilir bileşenler: Kullanıcı isteklerinden doğrudan yönlendirilebilir bileşenler Için. Ziyaretçilerin, bir yönergesi olan bir bileşen için tarayıcılarında bir HTTP isteği yapabilmeleri gerektiğinde bu kılavuzu izleyin [`@page`](xref:mvc/views/razor#page) .
  * [Bir sayfalar uygulamasında yönlendirilebilir bileşenleri kullanma Razor](#use-routable-components-in-a-razor-pages-app)
  * [MVC uygulamasında yönlendirilebilir bileşenleri kullanma](#use-routable-components-in-an-mvc-app)
* [Bir sayfadan veya görünümden bileşenleri işleme](#render-components-from-a-page-or-view): doğrudan Kullanıcı isteklerinden yönlendirilemeyen bileşenler için. Uygulama bileşenleri [bileşen etiketi Yardımcısı](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper)ile var olan sayfalara ve görünümlere eklerken bu kılavuzu izleyin.

## <a name="prepare-the-app"></a>Uygulamayı hazırlama

Mevcut Razor Sayfalar ve MVC uygulamaları, Razor bileşenleri sayfalar ve görünümler ile tümleştirilebilir:

1. Uygulamanın düzen dosyasında (*_Layout. cshtml*):

   * Aşağıdaki `<base>` etiketi `<head>` öğesine ekleyin:

     ```html
     <base href="~/" />
     ```

     `href`Yukarıdaki örnekte yer alan değer ( *uygulama temel yolu*), uygulamanın kök URL yolunda () bulunduğunu varsayar `/` . Uygulama bir alt uygulama ise, makalenin *uygulama temel yolu* bölümündeki yönergeleri izleyin <xref:host-and-deploy/blazor/index#app-base-path> .

     *_Layout. cshtml* dosyası, bir MVC uygulamasında sayfalar uygulaması veya görünümler/paylaşılan klasöründe *Sayfalar/paylaşılan* klasöründe bulunur Razor . *Views/Shared*

   * `<script>`Kapanış etiketinden hemen önce *blazor. Server. js* betiği için bir etiket ekleyin `</body>` :

     ```html
     <script src="_framework/blazor.server.js"></script>
     ```

     Framework, *blazor. Server. js* betiğini uygulamaya ekler. Betiği uygulamaya el ile eklemeniz gerekmez.

1. Aşağıdaki içerikle projenin kök klasörüne bir *_Imports. Razor* dosyası ekleyin (son ad alanını `MyAppNamespace` uygulamanın ad alanına değiştirin):

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

1. `Startup.ConfigureServices`' De, Blazor sunucu hizmetini kaydedin:

   ```csharp
   services.AddServerSideBlazor();
   ```

1. İçinde `Startup.Configure` , Blazor hub bitiş noktasını şu şekilde ekleyin `app.UseEndpoints` :

   ```csharp
   endpoints.MapBlazorHub();
   ```

1. Bileşenleri herhangi bir sayfa veya görünümle tümleştirin. Daha fazla bilgi için, [bir sayfadan veya görünümden bileşenleri işleme](#render-components-from-a-page-or-view) bölümüne bakın.

## <a name="use-routable-components-in-a-razor-pages-app"></a>Bir sayfalar uygulamasında yönlendirilebilir bileşenleri kullanma Razor

*Bu bölüm, Kullanıcı isteklerinden doğrudan yönlendirilebilir bileşenleri eklemeye aittir.*

RazorSayfalar uygulamalarında yönlendirilebilir bileşenleri desteklemek için Razor :

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

   <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>bileşenin şunları yapıp kullanmadığını yapılandırır `App` :

   * , Sayfaya ön gönderilir.
   * , Sayfada statik HTML olarak veya Kullanıcı aracısından bir uygulamayı önyüklemek için gerekli bilgileri içeriyorsa Blazor .

   | Oluşturma modu | Açıklama |
   | ---
title: ' ASP.NET Core Razor bileşenleri sayfalarla tümleştirin Razor ve MVC Apps ' Author: Açıklama: ' uygulamalar içindeki BILEŞENLER ve DOM öğeleri için veri bağlama senaryoları hakkında bilgi edinin Blazor . '
monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

-
title: ' ASP.NET Core Razor bileşenleri sayfalarla tümleştirin Razor ve MVC Apps ' Author: Açıklama: ' uygulamalar içindeki BILEŞENLER ve DOM öğeleri için veri bağlama senaryoları hakkında bilgi edinin Blazor . '
monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

-
title: ' ASP.NET Core Razor bileşenleri sayfalarla tümleştirin Razor ve MVC Apps ' Author: Açıklama: ' uygulamalar içindeki BILEŞENLER ve DOM öğeleri için veri bağlama senaryoları hakkında bilgi edinin Blazor . '
monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

------ | ---title: ' ASP.NET Core Razor bileşenlerini sayfalarla tümleştirin Razor ve MVC Apps ' Yazar: Açıklama: ' uygulamalardaki BILEŞENLER ve DOM öğeleri için veri bağlama senaryoları hakkında bilgi edinin Blazor . '
monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

-
title: ' ASP.NET Core Razor bileşenleri sayfalarla tümleştirin Razor ve MVC Apps ' Author: Açıklama: ' uygulamalar içindeki BILEŞENLER ve DOM öğeleri için veri bağlama senaryoları hakkında bilgi edinin Blazor . '
monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

-
title: ' ASP.NET Core Razor bileşenleri sayfalarla tümleştirin Razor ve MVC Apps ' Author: Açıklama: ' uygulamalar içindeki BILEŞENLER ve DOM öğeleri için veri bağlama senaryoları hakkında bilgi edinin Blazor . '
monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

------ | | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | `App`Bileşeni STATIK HTML olarak işler ve sunucu uygulaması için bir işaret içerir Blazor . Kullanıcı Aracısı başladığında, bu işaretleyici bir uygulamayı önyüklemek için kullanılır Blazor . | | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Sunucu uygulaması için bir işaret oluşturur Blazor . `App`Bileşen çıkışı dahil değildir. Kullanıcı Aracısı başladığında, bu işaretleyici bir uygulamayı önyüklemek için kullanılır Blazor . | | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | `App`Bileşeni STATIK HTML olarak işler. |

   Bileşen etiketi Yardımcısı hakkında daha fazla bilgi için bkz <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper> ..

1. *_Host. cshtml* sayfasına yönelik düşük öncelikli bir yolu, içindeki uç nokta yapılandırmasına ekleyin `Startup.Configure` :

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
   
   <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>bileşenin şunları yapıp kullanmadığını yapılandırır `App` :

   * , Sayfaya ön gönderilir.
   * , Sayfada statik HTML olarak veya Kullanıcı aracısından bir uygulamayı önyüklemek için gerekli bilgileri içeriyorsa Blazor .

   | Oluşturma modu | Açıklama |
   | ---
title: ' ASP.NET Core Razor bileşenleri sayfalarla tümleştirin Razor ve MVC Apps ' Author: Açıklama: ' uygulamalar içindeki BILEŞENLER ve DOM öğeleri için veri bağlama senaryoları hakkında bilgi edinin Blazor . '
monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

-
title: ' ASP.NET Core Razor bileşenleri sayfalarla tümleştirin Razor ve MVC Apps ' Author: Açıklama: ' uygulamalar içindeki BILEŞENLER ve DOM öğeleri için veri bağlama senaryoları hakkında bilgi edinin Blazor . '
monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

-
title: ' ASP.NET Core Razor bileşenleri sayfalarla tümleştirin Razor ve MVC Apps ' Author: Açıklama: ' uygulamalar içindeki BILEŞENLER ve DOM öğeleri için veri bağlama senaryoları hakkında bilgi edinin Blazor . '
monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

------ | ---title: ' ASP.NET Core Razor bileşenlerini sayfalarla tümleştirin Razor ve MVC Apps ' Yazar: Açıklama: ' uygulamalardaki BILEŞENLER ve DOM öğeleri için veri bağlama senaryoları hakkında bilgi edinin Blazor . '
monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

-
title: ' ASP.NET Core Razor bileşenleri sayfalarla tümleştirin Razor ve MVC Apps ' Author: Açıklama: ' uygulamalar içindeki BILEŞENLER ve DOM öğeleri için veri bağlama senaryoları hakkında bilgi edinin Blazor . '
monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

-
title: ' ASP.NET Core Razor bileşenleri sayfalarla tümleştirin Razor ve MVC Apps ' Author: Açıklama: ' uygulamalar içindeki BILEŞENLER ve DOM öğeleri için veri bağlama senaryoları hakkında bilgi edinin Blazor . '
monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

------ | | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | `App`Bileşeni STATIK HTML olarak işler ve sunucu uygulaması için bir işaret içerir Blazor . Kullanıcı Aracısı başladığında, bu işaretleyici bir uygulamayı önyüklemek için kullanılır Blazor . | | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Sunucu uygulaması için bir işaret oluşturur Blazor . `App`Bileşen çıkışı dahil değildir. Kullanıcı Aracısı başladığında, bu işaretleyici bir uygulamayı önyüklemek için kullanılır Blazor . | | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | `App`Bileşeni STATIK HTML olarak işler. |

   Bileşen etiketi Yardımcısı hakkında daha fazla bilgi için bkz <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper> ..

1. Ana denetleyiciye bir eylem ekleyin:

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. İçindeki uç nokta yapılandırmasına *_Host. cshtml* görünümünü döndüren denetleyici eylemi için düşük öncelikli bir yol ekleyin `Startup.Configure` :

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

Uygulamanın bileşenlerini tutmak için özel bir klasör kullanırken, klasörü/görünümü veya *_ViewImports. cshtml* dosyasını temsil eden ad alanını ekleyin. Aşağıdaki örnekte:

* `MyAppNamespace`Uygulamanın ad alanına geçin.
* Bileşenleri tutmak için *bileşen* adlı bir klasör kullanılmazsa, bileşenlerin bulunduğu klasöre geçin `Components` .

```cshtml
@using MyAppNamespace.Components
```

*_ViewImports. cshtml* dosyası bir Pages uygulamasının *Pages* klasöründe Razor veya bir MVC uygulamasının *views* klasöründe bulunur.

Daha fazla bilgi için bkz. <xref:blazor/components#import-components>.
