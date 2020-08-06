---
title: ASP.NET Core Blazor şablonları
author: guardrex
description: ASP.NET Core Blazor uygulama şablonları ve proje yapısı hakkında bilgi edinin Blazor .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/04/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/templates
ms.openlocfilehash: 65d6a3156419b57eae6c7e41a9778fa25fd88f4f
ms.sourcegitcommit: 6eacadf1be61679ab8e6f781ece76b7395512879
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/04/2020
ms.locfileid: "87758534"
---
# <a name="aspnet-core-no-locblazor-templates"></a>ASP.NET Core Blazor şablonları

[Daniel Roth](https://github.com/danroth27) ve [Luke Latham](https://github.com/guardrex) tarafından

BlazorFramework, barındırma modellerinin her biri için uygulama geliştirmeye yönelik şablonlar sağlar Blazor :

* Blazor WebAssembly (`blazorwasm`)
* Blazor Server (`blazorserver`)

Barındırma modelleri hakkında daha fazla bilgi için Blazor bkz <xref:blazor/hosting-models> ..

`--help`Seçenek CLI komutuna geçirerek şablon seçenekleri mevcuttur [`dotnet new`](/dotnet/core/tools/dotnet-new) :

```dotnetcli
dotnet new blazorwasm --help
dotnet new blazorserver --help
```

## <a name="no-locblazor-project-structure"></a>Blazorproje yapısı

Aşağıdaki dosyalar ve klasörler Blazor bir proje şablonundan oluşturulan bir uygulamayı yapar Blazor :

* `Program.cs`: Uygulamanın şunları ayarlayan giriş noktası:

  * ASP.NET Core [Host](xref:fundamentals/host/generic-host) ( Blazor Server )
  * WebAssembly Host ( Blazor WebAssembly ): Bu dosyadaki kod, şablondan oluşturulan uygulamalar için benzersizdir Blazor WebAssembly ( `blazorwasm` ).
    * `App`Bileşen, uygulamanın kök bileşenidir. `App`Bileşen, `app` `<app>...</app>` kök bileşen koleksiyonuna () DOM öğesi () olarak belirtilir `builder.RootComponents.Add<App>("app")` .
    * [Hizmetler](xref:blazor/fundamentals/dependency-injection) eklenir ve yapılandırılır (örneğin, `builder.Services.AddSingleton<IMyDependency, MyDependency>()` ).

* `Startup.cs`( Blazor Server ): Uygulamanın başlangıç mantığını içerir. `Startup`Sınıfı iki yöntemi tanımlar:

  * `ConfigureServices`: Uygulamanın [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection) hizmetlerini yapılandırır. Uygulamalar ' da Blazor Server ,, çağırarak <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A> ve `WeatherForecastService` hizmet kapsayıcısına örnek bileşen tarafından kullanılmak üzere eklendikten sonra, hizmetler eklenir `FetchData` .
  * `Configure`: Uygulamanın istek işleme ardışık düzenini yapılandırır:
    * <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A>, tarayıcıya gerçek zamanlı bağlantı için bir uç nokta ayarlamak üzere çağırılır. Bağlantı, [SignalR](xref:signalr/introduction) uygulamalarına gerçek zamanlı Web işlevselliği ekleme çerçevesi olan ile oluşturulur.
    * [`MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*), uygulamanın () kök sayfasını ayarlamak `Pages/_Host.cshtml` ve gezinmeyi etkinleştirmek için çağırılır.

* `wwwroot/index.html`( Blazor WebAssembly ): BIR HTML sayfası olarak uygulanan uygulamanın kök sayfası:
  * Uygulamanın herhangi bir sayfası başlangıçta istendiğinde, Bu sayfa işlenir ve yanıtta döndürülür.
  * Bu sayfa, kök bileşenin nerede `App` işleneceğini belirtir. Bileşen, `app` DOM öğesinin () konumunda işlenir `<app>...</app>` .
  * `_framework/blazor.webassembly.js`JavaScript dosyası yüklenir ve şunları yapın:
    * .NET çalışma zamanını, uygulamayı ve uygulamanın bağımlılıklarını indirir.
    * Uygulamayı çalıştırmak için çalışma zamanını başlatır.

* `App.razor`: Bileşeni kullanarak istemci tarafı yönlendirmeyi ayarlayan uygulamanın kök bileşeni <xref:Microsoft.AspNetCore.Components.Routing.Router> . <xref:Microsoft.AspNetCore.Components.Routing.Router>Bileşen tarayıcı gezintisini karşılar ve istenen adresle eşleşen sayfayı işler.

* `Pages`klasör: uygulamayı `.razor` Blazor ve uygulamanın kök sayfasını oluşturan yönlendirilebilir bileşenleri/sayfaları () içerir Razor Blazor Server . Her sayfanın yolu, yönergesi kullanılarak belirtilir [`@page`](xref:mvc/views/razor#page) . Şablon şunları içerir:
  * `_Host.cshtml`( Blazor Server ): Uygulamanın kök sayfası olarak Razor uygulandı Sayfasında
    * Uygulamanın herhangi bir sayfası başlangıçta istendiğinde, Bu sayfa işlenir ve yanıtta döndürülür.
    * `_framework/blazor.server.js`Tarayıcı ve sunucu arasındaki gerçek zamanlı bağlantıyı ayarlayan JavaScript dosyası yüklenir SignalR .
    * Ana bilgisayar sayfası, kök bileşeni 'nin `App` ( `App.razor` ) nerede işleneceğini belirtir.
  * `Counter`( `Pages/Counter.razor` ): Sayaç sayfasını uygular.
  * `Error`( `Error.razor` Blazor Server yalnızca uygulama): uygulamada işlenmeyen bir özel durum oluştuğunda işlenir.
  * `FetchData`( `Pages/FetchData.razor` ): Veri getirme sayfasını uygular.
  * `Index`( `Pages/Index.razor` ): Giriş sayfasını uygular.
  
* `Properties/launchSettings.json`: [Geliştirme ortamı yapılandırmasını](xref:fundamentals/environments#development-and-launchsettingsjson)barındırır.

* `Shared`klasör: uygulama tarafından kullanılan diğer Kullanıcı Arabirimi bileşenlerini ( `.razor` ) içerir:
  * `MainLayout`( `MainLayout.razor` ): Uygulamanın [Düzen bileşeni](xref:blazor/layouts).
  * `NavMenu`( `NavMenu.razor` ): Kenar çubuğu gezintisini uygular. [ `NavLink` ](xref:blazor/fundamentals/routing#navlink-component) <xref:Microsoft.AspNetCore.Components.Routing.NavLink> Diğer bileşenlere gezinti bağlantılarını işleyen bileşeni () içerir Razor . Bileşeni, <xref:Microsoft.AspNetCore.Components.Routing.NavLink> bileşeni yüklendiği zaman otomatik olarak seçili durumu gösterir ve bu, kullanıcının hangi bileşenin görüntülenmekte olduğunu anlamasına yardımcı olur.

* `_Imports.razor`: Razor `.razor` [`@using`](xref:mvc/views/razor#using) Ad alanları için yönergeler gibi uygulamanın bileşenlerine () dahil etmek için ortak yönergeleri içerir.

* `Data`Folder ( Blazor Server ): `WeatherForecast` `WeatherForecastService` uygulamanın bileşenine örnek Hava durumu verileri sağlayan sınıfını ve uygulamasını içerir `FetchData` .

* `wwwroot`: Uygulamanın ortak statik varlıklarını içeren uygulamanın [Web kök](xref:fundamentals/index#web-root) klasörü.

* `appsettings.json`: Uygulama için [yapılandırma ayarlarını](xref:blazor/fundamentals/configuration) tutar. Bir Blazor WebAssembly uygulamada, uygulama ayarları dosyası `wwwroot` klasöründe bulunur. Bir Blazor Server uygulamada, uygulama ayarları dosyası proje kökünde bulunur.
