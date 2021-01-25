---
title: ASP.NET Core Blazor proje yapısı
author: guardrex
description: ASP.NET Core Blazor App Project yapısı hakkında bilgi edinin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/19/2021
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
uid: blazor/project-structure
ms.openlocfilehash: 958fa23a1befac3696d850d5409d4021dd109c22
ms.sourcegitcommit: 610936e4d3507f7f3d467ed7859ab9354ec158ba
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/25/2021
ms.locfileid: "98751548"
---
# <a name="aspnet-core-no-locblazor-project-structure"></a>ASP.NET Core Blazor proje yapısı

[Daniel Roth](https://github.com/danroth27) ve [Luke Latham](https://github.com/guardrex) tarafından

Bu makalede, Blazor proje şablonlarından oluşturulan bir uygulamayı oluşturan dosya ve klasörler açıklanmaktadır Blazor .

## Blazor WebAssembly

Blazor WebAssemblyŞablon ( `blazorwasm` ) bir uygulama için başlangıç dosyalarını ve dizin yapısını oluşturur Blazor WebAssembly . Uygulama, bir statik varlıktan veri yükleyen bir bileşen için tanıtım kodu `FetchData` `weather.json` ve bileşenle Kullanıcı etkileşimi ile doldurulur `Counter` .

* `Pages` klasör: uygulamayı oluşturan yönlendirilebilir bileşenleri/sayfaları ( `.razor` ) içerir Blazor . Her sayfanın yolu, yönergesi kullanılarak belirtilir [`@page`](xref:mvc/views/razor#page) . Şablon aşağıdaki bileşenleri içerir:
  * `Counter` Component ( `Counter.razor` ): sayaç sayfasını uygular.
  * `FetchData` Component ( `FetchData.razor` ): veri getirme sayfasını uygular.
  * `Index` Component ( `Index.razor` ): giriş sayfasını uygular.
  
* `Properties/launchSettings.json`: [Geliştirme ortamı yapılandırmasını](xref:fundamentals/environments#development-and-launchsettingsjson)barındırır.

::: moniker range=">= aspnetcore-5.0"

* `Shared` klasör: aşağıdaki paylaşılan bileşenleri ve stil sayfalarını Içerir:
  * `MainLayout` Component ( `MainLayout.razor` ): uygulamanın [Düzen bileşeni](xref:blazor/layouts).
  * `MainLayout.razor.css`: Uygulamanın ana düzeni için stil sayfası.
  * `NavMenu` Component ( `NavMenu.razor` ): kenar çubuğu gezintisini uygular. [ `NavLink` ](xref:blazor/fundamentals/routing#navlink-and-navmenu-components) <xref:Microsoft.AspNetCore.Components.Routing.NavLink> Diğer bileşenlere gezinti bağlantılarını işleyen bileşeni () içerir Razor . Bileşeni, <xref:Microsoft.AspNetCore.Components.Routing.NavLink> bileşeni yüklendiği zaman otomatik olarak seçili durumu gösterir ve bu, kullanıcının hangi bileşenin görüntülenmekte olduğunu anlamasına yardımcı olur.
  * `NavMenu.razor.css`: Uygulamanın gezinti menüsünün stil sayfası.
  * `SurveyPrompt` Component ( `SurveyPrompt.razor` ): Blazor anket bileşeni.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* `Shared` klasör: aşağıdaki paylaşılan bileşenleri Içerir:
  * `MainLayout` Component ( `MainLayout.razor` ): uygulamanın [Düzen bileşeni](xref:blazor/layouts).
  * `NavMenu` Component ( `NavMenu.razor` ): kenar çubuğu gezintisini uygular. [ `NavLink` ](xref:blazor/fundamentals/routing#navlink-and-navmenu-components) <xref:Microsoft.AspNetCore.Components.Routing.NavLink> Diğer bileşenlere gezinti bağlantılarını işleyen bileşeni () içerir Razor . Bileşeni, <xref:Microsoft.AspNetCore.Components.Routing.NavLink> bileşeni yüklendiği zaman otomatik olarak seçili durumu gösterir ve bu, kullanıcının hangi bileşenin görüntülenmekte olduğunu anlamasına yardımcı olur.
  * `SurveyPrompt` Component ( `SurveyPrompt.razor` ): Blazor anket bileşeni.
  
::: moniker-end

::: moniker range=">= aspnetcore-5.0"

* `wwwroot`: Uygulamanın [](xref:fundamentals/index#web-root) ortak statik varlıklarını içeren uygulamanın, `appsettings.json` [yapılandırma ayarları](xref:blazor/fundamentals/configuration)için çevresel uygulama ayarları dosyaları dahil Web kök klasörü. `index.html`Web sayfası, BIR HTML sayfası olarak uygulanan uygulamanın kök sayfasıdır:
  * Uygulamanın herhangi bir sayfası başlangıçta istendiğinde, Bu sayfa işlenir ve yanıtta döndürülür.
  * Bu sayfa, kök bileşenin nerede `App` işleneceğini belirtir. Bileşeni, `div` () olan DOM öğesinin konumunda işlenir `id` `app` `<div id="app">Loading...</div>` .

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* `wwwroot`: Uygulamanın [](xref:fundamentals/index#web-root) ortak statik varlıklarını içeren uygulamanın, `appsettings.json` [yapılandırma ayarları](xref:blazor/fundamentals/configuration)için çevresel uygulama ayarları dosyaları dahil Web kök klasörü. `index.html`Web sayfası, BIR HTML sayfası olarak uygulanan uygulamanın kök sayfasıdır:
  * Uygulamanın herhangi bir sayfası başlangıçta istendiğinde, Bu sayfa işlenir ve yanıtta döndürülür.
  * Bu sayfa, kök bileşenin nerede `App` işleneceğini belirtir. Bileşen, `app` DOM öğesinin () konumunda işlenir `<app>Loading...</app>` .

::: moniker-end

> [!NOTE]
> Dosyaya eklenen JavaScript (JS) dosyaları `wwwroot/index.html` , kapanış etiketinden önce gelmelidir `</body>` . Özel JS kodunun, JS dosyalarından yüklendiği sıra, bazı senaryolarda önemlidir. Örneğin, birlikte çalışma yöntemlerine sahip JS dosyalarının Framework JS dosyalarından önce eklendiğinden emin olun Blazor .

* `_Imports.razor`: Razor `.razor` [`@using`](xref:mvc/views/razor#using) Ad alanları için yönergeler gibi uygulamanın bileşenlerine () dahil etmek için ortak yönergeleri içerir.

* `App.razor`: Bileşeni kullanarak istemci tarafı yönlendirmeyi ayarlayan uygulamanın kök bileşeni <xref:Microsoft.AspNetCore.Components.Routing.Router> . <xref:Microsoft.AspNetCore.Components.Routing.Router>Bileşen tarayıcı gezintisini karşılar ve istenen adresle eşleşen sayfayı işler.

::: moniker range=">= aspnetcore-5.0"

* `Program.cs`: WebAssembly konağını ayarlayan uygulamanın giriş noktası:
  
  * `App`Bileşen, uygulamanın kök bileşenidir. `App`Bileşen, `div` `id` `app` `<div id="app">Loading...</div>` `wwwroot/index.html` kök bileşen koleksiyonu () ile ( `builder.RootComponents.Add<App>("#app")` ) öğesine sahip DOM öğesi olarak belirtilir.
  * [Hizmetler](xref:blazor/fundamentals/dependency-injection) eklenir ve yapılandırılır (örneğin, `builder.Services.AddSingleton<IMyDependency, MyDependency>()` ).

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* `Program.cs`: WebAssembly konağını ayarlayan uygulamanın giriş noktası:

  * `App`Bileşen, uygulamanın kök bileşenidir. `App`Bileşen, `app` `<app>Loading...</app>` `wwwroot/index.html` kök bileşen koleksiyonunda () DOM öğesi olarak belirtilir `builder.RootComponents.Add<App>("app")` .
  * [Hizmetler](xref:blazor/fundamentals/dependency-injection) eklenir ve yapılandırılır (örneğin, `builder.Services.AddSingleton<IMyDependency, MyDependency>()` ).

::: moniker-end

## Blazor Server

Blazor ServerŞablon ( `blazorserver` ) bir uygulama için başlangıç dosyalarını ve dizin yapısını oluşturur Blazor Server . Uygulama, `FetchData` kayıtlı bir hizmetten verileri yükleyen bir bileşen için tanıtım kodu `WeatherForecastService` ve bileşenle Kullanıcı etkileşimi ile doldurulur `Counter` .

* `Data` klasör: `WeatherForecast` `WeatherForecastService` uygulamanın bileşenine örnek Hava durumu verileri sağlayan sınıfını ve uygulamasını içerir `FetchData` .

* `Pages` klasör: uygulamayı `.razor` Blazor ve uygulamanın kök sayfasını oluşturan yönlendirilebilir bileşenleri/sayfaları () içerir Razor Blazor Server . Her sayfanın yolu, yönergesi kullanılarak belirtilir [`@page`](xref:mvc/views/razor#page) . Şablon şunları içerir:
  * `_Host.cshtml`: Bir sayfa olarak uygulanan uygulamanın kök sayfası Razor :
    * Uygulamanın herhangi bir sayfası başlangıçta istendiğinde, Bu sayfa işlenir ve yanıtta döndürülür.
    * Ana bilgisayar sayfası, kök bileşeni 'nin `App` ( `App.razor` ) nerede işleneceğini belirtir.
  * `Counter` Component ( `Counter.razor` ): sayaç sayfasını uygular.
  * `Error` Component ( `Error.razor` ): uygulamada işlenmeyen bir özel durum oluştuğunda işlenir.
  * `FetchData` Component ( `FetchData.razor` ): veri getirme sayfasını uygular.
  * `Index` Component ( `Index.razor` ): giriş sayfasını uygular.

> [!NOTE]
> Dosyaya eklenen JavaScript (JS) dosyaları `Pages/_Host.cshtml` , kapanış etiketinden önce gelmelidir `</body>` . Özel JS kodunun, JS dosyalarından yüklendiği sıra, bazı senaryolarda önemlidir. Örneğin, birlikte çalışma yöntemlerine sahip JS dosyalarının Framework JS dosyalarından önce eklendiğinden emin olun Blazor .

* `Properties/launchSettings.json`: [Geliştirme ortamı yapılandırmasını](xref:fundamentals/environments#development-and-launchsettingsjson)barındırır.

::: moniker range=">= aspnetcore-5.0"

* `Shared` klasör: aşağıdaki paylaşılan bileşenleri ve stil sayfalarını Içerir:
  * `MainLayout` Component ( `MainLayout.razor` ): uygulamanın [Düzen bileşeni](xref:blazor/layouts).
  * `MainLayout.razor.css`: Uygulamanın ana düzeni için stil sayfası.
  * `NavMenu` Component ( `NavMenu.razor` ): kenar çubuğu gezintisini uygular. [ `NavLink` ](xref:blazor/fundamentals/routing#navlink-and-navmenu-components) <xref:Microsoft.AspNetCore.Components.Routing.NavLink> Diğer bileşenlere gezinti bağlantılarını işleyen bileşeni () içerir Razor . Bileşeni, <xref:Microsoft.AspNetCore.Components.Routing.NavLink> bileşeni yüklendiği zaman otomatik olarak seçili durumu gösterir ve bu, kullanıcının hangi bileşenin görüntülenmekte olduğunu anlamasına yardımcı olur.
  * `NavMenu.razor.css`: Uygulamanın gezinti menüsünün stil sayfası.
  * `SurveyPrompt` Component ( `SurveyPrompt.razor` ): Blazor anket bileşeni.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* `Shared` klasör: aşağıdaki paylaşılan bileşenleri Içerir:
  * `MainLayout` Component ( `MainLayout.razor` ): uygulamanın [Düzen bileşeni](xref:blazor/layouts).
  * `NavMenu` Component ( `NavMenu.razor` ): kenar çubuğu gezintisini uygular. [ `NavLink` ](xref:blazor/fundamentals/routing#navlink-and-navmenu-components) <xref:Microsoft.AspNetCore.Components.Routing.NavLink> Diğer bileşenlere gezinti bağlantılarını işleyen bileşeni () içerir Razor . Bileşeni, <xref:Microsoft.AspNetCore.Components.Routing.NavLink> bileşeni yüklendiği zaman otomatik olarak seçili durumu gösterir ve bu, kullanıcının hangi bileşenin görüntülenmekte olduğunu anlamasına yardımcı olur.
  * `SurveyPrompt` Component ( `SurveyPrompt.razor` ): Blazor anket bileşeni.
  
::: moniker-end

* `wwwroot`: Uygulamanın ortak statik varlıklarını içeren uygulamanın [Web kök](xref:fundamentals/index#web-root) klasörü.

* `_Imports.razor`: Razor `.razor` [`@using`](xref:mvc/views/razor#using) Ad alanları için yönergeler gibi uygulamanın bileşenlerine () dahil etmek için ortak yönergeleri içerir.

* `App.razor`: Bileşeni kullanarak istemci tarafı yönlendirmeyi ayarlayan uygulamanın kök bileşeni <xref:Microsoft.AspNetCore.Components.Routing.Router> . <xref:Microsoft.AspNetCore.Components.Routing.Router>Bileşen tarayıcı gezintisini karşılar ve istenen adresle eşleşen sayfayı işler.

* `appsettings.json` ve çevresel uygulama ayarları dosyaları: uygulama için [yapılandırma ayarlarını](xref:blazor/fundamentals/configuration) belirtin.

* `Program.cs`: ASP.NET Core [konağını](xref:fundamentals/host/generic-host)ayarlayan uygulamanın giriş noktası.

* `Startup.cs`: Uygulamanın başlangıç mantığını içerir. `Startup`Sınıfı iki yöntemi tanımlar:

  * `ConfigureServices`: Uygulamanın [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection) hizmetlerini yapılandırır. Hizmetler <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A> , çağırarak ve `WeatherForecastService` hizmet kapsayıcısına örnek bileşen tarafından kullanılmak üzere eklendikten sonra eklenir `FetchData` .
  * `Configure`: Uygulamanın istek işleme ardışık düzenini yapılandırır:
    * <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> , tarayıcıya gerçek zamanlı bağlantı için bir uç nokta ayarlamak üzere çağırılır. Bağlantı, [SignalR](xref:signalr/introduction) uygulamalarına gerçek zamanlı Web işlevselliği ekleme çerçevesi olan ile oluşturulur.
    * [`MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) , uygulamanın () kök sayfasını ayarlamak `Pages/_Host.cshtml` ve gezinmeyi etkinleştirmek için çağırılır.
