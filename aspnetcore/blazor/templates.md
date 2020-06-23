---
title: ASP.NET Core Blazor şablonları
author: guardrex
description: ASP.NET Core Blazor uygulama şablonları ve proje yapısı hakkında bilgi edinin Blazor .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/templates
ms.openlocfilehash: ce46d562285b95ff656ed43b3a63ca5e7315f4c8
ms.sourcegitcommit: 066d66ea150f8aab63f9e0e0668b06c9426296fd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/23/2020
ms.locfileid: "85243219"
---
# <a name="aspnet-core-blazor-templates"></a>ASP.NET Core Blazor şablonları

[Daniel Roth](https://github.com/danroth27) ve [Luke Latham](https://github.com/guardrex) tarafından

BlazorFramework, barındırma modellerinin her biri için uygulama geliştirmeye yönelik şablonlar sağlar Blazor :

* BlazorWebAssembly ( `blazorwasm` )
* BlazorSunucu ( `blazorserver` )

Barındırma modelleri hakkında daha fazla bilgi için Blazor bkz <xref:blazor/hosting-models> ..

Şablondan uygulama oluşturmaya yönelik adım adım yönergeler için Blazor , bkz <xref:blazor/get-started> ..

`--help`Seçenek CLI komutuna geçirerek şablon seçenekleri mevcuttur [`dotnet new`](/dotnet/core/tools/dotnet-new) :

```dotnetcli
dotnet new blazorwasm --help
dotnet new blazorserver --help
```

## <a name="blazor-project-structure"></a>Blazorproje yapısı

Aşağıdaki dosya ve klasörler Blazor bir şablondan oluşturulan bir uygulamayı yapar Blazor :

* `Program.cs`: Uygulamanın şunları ayarlayan giriş noktası:

  * ASP.NET Core [ana bilgisayar](xref:fundamentals/host/generic-host) ( Blazor sunucu)
  * WebAssembly Host ( Blazor webassembly): Bu dosyadaki kod, Blazor webassembly şablonundan () oluşturulan uygulamalar için benzersizdir `blazorwasm` .
    * `App`Uygulamanın kök bileşeni olan bileşeni, `app` yönteminin DOM öğesi olarak belirtilir `Add` .
    * Hizmetler, `ConfigureServices` ana bilgisayar Oluşturucu 'daki yöntemiyle yapılandırılabilir (örneğin, `builder.Services.AddSingleton<IMyDependency, MyDependency>();` ).
    * Yapılandırma, ana bilgisayar Oluşturucu () yoluyla sağlanabilir `builder.Configuration` .

* `Startup.cs`( Blazor Sunucu): uygulamanın başlangıç mantığını içerir. `Startup`Sınıfı iki yöntemi tanımlar:

  * `ConfigureServices`: Uygulamanın [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection) hizmetlerini yapılandırır. BlazorSunucu uygulamalarında, Hizmetleri çağırarak eklenir <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A> ve `WeatherForecastService` örnek bileşen tarafından kullanılmak üzere hizmet kapsayıcısına eklenir `FetchData` .
  * `Configure`: Uygulamanın istek işleme ardışık düzenini yapılandırır:
    * <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A>, tarayıcıya gerçek zamanlı bağlantı için bir uç nokta ayarlamak üzere çağırılır. Bağlantı, [SignalR](xref:signalr/introduction) uygulamalarına gerçek zamanlı Web işlevselliği ekleme çerçevesi olan ile oluşturulur.
    * [`MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*), uygulamanın () kök sayfasını ayarlamak `Pages/_Host.cshtml` ve gezinmeyi etkinleştirmek için çağırılır.

* `wwwroot/index.html`( Blazor Webassembly): BIR HTML sayfası olarak uygulanan uygulamanın kök sayfası:
  * Uygulamanın herhangi bir sayfası başlangıçta istendiğinde, Bu sayfa işlenir ve yanıtta döndürülür.
  * Bu sayfa, kök bileşenin nerede `App` işleneceğini belirtir. `App`Bileşen ( `App.razor` ), `app` içindeki metoduna DOM öğesi olarak belirtilir `AddComponent` `Startup.Configure` .
  * `_framework/blazor.webassembly.js`JavaScript dosyası yüklenir ve şunları yapın:
    * .NET çalışma zamanını, uygulamayı ve uygulamanın bağımlılıklarını indirir.
    * Uygulamayı çalıştırmak için çalışma zamanını başlatır.

* `App.razor`: Bileşeni kullanarak istemci tarafı yönlendirmeyi ayarlayan uygulamanın kök bileşeni <xref:Microsoft.AspNetCore.Components.Routing.Router> . <xref:Microsoft.AspNetCore.Components.Routing.Router>Bileşen tarayıcı gezintisini karşılar ve istenen adresle eşleşen sayfayı işler.

* `Pages`klasör: `.razor` Blazor Razor bir sunucu uygulamasının uygulamasını ve kök sayfasını oluşturan yönlendirilebilir bileşenleri/sayfaları () içerir Blazor . Her sayfanın yolu, yönergesi kullanılarak belirtilir [`@page`](xref:mvc/views/razor#page) . Şablon şunları içerir:
  * `_Host.cshtml`( Blazor Sunucu): uygulamanın kök sayfası olarak Razor uygulandı Sayfasında
    * Uygulamanın herhangi bir sayfası başlangıçta istendiğinde, Bu sayfa işlenir ve yanıtta döndürülür.
    * `_framework/blazor.server.js`Tarayıcı ve sunucu arasındaki gerçek zamanlı bağlantıyı ayarlayan JavaScript dosyası yüklenir SignalR .
    * Ana bilgisayar sayfası, kök bileşeni 'nin `App` ( `App.razor` ) nerede işleneceğini belirtir.
  * `Counter`( `Pages/Counter.razor` ): Sayaç sayfasını uygular.
  * `Error`( `Error.razor` Blazor Yalnızca sunucu uygulaması): uygulamada işlenmeyen bir özel durum oluştuğunda işlenir.
  * `FetchData`( `Pages/FetchData.razor` ): Veri getirme sayfasını uygular.
  * `Index`( `Pages/Index.razor` ): Giriş sayfasını uygular.

* `Shared`klasör: uygulama tarafından kullanılan diğer Kullanıcı Arabirimi bileşenlerini ( `.razor` ) içerir:
  * `MainLayout`( `MainLayout.razor` ): Uygulamanın Düzen bileşeni.
  * `NavMenu`( `NavMenu.razor` ): Kenar çubuğu gezintisini uygular. [ `NavLink` ](xref:blazor/fundamentals/routing#navlink-component) <xref:Microsoft.AspNetCore.Components.Routing.NavLink> Diğer bileşenlere gezinti bağlantılarını işleyen bileşeni () içerir Razor . Bileşeni, <xref:Microsoft.AspNetCore.Components.Routing.NavLink> bileşeni yüklendiği zaman otomatik olarak seçili durumu gösterir ve bu, kullanıcının hangi bileşenin görüntülenmekte olduğunu anlamasına yardımcı olur.

* `_Imports.razor`: Razor `.razor` [`@using`](xref:mvc/views/razor#using) Ad alanları için yönergeler gibi uygulamanın bileşenlerine () dahil etmek için ortak yönergeleri içerir.

* `Data`klasör ( Blazor sunucu): `WeatherForecast` `WeatherForecastService` uygulamanın bileşenine örnek Hava durumu verileri sağlayan sınıfını ve uygulamasını içerir `FetchData` .

* `wwwroot`: Uygulamanın ortak statik varlıklarını içeren uygulamanın [Web kök](xref:fundamentals/index#web-root) klasörü.

* `appsettings.json`( Blazor Sunucu): uygulamanın yapılandırma ayarları.
