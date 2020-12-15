---
title: ASP.NET Core Blazor proje yapısı
author: guardrex
description: ASP.NET Core Blazor App Project yapısı hakkında bilgi edinin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/07/2020
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
ms.openlocfilehash: 6df84366dc3fc99d31a8a0e614ca860a50df7f5c
ms.sourcegitcommit: 6b87f2e064cea02e65dacd206394b44f5c604282
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 12/15/2020
ms.locfileid: "97513551"
---
# <a name="aspnet-core-no-locblazor-project-structure"></a>ASP.NET Core Blazor proje yapısı

[Daniel Roth](https://github.com/danroth27) ve [Luke Latham](https://github.com/guardrex) tarafından

Aşağıdaki dosyalar ve klasörler Blazor bir proje şablonundan oluşturulan bir uygulamayı yapar Blazor :

::: moniker range=">= aspnetcore-5.0"

* `Program.cs`: Uygulamanın şunları ayarlayan giriş noktası:

  * ASP.NET Core [Host](xref:fundamentals/host/generic-host) ( Blazor Server )
  * WebAssembly Host ( Blazor WebAssembly ): Bu dosyadaki kod, şablondan oluşturulan uygulamalar için benzersizdir Blazor WebAssembly ( `blazorwasm` ).
    * `App`Bileşen, uygulamanın kök bileşenidir. `App`Bileşen, `div` `id` `app` `<div id="app">Loading...</div>` `wwwroot/index.html` kök bileşen koleksiyonu () ile ( `builder.RootComponents.Add<App>("#app")` ) öğesine sahip DOM öğesi olarak belirtilir.
    * [Hizmetler](xref:blazor/fundamentals/dependency-injection) eklenir ve yapılandırılır (örneğin, `builder.Services.AddSingleton<IMyDependency, MyDependency>()` ).

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* `Program.cs`: Uygulamanın şunları ayarlayan giriş noktası:

  * ASP.NET Core [Host](xref:fundamentals/host/generic-host) ( Blazor Server )
  * WebAssembly Host ( Blazor WebAssembly ): Bu dosyadaki kod, şablondan oluşturulan uygulamalar için benzersizdir Blazor WebAssembly ( `blazorwasm` ).
    * `App`Bileşen, uygulamanın kök bileşenidir. `App`Bileşen, `app` `<app>Loading...</app>` `wwwroot/index.html` kök bileşen koleksiyonunda () DOM öğesi olarak belirtilir `builder.RootComponents.Add<App>("app")` .
    * [Hizmetler](xref:blazor/fundamentals/dependency-injection) eklenir ve yapılandırılır (örneğin, `builder.Services.AddSingleton<IMyDependency, MyDependency>()` ).

::: moniker-end

* `Startup.cs` ( Blazor Server ): Uygulamanın başlangıç mantığını içerir. `Startup`Sınıfı iki yöntemi tanımlar:

  * `ConfigureServices`: Uygulamanın [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection) hizmetlerini yapılandırır. Uygulamalar ' da Blazor Server ,, çağırarak <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A> ve `WeatherForecastService` hizmet kapsayıcısına örnek bileşen tarafından kullanılmak üzere eklendikten sonra, hizmetler eklenir `FetchData` .
  * `Configure`: Uygulamanın istek işleme ardışık düzenini yapılandırır:
    * <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> , tarayıcıya gerçek zamanlı bağlantı için bir uç nokta ayarlamak üzere çağırılır. Bağlantı, [SignalR](xref:signalr/introduction) uygulamalarına gerçek zamanlı Web işlevselliği ekleme çerçevesi olan ile oluşturulur.
    * [`MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) , uygulamanın () kök sayfasını ayarlamak `Pages/_Host.cshtml` ve gezinmeyi etkinleştirmek için çağırılır.

::: moniker range=">= aspnetcore-5.0"

* `wwwroot/index.html` ( Blazor WebAssembly ): BIR HTML sayfası olarak uygulanan uygulamanın kök sayfası:
  * Uygulamanın herhangi bir sayfası başlangıçta istendiğinde, Bu sayfa işlenir ve yanıtta döndürülür.
  * Bu sayfa, kök bileşenin nerede `App` işleneceğini belirtir. Bileşeni, `div` () olan DOM öğesinin konumunda işlenir `id` `app` `<div id="app">Loading...</div>` .
  * `_framework/blazor.webassembly.js`JavaScript dosyası yüklenir ve şunları yapın:
    * .NET çalışma zamanını, uygulamayı ve uygulamanın bağımlılıklarını indirir.
    * Uygulamayı çalıştırmak için çalışma zamanını başlatır.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* `wwwroot/index.html` ( Blazor WebAssembly ): BIR HTML sayfası olarak uygulanan uygulamanın kök sayfası:
  * Uygulamanın herhangi bir sayfası başlangıçta istendiğinde, Bu sayfa işlenir ve yanıtta döndürülür.
  * Bu sayfa, kök bileşenin nerede `App` işleneceğini belirtir. Bileşen, `app` DOM öğesinin () konumunda işlenir `<app>Loading...</app>` .
  * `_framework/blazor.webassembly.js`JavaScript dosyası yüklenir ve şunları yapın:
    * .NET çalışma zamanını, uygulamayı ve uygulamanın bağımlılıklarını indirir.
    * Uygulamayı çalıştırmak için çalışma zamanını başlatır.

::: moniker-end

* `App.razor`: Bileşeni kullanarak istemci tarafı yönlendirmeyi ayarlayan uygulamanın kök bileşeni <xref:Microsoft.AspNetCore.Components.Routing.Router> . <xref:Microsoft.AspNetCore.Components.Routing.Router>Bileşen tarayıcı gezintisini karşılar ve istenen adresle eşleşen sayfayı işler.

* `Pages` klasör: uygulamayı `.razor` Blazor ve uygulamanın kök sayfasını oluşturan yönlendirilebilir bileşenleri/sayfaları () içerir Razor Blazor Server . Her sayfanın yolu, yönergesi kullanılarak belirtilir [`@page`](xref:mvc/views/razor#page) . Şablon şunları içerir:
  * `_Host.cshtml` ( Blazor Server ): Uygulamanın kök sayfası olarak Razor uygulandı Sayfasında
    * Uygulamanın herhangi bir sayfası başlangıçta istendiğinde, Bu sayfa işlenir ve yanıtta döndürülür.
    * `_framework/blazor.server.js`Tarayıcı ve sunucu arasındaki gerçek zamanlı bağlantıyı ayarlayan JavaScript dosyası yüklenir SignalR .
    * Ana bilgisayar sayfası, kök bileşeni 'nin `App` ( `App.razor` ) nerede işleneceğini belirtir.
  * `Counter` Component ( `Pages/Counter.razor` ): sayaç sayfasını uygular.
  * `Error` bileşen ( `Error.razor` Blazor Server yalnızca uygulama): uygulamada işlenmeyen bir özel durum oluştuğunda işlenir.
  * `FetchData` Component ( `Pages/FetchData.razor` ): veri getirme sayfasını uygular.
  * `Index` Component ( `Pages/Index.razor` ): giriş sayfasını uygular.
  
* `Properties/launchSettings.json`: [Geliştirme ortamı yapılandırmasını](xref:fundamentals/environments#development-and-launchsettingsjson)barındırır.

::: moniker range=">= aspnetcore-5.0"

* `Shared` klasör: uygulama tarafından kullanılan diğer Kullanıcı Arabirimi bileşenlerini ( `.razor` ) içerir:
  * `MainLayout` Component ( `MainLayout.razor` ): uygulamanın [Düzen bileşeni](xref:blazor/layouts).
  * `MainLayout.razor.css`: Uygulamanın ana düzeni için stil sayfası.
  * `NavMenu` Component ( `NavMenu.razor` ): kenar çubuğu gezintisini uygular. [ `NavLink` ](xref:blazor/fundamentals/routing#navlink-component) <xref:Microsoft.AspNetCore.Components.Routing.NavLink> Diğer bileşenlere gezinti bağlantılarını işleyen bileşeni () içerir Razor . Bileşeni, <xref:Microsoft.AspNetCore.Components.Routing.NavLink> bileşeni yüklendiği zaman otomatik olarak seçili durumu gösterir ve bu, kullanıcının hangi bileşenin görüntülenmekte olduğunu anlamasına yardımcı olur.
  * `NavMenu.razor.css`: Uygulamanın gezinti menüsünün stil sayfası.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* `Shared` klasör: uygulama tarafından kullanılan diğer Kullanıcı Arabirimi bileşenlerini ( `.razor` ) içerir:
  * `MainLayout` Component ( `MainLayout.razor` ): uygulamanın [Düzen bileşeni](xref:blazor/layouts).
  * `NavMenu` Component ( `NavMenu.razor` ): kenar çubuğu gezintisini uygular. [ `NavLink` ](xref:blazor/fundamentals/routing#navlink-component) <xref:Microsoft.AspNetCore.Components.Routing.NavLink> Diğer bileşenlere gezinti bağlantılarını işleyen bileşeni () içerir Razor . Bileşeni, <xref:Microsoft.AspNetCore.Components.Routing.NavLink> bileşeni yüklendiği zaman otomatik olarak seçili durumu gösterir ve bu, kullanıcının hangi bileşenin görüntülenmekte olduğunu anlamasına yardımcı olur.
  
::: moniker-end

* `_Imports.razor`: Razor `.razor` [`@using`](xref:mvc/views/razor#using) Ad alanları için yönergeler gibi uygulamanın bileşenlerine () dahil etmek için ortak yönergeleri içerir.

* `Data` Folder ( Blazor Server ): `WeatherForecast` `WeatherForecastService` uygulamanın bileşenine örnek Hava durumu verileri sağlayan sınıfını ve uygulamasını içerir `FetchData` .

* `wwwroot`: Uygulamanın ortak statik varlıklarını içeren uygulamanın [Web kök](xref:fundamentals/index#web-root) klasörü.

* `appsettings.json`: Uygulama için [yapılandırma ayarlarını](xref:blazor/fundamentals/configuration) tutar. Bir Blazor WebAssembly uygulamada, uygulama ayarları dosyası `wwwroot` klasöründe bulunur. Bir Blazor Server uygulamada, uygulama ayarları dosyası proje kökünde bulunur.
